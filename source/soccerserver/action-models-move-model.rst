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
