--------------------------------------------------
Change Focus Model
--------------------------------------------------

The focus point is a feature developed in server v.18, which can be
used by all versions of players.
It represents a position inside a player's view angle, and can be up
to 40.0 meters away from the player's position.
The focus point affects the visual sensor noise model, with the noise
of observed objects increasing as the distance between the focus point
and the object increases.

.. The initial position of the focus point is the player's position,
   and if a player does not change the focus point position, the
   server visual noise model behaves as in server v.17.

The initial position of the focus point is the player's position.
Players can change the position of the focus point by sending a
**change_focus** command.
This command takes two parameters, *dist_moment* and *dir_moment*, and
changes the position of the focus point relative to the player's neck
angle.

It is important to note that players are not allowed to move the focus
point outside of their view angle.
Additionally, if a player changes their view angle to a smaller one,
the server will automatically move the focus point back into the
player's view angle.

See :ref:`sec-visionsensor` in detail about the vison sensor.
