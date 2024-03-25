
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

.. include:: soccerserver/sensor-models-aural-sensor-model.rst

.. include:: soccerserver/sensor-models-vison-sensor-model.rst

.. include:: soccerserver/sensor-models-body-sensor-model.rst

--------------------------------------------------
Fullstate Sensor Model
--------------------------------------------------

**TODO**
