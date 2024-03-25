.. _sec-kickmodel:

--------------------------------------------------
Kick Model
--------------------------------------------------

The *kick* command takes two parameters, the kick power the player
client wants to use (between **server::minpower** and
**server::maxpower**) and the angle the player kicks the ball to.
The angle is given in degrees and has to be between
**server::minmoment** and **server::maxmoment**
(see :numref:`param-kick` for current parameter values).

Once the *kick* command arrived at the server, the kick will be
executed if the ball is kick-able for the player and the player is not
marked offside.
The ball is kick-able for the player, if the distance between the
player and the ball is between 0 and **kickable_margin**.
Heterogeneous players can have different kickable margins.
For the calculation of the distance during this section, it is
important to know that if we talk of distance between player and ball,
we talk about the minimal distance between the outer shape of both
player and ball.
So the distance in this section is the distance between the center of
both objects *minus* the radius of the ball *minus* the radius of the player.

The first thing to be calculated for the kick is the effective kick power ep:

.. math::
  :label: eq:effectivekick1

  {\mathrm ep} = {\mathrm {kick\_power}} \cdot {\mathrm {kick\_power\_rate}}


If the ball is not directly in front of the player, the effective kick
power will be reduced by a certain amount dependent on the position of
the ball with respect to the player.
Both angle and distance are important.

If the relative angle of the ball is :math:`0^\circ` wrt. the body
direction of the player client - i.e. the ball is in front of the
player - the effective power will stay as it is.
The larger the angle gets, the more the effective power will be
reduced.
The worst case is if the ball is lying behind the player (angle
:math:`180^\circ`): the effective power is reduced by 25%.

The second important variable for the effective kick power is the
distance from the ball to the player: it is quite obvious that -
should the kick be executed - the distance between ball and player is
between 0 and player's **kickable margin**.
If the distance is 0, the effective kick power will not be reduced
again.
The further the ball is away from the player client, the more the
effective kick power will be reduced.
If the ball distance is player's **kickable margin**, the effective
kick power will be reduced by 25% of the original kick power.

The overall worst case for kicking the ball is if a player kicks a
distant ball behind itself: 50% of kick power will be used.
For the effective kick power, we get the formula :eq:`eq:effectivekick2`.
(dir diff means the absolute direction difference between ball and the player’s body
direction, dist diff means the absolute distance between ball and
player.)
:math:`0\le\mathrm{dir\_diff}\le180^\circ\land0\le\mathrm{dist\_diff}\le\mathrm{kickable\_margin}`

.. math::
  :label: eq:effectivekick2

  {\mathrm ep} = \mathrm{ep} \cdot (1 - 0.25 \cdot \frac{\mathrm{dir\_diff}}{180^\circ} - 0.25 \cdot \frac{\mathrm{dist\_ball}}{\mathrm{kickable\_margin}})


The effective kick power is used to calculate :math:`\vec{a}_{{n}_{i}}`,
an acceleration vector that will be added to the global ball
acceleration :math:`\vec{a}_{n}` during cycle :math:`n` (remember that
we have a multi agent system and *each* player close to the ball can
kick it during the same cycle).

There is a server parameter, **server::kick_rand**, that can be used to
generate some noise to the ball acceleration.
For the default players, **kick_rand** is 0.1.
For heterogeneous players, **kick_rand** depends on
**player::kick_rand_delta_factor** in ``player.conf`` and on the
actual kickable margin.
.. In RoboCup 2000, **kick_rand** was used to generate some noise during evaluation round for the normal players.

- **TODO: new kick/tackle noise model. See [12.0.0 pre-20080210] in NEWS**
- **TODO: heterogeneous kick power rate. See [14.0.0] in NEWS**

During the transition from simulation step :math:`n` to simulation step
:math:`n+1` acceleration :math:`\vec{a}_{n}` is applied:

#. :math:`\vec{a}_{n}` is normalized to a maximum length of
   **server::ball_accel_max**.
#. :math:`\vec{a}_{n}` is added to the current ball speed :math:`\vec{v}_{n}`.
   :math:`\vec{v}_{n}` will be normalized to a maximum length of **server::ball_speed_max**.
#. Noise :math:`\vec{n}` and wind :math:`\vec{w}` will be added to
   :math:`\vec{v}_{n}`.
   Both noise and wind are configurable in ``server.conf``.
   The responsible parameter for the noise is **server::ball_rand**.
   Both direction and length of the noise vector are within the interval :math:`[ -|\vec{v}_{n}| \cdot \mathrm{ball\_rand} \ldots |\vec{v}_{n}| \cdot \mathrm{ball\_rand}]``.
   Parameters responsible for the wind are **server::wind_force**,
   **server::wind_dir** and **server::wind_rand**.
#. The new position of the ball :math:`\vec{p}_{n+1}` is the old
   position :math:`\vec{p}_{n}` plus the velocity vector
   :math:`\vec{v}_{n}` (i.e. the maximum distance difference for the
   ball between two simulation steps is **server::ball_speed_max**).
#. **server::ball_decay** is applied for the velocity of the ball: :math:`\vec{v}_{n+1} = \vec{v}_{n} \cdot \mathrm{ball\_decay}`.
   Acceleration :math:`\vec{a}_{n+1}` is set to zero.

With the current settings the ball covers a distance up to 50,
assuming an optimal kick.
55 cycles after an optimal kick, the distance from the kick off
position to the ball is about 48, the remaining velocity is smaller
than 0.1.
18 cycles after an optimal kick, the ball covers a distance of 34 - 34
and the remaining veloctity is slightly smaller than 1.

Implications from the kick model and the current parameter settings are
that it still might be helpful to use several small kicks for a compound
kick -- for example stopping the ball, kick it to a more advantageous
position within the kickable area and kick it to the desired direction.
It would be another possibility to accelerate the ball to maximum speed
without putting it to relative position (0,0{\textdegree}) using a
compound kick.

.. table:: Ball and Kick Model Parameters
   :name: param-kick

   +---------------------------------+----------------------------+-------------------------------------------+------------+
   || Default Parameters             || Default Value (Range)     || Heterogeneous Player Parameters          || Value     |
   ||  ``server.conf``               ||                           ||   ``player.conf``                        ||           |
   +=================================+============================+===========================================+============+
   | server::minpower                | -100                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::maxpower                | 100                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::minmoment               | -180                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::maxmoment               | 180                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::kickable_margin         | 0.7 ([0.6, 0.8])           || player::kickable_margin_delta_min        |-0.1        |
   |                                 |                            || player::kickable_margin_delta_max        |0.1         |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::kick_power_rate         | 0.027                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::kick_rand               | 0.1 ([0.0, 0.2])           || player::kick_rand_delta_factor           |1           |
   |                                 |                            || player::kickable_margin_delta_min        |-0.1        |
   |                                 |                            || player::kickable_margin_delta_max        |0.1         |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::ball_size               | 0.085                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::ball_decay              | 0.94                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::ball_rand               | 0.05                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::ball_speed_max          | 3.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::ball_accel_max          | 2.7                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_force              | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_dir                | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_rand               | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+

