.. -*- coding: utf-8; -*-

Last update: |today|

.. _cha-soccerserver:

*************************************************
Soccer Server
*************************************************


==================================================
Objects
==================================================

.. figure:: ./images/objects.*
  :align: center
  :scale: 80%
  :name: objects

  UML diagram of the objects in the simulation

==================================================
Protocols
==================================================

.. include:: soccerserver/protocols.rst


.. _sec-sensormodels:

==================================================
Sensor Models
==================================================

A RoboCup agent has three different sensors (and one special sensor).
The aural sensor detects messages sent by the referee, the coaches and the
other players.
The visual sensor detects visual information about the field, like the
distance and direction to objects in the player's current field of
view. The visual sensor also works as a proximity sensor by "seeing"
objects that are close, but behind the player.
The body sensor detects the current "physical" status of the player, like
its stamina, speed and neck angle.
Together the sensors give the agent quite a good picture of the environment.

--------------------------------------------------
Aural Sensor Model
--------------------------------------------------

.. include:: soccerserver/sensor-models-aural-sensor-model.rst


.. _sec-visionsensor:

--------------------------------------------------
Vision Sensor Model
--------------------------------------------------

.. include:: soccerserver/sensor-models-vison-sensor-model.rst


--------------------------------------------------
Body Sensor Model
--------------------------------------------------

.. include:: soccerserver/sensor-models-body-sensor-model.rst


--------------------------------------------------
Fullstate Sensor Model
--------------------------------------------------

**TODO**

.. _sec-movementmodels:

==================================================
Movement Models
==================================================

.. include:: soccerserver/movement-models.rst


==================================================
Action Models
==================================================

.. include:: soccerserver/action-models-catch-model.rst

.. include:: soccerserver/action-models-dash-model.rst

.. include:: soccerserver/action-models-kick-model.rst


--------------------------------------------------
Move Model
--------------------------------------------------

The *move command* can be used to place a player directly onto a desired position on the field. move exists to set up the team and does not work during normal play. It is available at the beginning of each half (play mode ``before_kick_off``’) and after a goal has been scored (play modes ``goal_l_?`` or ``goal_r_?`` ’). In these situations, players can be placed on any position in their own half (i.e. X < 0) and can be moved any number of times, as long as the play mode does not change. Players moved to a position on the opponent half will be set to a random position on their own side by the server.
A second purpose of the *move command* is to move the goalie within the penalty area after the goalie caught the ball. If the goalie caught the ball, it can move together with the ball within the penalty area. The goalie is allowed to move *goalie_max_moves* times before it kicks the ball. Additional *move commands* do not have any effect and the server will respond with (error too_many_moves).

.. table:: Parameter for the move_command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |goalie_max_moves                                 |2          |
   +-------------------------------------------------+-----------+


--------------------------------------------------
Say Model
--------------------------------------------------

Using the *say command*, players can broadcast messages to other players. Messages can be say_msg_size characters long, where valid characters for say messages are from the set sth (without the square brackets). Messages players say can be heard within a distance of *audio_cut_dist* by members of both teams . **Say messages** sent to the server will be sent back to players within that distance immediately. The use of the *say command* is only restricted by the limited capacity of the players of hearing messages.

.. table:: Parameter for the say command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |say_msg_size                                     |10         |
   +-------------------------------------------------+-----------+
   |audio_cut_dist                                   |50         |
   +-------------------------------------------------+-----------+
   |hear_max                                         |1          |
   +-------------------------------------------------+-----------+
   |hear_inc                                         |1          |
   +-------------------------------------------------+-----------+
   |hear_decay                                       |1          |
   +-------------------------------------------------+-----------+


--------------------------------------------------
Tackle Model
--------------------------------------------------

The tackle command is to accelerate the ball towards the player's
body(**TODO:new tackle model [12.0.0 pre-20080210]**).
Players can kick the ball that can not be kicked with the kick command
by executing the tackle command.
The success of tackle depends on a random probability related to the
position of the ball. It can be obtained by the following formula.

The probability of a tackle failure when the ball is in front of the player is:

.. math::

  {\mathrm fail\_prob = (player\_to\_ball.x \div tackle\_dist)^{tackle\_exponent} + (player\_to\_ball.y \div tackle\_width)^{tackle\_exponent}}

The probability of a tackle failure when the ball is behind the player is:

.. math::

  {\mathrm fail\_prob = (player\_to\_ball.x \div tackle\_back\_dist)^{tackle\_exponent} + (player\_to\_ball.y \div tackle\_back\_width)^{tackle\_exponent}}

The probability of processing success is:

.. math::

  {\mathrm tackle\_prob = 1.0 – fail\_prob}

In this case, when the ball is in front of the player, it is used to *tackle_dist* (default is 2.0), otherwise it is used to **tackle_back_dist** (default is 0.5); **player_to_ball** is a vector from the player to the ball, relative to the body direction of the player. When the tackle command is successful, it will give the ball an acceleration in its own body direction.

The execution effect of tackle is similar to that of kick, which is obtained by multiplying the parameter **tackle_power_rate** (default is 0.027) with power. It can be expressed by the following formula:

.. math::

  {\mathrm effective\_power} = {\mathrm power} \times {\mathrm tackle\_power\_rate}

Once the player executes the tackle command, whether successful or not, the player can no longer move within 10 cycles. The following table shows the parameters used in tackle command.

**TODO**

- [12.0.0 pre-20080210] new kick/tackle noise model
- [12.0.0 pre-20080210] max_back_tackle_power
- [13.0.0] forbid backward tackle
- [14.0.0] increasing tackle noise using server::tackle_rand_factor

.. table:: Parameters for the tackle command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |tackle_dist                                      |2          |
   +-------------------------------------------------+-----------+
   |tackle_back_dist                                 |0          |
   +-------------------------------------------------+-----------+
   |tackle_width                                     |1.25       |
   +-------------------------------------------------+-----------+
   |tackle_cycles                                    |10         |
   +-------------------------------------------------+-----------+
   |tackle_exponent                                  |6          |
   +-------------------------------------------------+-----------+
   |tackle_power_rate                                |0.027      |
   +-------------------------------------------------+-----------+
   |max_tackle_power                                 |100        |
   +-------------------------------------------------+-----------+
   |max_back_tackle_power                            |0          |
   +-------------------------------------------------+-----------+
   |tackle_rand_factor                               |2          |
   +-------------------------------------------------+-----------+

--------------------------------------------------
Foul Model
--------------------------------------------------

**TODO**

- [14.0.0] foul model and intentional foul option
- [14.0.0] trade off between foul detect probability and kick power rate
- [15.0.0] improve foul model (red_card_probability)


--------------------------------------------------
Turn Model
--------------------------------------------------

While *dash* is used to accelerate the player in direction of its
body, the *turn command* is used to change the players body direction.
The argument for the turn command is the moment; valid values for the
moment are between **server::minmoment** and **server::maxmoment**.
If the player does not move, the moment is equal to the angle the
player will turn. However, there is a concept of inertia that makes it
more difficult to turn when you are moving.
Specifically, the actual angle the player is turned is as follows:

.. math::

   {\mathrm actual\_angle} = {\mathrm moment \div (1.0 + inertia\_moment} \times {\mathrm player\_speed)}

**server::inertia_moment** is a server.conf parameter with default
value 5.0.
Therefore (with default values), when the player is at speed 1.0, the
*maximum effective* turn he can do is :math:`\pm30`.
However, notice that because you can not dash and turn during the same
cycle, the fastest that a player can be going when executing a turn is
:math:`player\_speed\_max \times player\_decay`, which means the effective turn for a default player
(with default values) is :math:`\pm60`.

For heterogeneous players, the inertia moment is the default inertia
value plus a value between
:math:`{\mathrm player\_decay\_delta\_min \times inertia\_moment\_delta\_factor}` and
:math:`{\mathrm player\_decay\_delta\_max \times inertia\_moment\_delta\_factor}`.

.. table:: Turn Model Parameter

   +-----------------------+------------------------+--------------------------------------+--------+
   || Default Parameters   || Default Value (Range) || Heterogeneous Player Parameters     || Value |
   ||  ``server.conf``     |                        ||  ``player.conf``                    |        |
   +=======================+========================+======================================+========+
   |       Name            |                        |         Name                         |        |
   +-----------------------+------------------------+--------------------------------------+--------+
   |server::minmoment      | -180                   |                                      |        |
   +-----------------------+------------------------+--------------------------------------+--------+
   |server::maxmoment      |  180                   |                                      |        |
   +-----------------------+------------------------+--------------------------------------+--------+
   |server::inertia_moment | 5.0([2.5, 7.5])        || player::player_decay_delta_min      || -0.1  |
   |                       |                        || player::player_decay_delta_max      || 0.1   |
   |                       |                        || player::inertia_moment_delta_factor || 25    |
   +-----------------------+------------------------+--------------------------------------+--------+

.. _sec-turnneckmodel:

--------------------------------------------------
TurnNeck Model
--------------------------------------------------

With *turn_neck*, a player can turn its neck somewhat independently of
its body.
The angle of the head of the player is the viewing angle of the
player.
The *turn command* changes the angle of the body of the player while
turn_neck changes the neck angle of the player relative to its body.
The **minimum** and **maximum** relative angle for the player’s neck
are given by **server::minneckang** and **server::maxneckang** in
server.conf.
Remember that the neck angle is relative to the body of the player so
if the client issues a *turn command*, the viewing angle changes even
if no turn_neck command was issued.
Also, *turn_neck commands* can be executed during the same cycle as
turn, dash, and *kick commands*.
turn_neck is not affected by momentum like turn is.
The argument for a *turn_neck command* must be in the range between
**server::minneckmoment** and **server::maxneckmoment**.

.. table:: Parameter for the turn neck command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |minneckang                                       | -90       |
   +-------------------------------------------------+-----------+
   |maxneckang                                       |  90       |
   +-------------------------------------------------+-----------+
   |minneckmoment                                    | -180      |
   +-------------------------------------------------+-----------+
   |maxneckmoment                                    |  180      |
   +-------------------------------------------------+-----------+

--------------------------------------------------
Change Focus Model
--------------------------------------------------
The focus point is a feature developed in server v.18, 
which can be used by players v.18 and above. 
It represents a position inside a player's view angle, 
and can be up to 40.0 meters away from the player's position. 
The focus point affects the visual sensor noise model, 
with the noise of observed objects increasing as the distance 
between the focus point and the object increases.

The initial position of the focus point is the player's position, 
and if a player does not change the focus point position, 
the server visual noise model behaves as in server v.17. 
However, a player can change the position of the focus point 
by sending a **change_focus** command. This command takes two parameters, 
*dist_moment* and *dir_moment*, and changes the position 
of the focus point relative to the player's neck angle.

It is important to note that players are not allowed 
to move the focus point outside of their view angle. 
Additionally, if a player changes their view angle to a smaller one, 
the server will automatically move the focus point back 
into the player's view angle.

--------------------------------------------------
Pointto Model
--------------------------------------------------

Players can send commands to point to a spot on the field of the form:

  (pointto <DIST> <DIR>)

or

  (pointto off)

The first form will cause the arm to point to the spot DIST meters
from the player in DIR direction, relative to the player's current
face direction.
The player will continue to point to the same location on the field
independent of an motion or rotation of the player for at least
**server::point_to_ban** cycles, and until another ``pointto`` command is
issued or **server::point_to_duration** cycles pass.
The second form disables a previous call of pointto.

.. table:: Parameter for the pointto command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |point_to_ban                                     |  5        |
   +-------------------------------------------------+-----------+
   |point_to_duration                                |  20       |
   +-------------------------------------------------+-----------+

Version 8+ clients can see where a player is pointing, if the player is pointing, the player is in view and they are close enough to determine their team name.
In these cases the player part of the ``see`` message has the form (without the newline):

    (p "<TEAMNAME>" <UNUM>) <DIST> <DIR> <DISTCHG> <DIRCHG>
                            <BDIR> <HDIR> <POINTDIR>)

  or

    (p "<TEAMNAME>") <DIST> <DIR> <POINTDIR>)

Where ``POINTDIR`` is the direction the players are is pointing with random Gaussian (normal)noise added to the actual direction, with a mean of zero and a standard deviation calculated as follows:

    sigma = pow(dist / team_too_far_length, 4) * 178.25 + 1.75

This means that sigma is a minimum of 1.75 deg and reaches 180 deg
when the player is observing a pointing arm from a distance of team_too_far_length.
Since 95% of values in a normal distributionare within two standard deviations,
then 95% of the time the noise will be in the range +- 2.5 deg when the player is very close and in the range +- 360.0 deg
when the player is team_too_far_length away.

``sense_body`` messages for version 8+ clients contain information about the arm actuator.
The following has been inserted into the sense_body message, just before the last ')', without the new line:

    (arm (movable <MOVABLE>) (expires <EXPIRES>)
         (target <DIST> <DIR>) (count <COUNT>))

Where:

- <MOVABLE> is the number of cycles till the arm is movable. 0 indicates the arm is movable now
- <EXPIRES> is the number of cycles till the arm stops pointing. 0 indicates that the arm is no longer pointing,
- <DIST> and <DIR> are the distance and direction of the point the player is pointing to, relative to the players location, orientation and neck angle, accurate to 10cm or 0.1 deg.
- <COUNT> is the number of times the ``pointto`` command has been successfully executed by the player.

Fullstate messages have both <POINTDIST> and <POINTDIR> included between neck angle and stamina.
The players own arm state has the same format as in sense body (see below) and can be found between the count and score part.

Version 8+ coaches (on and offline) can see where a player is pointing to if the player is pointing.
The direction the player is pointing comes just after the players neck angle.


.. _sec-attentiontomodel:

--------------------------------------------------
Attentionto Model
--------------------------------------------------

Version 8 and above players can send ``attentionto`` commands to focus their attention on a particular player.
The command has the form:

  (attentionto <TEAM> <UNUM>) | (attentionto off)

Where ``<TEAM>`` is

  ``opp`` | ``our`` | ``l`` | ``r`` | ``left`` | ``right`` | <TEAM_NAME>

and ``<UNUM>`` is integer identifying a member of the team specified.
Players can only focus on one player at a time (each attentionto command
overrides the previous) and cannot focus on themselves.

See :ref:`sec-sensormodels` in detail about the aural sensor.

.. _sec-heterogeneousplayers:

==================================================
Heterogeneous Players
==================================================

With the rcssserver version 7, heterogeneous players were introduced.
For heterogeneous players, the server generates
**player::player_types** random player types at startup.
The player types have different capabilities based on the trade-offs
defined in the player.conf file.
Both teams of a match use the same player types.
Type 0 is the default type and is always the same.
If **player::random_seed** is not 0, the fixed set of heterogenous
player paramters can be generated based on the given seed value.
:numref:`tab-hetero` shows the differences of heterogeneous players:

When players and coaches connect to the server, they can receive
information about the available player types.
The online coaches can change player types unlimited times before the
first kick off and change player types **player::subs_max** times
during other non-`play_on` play modes using the `change_player_type`
command (see :ref:`sec-coachcommand`).

The online coach can substitute a same player type within
**player::pt_max** times.
This restriction also applied to the default player type.
This means that all field players have to be changed to the
non-default type.
In version 16, the goalie is still allowed to be assigned the default type.
However, if server::allow_mult_default_type is false and teams
use the default player type more than player::pt_max, rcssserver
automatically assign the heterogeneous player type to field
players just before the playmode is changed to kick-off.

The online coach can substitute a same player type within
**player::pt_max** times.
This restriction is not applied to the default player type.
If player::pt_max is 1, each player type except the default type can be used only once.

Each time a player is substituted by some other player type, its
stamina, recovery and effort is reset to the initial (maximum) value
of the respective player type.

.. table:: The parameter differences of heterogeneous players
   :name: tab-hetero

   +--------------------------+-----------------------------------------------------------------------------------------------+
   |Parameter                 |Description                                                                                    |
   +==========================+===============================================================================================+
   |PlayerSpeedMax            |maximum speed                                                                                  |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |StaminaIncMax             |Amount of stamina recovered in one step                                                        |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |PlayerDecay               |Player speed decay rate                                                                        |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |InertiaMoment             |Player inertia force when moving                                                               |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |DashPowerRate             |Dash acceleration rate                                                                         |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |PlayerSize                | Player size                                                                                   |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |KickableMargin            |Kickable area radius                                                                           |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |KickRand                  |The amount of noise added to the kick                                                          |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |ExtraStamina              |Extra stamina available when stamina is exhausted                                              |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |EffortMax                 |Maximum value of the player's effort amount                                                    |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |EffortMin                 |The minimum amount of effort for the player                                                    |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |CatchAreaLengthStretch    |Streach Length to Catch                                                                        |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |KickPowerRate             |Kick Power Rate                                                                                |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |FoulDetectProbability     |Probability that the referee will take the foul                                                |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |UnumFarLength             |If dist less than unum_far_length, then both uniform number and team name are visible          |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |UnumTooFarLength          |If dist more than unum_too_far_length, then the uniform number is not visible                  |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |TeamFarLength             |If dist less than team_far_length, then the team name is visible                               |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |TeamTooFarLength          |If dist more than team_too_far_length, then the team name is not visible.                      |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |PlayerMaxObservationLength|If dist more than player_max_observation_length, then the player is not visible.               |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |BallVelFarLength          |If dist less than ball_vel_far_length, then ball vel is visible                                |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |BallVelTooFarLength       |If dist more than ball_vel_too_far_length, then ball vel is not visible                        |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |BallMaxObservationLength  |If dist more than ball_max_observation_length, then the ball is not visible.                   |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |FlagChgFarLength          |If dist less than flag_chg_far_length, then the flag dist change is sent.                      |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |FlagChgTooFarLength       |If dist less than flag_chg_too_far_length, then the flag dist change is not sent.              |
   +--------------------------+-----------------------------------------------------------------------------------------------+
   |FlagMaxObservationLength  |If dist more than flag_max_observation_length, then the flag is not visible.                   |
   +--------------------------+-----------------------------------------------------------------------------------------------+
  

Heterogeneous player parameters given for each match are different.
Therefore, each agent does not necessarily have the parameters needed to implement the tactics.
Whatever the situation, you need a way to choose the best combination of heterogeneous players.


.. table:: Parameter for substitutions and heterogeneous player types

   +----------------------------+---------+
   |Parameter in player.conf    |Value    |
   +============================+=========+
   |player_types                |18       |
   +----------------------------+---------+
   |subs_max                    |3        |
   +----------------------------+---------+
   |pt_max                      |1        |
   +----------------------------+---------+


==================================================
Referee Model
==================================================

The Automated Referee sends messages to the players, so that players know the actual
play mode of the game. The rules and the behavior for the automated referee are
described in Sec. :ref:`sec-overview-referee`.
Players receive the referee messages as hear messages.
A player can hear referee messages in every situation independent of
the number of messages the player heard from other players.

.. _sec-playmodes:

--------------------------------------------------
Play Modes and referee messages
--------------------------------------------------

The change of the play mode is announced by the referee. Additionally, there are some
referee messages announcing events like a goal or a foul. If you have a look into the
server source code, you will notice some additional play modes that are currently not
used. Both play modes and referee messages are announced using (referee String ),
where String is the respective play mode or message string. The play modes are listed
in :numref:`tab-playmode`, for the messages see :numref:`tab-refereemessages`.

.. table:: Play Modes
   :name: tab-playmode

   +-----------------------------+------+----------------------+------------------------------------------+
   |Play Mode                    |tc    | subsequent play mode | comment                                  |
   +=============================+======+======================+==========================================+
   |before_kick_off              |0     |  kick_off\_\ *Side*  |at the beginning of a half                |
   +-----------------------------+------+----------------------+------------------------------------------+
   |play_on                      |      |                      |during normal play                        |
   +-----------------------------+------+----------------------+------------------------------------------+
   |time_over                    |      |                      |End of the game                           |
   +-----------------------------+------+----------------------+------------------------------------------+
   |kick_off\_\ *Side*           |      |                      |announce start of play                    |
   |                             |      |                      |(after pressing the Kick Off button)      |
   +-----------------------------+------+----------------------+------------------------------------------+
   |kick_in\_\ *Side*            |      | 	               |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |free_kick\_\ *Side*          |      |                      |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |corner_kick\_\ *Side*        |      |                      |when the ball goes out of play over the   |
   |                             |      |                      |goal line, without a goal being scored    |
   |                             |      |                      |and having last been touched by a member  |
   |                             |      |                      |of the defending team.                    |
   +-----------------------------+------+----------------------+------------------------------------------+
   |goal_kick_*Side*             |      |  play_on             |play mode changes once                    |
   |                             |      |                      |the ball leaves the penalty area          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |goal_*Side*                  |      |                      |currently unused                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |drop_ball                    |0     | play_on              |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |offside\_\ *Side*            |30    | free_kick\_\ *Side*  |An offside player who is closer to the    |
   |                             |      |                      |opponent's goal when his teammate hits    |
   |                             |      |                      |the ball, both in front of the ball and   |
   |                             |      |                      |in front of the last player of the        |
   |                             |      |                      |opposing team.                            |
   |                             |      |                      |The offside rule prevents players from    |
   |                             |      |                      |concentrating in front of the opponent's  |
   |                             |      |                      |goal, as no player can stand near the     |
   |                             |      |                      |opponent's goal and have a chance to      |
   |                             |      |                      |score by waiting for the ball, and the    |
   |                             |      |                      |possibility of sending long passes close  |
   |                             |      |                      |to the opponent's goal is limited. In     |
   |                             |      |                      |this way, defenders can distance          |
   |                             |      |                      |themselves from their own goal and        |
   |                             |      |                      |participate more during the game.         |
   +-----------------------------+------+----------------------+------------------------------------------+
   |penalty_kick\_\ *Side*       |      |                      |When the game ends in a draw of 6,000     |
   |                             |      |                      |cycles and overtime, the winner will be   |
   |                             |      |                      |determined by penalty kicks.              |
   +-----------------------------+------+----------------------+------------------------------------------+
   |foul_charge\_\ *Side*        |      |                      |Pushing the opposing player               |
   +-----------------------------+------+----------------------+------------------------------------------+
   |back_pass\_\ *Side*          |      |                      |A goalkeeper is not allowed to catch the  |
   |                             |      |                      |ball inside his own penalty area if a     |
   |                             |      |                      |teammate sends the ball to him.           |
   |                             |      |                      |The opposing team will receive an         |
   |                             |      |                      |indirect free-kick at the point of touch  |
   |                             |      |                      |if the goalkeeper makes the mistake.      |
   +-----------------------------+------+----------------------+------------------------------------------+
   |free_kick_fault\_\ *Side*    |      |                      |Players are not allowd to kick the ball   |
   |                             |      |                      |to themselves after a free kick. If a     |
   |                             |      |                      |player does kick the ball to themselves   |
   |                             |      |                      |after a free kick, a free kick is awarded |
   |                             |      |                      |to the opposing team at the point that    |
   |                             |      |                      |the second kick occurred.                 |
   +-----------------------------+------+----------------------+------------------------------------------+
   |indirect_free_kick\_\ *Side* |      |                      |In a direct free kick, the player can     |
   |                             |      |                      |shoot the ball directly towards the goal, |
   |                             |      |                      |but an indirect free kick cannot and      |
   |                             |      |                      |must pass the ball to a teammate.         |
   +-----------------------------+------+----------------------+------------------------------------------+
   |illegal_defense\_\ *Side*    |      |                      |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+

where Side is either the character *l* or *r*, OSide means opponent’s side.
tc is the time (in number of cycles) until the subsequent play mode will be announced

.. table:: Referee Messages
   :name: tab-refereemessages

   +-------------------------+------+----------------------+----------------------------------------+
   |Message                  |tc    | subsequent play mode | comment                                |
   +=========================+======+======================+========================================+
   |goal_*Side*_*n*          | 50   | kick_off_*OSide*     |announce the *n* th goal for a team     |
   +-------------------------+------+----------------------+----------------------------------------+
   |foul_*Side*              | 0    | free_kick_*OSide*    |announce a foul                         |
   +-------------------------+------+----------------------+----------------------------------------+
   |yellow_card_*Side*_*Unum*| 0    |                      |announce an yellow card information     |
   +-------------------------+------+----------------------+----------------------------------------+
   |red_card_*Side*_*Unum*   | 0    |                      |announce a red card information         |
   +-------------------------+------+----------------------+----------------------------------------+
   |goalie_catch_ball_*Side* | 0    | free_kick_*OSide*    |                                        |
   +-------------------------+------+----------------------+----------------------------------------+
   |time_up_without_a_team   | 0    | time_over	           |sent if there was no opponent until     |
   |                         |      |                      |the end of the second half              |
   +-------------------------+------+----------------------+----------------------------------------+
   |time_up                  | 0    | time_over	           |sent once the game is over	            |
   |                         |      |                      |(if the time is ≥ second half and       |
   |                         |      |                      |the scores for each team are different) |
   +-------------------------+------+----------------------+----------------------------------------+
   |half_time                | 0    | before_kick_off      |                                        |
   +-------------------------+------+----------------------+----------------------------------------+
   |time_extended            | 0    | before_kick_off      |                                        |
   +-------------------------+------+----------------------+----------------------------------------+

where *Side* is either the character `l` or `r`, *OSide* means opponent’s side.
tc is the time (in number of cycles) until the subsequent play mode will be announced.

--------------------------------------------------
Time Referee
--------------------------------------------------

**TODO**

- Judges the game time
- server::half_time
- [12.1.3] server::extra_half_time
- [13.0.0] change a length of overtime

--------------------------------------------------
Offside Referee
--------------------------------------------------

The offside referee is a module that observes the field, particularly passes, to check whether the offside foul happens.
This module determines offside lines every cycle, then specifies several candidates from players which would result in an offside if they receive a pass.

The referee is configurable by some parameters in server.conf file. some useful parameters are explained below.

::

  server::use_offside = true  // true: enable, false: disable

This parameter determines whether the offside referee is enabled or disabled. 

::

  server::offside_active_area_size = 2.5

This parameter determines the radius of an area around a candidate pass receiver.
If the ball enters the area and the candidate performs a kick or tackle command, the offside foul is called.
Offside is also called if the candidate collides with the ball.

::

  offside_kick_margin = 9.15

This parameter determines the radius of area that every player in the team which has done offside foul must stay out when the other team wants to free-kick. If there is a player in that area, server moves them out of that.

.. figure:: ./images/offside-example.*
  :align: center
  :name: offside-example


--------------------------------------------------
FreeKick Referee
--------------------------------------------------

Free kicks are detected automatically by the soccer server in many relevant cases.
The Free kick referee is a module that observes the play mode, to check whether the free kick foul happens and what should teams do.
Some methods are explained below.

::

  void FreeKickRef::kickTaken

This method is executed when foul has occurred by the player. This method checks whether the kick is correctly done or not.

::

  void FreeKickRef::tackleTaken

This method is executed when a tackle foul has occurred by the player

::

  void FreeKickRef::ballTouched

This method checks whether the ball has been touched by an unauthorized player.

::
  
  void FreeKickRef::analyse

This method checks the game play mode and removes unauthorized players from the foul area due to the situation.

::

  void FreeKickRef::playModeChange

This method provides the free kick conditions according to the game mode and occurs when the mode has changed.

::

  void FreeKickRef::callFreeKickFault

This method is for calling the free kick and receives the side and the foul location as inputs.

::
  
  bool FreeKickRef::goalKick

If the right or left goal kick has occurred, the output value of this method is true.

::

  bool FreeKickRef::freeKick

If foul occurs, the output value of this method is true.

::
  
  bool FreeKickRef::ballStopped

If the ball stops moving, the output value of this method is true.

::

  bool FreeKickRef::tooManyGoalKicks

If the value of goal kick count is greater than maxGoalKicks the output value of this method is true.

::

  void FreeKickRef::placePlayersForGoalkick

This method sends the opponent players out of the penalty area if a goal kick occurs.


--------------------------------------------------
Touch Referee
--------------------------------------------------

**TODO**

- Judge the goal
- [14.0.0] golden goal option, server::golden_goal

Checking for goals, out of bounds and within penalty area no
complies with FIFA regulations. For a goal to be scored the ball
must be totally within the goal - i.e.

.. math::

  |ball.x| > FIELD\_LENGTH \cdot 0.5 + ball\_radius

Similarly the ball must be completely out of the pitch before it is
considered out - i.e

.. math::

  |ball.x| &> FIELD\_LENGTH \cdot 0.5 + ball\_radius \: ||\\
  |ball.y| &> FIELD\_WIDTH \cdot 0.5 + ball\_radius

Lastly the ball is within the penalty area (and thus catchable) if
the ball is at least partially within the penalty area - i.e.

.. math::

  |ball.y| &<= PENALTY\_WIDTH \cdot 0.5 + ball\_radius \: \&\&\\
  |ball.x| &<= FIELD\_LENGTH \cdot 0.5 + ball\_radius \: \&\&\\
  |ball.x| &>= FIELD\_LENGTH \cdot 0.5 - (PENALTY\_LENGTH \cdot 0.5 + ball\_radius)
  

--------------------------------------------------
Catch Referee
--------------------------------------------------

**TODO**

- Judges the goalie's catch behavior
- [12.0.0 pre-20071217] change the rules of back pass and catch fault
- [12.0.0 pre-20071217] change the rule of goalies' catch vioration
- [12.1.1] fix the back pass rule


.. _sec-foulreferee:

--------------------------------------------------
Foul Referee
--------------------------------------------------

**TODO**

- Judges the foul
- [14.0.0] foul model and intentional foul option
- [14.0.0] foul information in sense_body/fullstate
- [14.0.0] red/yellow card message

If an intentional and dangerous foul is detected, the referee penalizes the player and sends the yellow/red card message to clients.
The message format is similar to playmode messages.
Side and uniform number information of penalized player are appended to the card message: 

  (referee TIME yellow_card_[lr]_[1-11]) or (referee TIME red_card_[lr]_[1-11])

--------------------------------------------------
Ball Stuck Referee
--------------------------------------------------

**TODO: server::ball_stuck_area. [11.0.0] in NEWS**

--------------------------------------------------
Illegal Defense Referee
--------------------------------------------------
From the server version 16, a new referee module has been added to control the number of defensive players.
We have four new variables in **server_param** to change the parameters of this referee.

::

    server::illegal_defense_duration = 20

This parameter determines the number of cycles that illegal defense situation would have to remain before calling a free kick.

::

    server::illegal_defense_number = 0

This parameter determines how many players would need to be
in the specified zone before the illegal defense situation countdown
starts.
If the value is set to 0, the referee never detects illegal defense situations.

::

    server::illegal_defense_dist_x = 16.5

This parameter determines the distance from the field's goal lines for
detecting defensive players.


::

    server::illegal_defense_width = 40.32

This parameter determines the horizontal distance from the horizontal
symmetry line for detecting defensive players.

--------------------------------------------------
Keepaway Referee
--------------------------------------------------

**TODO**

- [9.1.0] keepaway mode


--------------------------------------------------
Penalty Shootouts Referee
--------------------------------------------------

**TODO**

- [9.3.0] penalty shootouts
- [9.4.0] pen_coach_moves_players


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Rules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If defensive players exists within the rectangle defined by
**illegal_defense_dist_x** and **illegal_defense_width**, they are
marked as an illegal state.
if the number of markerd players becomes greater than or equal to
**illegal_defense_number** and this continues for
**illegal_defense_duration** cycles, then play mode will change to
**free_kick_[lr]** for the offensive team.

A team is considered as the offensive team when their player is the latest player to kick the ball.
If both teams perform a kick on the same cycle, neither team is considered as offensive, and the countdown resets.
The above rule is applied to the tackle action too.
The change of play mode does not affect cycles of illegal defense situations.


==================================================
The Soccer Simulation
==================================================

In Sec. 4.4, we gave a description on how the objects are moved with respect to their accelerations and velocities. In this section, we describe at what point in time acceleration
and velocities are applied to the objects during the simulation.

--------------------------------------------------
Description of the simulation algorithm
--------------------------------------------------

In Soccer Server, time is updated in discrete steps. A simulation step is 100ms. During
each simulation step, objects (i.e. players and the ball) stay on their positions. If
players decide to act within a step, actions are applied to the players and the ball at the
transition from one simulation cycle to the next. Depending on the play mode, not all
actions are allowed for the players (for instance in 'before kick off' mode, players can
**turn** and **move**, but they cannot **dash**), so only allowed actions will be applied and
take effect.
If during a step, several players kick the ball, all the kicks are applied to the ball
and a resulting acceleration is calculated. If the resulting acceleration is larger than the
maximum acceleration for the ball, acceleration is normalized to its maximum value.
After moving the objects, the server checks for collisions and updates velocities if a
collision occurred (see also Sec. 4.4.2).
When applying accelerations and velocities to the objects, the order of application is
randomized. After changing objects positions, and updating velocities and accelerations,
the automated referee checks the situation and changes the play mode or the object
positions, if necessary. Changes to the play mode are announced immediately. Finally,
stamina for each player is updated.

In Soccer Server, time is updated in discrete steps. A simulation step is 100ms. During each simulation step, objects (i.e. players and the ball) stay on their positions. If players decide to act within a step, actions are applied to the players and the ball at the transition from one simulation cycle to the next. Depending on the play mode, not all actions are allowed for the players (for instance in ``before_kick_off`` mode, players can **turn** and **move**, but they cannot **dash**), so only allowed actions will be applied and take effect.

If during a step, several players kick the ball, all the kicks are applied to the ball and a resulting acceleration is calculated. If the resulting acceleration is larger than the maximum acceleration for the ball, acceleration is normalized to its maximum value. After moving the objects, the server checks for collisions and updates velocities if a collision occurred (see also Sec. 4.4.2).

When applying accelerations and velocities to the objects, the order of application is randomized. After changing objects positions, and updating velocities and accelerations, the automated referee checks the situation and changes the play mode or the object positions, if necessary. Changes to the play mode are announced immediately. Finally, stamina for each player is updated.

--------------------------------------------------
Keepaway Mode
--------------------------------------------------

**TODO: [9.1.0] in NEWS**

==================================================
Using Soccerserver
==================================================

To start the server either type::

  ./rcssserver

from the directory containing the executable or::

  rcssserver

if you installed the executables in your PATH.

--------------------------------------------------
Configuration Files
--------------------------------------------------

rcssserver will look in your home directory for the configuration files:

* .rcssserver/server.conf
* .rcssserver/player.conf
* .rcssserver/CSVSaver.conf
* .rcssserver-landmark.xml

If .conf files do not exist, they will be created and populated with
default values.

You can include additional configuration files by using the ``include=FILE``
option to ``rcssserver``.

**TOOD**

- [8.01] landmark reader
- [13.0.0] RCSS_CONF_DIR


--------------------------------------------------
Recording Command Log
--------------------------------------------------

**TODO: description about .rcl file**

--------------------------------------------------
Automatic Mode
--------------------------------------------------

**TODO: [9.0.2]**

--------------------------------------------------
Anonyous Mode
--------------------------------------------------
Anonymous Mode,which was introduced in server version 16.0.0 allows the server 
to hide team names from opponents. There are two parameters inside server.conf, which
allow each side's name to be set to a fixed string. If the parameter is empty, the
real name of the team will be reported to the opponent.

.. table:: Server parameters for Anonymous mode
   :name: tab-anonymous

   +-------------------------+-------------------------------------------------------------+
   |Parameter                |Description                                                  |
   +=========================+=============================================================+
   |server::fixed_teamname_l |Fixed name of the left team, which is sent to the right team.|
   |                         |Leave empty for real name.                                   |
   +-------------------------+-------------------------------------------------------------+
   |server::fixed_teamname_r |Fixed name of the left team, which is sent to the right team.|
   |                         |Leave empty for real name.                                   |
   +-------------------------+-------------------------------------------------------------+
   
--------------------------------------------------
Synchronous Mode
--------------------------------------------------

**TODO: [7.11] in ChangeLog**

--------------------------------------------------
Result Saver
--------------------------------------------------

**TODO**

- [9.4.0] StdOutSaver, MySQLSaver
- [9.4.3] CSVSaver

--------------------------------------------------
The Soccerserver Parameters
--------------------------------------------------

.. list-table:: Parameters adjustable in ``server.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``server.conf``
     - Description
   * - version
     - '16.0.1'
     - soccer server version
   * - catch_ban_cycle
     - 5
     - goalies cannot execute the next catch until this cycle has passed after the successful catch.
   * - clang_win_size
     - 300
     - time window which controls how many messages can be sent (coach language)
   * - clang_advice_win
     - 1
     - number of advice messages per window
   * - clang_define_win
     - 1
     - number of define messages per window
   * - clang_del_win
     - 1
     - number of del messages per window
   * - clang_info_win
     - 1
     - number of info messages per window
   * - clang_mess_delay
     - 50
     - delay between receipt of message and send to players
   * - clang_mess_per_cycle
     - 1
     - maximum number of coach messages sent per cycle
   * - clang_meta_win
     - 1
     - number of meta messages per window
   * - clang_rule_win
     - 1
     - number of rule messages per window
   * - clang_win_size
     - 1
     - The length of clang message window
   * - coach_port
     - 6001
     - (offine) coach port
   * - connect_wait
     - 300
     - maximum cycle to wait for client connections in automatic mode
   * - drop_ball_time
     - 100
     - number of cycles to wait until dropping the ball automatically
   * - extra_half_time
     - 100
     - length of a half time of extra halves in seconds
   * - foul_cycles
     - 5
     - idle cycles of foul charged players
   * - freeform_send_period
     - 20
     - online coaches can send a freeform message during this period after the waiting period
   * - freeform_wait_period
     - 600
     - online coaches can send a freeform message after waiting this period
   * - game_log_compression
     - 0
     - compression level of game log file
   * - game_log_version
     - 5
     - version of game log format
   * - game_over_wait
     - 100
     - maximum cycle to wait for server termination in automatic mode
   * - goalie_max_moves
     - 2
     - goalie max. moves after a catch
   * - half_time
     - 300
     - length of a half time in seconds
   * - hear_decay
     - 1
     - value that reduces the auditory capacity when receiving an auditory message
   * - hear_inc
     - 1
     - value that increases the auditory capacity when the game cycle is updated
   * - hear_max
     - 1
     - maximum value of audiotory capacity
   * - illegal_defense_duration
     - 20
     - threshold count to detect illegal defense behavior
   * - illegal_defense_number
     - 0
     - number of players judged to be illegal illegal defense behavior
   * - keepaway_start
     - -1
     - automatic referee changes playmode to play_on after this
	   seconds elapsed
   * - kick_off_wait
     - 100
     - maximum cycle to wait kick-off in automatic mode
   * - max_goal_kicks
     - 3
     - (actually no effect)
   * - max_monitors
     - -1
     - max number of monitor connections
   * - nr_extra_halfs
     - 2
     - number of extra halves in a game
   * - nr_normal_halfs
     - 2
     - number of normal halves in a game
   * - olcoach_port
     - 6002
     - online coach port
   * - pen_before_setup_wait
     - 10
     - max waiting cycles in penalty_miss_[lr] or penalty_score_[lr]
   * - pen_max_extra_kicks
     - 5
     - max extra kick trials in penalty shootouts
   * - pen_nr_kicks
     - 5
     - number of normal kick trials in penalty shootouts
   * - pen_ready_wait
     - 10
     - max waiting cycles in penalty_ready_[lr]
   * - pen_setup_wait
     - 70
     - max waiting cycles in penalty_setup_[lr]
   * - pen_taken_wait
     - 150
     - max cycles in penalty_taken_[lr]
   * - point_to_ban
     - 5
     - players cannot execute the next pointto until this cycle has passed
   * - point_to_duration
     - 20
     - point to continues automatically for up to this cycle
   * - port
     - 6000
     - player port number
   * - recv_step
     - 10
     - time step of acception of commands [unit: msec]
   * - say_coach_cnt_max
     - 128
     - upper limit of the number of online coach's message
   * - say_coach_msg_size
     - 128
     - upper limit of length of online coach's message
   * - say_msg_size
     - 10
     - string size of say message [unit:byte]
   * - send_step
     - 150
     - time step of visual information [unit:msec]
   * - send_vi_step
     - 100
     - interval of online coach's look
   * - sense_body_step
     - 100
     - time step of player's body information [unit:msec]
   * - simulator_step
     - 100
     - time step of simulation [unit:msec]
   * - slow_down_factor
     - 1
     - coefficient that slows down simulation time
   * - start_goal_l
     - 0
     - initial score of the left team
   * - start_goal_r
     - 0
     - initial score of the right team
   * - synch_micro_sleep
     - 1
     - sleep time to wait clients in synch mode [unit:msec]
   * - synch_offset
     - 60
     - offset time from the beginning of the cycle to send *think* message [unit:msec]
   * - synch_see_offset
     - 0
     - offset time from the beginning of the cycle to send *see* message if players uses *synch_see* mode [unit:msec]
   * - tackle_cycles
     - 10
     - idle cycles of the players that executed *tackle*
   * - text_log_compression
     - 0
     - compression level of text log file
   * - auto_mode
     - false
     - enable auto start of the match
   * - back_passes
     - true
     - enable back pass rule
   * - coach
     - false
     -
   * - coach_w_referee
     - false
     - allows trainer with automatic referee
   * - forbid_kick_off_offside
     - true
     - forbid kick off offside
   * - free_kick_faults
     - true
     - enable free kick fault rule
   * - fullstate_l
     - false
     - enable full state information for left team
   * - fullstate_r
     - false
     - enable full state information for right team
   * - game_log_dated
     - true
     - flag to write date in game log name
   * - game_log_fixed
     - false
     - enable fixed name in game log
   * - game_logging
     - true
     - flag for game logging
   * - golden_goal
     - false
     - flag for the golden goal rule
   * - keepaway
     - false
     - flag for keepaway mode
   * - keepaway_log_dated
     - true
     - flag to write date in keep away log name
   * - keepaway_log_fixed
     - false
     - enable fixed name in keep away log
   * - keepaway_logging
     - true
     - enable logging in keep away mode
   * - log_times
     - false
     -
   * - old_coach_hear
     - false
     -
   * - pen_allow_mult_kicks
     - true
     - Turn on to allow dribbling in penalty shootouts
   * - pen_coach_moves_players
     - true
     - Turn on to have the server automatically position players for peanlty shootouts
   * - pen_random_winner
     - false
     - enable random winner in penalties
   * - penalty_shootouts
     - true
     - Set to true to enable penalty shootouts after normal time and extra time if the game is drawn.
   * - profile
     - false
     -
   * - proper_goal_kicks
     - false
     -
   * - record_messages
     - false
     - enables recording message to game log file
   * - send_comms
     - false
     - enables sending message to monitors
   * - synch_mode
     - false
     - enables synchronous mode
   * - team_actuator_noise
     - false
     - flag whether to use team specic actuator noise
   * - text_log_dated
     - true
     - flag to write date in text log name
   * - text_log_fixed
     - false
     - enable fixed name in text log
   * - text_logging
     - true
     - flag for recording client command log
   * - use_offside
     - true
     - flag for using off side rule
   * - verbose
     - false
     - flag for verbose mode
   * - wind_none
     - false
     - wind factor is none
   * - wind_random
     - false
     - wind factor is random
   * - audio_cut_dist
     - 50.0
     - audio cut off distance
   * - back_dash_rate
     - 0.6
     - dash power date for the backward dash
   * - ball_accel_max
     - 2.7
     - max. ball acceleration
   * - ball_decay
     - 0.94
     - ball decay
   * - ball_rand
     - 0.05
     - noise parameter for the ball movement
   * - ball_size
     - 0.085
     - ball size
   * - ball_speed_max
     - 3.0
     - max. ball velocity
   * - ball_stuck_area
     - 3.0
     - threshold of distance to detect a stucked situation
   * - ball_weight
     - 0.2
     - (not used) weight of the ball
   * - catch_probability
     - 1.0
     - default goalie catch probability
   * - catchable_area_l
     - 1.2
     - goalie's defalut catchable area length
   * - catchable_area_w
     - 1.0
     - goalie's catchable area width
   * - ckick_margin
     - 1.0
     - corner kick margin
   * - control_radius
     - 2.0
     - (not used)
   * - dash_angle_step
     - 1.0
     - minimum angle step for dash command
   * - dash_power_rate
     - 0.006
     - default dash power rate
   * - effort_dec
     - 0.005
     - dash effort decrement
   * - effort_dec_thr
     - 0.3
     - player dash effort decrement threshold
   * - effort_inc
     - 0.01
     - dash effort increment
   * - effort_inc_thr
     - 0.6
     - dash effort increment treshold
   * - effort_init
     - 1.0
     - default effort value
   * - effort_min
     - 0.6
     - min. player dash effort
   * - extra_stamina
     - 50.0
     - default extra stamina
   * - foul_detect_probability
     - 0.5
     - default foul detect probability
   * - foul_exponent
     - 10.0
     -
   * - goal_width
     - 14.02
     - goal width
   * - illegal_defense_dist_x
     - 16.5
     -
   * - illegal_defense_width
     - 40.32
     -
   * - inertia_moment
     - 5.0
     - default intertia moment for turn
   * - keepaway_length
     - 20
     - length of rectangle in keep away mode
   * - keepaway_width
     - 20
     - width of rectangle in keep away mode
   * - kick_power_rate
     - 0.027
     - kick power rate
   * - kick_rand
     - 0.1
     - base parameter for noise added directly to kicks
   * - kick_rand_factor_l
     - 1.0
     - factor to multiply kick rand for left team
   * - kick_rand_factor_r
     - 1.0
     - factor to multiply kick rand for right team
   * - kickable_margin
     - 0.7
     - default kickable margin
   * - max_back_tackle_power
     - 0.0
     - maximum back tackle power
   * - max_dash_angle
     - 180.0
     - maximum dash angle relative to player's body angle
   * - max_dash_power
     - 100.0
     - maximum dash acceleration power
   * - max_tackle_power
     - 100.0
     - maximum tackle power
   * - maxmoment
     - 180.0
     - max. moment
   * - maxneckang
     - 90.0
     - max. neck angle
   * - maxneckmoment
     - 180.0
     - max. neck moment
   * - maxpower
     - 100.0
     - max kick power
   * - min_dash_angle
     - -180.0
     - minimum dash angle relative to player's body angle
   * - min_dash_power
     - -100.0
     - minimum dash acceleration power
   * - minmoment
     - -180.0
     - max. moment
   * - minneckang
     - -90.0
     - max. neck angle
   * - minneckmoment
     - -180.0
     - max. neck moment
   * - minpower
     - -100
     - min kick power
   * - offside_active_area_size
     - 2.5
     - if offside marked players try to kick/tackle command and their distance from the ball is less than this value, referee detects
	   offside
   * - offside_kick_margin
     - 9.15
     -
   * - offside_kick_margin
     - 9.15
     -
   * - pen_dist_x
     - 42.5
     -
   * - pen_max_goalie_dist_x
     - 14
     -
   * - player_accel_max
     - 1.0
     - max. player acceleration
   * - player_decay
     - 0.4
     - default player decay
   * - player_rand
     - 0.1
     - players' movement noise parameter
   * - player_size
     - 0.3
     - player radius
   * - player_speed_max
     - 1.05
     - maxium speed of players
   * - player_speed_max_min
     - 0.75
     - The minumum value of the maximum speed of players
   * - player_weight
     - 60.0
     - (not used) player weight
   * - prand_factor_l
     - 1
     - factor to multiply prand for left team
   * - prand_factor_r
     - 1
     - factor to multiply prand for right team
   * - quantize_step
     - 0.1
     - quantize step of distance for movable objects
   * - quantize_step_l
     - 0.01
     - quantize step of distance for landmarks
   * - recover_dec
     - 0.002
     - player recovery decrement
   * - recover_dec_thr
     - 0.3
     - player recovery decrement threshold
   * - recover_init
     - 1.0
     - player's initial recovery value
   * - red_card_probability
     - 0.0
     - probability of red card in a foul
   * - side_dash_rate
     - 0.4
     - factor to multiply effective power when side dash is performed
   * - slowness_on_top_for_left_team
     - 1
     -
   * - slowness_on_top_for_right_team
     - 1
     -
   * - stamina_capacity
     - 130600
     - max. recovery capacity of each player's stamina
   * - stamina_inc_max
     - 45.0
     - default max. player stamina increment
   * - stamina_max
     - 8000.0
     - max. player stamina
   * - stopped_ball_vel
     - 0.01
     - threshold value to detect ball is moving or not
   * - tackle_back_dist
     - 0.0
     - max. x distance between player and ball that player may perform a tackle when ball is behind the player
   * - tackle_dist
     - 2.0
     - max. x distance between player and ball that player may perform a tackle when ball is in front of the player
   * - tackle_exponent
     - 6.0
     - exponent used in tackle failure probability equation
   * - tackle_power_rate
     - 0.027
     - tackle power rate
   * - tackle_rand_factor
     - 2.0
     -
   * - tackle_width
     - 1.25
     - max. y distance between player and ball that player may perform a tackle when ball is in front of the player
   * - visible_angle
     - 90.0
     - visible angle
   * - visible_distance
     - 3.0
     -
   * - wind_ang
     - 0.0
     -
   * - wind_dir
     - 0.0
     - wind direction
   * - wind_force
     - 0.0
     -
   * - wind_rand
     - 0.0
     -
   * - coach_msg_file
     - ''
     -
   * - fixed_teamname_l
     - ''
     - fixed name of left team's opponent
   * - fixed_teamname_r
     - ''
     - fixed name of right team's opponent
   * - game_log_dir
     - './'
     - path to game log directory
   * - game_log_fixed_name
     - 'rcssserver'
     - fixed name of game log
   * - keepaway_log_dir
     - './'
     - path to keep away log directory
   * - keepaway_log_fixed_name
     - 'rcssserver'
     - fixed name of keep away log
   * - landmark_file
     - '~/.rcssserver-landmark.xml'
     -
   * - log_date_format
     - '%Y%m%d%H%M%S-'
     - date format in game log
   * - team_l_start
     - ''
     - path to start script of left team
   * - team_r_start
     - ''
     - path to start script of right team
   * - text_log_dir
     - './'
     - path to text log directory
   * - text_log_fixed_name
     - ''
     - fixed name of text log

.. list-table:: Parameters adjustable in ``player.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``player.conf``
     - Description
   * - version
     - '16.0.1'
     - soccer server version
   * - player_types
     - 18
     - number of random player types generated at match startup
   * - pt_max
     - 1
     - number of times that online coach can substitute a player to another player of the same type
   * - random_seed
     - -1
     - seed to generate heterogeneous players parameters of a match if it is non zero
   * - subs_max
     - 3
     - maximum number of substitutions in a match
   * - allow_mult_default_type
     - false
     -
   * - catchable_area_l_stretch_max
     - 1.3
     - defines the upper bound of player's catchable_area_l_stretch
   * - catchable_area_l_stretch_min
     - 1
     - defines the lower bound of player's catchable_area_l_stretch
   * - dash_power_rate_delta_max
     - 0
     - defines the upper bound of player's dash power rate when added to default dash power rate
   * - dash_power_rate_delta_min
     - 0
     - defines the lower bound of player's dash power rate when added to default dash power rate
   * - effort_max_delta_factor
     - -0.004
     - controls the upper bound of player's effort amount
   * - effort_min_delta_factor
     - -0.004
     - controls the lower bound of player's effort amount
   * - extra_stamina_delta_max
     - 50
     - defines the upper bound of player's extra stamina when added to default extra stamina
   * - extra_stamina_delta_min
     - 0
     - defines the lower bound of player's extra stamina when added to default extra stamina
   * - foul_detect_probability_delta_factor
     - 0
     - defines the range of heterogeneous player's foul detect probability
   * - inertia_moment_delta_factor
     - 25
     - factor to control the length of inertia moment delta interval
   * - kick_power_rate_delta_max
     - 0
     - defines the upper bound of player's kick power rate when added to default kick power rate
   * - kick_power_rate_delta_min
     - 0
     - defines the lower bound of player's kick power rate when added to default kick power rate
   * - kick_rand_delta_factor
     - 1
     -
   * - kickable_margin_delta_max
     - 0.1
     - defines the upper bound of player's kickable margin when added to default kickable margin
   * - kickable_margin_delta_min
     - -0.1
     - defines the lower bound of player's kickable margin when added to default kickable margin
   * - new_dash_power_rate_delta_max
     - 0.0008
     -
   * - new_dash_power_rate_delta_min
     - -0.0012
     -
   * - new_stamina_inc_max_delta_factor
     - -6000
     -
   * - player_decay_delta_max
     - 0.1
     - defines the upper bound of inertia moment delta when multiplied by inertia moment delta factor
   * - player_decay_delta_min
     - -0.1
     - defines the lower bound of inertia moment delta when multiplied by inertia moment delta factor
   * - player_size_delta_factor
     - -100
     - controls the range of heterogeneous player's size
   * - player_speed_max_delta_max
     - 0
     - defines the upper bound of player's maximum speed when added to server::player_speed_max
   * - player_speed_max_delta_min
     - 0
     - defines the lower bound of player's maximum speed when added to server::player_speed_max
   * - stamina_inc_max_delta_factor
     - 0
     -

.. list-table:: Parameters adjustable in ``CSVSaver.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``CSVSaver.conf``
     - Description
   * - version
     - '16.0.1'
     - soccer server version
   * - save
     - false
     - flag to save matches result in a file
   * - filename
     - 'rcssserver.csv'
     - file to save the results to. If this file does not exist it will be created. Otherwise, the results will be appended to the end.
