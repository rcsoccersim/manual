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
