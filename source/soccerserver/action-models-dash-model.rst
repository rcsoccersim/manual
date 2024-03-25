
--------------------------------------------------
Dash Model
--------------------------------------------------

The **dash** command is used to accelerate the player in direction of
its body.
**dash** takes the acceleration *power* as a parameter.
The valid range for the acceleration *power* can be configured in
``server.conf``, the respective parameters are **server::min_dash_power**
and **server::max_dash_power**.
For the current values of parameters for the dash model, see
the following table:

.. table:: Dash and Stamina Model Parameters

   +---------------------------------+----------------------------+-------------------------------------------+------------+
   || Default Parameters             || Default Value (Range)     || Heterogeneous Player Parameters          || Value     |
   ||  ``server.conf``               ||                           ||   ``player.conf``                        ||           |
   +=================================+============================+===========================================+============+
   | server::min_dash_power          |-100.0                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::max_dash_power          |100.0                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::player_decay            || 0.4 ([0.3, 0.5])          || player::player_decay_delta_min           || -0.1      |
   | server::inertia_moment          || 5.0 ([2.5, 7.5])          || player::player_decay_delta_max           || 0.1       |
   |                                 |                            || player::inertia_moment_delta_factor      || 25.0      |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::player_accel_max        | 1.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::player_rand             | 0.1                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::player_speed_max        | 1.05                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::player_speed_max_min    | 0.75                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::stamina_max             |8000.0                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::stamina_capacity        |130600.0                    |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   || server::stamina_inc_max        || 45.0  ([40.2, 52.2])      || player::new_dash_power_rate_delta_min    || -0.0012   |
   || server::dash_power_rate        || 0.006 ([0.0048, 0.0068])  || player::new_dash_power_rate_delta_max    || 0.0008    |
   |                                 |                            || player::new_stamina_inc_max_delta_factor || -6000     |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   || server::extra_stamina          || 50.0  ([50.0, 100.0])     || player::extra_stamina_delta_min          || 0.0       |
   || server::effort_init            || 1.0   ([0.8, 1.0])        || player::extra_stamina_delta_max          || 50.0      |
   || server::effort_min             || 0.6   ([0.4, 0.6])        || player::effort_max_delta_factor          || -0.004    |
   |                                 |                            || player::effort_min_delta_factor          || -0.004    |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::effort_dec              | 0.3                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::effort_dec_thr          | 0.005                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::effort_inc              | 0.01                       |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::effort_inc_thr          | 0.6                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::recover_dec_thr         | 0.3                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::recover_dec             | 0.002                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::recover_init            | 1.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::recover_min             | 0.5                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_ang                | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_dir                | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_force              | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::wind_rand               | 0.0                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+


Each player has a certain amount of stamina that will be consumed by
**dash** commands.
At the beginning of each half, the stamina of a player is set to
**server::stamina_max**.
If a player accelerates forward (:math:`power> 0`), stamina is
reduced by *power*.
Accelerating backwards (:math:`power< 0`) is more expensive for the
player: stamina is reduced by :math:`-2 \times power`.
If the player's stamina is lower than the power needed for the
**dash**, *power* is reduced so that the **dash** command does not
need more stamina than available.
Some extra stamina can be used every time the available power is lower
than the needed stamina.
The amount of extra stamina depends on the player type and the
parameters **player::extra_stamina_delta_min** and
**player::extra_stamina_delta_max**.

After reducing the stamina, the server calculates the *effective  dash
power* for the **dash** command.
The effective dash power *edp* depends on the **dash_power_rate** and the
current effort of the player.
The effort of a player is a value between **effort_min** and **effort_max**;
it is dependent on the stamina management of the player (see below).

.. math::
  :label: eq:effectivedash

  {\mathrm edp} = {\mathrm effort} \cdot {\mathrm dash\_power\_rate} \cdot {\mathrm power}

*edp* and the players current body direction are tranformed into vector and
added to the players current acceleration vector :math:`\vec{a}_n`
(usually, that should be 0 before, since a player cannot dash more than once
a cycle and a player does not get accelerated by other means than dashing).

At the transition from simulation step :math:`n` to simulation step
:math:`n + 1`, acceleration :math:`\vec{a}_n` is applied:
**TODO: dash speed restriction. See [12.0.0_pre-20071217]**

1. :math:`\vec{a}_n` is normalized to a maximum length of **server::player_accel_max**.
2. :math:`\vec{a}_n` is added to current players speed
   :math:`\vec{v}_n`. :math:`\vec{v}_n` will be normalized to a
   maximum length of **player_speed_max**.
   players, the  maximum speed is a value between
   **server::player_speed_max** +
   **player::player_speed_max_delta_min** and
   **server::player_speed_max** +
   **player::player_speed_max_delta_max** in ``player.conf``.
3. Noise :math:`\vec{n}` and wind :math:`\vec{w}` will be added to
   :math:`\vec{v}_{n}`. Both noise and wind are configurable in
   `server.conf`. Parameters responsible for the wind are
   **server::wind_force**, **server::wind_dir** and
   **server::wind_rand**. With the current settings, there is no wind
   on the simulated soccer field. The responsible parameter for the
   noise is **server::player_rand**. Both direction and length
   of the noise vector are within the interval
   :math:`[ -|\vec{v}_{n}| \cdot {\mathrm player\_rand} \ldots |\vec{v}_{n}| \cdot {\mathrm player\_rand}]`.
4. The new position of the player :math:`\vec{p}_{n+1}` is the old position
   :math:`\vec{p}_{n}` plus the velocity vector :math:`\vec{v}_{n}`
   (i.e.\ the maximum distance difference for the player between two
   simulation steps is **player_speed_max**).
5. **player_decay** is applied for the velocity of the player:
   :math:`\vec{v}_{n+1} = \vec{v}_{n} \cdot {\mathrm player\_decay}`.
   Acceleration :math:`\vec{a}_{n+1}` is set to zero.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Sideward and Omni-Directional Dashes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Besides the forward and backward dashes that were already described in
the previous section, since version 13 the Soccer Server also supports the
possibility to perform sideward and even omni-directional dashes.
In addition to the already known
parameter of the **dash(x)** command where :math:`x\in[-100,100]` determines
the relativ strength of the dash (with negative sign indicating a backward
dash), the omni-directional dash model uses two parameters to the **dash**
command:

.. math::
  :label: eq:omniDash

  dash(power,dir)

where :math:`power` determines the relative strength of the dash
and :math:`dir` represents the direction of the dash accelaration
relative to the player's body
angle. The format in which the command needs to be sent to the Soccer Server
is ``(dash <power> <dir>)``.
If a negative value is used for :math:`power`, then the reverse side angle
of :math:`dir`
will be used. Practically, the direction of the dash is restricted to by the
corresponding Soccer Server parameters to

.. math::
   dir \in [server::min\_dash\_angle, server::max\_dash\_angle]

The effective power of the dash command is determined by the absolute value
of the dash direction. Players will always dash with full effective power
(100\%) alongside their current body orientation, i.e. when using a zero
direction angle as described in the preceding section.
Two further Soccer Server parameters, ``server::side_dash_rate``
and ``server::back_dash_rate``, determine the
effective power that is applied when a non-straight dash is performed.

Thus, for example, strafing movements (90 degrees left/right to the player)
will be performed with 40\% of effective power,
whereas backward dashes will performed with 60\%
(according to current Soccer Server parameter default values).
For values between these four main
directions a linear interpolation of the effective power will be applied.
The following formula explains the maths behind the sideward dash model.

.. math::
   :label: eq:omniDashEffPower

   dir\_rate = \begin{cases}
                  back\_dash\_rate - ( back\_dash\_rate - side\_dash\_rate ) * ( 1.0 - ( fabs( dir ) - 90.0 ) / 90.0 ) & \text{if } fabs( dir ) > 90.0 \\
                  side\_dash\_rate + ( 1.0 - side\_dash\_rate ) * ( 1.0 - fabs( dir ) / 90.0 ) ) & \text{else}
               \end{cases}

As discussed in the description of the forward/backward dash model in the
preceding section, there exists the server parameter
``server::min_dash_power`` which determines the highest minimal value
that can be used for the first parameter :math:`power` of the dash command.
It is expected that
this parameter will be set to zero in future versions of the Soccer Server,
while, for reasons of compatibility with older team binaries, its default value
of -100 is encouraged currently.

Finally, the parameter ``server::dash_angle_step`` allows for a finer
discreteness
of players' dash directions. If this value is set to 90.0 degrees, players are
allowed to dash into the four main directions, for a setting of 45.0 we
arrive at eight different directions. Setting this parameter to 1.0,
the Soccer Server is capable of emulating an omnidirectional movement
model as it is commen, for example, in the MidSize League.

The following table summarizes all Soccer Server parameters that are of
relevance for omni-directional dashing.

.. table:: Ominidirectional Dash Parameters

   +---------------------------------+----------------------------+-------------------------------------------+------------+
   || Default Parameters             || Default Value (Range)     || Heterogeneous Player Parameters          || Value     |
   ||  ``server.conf``               ||                           ||   ``player.conf``                        ||           |
   +=================================+============================+===========================================+============+
   | server::server::max_dash_angle  | 180.0                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::server::min_dash_angle  |-180.0                      |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::side_dash_rate          | 0.4                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::back_dash_rate          | 0.6                        |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+
   | server::dash_angle_step         | 1                          |                                           |            |
   +---------------------------------+----------------------------+-------------------------------------------+------------+


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Bipedal Dash Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. include:: soccerserver/bipedal-dash-model.rst

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Stamina Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For the stamina of a player, there are three important variables: the
*stamina* value, *recovery* and *effort*.
*stamina* is decreased when dashing and gets replenished slightly each
cycle. *recovery* is responsible for how much the *stamina* recovers
each cycle, and the *effort* says how effective dashing is (see
section above).
Important parameters for the stamina model are changeable in the files
``server.conf`` and ``player.conf``.
Basically, the algorithm shown in the following code block says that
every simulation step the stamina is below some threshold, effort or
recovery are reduced until a minimum is reached.
Every step the stamina of the player is above some threshold, *effort*
is increased up to a maximum.
The *recovery* value is only reset to 1.0 each half, but it will not
be increased during a game.

::

    # if stamina is below recovery decrement threshold, recovery is reduced
    if stamina <= recover_dec_thr * stamina_max
      if recovery > recover_min
         recovery = recovery - recover_dec

    # if stamina is below effort decrement threshold, effort is reduced
    if stamina <= effort_dec_thr * stamina_max
      if effort > effort_min
        effort = effort - effort_dec
	  effort = max(effort, effort_min)

    # if stamina is above effort increment threshold, effort is increased
    if stamina >= effort_inc_thr * stamina\_max
      if effort < effort_max
        effort = effort + effort_inc
        effort = min(effort, effort_max)

    # recover the stamina a bit
    stamina_inc = recovery * stamina_inc_max
    stamina = min(stamina + stamina_inc, stamina_max)

In rcssserver version 13 or later, the **stamina_capacity** variable
has been implemented as one of the player's stamina models in addition to the above
three *stamina* variables.
*stamina_capacity* is defined as the maximum recovery capacity of each player's stamina.
When a player's *stamina* is recovered during a game, the same amount of *stamina* is also consumed from one's *stamina_capacity*.
Once the player's *stamina_capacity* becomes 0, one's stamina is never recovered and the only **extra_stamina** is consumed instead of the normal *stamina*.
The updated algorithm is shown in the following code block.
``stamina_inc`` can be available from the previous code block.

::

   # stamina_inc is restricted by the residual capacity
   if stamina_capacity >= 0.0
     if stamina_inc > stamina_capacity
       stamina_inc = stamina_capacity
   stamina = min(stamina + stamina_inc, stamina_max)

   # stamina capacity is reduced as the same amount as stamina_inc
   if stamina_capacity >= 0.0
     stamina_capacity = max(0.0, stamina_capacity - stamina_inc)

*stamina_capacity* is reset to the initial value just after the kick-off of normal halves as well as the other stamina-related variables.
However, *stamina_capacity* is never recovered at the half time of extra-inning games and before the penalty shootouts.
The *stamina_capacity* is defined as one of the parameters of rcssserver **server::stamina_capacity** (the default value of *stamina_capacity* is 130600 as of rcsserver version 16.0.0).
If *server::stamina_capacity* is set to a negative value, each player has an infinite stamina capacity.
This setting makes the stamina-model including stamina_capacity
completely the same with the stamina model before rcssserver version 13.
*stamina_capacity* information is received as the following *sense_body message*:

  (stamina <STAMINA> <EFFORT> <CAPACITY>)

