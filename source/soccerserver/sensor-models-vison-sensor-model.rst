.. _sec-visionsensor:

--------------------------------------------------
Vision Sensor Model
--------------------------------------------------

The visual sensor reports the objects currently seen by the player.
The information is automatically sent to the player every
**server::sense_step**, currently 150, milli-seconds, in the default setting.
If players use the synchronous mode, the frequency of the visual information is synchronized with the simulation step.
The simulation parameters related to the visual information are listed in :numref:`param-visualsensor` and :numref:`heterogenious-param-visualsensor`.

Visual information arrives from the server in the following basic format:

  (see *ObjName* *Distance* *Direction* *DistChng* *DirChng* *BodyDir* *HeadDir*)

*Distance*, *Direction*, *DistChng* and *DirChng* are calculated in the
following way:


.. math::

  p_{rx} &= p_{xt} - p_{xo} \\
  p_{ry} &= p_{yt} - p_{yo} \\
  v_{rx} &= v_{xt} - v_{xo} \\
  v_{ry} &= v_{yt} - v_{yo} \\
  Distance &= \sqrt{p_{rx}^2 + p_{ry}^2} \\
  Direction &= \arctan{(p_{ry}/p_{rx})} - a_o \\
  e_{rx} &= p_{rx} / Distance \\
  e_{ry} & = p_{ry} / Distance \\
  DistChng &= (v_{rx} * e_{rx}) + (v_{ry} * e_{ry}) \\
  DirChng &= [(-(v_{rx} * e_{ry}) + (v_{ry} * e_{rx})) / Distance] * (180 / \pi)  \\
  BodyDir &= PlayerBodyDir - AgentBodyDir - AgentHeadDir \\
  HeadDir &= PlayerHeadDir - AgentBodyDir - AgentHeadDir


where :math:`(p_{xt},p_{yt})` is the absolute position of the target object,
:math:`(p_{xo},p_{yo})` is the absolute position of the sensing player,
:math:`(v_{xt},v_{yt})` is the absolute velocity of the target object,
:math:`(v_{xo},v_{yo})` is the absolute velocity of the sensing player,
and :math:`a_o` is the absolute direction the sensing player is facing.
The absolute facing direction of a player is the sum of the *BodyDir* and
the *HeadDir* of that player.
In addition to it, :math:`(p_{rx},p_{ry})` and :math:`(v_{rx},v_{ry})` are
respectively the relative position and the relative velocity of the target,
and :math:`(e_{rx},e_{ry})` is the unit vector that is parallel to the vector
of the relative position.
*BodyDir* and *HeadDir* are only included if the observed object is a player,
and is the body and head directions of the observed player relative to the body
and head directions of the observing player.
Thus, if both players have their bodies turned in the same direction, then
*BodyDir* would be 0.  The same goes for *HeadDir*.

The **(goal r)** object is interpreted as the center of the right hand side
goalline.
**(f c)** is a virtual flag at the center of the field.
**(f l b)** is the flag at the lower left of the field.
**(f p l b)** is a virtual flag at the lower right corner of the penalty box
on the left side of the field.
**(f g l b)** is a virtual flag marking the right goalpost on the left goal.
The remaining types of flags are all located 5 meters outside the playing
field. For example, **(f t l 20)** is 5 meters from the top sideline and 20
meters left from the center line.
In the same way, **(f r b 10)** is 5 meters right of the right sideline and
10 meters below the center of the right goal.
Also, **(f b 0)** is 5 meters below the midpoint of the bottom sideline.

In the case of **(l ...)**, *Distance* is the distance to the point where
the center line of the player's view crosses the line, and *Direction* is
the direction of the line.

Currently there are 55 flags (the goals counts as flags) and 4 lines to be
seen. All of the flags and lines are shown in :numref:`field-detailed`.

.. figure:: ./images/field-detailed.*
  :align: center
  :name: field-detailed

  The flags and lines in the simulation.

In protocol versions 13+, when a player's team is visible, their tackling and
kicking state is also visible via `t` and `k`. If the player is tackling,
`t` is present. If they are kicking, `k` is present instead. If an observed
player is tackling, the kicking flag is always overwritten by the tackle flag.
The kicking state is visible the cycle directly after kicking.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Asynchronous mode and Synchronous mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are two modes available for all players: asynchronous mode
and synchronous mode.
The asynchronous mode functions exactly like the default time step
in version 11 or older.
In server versions 17 and below, asynchronous mode is still the
default mode for all players, including versions 12 to 17.

In server versions 17 and below, asynchronous mode is the
default mode for all players, including versions 12 to 17.
If players wish to switch to synchronous mode, they can do
so by using the "(synch_see)" command. Once they have switched
to synchronous mode, they cannot return to asynchronous mode.
Additionally, players using version 11 or older can also use
the "(synch_see)" command to access synchronous mode.

In server versions 18 and above, players using version 18 are
required to use synchronous mode. However, players using older
versions can still switch to synchronous mode by using the
"(synch_see)" command to change the default view mode.




^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Range of View
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The visible sector of a player is dependant on several factors.
First of all we have the server parameters **server::sense_step** and
**server::visible_angle** which determines the basic time step between
visual information and how many degrees the player's normal view cone is.
The default values in the asynchronous mode are 150 milli-seconds and 90 degrees.
If players use the synchronous mode, the frequency of the visual information
is synchronized with the simulation step. See the next section in detail.

The player can also influence the frequency and quality of the information
by changing *ViewWidth* and *ViewQuality*.

To calculate the current view frequency and view angle of the agent
use equations :eq:`view-freq` and :eq:`view-angle`.

.. math::
  :label: view-freq

  view\_frequency = sense\_step * view\_quality\_factor * view\_width\_factor

where view_quality_factor is 1 if *ViewQuality* is ``high``
and 0.5 if *ViewQuality* is ``low``;
view_width_factor is 2 if *ViewWidth* is ``narrow``,
1 if *ViewWidth* is ``normal``, and 0.5 if *ViewWidth* is ``wide``.

.. math::
  :label: view-angle

  view\_angle = visible\_angle * view\_width\_factor

where view_width_factor is 0.5 if *ViewWidth* is ``narrow``,
1 if *ViewWidth* is ``normal``, and 2 if *ViewWidth* is ``wide``.

The player can also "see" an object if it's within **server::visible_distance**
meters of the player.
If the objects is within this distance but not in the view cone then the
player can know only the type of the object (ball, player, goal or flag),
but not the exact name of the object.
Moreover, in this case, the capitalized name, that is "B", "P", "G" and "F",
is used as the name of the object rather than "b", "p", "g" and "f".

.. figure:: ./images/view-example.*
  :align: center
  :name: view-example

  The visible range of an individual agent in the soccer server.
  The viewing agent is the one shown as two semi-circles. The light
  semi-circle is its front. The black circles represent objects in the world.
  Only objects within **server::view_angle**/2, and those within
  **server::visible_distance** of the viewing agent can be seen.
  **unum_far_length**, **unum_too_far_length**, **team_far_length**, and
  **team_too_far_length** affect the amount of precision
  with which a player's identity is given. Taken from [Stone98]_.


The following example and :numref:`view-example` are taken from [Stone98]_.

The meaning of the view_angle parameter is illustrated in :numref:`view-example`.
In this figure, the viewing agent is the one shown as two semi-circles.
The light semi-circle is its front.
The black circles represent objects in the world.
Only objects within :math:`view\_angle^\circ/2`, and those within
visible_distance of the viewing agent can be seen.
Thus, objects *b* and *g* are not visible; all of the rest are.

As object *f* is directly in front of the viewing agent, its angle would be
reported as 0 degrees.
Object *e* would be reported as being roughly :math:`-40^\circ`, while object
*d* is at roughly :math:`20^\circ`.

Also illustrated in :numref:`view-example`, the amount of information
describing a player varies with how far away the player is.
For nearby players, both the team and the uniform number of the player are
reported.
However, as distance increases, first the likelihood that the uniform number
is visible decreases, and then even the team name may not be visible.
It is assumed in the server that **unum_far_length** :math:`\leq`
**unum_too_far_length** :math:`\leq` **team_far_length** :math:`\leq`
**team_too_far_length**.
Let the player's distance be *dist*. Then

- If *dist* :math:`\leq` **unum_far_length**, then both uniform number and
  team name are visible.
- If **unum_far_length** :math:`<` *dist* :math:`<` **unum_too_far_length**,
  then the team name is always visible, but the probability that the uniform
  number is visible decreases linearly from 1 to 0 as *dist* increases.
- If *dist* :math:`\geq` **unum_too_far_length**, then the uniform number is
  not visible.
- If *dist* :math`\leq` **team_far_length**, then the team name is visible.
- If **team_far_length** :math:`<` *dist* :math:`<` **team_too_far_length**,
  then the probability that the team name is visible decreases linearly from 1
  to 0 as *dist* increases.
- If *dist* :math:`\geq` **team_too_far_length**, then the team name is not
  visible.

For example, in :numref:`view-example`, assume that all of the labeled circles
are players.
Then player *c* would be identified by both team name and uniform number;
player *d* by team name, and with about a 50% chance, uniform number;
player *e* with about a 25% chance, just by team name, otherwise with neither;
and player *f* would be identified simply as an anonymous player.

.. list-table:: Parameters for the visual sensors in server.conf.
   :name: param-visualsensor
   :header-rows: 1
   :widths: 60 40

   * - Parameter in ``server.conf``
     - Value
   * - server::sense_step
     - 150
   * - server::visible_angle
     - 90.0
   * - server::visible_distance
     - 3.0
   * - server::quantize_step
     - 0.1
   * - server::quantize_step_l
     - 0.01

.. list-table:: Heterogenious parameters for the visual sensors.
   :name: heterogenious-param-visualsensor
   :header-rows: 1
   :widths: 60 40

   * - Parameters in player_type
     - Value
   * - unum_far_length
     - 20.0
   * - unum_too_far_length
     - 40.0
   * - team_far_length
     - maximum_dist_in_pitch
   * - team_too_far_length
     - maximum_dist_in_pitch
   * - player_max_observation_length
     - maximum_dist_in_pitch
   * - ball_vel_far_length
     - 20
   * - ball_vel_too_far_length
     - 40
   * - ball_max_observation_length
     - maximum_dist_in_pitch
   * - flag_chg_far_length
     - 20
   * - flag_chg_too_far_length
     - 40
   * - flag_max_observation_length
     - maximum_dist_in_pitch


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Range of View and View Frequency in Synchronous mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In synchronous mode, the "low" view quality is not available,
and the view widths in :numref:`setting-synchronousmode-v17` are available.
In all view widths, rcssserver send see messages at
**server::synch_see_offset** milli-seconds from the beginning
of the cycle.


.. .. table::  Settings of the synchronous mode in server v.17 and older versions
.. table::  Settings of the synchronous mode
   :name: setting-synchronousmode-v17

   +-----------+----------------------+----------------+
   |mode       |view width(degree)    |see frequency   |
   +===========+======================+================+
   |narrow     |60                    |every cycle     |
   +-----------+----------------------+----------------+
   |normal     |120                   |every 2 cycles  |
   +-----------+----------------------+----------------+
   |wide       |180                   |every 3 cycles  |
   +-----------+----------------------+----------------+

.. .. table::  Settings of the synchronous mode in server v.18 and players v.17
..   :name: setting-synchronousmode-v18-v17

..   +-----------+----------------------+----------------+----------------+
..   |mode       |view width(degree)    |see frequency   |noise term      |
..   +===========+======================+================+================+
..   |narrow     |60                    |every cycle     | 0.1            |
..   +-----------+----------------------+----------------+----------------+
..   |normal     |120                   |every 2 cycles  | 0.1            |
..   +-----------+----------------------+----------------+----------------+
..   |wide       |180                   |every 3 cycles  | 0.1            |
..   +-----------+----------------------+----------------+----------------+

.. .. table::  Settings of the synchronous mode in server v.18 and players v.18
..   :name: setting-synchronousmode-v18-v18
..
..   +-----------+----------------------+----------------+----------------+
..   |mode       |view width(degree)    |see frequency   |noise term      |
..   +===========+======================+================+================+
..   |narrow     |60                    |every cycle     | 0.05           |
..   +-----------+----------------------+----------------+----------------+
..   |normal     |120                   |every cycle     | 0.075          |
..   +-----------+----------------------+----------------+----------------+
..   |wide       |180                   |every cycle     | 0.1            |
..   +-----------+----------------------+----------------+----------------+


.. The concept of the noise term was developed in server version 18.
.. By increasing the noise term, the server introduces more noise to observed objects.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Focus Point
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The focus point concept was developed in server version 18 to make observations
in the game more closely resemble those made by human observers and camera
lenses. The position of the focus point affects the observation noise model.
In brief, the server introduces more noise to the distance of an observed
object if the object is farther from the observer's focus point.

The default position of the focus point is the player's position. However,
the player can change the focus point by sending the
"(change_focus dist_moment dir_moment)" command.
It's worth noting that the focus point cannot be outside the
player's view angle, and its maximum distance from the player is 40.

This feature is available to players using version 18 or above on
server versions 18 or above.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Visual Sensor Noise Model: Protocol v17 or older
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to introduce noise in the visual sensor data the values sent from
the server is quantized.
For example, the distance value of the object, in the case where the object
in sight is a ball or a player, is quantized in the following manner:

.. math::

  d' = {\mathrm Quantize}(\exp({\mathrm Quantize}(\log(d),quantize\_step)),0.1)


where :math:`d` and :math:`d'` are the exact distance and quantized distance
respectively, and

.. math::

  {\mathrm Quantize}(V,Q) = {\mathrm ceiling}(V/Q) \cdot Q


This means that players can not know the exact positions of very far objects.
For example, when distance is about 100.0 the maximum noise is about 10.0,
while when distance is less than 10.0 the noise is less than 1.0.

In the case of flags and lines, the distance value is quantized in the
following manner.

.. math::

  d' = {\mathrm Quantize}(\exp({\mathrm Quantize}(\log(d),quantize\_step\_l)),0.1)


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Visual Sensor Noise Model: Protocol v18
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If players use the protocl version 18, the visual sensor noise model is changed as follows:

.. .. math::
..  quantize\_step' = quantize\_step \cdot ViewAngleNoiseTerm


.. math::
  p_{rfx} &= p_{xf} - p_{xo} \\
  p_{rfy} &= p_{yf} - p_{yo} \\
  f &= \sqrt{p_{rfx}^2 + p_{rfy}^2} \\
  f' &= \exp({\mathrm Quantize}(\log(f),quantize\_step)) \\
  d'' &= {\mathrm Quantize}({\mathrm max}(0.0, d - (f - f')), 0.1)

..  f' = {\mathrm Quantize}(\exp({\mathrm Quantize}(\log(f),quantize\_step')),0.1)

where :math:`(p_{xf},p_{yf})` is the absolute position of the focus point of the observer,
:math:`(p_{xo},p_{yo})` is the absolute position of the observer,
:math:`d` is the exact distance of the observer to the object,
:math:`f` and :math:`f'` are the exact distance and quantized distance
of the focus point to the object respectively,
and :math:`d''` is the result distance value sent to the observer.

This noise model is applied to observations made by players using version 18.
When the observer's focus point is set to the default position (i.e., the observer's position),
this model functions in exactly the same  way as the visual sensor noise model in server version 17.
