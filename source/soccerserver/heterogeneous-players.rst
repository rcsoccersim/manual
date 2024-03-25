
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
