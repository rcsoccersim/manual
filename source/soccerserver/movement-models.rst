--------------------------------------------------
Basics
--------------------------------------------------

In each simulation step, movement of each object is calculated as following manner:

.. math::
  :label: eq:u-t

   (u_x^{t+1},u_y^{t+1}) &= (v_x^t,v_y^t)+(a_x^t,a_y^t) : accelerate \\
   (p_x^{t+1},p_y^{t+1}) &= (p_x^t,p_y^t)+(u_x^{t+1},u_y^{t+1}) : move \\
   (v_x^{t+1},v_y^{t+1}) &= decay \times (u_x^{t+1},u_y^{t+1}) : decay\ speed \\
   (a_x^{t+1},a_y^{t+1}) &= (0,0) : reset\ acceleration

where, :math:`(p_x^t,p_y^t)`, and :math:`(v_x^t,v_y^t)` are respectively position
and velocity of the object in timestep :math:`t`. decay is a decay parameter
specified by ``ball_decay`` or ``player_decay``. :math:`(a_x^t,a_y^t)` is
acceleration of object, which is derived from Power parameter in ``dash``
(in the case the object is a player) or ``kick`` (in the case of a ball)
commands in the following manner:

.. math::
  (a_x^{t},a_y^{t}) = Power \times power\_rate \times (\cos(\theta^t),\sin(\theta^t))

where :math:`\theta^t` is the direction of the object in timestep :math:`t` and
power_rate is ``dash_power_rate`` or is calculated from ``kick_power_rate``
as described in Sec. :ref:`sec-kickmodel`.
In the case of a player, this is just the direction the player is facing.
In the case of a ball, its direction is given as the following manner:

.. math::

  \theta^t_{ball} = \theta^t_{kicker} + Direction

where :math:`\theta^t_{ball}` and :math:`\theta^t_{kicker}` are directions of
ball and kicking player respectively, and *Direction* is the second parameter
of a **kick** command.


--------------------------------------------------
Movement Noise Model
--------------------------------------------------

In order to reflect unexpected movements of objects in real world,
rcssserver adds noise to the movement of objects and parameters of commands.

Concerned with movements,
noise is added into Eqn.:ref:`eq:u-t` as follows:
**TODO: new noise model. See [12.0.0 pre-20071217] in NEWS**

.. math::

  (u_x^{t+1},u_y^{t+1}) = (v_x^{t}, v_y^{t}) + (a_x^{t}, a_y^{t}) + (\tilde{r}_{\mathrm rmax},\tilde{r}_{\mathrm rmax})

where :math:`\tilde{r}_{\mathrm rmax}` is a random number whose distribution
is uniform over the range :math:`[-{\mathrm rmax},{\mathrm rmax}]`.
:math:`{\mathrm rmax}` is a parameter that depends on amount of velocity
of the object as follows:

.. math::

  {\mathrm rmax} = {\mathrm rand} \cdot |(v_x^{t}, v_y^{t})|

where :math:`{\mathrm rand}` is a parameter specified by **server::player_rand**
or **server::ball_rand**.

Noise is added also into the *Power* and *Moment* arguments of a
command as follows:

.. math::

  argument = (1 + \tilde{r}_{\mathrm rand}) \cdot argument


--------------------------------------------------
Collision Model
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Collision with other movable objects
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If at the end of the simulation cycle, two objects overlap, then the
objects are moved back until they do not overlap.
Then the velocities are multiplied by -0.1.
Note that it is possible for the ball to go through a player as long
as the ball and the player never overlap at the end of the cycle.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Collision with goal posts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Goal posts are circular with a radius of 6cm and they are located at:

.. math::

  x &= \pm (FIELD\_LENGTH \cdot 0.5 - 6cm)\\
  y &= \pm (GOAL\_WIDTH \cdot 0.5 + 6cm)

The goal posts have different collision dynamics than other
objects. An object collides with a post if it's path gets within
object.size + 6cm of the center of the post. An object that
collides with the post bounces off elastically.
