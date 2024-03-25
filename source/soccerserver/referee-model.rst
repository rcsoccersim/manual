
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
