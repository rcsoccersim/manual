Since rcssserver version 19, a bipedal dash model has been introduced.
In the bipedal model, players can independently issue dash commands to the left and right legs.
This means that players can now apply different accelerations to each leg.
With the bipedal dash model, players can perform acceleration and direction changes simultaneously.
The bipedal dash model is based on the differential drive dynamics model used in two-wheeled mobile robots and is available regardless of the client version.

In the bipedal dash model, parameters can be independently assigned to each leg using the dash command.
The parameters, namely power and direction, are the same as the dash command in versions 18 and earlier.
The derivation formula for acceleration obtained for each leg also follows the conventional model.
The differences from the conventional model lie in the stamina consumption,
the derivation formula for the player's body acceleration obtained as a result,
and the player's rotation based on the speed difference between both legs.

Stamina consumed by each leg is half of the conventional model.
The overall stamina consumption is the sum of stamina consumption for both legs.
In other words, when the same power is applied to both legs ``(dash (l power dir) (r power dir))``,
the stamina consumption is the same as applying that power to both legs in the conventional model ``(dash power dir)``.

The dash command can be issued not only simultaneously to both legs but also separately.
Even if the dash command is issued separately,
as long as a dash command has been issued to both legs by the time of the cycle update,
the same effect as issuing simultaneously can be achieved.
If the dash command is issued to only one leg, rotation of the player does not occur,
and acceleration is obtained solely from the individual leg.

Based on the given command parameters, velocity is first derived for each leg.
This derivation formula is the same as the conventional model.
Provisional accelerations :math:`\hat{a}_L` and :math:`\hat{a}_R` are independently calculated for each leg.
Next, the current player velocity :math:`v_t` and the provisional velocities :math:`\hat{v}_L` and :math:`\hat{v}_R`
for each leg are obtained from the provisional accelerations.
The provisional velocity :math:`\hat{v}^{t+1}` for the player's body is then determined by the average of :math:`\hat{v}_L` and :math:`\hat{v}_R`.
The player's body acceleration :math:`a^t` is reverse-calculated from the difference between :math:`\hat{v}^{t+1}` and :math:`v^t`.
Noise is added according to the update formula in section :ref:`sec-movementmodels`, and the velocity for the next step, :math:`v{t+1}`, is updated.

.. math::

 edp_L &= effort \times dash\_power\_rate \times dash\_rate \times dash\_power_L \\
 edp_R &= effort \times dash\_power\_rate \times dash\_rate \times dash\_power_R \\
 accel\_dir_L &= body\_angle^t + dash\_dir_L \\
 accel\_dir_R &= body\_angle^t + dash\_dir_R \\
 \vec{\hat{a}_L} &= edp_L \times (\cos(accel\_dir_L), \sin(accel\_dir_L)) \\
 \vec{\hat{a}_R} &= edp_R \times (\cos(accel\_dir_R), \sin(accel\_dir_R)) \\
 \vec{\hat{v}_L} &= (\vec{v^t} + \vec{\hat{a}_L}) \\
 \vec{\hat{v}_R} &= (\vec{v^t} + \vec{\hat{a}_R}) \\
 \vec{\hat{v}^{t+1}} &= \frac{ \vec{\hat{v}_L} +  \vec{\hat{v}_R} }{2}  \\
 \vec{a^t} &= \vec{\hat{v}^{t+1}} - \vec{v^t}


When dash parameters are assigned to both legs and there is a difference in the velocity component of each leg in the body direction,
the player rotates based on that speed difference.
The rotation equation is identical to the differential drive kinematics.

.. math::

 \vec{e} &= (cos(body\_angle^t), sin(body\_angle^t)) \\
 \hat{v}_{L,body} &= (\vec{v_{t}} + \vec{a_L}) \cdot \vec{e} \\
 \hat{v}_{R,body} &= (\vec{v_{t}} + \vec{a_R}) \cdot \vec{e} 

.. math::

 \omega &= \frac{(\hat{v}_{L,body} - \hat{v}_{R,body})}{b} \\
 body\_angle^{t+1} &= body\_angle^{t} + (1.0 + random(-player\_rand,player\_rand)) \times \omega

where :math:`\omega` is the angular velocity, 
and :math:`b` is the width between wheels (=player_size x 2). 

