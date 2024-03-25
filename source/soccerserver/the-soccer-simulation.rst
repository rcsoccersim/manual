
In :ref:`sec-movementmodels`, we gave a description on how the objects are moved with respect to their accelerations and velocities. In this section, we describe at what point in time acceleration
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

If during a step, several players kick the ball, all the kicks are applied to the ball and a resulting acceleration is calculated. If the resulting acceleration is larger than the maximum acceleration for the ball, acceleration is normalized to its maximum value. After moving the objects, the server checks for collisions and updates velocities if a collision occurred (see also :ref:`sec-collisionmodel`).

When applying accelerations and velocities to the objects, the order of application is randomized. After changing objects positions, and updating velocities and accelerations, the automated referee checks the situation and changes the play mode or the object positions, if necessary. Changes to the play mode are announced immediately. Finally, stamina for each player is updated.

--------------------------------------------------
Keepaway Mode
--------------------------------------------------

**TODO: [9.1.0] in NEWS**
