.. -*- coding: utf-8; -*-

Last update: |today|

.. _cha-soccerserver:

*************************************************
Soccer Server
*************************************************


==================================================
Objects
==================================================

.. figure:: ./images/objects.*
  :align: center
  :scale: 80%
  :name: objects

  UML diagram of the objects in the simulation

==================================================
Protocols
==================================================

.. _sec-playercommmandprotocol:

--------------------------------------------------
Player Command Protocol
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Connecting, reconnecting, and disconnecting
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------------------------------+-------------------------------------------------+
|From player to server                                   |From server to client                            |
+========================================================+=================================================+
| | (init *TeamName* [(version *VerNum* )] [(goalie)])   | | (init *Side* *Unum* *PlayMode*)               |
| |     *TeamName* ::= \[+-_a-zA-Z0-9\]+                 | |          *Side* ::= ``l`` \| ``r``            |
| |       *VerNum* ::= the protocol version (e.g. 15)    | |          *Unum* ::= 1~11                      |
|                                                        | |      *PlayMode* ::= one of play modes         |
|                                                        | | (error no_more_team_or_player)                |
|                                                        | | (error reconnect)                             |
+--------------------------------------------------------+-------------------------------------------------+
| | (reconnect ...)                                      | |                                               |
| |     TeamName :=                                      | |                                               |
+--------------------------------------------------------+-------------------------------------------------+
| (bye)                                                  |                                                 |
+--------------------------------------------------------+-------------------------------------------------+


If your client connects or reconnects sucessfully with a protocol version >= 7.0, the
server will additionally send following messages: ``server_param`` (a message
containing the server parameters), ``player_param`` (a message containing the
player parameters) and ``player_type`` (a message containing the player types).
Finally, the player will receive a message on changed
players (see Sec. :ref:`sec-heterogeneousplayers`).


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Initial Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------------------------------------+-------------------------------------------------------------+
|From player to server                                      |From server to player                                        |
+===========================================================+=============================================================+
| | (compression *Level*)                                   | | (ok compression *Level*)                                  |
| |    *Level* ::= zlib compression level [0,9]             | | (warning compression_unsupported)                         |
| |                or negative number                       | |                                                           |
+-----------------------------------------------------------+-------------------------------------------------------------+
| | (clang (ver *MinVer* *MaxVer*))                         | (ok clang (ver *MinVer* *MaxVer*))                          |
| |    *MinVer* ::= integer                                 |                                                             |
| |    *MaxVer* ::= integer                                 |                                                             |
+-----------------------------------------------------------+-------------------------------------------------------------+
| | (ear (*OnOff* [*Team*] [*Type*]))                       | | no response if succeeded                                  |
| |    *OnOff* ::= ``on`` \| ``off``                        | | (error no team with name *TeamName*)                      |
| |    *Team* ::= ``our`` \| ``opp`` \|                     |                                                             |
| |               ``left`` \| ``right`` \|                  |                                                             |
| |               ``l`` \| ``r`` \|                         |                                                             |
| |               *TeamName*                                |                                                             |
+-----------------------------------------------------------+-------------------------------------------------------------+
| (synch_see)                                               | (ok synch_see)                                              |
+-----------------------------------------------------------+-------------------------------------------------------------+




^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Player Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+------------------------------------------------------------------------------+--------------------------+
|From player to server                                                         |Only once per cycle       |
+==============================================================================+==========================+
| | (turn *Moment*)                                                            | Yes                      |
| |     *Moment* ::= **minmoment** ~ **maxmoment** degrees                     |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (dash *Power* [*Direction*])                                               | Yes                      |
| |     *Power* ::= **min_dash_power** ~ **max_dash_power**                    |                          |
| |     *Direction* ::= **min_dash_angle** ~ **max_dash_angle** degrees        |                          |
| | Note: backward dash (negative power) consumes double stamina.              |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (kick *Power* *Direction*)                                                 | Yes                      |
| |     *Power* ::= **minpower** ~ **maxpower**                                |                          |
| |     *Direction* ::= **minmoment** ~ **maxmoment** degrees                  |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (tackle *PowerOrAngle* [*Foul*])                                           | Yes                      |
| |     *PowerOrAngle* ::= **minmoment** ~ **maxmoment** degrees               |                          |
|                        : if client version >= 12                             |                          |
| |     *PowerOrAngle* ::= **-max_back_tackle_power** ~ **max_tackle_power**   |                          |
|                        : if client version <  12                             |                          |
| |     *Foul* ::= ``true`` \| ``false`` \| ``on`` \| ``off``                  |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (catch *Direction*)                                                        | Yes                      |
| |     *Direction* ::= **minmoment** ~ **maxmoment** degrees                  |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (move *X* *Y*)                                                             | Yes                      |
| |     *X* ::= real number                                                    |                          |
| |     *Y* ::= real number                                                    |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (change_view [*Width*] *Quality*)                                          | No                       |
| |     *Width* ::= ``narrow`` \| ``normal`` \| ``wide``                       |                          |
| |     *Quality* ::= ``high`` \| ``low``                                      |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (say "*Message*")                                                          | No                       |
| |     *Message* ::= \[-0-9a-zA-Z ().+\*/?<>_\]\*                             |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (pointto *Distance* *Direction*)                                           | No                       |
| | (pointto *Off*)                                                            |                          |
| |     *Distance* ::= real number                                             |                          |
| |     *Direction* ::= real number degree                                     |                          |
| |     *Off* ::= ``false`` \| ``off``                                         |                          |
+------------------------------------------------------------------------------+--------------------------+
| | (attentionto *Team* *Unum*)                                                | No                       |
| | (attentionto *Off*)                                                        |                          |
| |    *Team* ::= ``our`` \| ``opp`` \|                                        |                          |
| |               ``left`` \| ``right`` \|                                     |                          |
| |               ``l`` \| ``r`` \|                                            |                          |
| |               *TeamName*                                                   |                          |
| |     *Unum* ::= integer                                                     |                          |
| |     *Off* ::= ``false`` \| ``off``                                         |                          |
+------------------------------------------------------------------------------+--------------------------+
| (done)                                                                       | Yes                      |
+------------------------------------------------------------------------------+--------------------------+


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Others
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
+----------------------------------------------+----------------------------------------------------------+
|From player to server                         |From server to player                                     |
+==============================================+==========================================================+
| (sense_body)                                 | sense_body message                                       |
+----------------------------------------------+----------------------------------------------------------+
|  (score)                                     | | (score *Time* *Our* *Opp*)                             |
|                                              | |    *Time* ::= simulation cycle of rcssserver           |
|                                              | |    *Our* ::= sender's team score                       |
|                                              | |    *Opp* ::= opponent team score                       |
+----------------------------------------------+----------------------------------------------------------+


The server may respond to the above commands with the errors:
(error unknown command) or
(error illegal command form)

--------------------------------------------------
Player Sensor Protocol
--------------------------------------------------

The following table shows the protocol for client version 14 or later.

+--------------------------------------------------------------------------------------------------------------------------+
|From server to player                                                                                                     |
+==========================================================================================================================+
| | (hear *Time* *Sender* "*Message*")                                                                                     |
| | (hear *Time* *OnlineCoach* *CoachLanguageMessage*)                                                                     |
| |    *Time* ::= simulation cycle of rcssserver                                                                           |
| |    *Sender* ::= ``online_coach_left`` | ``online_coach_right`` | ``coach`` | ``referee`` | ``self`` | *Direction*      |
| |    *Direction* ::= -180 ~ 180 degrees                                                                                  |
| |    *Message* ::= string                                                                                                |
| |    *OnlineCoach* ::= ``online_coach_left`` | ``online_coach_right``                                                    |
| |    *CoachLanguageMessage* ::= see the standard coach language section                                                  |
+--------------------------------------------------------------------------------------------------------------------------+
| | (see *Time* *ObjInfo*\+)                                                                                               |
| |    *Time* ::= simulation cycle of rcssserver                                                                           |
| |    *ObjInfo* ::=                                                                                                       |
| |               (*ObjName* *Distance* *Direction* *DistChange* *DirChange* *BodyFacingDir* *HeadFacingDir*               |
|                       [*PointDir*] [t] [k]])                                                                             |
| |               \| (*ObjName* *Distance* *Direction* *DistChange* *DirChange* [*PointDir*] [{t|k}])                      |
| |               \| (*ObjName* *Distance* *Direction* [t] [k])                                                            |
| |               \| (*ObjName* *Diretion*)                                                                                |
| |    *ObjName* ::= (p ["*TeamName*" [*UniformNumber* [goalie]]])                                                         |
| |               \| (b)                                                                                                   |
| |               \| (g {l\|r})                                                                                            |
| |               \| (f c)                                                                                                 |
| |               \| (f {l\|c\|r} {t\|b})                                                                                  |
| |               \| (f p {l\|r} {t\|c\|b})                                                                                |
| |               \| (f g {l\|r} {t\|b})                                                                                   |
| |               \| (f {l\|r\|t\|b} 0)                                                                                    |
| |               \| (f {t\|b} {l\|r} {10\|20\|30\|40\|50})                                                                |
| |               \| (f {l\|r} {t\|b} {10\|20\|30})                                                                        |
| |               \| (l {l\|r\|t\|b} 0)                                                                                    |
| |               \| (P)                                                                                                   |
| |               \| (B)                                                                                                   |
| |               \| (G)                                                                                                   |
| |               \| (F)                                                                                                   |
| |     *Distance* ::= positive real number                                                                                |
| |     *Direction* ::= -180 ~ 180 degrees                                                                                 |
| |     *DistChange* ::= real number                                                                                       |
| |     *DirChange* ::= real number                                                                                        |
| |     *BodyFacingDir* ::= -180 ~ 180 degrees                                                                             |
| |     *HeadFacingDir* ::= -180 ~ 180 degrees                                                                             |
| |     *PointDir* ::= -180 ~ 180 degrees                                                                                  |
| |     *TeamName* ::= string                                                                                              |
| |     *UniformNumber* ::= 1 ~ 11                                                                                         |
+--------------------------------------------------------------------------------------------------------------------------+
| | (sense_body *Time*                                                                                                     |
| |     (view_mode {high\|low} {narrow\|normal\|wide})                                                                     |
| |     (stamina *Stamina* *Effort* *Capacity*)                                                                            |
| |     (speed *AmountOfSpeed* *DirectionOfSpeed*)                                                                         |
| |     (head_angle *HeadAngle*)                                                                                           |
| |     (kick *KickCount*)                                                                                                 |
| |     (dash *DashCount*)                                                                                                 |
| |     (turn *TurnCount*)                                                                                                 |
| |     (say *SayCount*)                                                                                                   |
| |     (turn_neck *TurnNeckCount*)                                                                                        |
| |     (catch *CatchCount*)                                                                                               |
| |     (move *MoveCount*)                                                                                                 |
| |     (change_view *ChangeViewCount*)                                                                                    |
| |     (arm (movable *MovableCycles*) (expires *ExpireCycles*) (count *PointtoCount*))                                    |
| |     (focus (target {none\|{l\|r} *Unum*}) (count *AttentiontoCount*))                                                  |
| |     (tackle (expires *ExpireCycles*) (count *TackleCount*))                                                            |
| |     (collision {none\|[(ball)] [(player)] [(post)]})                                                                   |
| |     (foul (charged *FoulCycles*) (card {red\|yellow\|none})))                                                          |
+--------------------------------------------------------------------------------------------------------------------------+
| | (fullstate *Time*                                                                                                      |
| |     (pmode {goalie_catch_ball\_{l\|r}|*PlayMode*})                                                                     |
| |     (vmode {high\|low} {narrow\|normal\|wide})                                                                         |
| |     (count *KickCount* *DashCount* *TurnCount* *CatchCount* *MoveCount* *TurnNeckCount* *ChangeViewCount* *SayCount*)  |
| |     (arm (movable *MovableCycles*) (expires *ExpireCycles*))                                                           |
|            (target *Distance* *Direction*) (count *PointtoCount*)                                                        |
| |     (score *Time* *Our* *Opp*)                                                                                         |
| |     ((b) *X* *Y* *VelX* *VelY*)                                                                                        |
| |     *Players*\+)                                                                                                       |
| |         *Players* ::= ((p {l\|r} *UniformNumber* [g] *PlayerType*)                                                     |
|                             *X* *Y* *VelX* *VelY* *BodyDir* *NeckDir* [*PointtoDist* *PointtoDir*]                       |
|                             (stamina *Stamina* *Effort* *Recovery* *Capacity*)                                           |
|                             [k\|t\|f] [r\|y]))                                                                           |
+--------------------------------------------------------------------------------------------------------------------------+


.. _sec-sensormodels:

==================================================
Sensor Models
==================================================

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

--------------------------------------------------
Aural Sensor Model
--------------------------------------------------

Aural sensor messages are sent when a client or a coach sends a say command.
The calls from the referee is also received as aural messages.
All messages are received immediately.

The format of the aural sensor message from the soccer server is:

  (hear  *Time*  *Sender*  ''*Message*'')

- *Time* indicates the current time.
- *Sender* is the relative direction to the sender if it is another player,
  otherwise it is one of the following:

  - ``self``: when the sender is yourself.
  - ``referee``: when the sender is the referee.
  - ``online_coach_left`` or ``online_coach_right``: when the sender is one of the online coaches.

- *Message* is the message. The maximum length is **server::say_msg_size** bytes.
  The possible messages from the referee are described in Section :ref:`sec-playmodes`.
  **TODO: about yellow/red card information from the referee. See [14.0.0] in NEWS.**

The server parameters that affects the aural sensor are described in :numref:`param-auralsensor`.

.. list-table:: Parameters for the aural sensor.
   :name: param-auralsensor
   :header-rows: 1
   :widths: 60 40

   * - Parameter in server.conf
     - Value
   * - audio_cut_dist
     - 50.0
   * - hear_max
     - 1
   * - hear_inc
     - 1
   * - hear_decay
     - 1

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Capacity of the Aural Sensor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A player can only hear a message if the player's hear capacity is at least
**server::hear_decay**, since the hear capacity of the player is decreased by
that number when a message is heard.
Every cycle the hear capacity is increased with **server::hear_inc**.
The maximum hear capacity is **server::hear_max**.
To avoid a team from making the other team's communication useless by
overloading the channel the players have separate hear capacities for each team.
With the current server.conf file this means that a player can hear at most
one message from each team every second simulation cycle.

If more messages arrive at the same time than the player can hear the messages
actually heard are chosen randomly. **(TODO: Attentionto Model)**
.. (The current implementation choose the messages according to the order of arrival.)
This rule does not include messages from the referee, or messages from oneself.
.. In other words, a player can say a message and hear a message from another
.. player in the same timestep.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Focus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**(TODO: Attentionto Model. [8.04] in NEWS)**

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Range of Communication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A message said by a player is transmitted only to players within
**server::audio_cut_dist** meters from that player.
For example, a defender, who may be near his own goal, can hear a message
from his goal-keeper but a striker who is near the opponent goal can not hear
the message.
Messages from the referee can be heard by all players.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Aural Sensor Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This example should show which messages get through and how to calculated
the hear capacity.

Example:
Each coach sends a message every cycle.
The referee send a message every cycle.
The four players in the example all send a message every cycle.
Show which messages gets through during 10 cycles (6 might be enough).


.. _sec-visionsensor:

--------------------------------------------------
Vision Sensor Model
--------------------------------------------------


The visual sensor reports the objects currently seen by the player.
The information is automatically sent to the player every
**server::sense_step**, currently 150, milli-seconds, in the default setting.

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
Anonyous Mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**TODO: anonymous mode. [16.0.0]**

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Range of View
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The visible sector of a player is dependant on several factors.
First of all we have the server parameters **server::sense_step** and
**server::visible_angle** which determines the basic time step between
visual information and how many degrees the player's normal view cone is.
The current default values are 150 milli-seconds and 90 degrees.

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

.. list-table:: Parameters for the visual sensors.
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
   * - unum_far_length :math:`^\dagger`
     - 20.0
   * - unum_too_far_length :math:`^\dagger`
     - 40.0
   * - team_far_length :math:`^\dagger`
     - 40.0
   * - team_too_far_length :math:`^\dagger`
     - 60.0
   * - server::quantize_step
     - 0.1
   * - server::quantize_step_l
     - 0.01

:math:`^\dagger` : Not in ``server.conf``, but compiled into the server.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Synchronous Mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**TODO: See [12.0.0_pre20080210],[13.2.0] in NEWS**

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Visual Sensor Noise Model
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


--------------------------------------------------
Body Sensor Model
--------------------------------------------------

The body sensor reports the current "physical" status of the
player.
he information is automatically sent to the player every
**server::sense_body_step**, currently 100, milli-seconds.

The format of the body sensor message is:

+------------------------------------------------------------------------------------------------+
| | (sense_body *Time*                                                                           |
| |              (view_mode *ViewQuality* *ViewWidth*)                                           |
| |              (stamina *Stamina* *Effort* *Capacity*)                                         |
| |              (speed *AmountOfSpeed* *DirectionOfSpeed*)                                      |
| |              (head_angle *HeadAngle*)                                                        |
| |              (kick *KickCount*)                                                              |
| |              (dash *DashCount*)                                                              |
| |              (turn *TurnCount*)                                                              |
| |              (say *SayCount*)                                                                |
| |              (turn_neck *TurnNeckCount*)                                                     |
| |              (catch *CatchCount*)                                                            |
| |              (move *MoveCount*)                                                              |
| |              (change_view *ChangeViewCount*)                                                 |
| |              (arm (movable *MovableCycles*) (expires *ExpireCycles*) (count *PointtoCount*)) |
| |              (focus (target {none\|{l\|r} *Unum*}) (count *AttentiontoCount*))               |
| |              (tackle (expires *ExpireCycles*) (count *TackleCount*))                         |
| |              (collision {none\|[(ball)] [(player)] [(post)]})                                |
| |              (foul (charged *FoulCycles*) (card {red\|yellow\|none})))                       |
+------------------------------------------------------------------------------------------------+

- *ViewQuality* is one of ``high`` and ``low``.
- *ViewWidth* is one of ``narrow``, ``normal``, and ``wide``.
- *AmountOfSpeed* is an approximation of the amount of the player's speed.
- *DirectionOfSpeed* is an approximation of the direction of the player's speed.
- *HeadDirection* is the relative direction of the player's head.
- *\*Count* variables are the total number of commands of that type
  executed by the server.  For example *DashCount* = 134 means
  that the player has executed 134 **dash** commands so far.
- *MovableCycles*
- *ExpireCycles*
- *FoulCycles*

**TODO: add descriptions about values. arm [8.03], focus [8.04], tackle [8.04], collision [12.0.0_pre-20071217], foul [14.0.0] in NEWS**

The semantics of the parameters are described where they are actually
used.
The *ViewQuality* and *ViewWidth* parameters are for example described
in the Section :ref:`sec-visionsensor`.


The server parameters that affects the body sensor are described in
the following table:

.. list-table::  Parameters for the body sensor.
   :name: param-bodysensor
   :header-rows: 1
   :widths: 60 40

   * - Parameter in server.conf
     - Value
   * - server::sense_body_step
     - 100


--------------------------------------------------
Fullstate Sensor Model
--------------------------------------------------

**TODO**

==================================================
Movement Models
==================================================

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

--------------------------------------------------
Collision with other movable objects
--------------------------------------------------

If at the end of the simulation cycle, two objects overlap, then the
objects are moved back until they do not overlap.
Then the velocities are multiplied by -0.1.
Note that it is possible for the ball to go through a player as long
as the ball and the player never overlap at the end of the cycle.

--------------------------------------------------
Collision with goal posts
--------------------------------------------------

**TODO: See [9.2.0] in NEWS**


==================================================
Action Models
==================================================

--------------------------------------------------
Catch Model
--------------------------------------------------

.. figure:: ./images/catcharea.*
  :align: center
  :name: catcharea

  Catchable area of the goalie when doing a (catch 45)

The goalie is the only player with the ability to catch a ball. The
goalie can catch the ball in play mode ``play_on`` in any direction,
if the ball is within the catchable area and the goalie is inside the
penalty area.  If the goalie catches into direction :math:`\varphi`,
the catchable area is a rectangular area of length
**server::catchable_area_l** and width **server::catchable_area_w** in
direction :math:`\varphi` (see :numref:`catcharea`).
The ball will be caught with probability
**server::catch_probability**, if it is inside this area (and it will
not be caught if it is outside this area).
For the current values of catch command parameters see :numref:`param-goaliecatch`:

.. table::  Parameters for the goalie catch command
   :name: param-goaliecatch

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf`` and ``player.conf`` |Value      |
   +=================================================+===========+
   |server::catchable_area_l                         |2.0        |
   +-------------------------------------------------+-----------+
   |server::catchable_area_w                         |1.0        |
   +-------------------------------------------------+-----------+
   |server::catch_probability                        |1.0        |
   +-------------------------------------------------+-----------+
   |server::catch_ban_cycle                          |5          |
   +-------------------------------------------------+-----------+
   |server::goalie_max_moves                         |2          |
   +-------------------------------------------------+-----------+
   |player::catchable_area_l_stretch_max             |1.3        |
   +-------------------------------------------------+-----------+
   |player::catchable_area_l_stretch_min             |1          |
   +-------------------------------------------------+-----------+


First time when goalie has been introduced in Soccer Simulation 2D was with server
version 4.0.0:
When a client connects the server with '(init TEAMNAME (goalie)',
the client becomes a goalies. The goalie can use '(catch DIR)' command
that enable to capture the ball.

With server version 4.0.2 another parameter named **server::catch_probability** has
been introduced. This parameter represents the probability that a goalie succeed to
catch the ball by a catch command. (default value: 1.0)

.. In 2008 a new catch model has been introduced in server version 12.0.0. In the old model
.. if the ball would been in the rectangle determined by the position of the goalie and ball,
.. catch direction from the catch command, catchable_area_l and catchabale_area_w, the ball
.. would been successfully caught. In the new designed model, the catch probability is set to
.. unreliable catches. If ball is not within the goalie's reliable catch area, the catch
.. probability is calculated according to the ball position, so the goalie's catch command might
.. be failed. With this server version, the value of the parameter catchable_area_l has been
.. changed from 2.0 to 1.2. If you want to test this rule, you need to change the
.. **server::catchable_area_l** (default value: 1.2) parameter to the value greater than
.. **server::reliable_catch_area_l** (default value: 1.2).
.. And **server::min_catch_probability** (default value: 1) also need to be change to [0, 1].
.. All these parameters are defined in server.conf file.

Later, in server version 14.0.0 a heterogeneous goalie has been introduced. Beginning
with this version online coaches can change the player type of goalie. The
'catchable_area_l_stretch' variable was added to each heterogeneous player type through
two new parameters: player::catchable_area_l_stretch_min (default value: 1.0) and
player::catchable_area_l_stretch_max (default value: 1.3)

The following pseudo code shows a trade-off rule of the catch model:

.. code-block:: c

 // catchable_area_l_stretch is the heterogeneous parameter, currenlty within [1.0,1.3]

 double this_catchable_are_delta = server::catchable_area_l * (catchable_area_l_stretch - 1.0)
 double this_catchable_area_l_max = server::catchable_area_l + this_catchable_are_delta
 double this_catchable_area_l_min = server::catchable_area_l - this_catchable_are_delta

 if (ball_pos is inside the MINIMAL catch area)
 {
     // the MINIMAL catch area has a length of this_catchable_area_l_min and width server::catchable_area_w goalie
     // catches the ball with probability server::catch_probability (which is 1.0 by default)
 }
 else if (ball_pos is beyond the MAXIMAL (stretched) area)
 {
     // the MAXIMAL catch area has a length of this_catchable_area_l_max and width server::catchable_area_w goalie
     // definitely misses the ball
 }
 else
 {
     double ball_relative_x = (ball_pos - goalie_pos).rotate(-(goalie_body + catch_dir)).x
     double catch_prob = server::catch_probability
                         - server::catch_probability
                           * (ball_relative_x - this_catchable_area_l_min)
                           / (this_catchable_area_l_max - this_catchable_area_l_min)
     // goalie catches the ball with probability catch_prob it holds: catch_prob is in [0.0,1.0]
 }

If a catch command was unsuccessful, it takes **server::catch_ban_cycle** cycles until another catch command can be used (catch commands during this time have simply no effect).
If the goalie succeeded in catching the ball, the play mode will change to ``goalie_catch_ball_[l|r]`` first and ``free_kick_[l|r]``, after that during the same cycle.
Once the goalie caught the ball, it can use the **move** command to move with the ball inside the penalty area.
The goalie can use the **move** command **server::goalie_max_moves** times before it kicks the ball.
Additional **move** commands do not have any effect and the server will respond with ``(error too_many_moves)``.
Please note that catching the ball, moving around, kicking the ball a short distance and immediately catching it again to move more than **server::goalie_max_moves** times is considered as ungentlemanly play.

**TODO: Improvement of the catch model. See [15.0.0] in NEWS**

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

**TODO: stamina recovery in overtime. See [12.0.0 pre-20071217]**

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

  {\mathrm ep} = {\mathrm kick power} \cdot {\mathrm kick\_power\_rate}


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


--------------------------------------------------
Say Model
--------------------------------------------------

Using the *say command*, players can broadcast messages to other players. Messages can be say_msg_size characters long, where valid characters for say messages are from the set sth (without the square brackets). Messages players say can be heard within a distance of *audio_cut_dist* by members of both teams . **Say messages** sent to the server will be sent back to players within that distance immediately. The use of the *say command* is only restricted by the limited capacity of the players of hearing messages.

.. table:: Parameter for the say command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |say_msg_size                                     |10         |
   +-------------------------------------------------+-----------+
   |audio_cut_dist                                   |50         |
   +-------------------------------------------------+-----------+
   |hear_max                                         |1          |
   +-------------------------------------------------+-----------+
   |hear_inc                                         |1          |
   +-------------------------------------------------+-----------+
   |hear_decay                                       |1          |
   +-------------------------------------------------+-----------+


--------------------------------------------------
Tackle Model
--------------------------------------------------

The tackle command is to accelerate the ball towards the player's
body(**TODO:new tackle model [12.0.0 pre-20080210]**).
Players can kick the ball that can not be kicked with the kick command
by executing the tackle command.
The success of tackle depends on a random probability related to the
position of the ball. It can be obtained by the following formula.

The probability of a tackle failure when the ball is in front of the player is:

.. math::

  {\mathrm fail\_prob = (player\_to\_ball.x \div tackle\_dist)^{tackle\_exponent} + (player\_to\_ball.y \div tackle\_width)^{tackle\_exponent}}

The probability of a tackle failure when the ball is behind the player is:

.. math::

  {\mathrm fail\_prob = (player\_to\_ball.x \div tackle\_back\_dist)^{tackle\_exponent} + (player\_to\_ball.y \div tackle\_back\_width)^{tackle\_exponent}}

The probability of processing success is:

.. math::

  {\mathrm tackle\_prob = 1.0 – fail\_prob}

In this case, when the ball is in front of the player, it is used to *tackle_dist* (default is 2.0), otherwise it is used to **tackle_back_dist** (default is 0.5); **player_to_ball** is a vector from the player to the ball, relative to the body direction of the player. When the tackle command is successful, it will give the ball an acceleration in its own body direction.

The execution effect of tackle is similar to that of kick, which is obtained by multiplying the parameter **tackle_power_rate** (default is 0.027) with power. It can be expressed by the following formula:

.. math::

  {\mathrm effective\_power} = {\mathrm power} \times {\mathrm tackle\_power\_rate}

Once the player executes the tackle command, whether successful or not, the player can no longer move within 10 cycles. The following table shows the parameters used in tackle command.

**TODO**

- [12.0.0 pre-20080210] new kick/tackle noise model
- [12.0.0 pre-20080210] max_back_tackle_power
- [13.0.0] forbid backward tackle
- [14.0.0] increasing tackle noise using server::tackle_rand_factor

.. table:: Parameters for the tackle command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |tackle_dist                                      |2          |
   +-------------------------------------------------+-----------+
   |tackle_back_dist                                 |0          |
   +-------------------------------------------------+-----------+
   |tackle_width                                     |1.25       |
   +-------------------------------------------------+-----------+
   |tackle_cycles                                    |10         |
   +-------------------------------------------------+-----------+
   |tackle_exponent                                  |6          |
   +-------------------------------------------------+-----------+
   |tackle_power_rate                                |0.027      |
   +-------------------------------------------------+-----------+
   |max_tackle_power                                 |100        |
   +-------------------------------------------------+-----------+
   |max_back_tackle_power                            |0          |
   +-------------------------------------------------+-----------+
   |tackle_rand_factor                               |2          |
   +-------------------------------------------------+-----------+

--------------------------------------------------
Foul Model
--------------------------------------------------

**TODO**

- [14.0.0] foul model and intentional foul option
- [14.0.0] trade off between foul detect probability and kick power rate
- [15.0.0] improve foul model (red_card_probability)


--------------------------------------------------
Turn Model
--------------------------------------------------

While *dash* is used to accelerate the player in direction of its
body, the *turn command* is used to change the players body direction.
The argument for the turn command is the moment; valid values for the
moment are between **server::minmoment** and **server::maxmoment**.
If the player does not move, the moment is equal to the angle the
player will turn. However, there is a concept of inertia that makes it
more difficult to turn when you are moving.
Specifically, the actual angle the player is turned is as follows:

.. math::

   {\mathrm actual\_angle} = {\mathrm moment \div (1.0 + inertia\_moment} \times {\mathrm player\_speed)}

**server::inertia_moment** is a server.conf parameter with default
value 5.0.
Therefore (with default values), when the player is at speed 1.0, the
*maximum effective* turn he can do is :math:`\pm30`.
However, notice that because you can not dash and turn during the same
cycle, the fastest that a player can be going when executing a turn is
:math:`player\_speed\_max \times player\_decay`, which means the effective turn for a default player
(with default values) is :math:`\pm60`.

For heterogeneous players, the inertia moment is the default inertia
value plus a value between
:math:`{\mathrm player\_decay\_delta\_min \times inertia\_moment\_delta\_factor}` and
:math:`{\mathrm player\_decay\_delta\_max \times inertia\_moment\_delta\_factor}`.

.. table:: Turn Model Parameter

   +-----------------------+------------------------+--------------------------------------+--------+
   || Default Parameters   || Default Value (Range) || Heterogeneous Player Parameters     || Value |
   ||  ``server.conf``     |                        ||  ``player.conf``                    |        |
   +=======================+========================+======================================+========+
   |       Name            |                        |         Name                         |        |
   +-----------------------+------------------------+--------------------------------------+--------+
   |server::minmoment      | -180                   |                                      |        |
   +-----------------------+------------------------+--------------------------------------+--------+
   |server::maxmoment      |  180                   |                                      |        |
   +-----------------------+------------------------+--------------------------------------+--------+
   |server::inertia_moment | 5.0([2.5, 7.5])        || player::player_decay_delta_min      || -0.1  |
   |                       |                        || player::player_decay_delta_max      || 0.1   |
   |                       |                        || player::inertia_moment_delta_factor || 25    |
   +-----------------------+------------------------+--------------------------------------+--------+

.. _sec-turnneckmodel:

--------------------------------------------------
TurnNeck Model
--------------------------------------------------

With *turn_neck*, a player can turn its neck somewhat independently of
its body.
The angle of the head of the player is the viewing angle of the
player.
The *turn command* changes the angle of the body of the player while
turn_neck changes the neck angle of the player relative to its body.
The **minimum** and **maximum** relative angle for the player’s neck
are given by **server::minneckang** and **server::maxneckang** in
server.conf.
Remember that the neck angle is relative to the body of the player so
if the client issues a *turn command*, the viewing angle changes even
if no turn_neck command was issued.
Also, *turn_neck commands* can be executed during the same cycle as
turn, dash, and *kick commands*.
turn_neck is not affected by momentum like turn is.
The argument for a *turn_neck command* must be in the range between
**server::minneckmoment** and **server::maxneckmoment**.

.. table:: Parameter for the turn neck command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |minneckang                                       | -90       |
   +-------------------------------------------------+-----------+
   |maxneckang                                       |  90       |
   +-------------------------------------------------+-----------+
   |minneckmoment                                    | -180      |
   +-------------------------------------------------+-----------+
   |maxneckmoment                                    |  180      |
   +-------------------------------------------------+-----------+

--------------------------------------------------
Pointto Model
--------------------------------------------------

**TODO: See [8.03] in NEWS**

--------------------------------------------------
Attentionto Model
--------------------------------------------------

**TODO: See [8.04] in NEWS**


.. _sec-heterogeneousplayers:

==================================================
Heterogeneous Players
==================================================

With the rcssserver version 7, heterogeneous players were introduced.
For heterogeneous players, the server generates
**player::player_types** random player types at startup.
The player types have different capabilities based on the trade-offs
defined in the player.conf file.
Both teams of a match use the same player types.
Type 0 is the default type and is always the same.
If **player::random_seed** is not 0, the fixed set of heterogenous
player paramters can be generated based on the given seed value.
:numref:`tab-hetero` shows the differences of heterogeneous players:

When players and coaches connect to the server, they can receive
information about the available player types.
The online coaches can change player types unlimited times before the
first kick off and change player types **player::subs_max** times
during other non-`play_on` play modes using the `change_player_type`
command (see :ref:`sec-coachcommand`).

The online coach can substitute a same player type within
**player::pt_max** times.
This restriction also applied to the default player type.
This means that all field players have to be changed to the
non-default type.
In version 16, the goalie is still allowed to be assigned the default type.
However, if server::allow_mult_default_type is false and teams
use the default player type more than player::pt_max, rcssserver
automatically assign the heterogeneous player type to field
players just before the playmode is changed to kick-off.

The online coach can substitute a same player type within
**player::pt_max** times.
This restriction is not applied to the default player type.
If player::pt_max is 1, each player type except the default type can be used only once.

Each time a player is substituted by some other player type, its
stamina, recovery and effort is reset to the initial (maximum) value
of the respective player type.

.. table:: The parameter differences of heterogeneous players
   :name: tab-hetero

   +----------------------+----------------------------------------------------+
   |Parameter             |Description                                         |
   +======================+====================================================+
   |PlayerSpeedMax        |maximum speed                                       |
   +----------------------+----------------------------------------------------+
   |StaminaIncMax         |Amount of stamina recovered in one step             |
   +----------------------+----------------------------------------------------+
   |PlayerDecay           |Player speed decay rate                             |
   +----------------------+----------------------------------------------------+
   |InertiaMoment         |Player inertia force when moving                    |
   +----------------------+----------------------------------------------------+
   |DashPowerRate         |Dash acceleration rate                              |
   +----------------------+----------------------------------------------------+
   |PlayerSize            | Player size                                        |
   +----------------------+----------------------------------------------------+
   |KickableMargin        |Kickable area radius                                |
   +----------------------+----------------------------------------------------+
   |KickRand              |The amount of noise added to the kick               |
   +----------------------+----------------------------------------------------+
   |ExtraStamina          |Extra stamina available when stamina is exhausted   |
   +----------------------+----------------------------------------------------+
   |EffortMax             |Maximum value of the player's effort amount         |
   +----------------------+----------------------------------------------------+
   |EffortMin             |The minimum amount of effort for the player         |
   +----------------------+----------------------------------------------------+
   |CatchAreaLengthStretch|Streach Length to Catch                             |
   +----------------------+----------------------------------------------------+
   |KickPowerRate         |Kick Power Rate                                     |
   +----------------------+----------------------------------------------------+
   |FoulDetectProbability |Probability that the referee will take the foul     |
   +----------------------+----------------------------------------------------+

Heterogeneous player parameters given for each match are different.
Therefore, each agent does not necessarily have the parameters needed to implement the tactics.
Whatever the situation, you need a way to choose the best combination of heterogeneous players.


.. table:: Parameter for substitutions and heterogeneous player types

   +----------------------------+---------+
   |Parameter in player.conf    |Value    |
   +============================+=========+
   |player_types                |18       |
   +----------------------------+---------+
   |subs_max                    |3        |
   +----------------------------+---------+
   |pt_max                      |1        |
   +----------------------------+---------+


==================================================
Referee Model
==================================================

The Automated Referee sends messages to the players, so that players know the actual
play mode of the game. The rules and the behavior for the automated referee are
described in Sec. :ref:`sec-overview-referee`.
Players receive the referee messages as hear messages.
A player can hear referee messages in every situation independent of
the number of messages the player heard from other players.

.. _sec-playmodes:

--------------------------------------------------
Play Modes and referee messages
--------------------------------------------------

The change of the play mode is announced by the referee. Additionally, there are some
referee messages announcing events like a goal or a foul. If you have a look into the
server source code, you will notice some additional play modes that are currently not
used. Both play modes and referee messages are announced using (referee String ),
where String is the respective play mode or message string. The play modes are listed
in :numref:`tab-playmode`, for the messages see :numref:`tab-refereemessages`.

.. table:: Play Modes
   :name: tab-playmode

   +-----------------------------+------+----------------------+------------------------------------------+
   |Play Mode                    |tc    | subsequent play mode | comment                                  |
   +=============================+======+======================+==========================================+
   |before_kick_off              |0     |  kick_off\_\ *Side*  |at the beginning of a half                |
   +-----------------------------+------+----------------------+------------------------------------------+
   |play_on                      |      |                      |during normal play                        |
   +-----------------------------+------+----------------------+------------------------------------------+
   |time_over                    |      |                      |End of the game                           |
   +-----------------------------+------+----------------------+------------------------------------------+
   |kick_off\_\ *Side*           |      |                      |announce start of play                    |
   |                             |      |                      |(after pressing the Kick Off button)      |
   +-----------------------------+------+----------------------+------------------------------------------+
   |kick_in\_\ *Side*            |      | 	               |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |free_kick\_\ *Side*          |      |                      |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |corner_kick\_\ *Side*        |      |                      |when the ball goes out of play over the   |
   |                             |      |                      |goal line, without a goal being scored    |
   |                             |      |                      |and having last been touched by a member  |
   |                             |      |                      |of the defending team.                    |
   +-----------------------------+------+----------------------+------------------------------------------+
   |goal_kick_*Side*             |      |  play_on             |play mode changes once                    |
   |                             |      |                      |the ball leaves the penalty area          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |goal_*Side*                  |      |                      |currently unused                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |drop_ball                    |0     | play_on              |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+
   |offside\_\ *Side*            |30    | free_kick\_\ *Side*  |An offside player who is closer to the    |
   |                             |      |                      |opponent's goal when his teammate hits    |
   |                             |      |                      |the ball, both in front of the ball and   |
   |                             |      |                      |in front of the last player of the        |
   |                             |      |                      |opposing team.                            |
   |                             |      |                      |The offside rule prevents players from    |
   |                             |      |                      |concentrating in front of the opponent's  |
   |                             |      |                      |goal, as no player can stand near the     |
   |                             |      |                      |opponent's goal and have a chance to      |
   |                             |      |                      |score by waiting for the ball, and the    |
   |                             |      |                      |possibility of sending long passes close  |
   |                             |      |                      |to the opponent's goal is limited. In     |
   |                             |      |                      |this way, defenders can distance          |
   |                             |      |                      |themselves from their own goal and        |
   |                             |      |                      |participate more during the game.         |
   +-----------------------------+------+----------------------+------------------------------------------+
   |penalty_kick\_\ *Side*       |      |                      |When the game ends in a draw of 6,000     |
   |                             |      |                      |cycles and overtime, the winner will be   |
   |                             |      |                      |determined by penalty kicks.              |
   +-----------------------------+------+----------------------+------------------------------------------+
   |foul_charge\_\ *Side*        |      |                      |Pushing the opposing player               |
   +-----------------------------+------+----------------------+------------------------------------------+
   |back_pass\_\ *Side*          |      |                      |A goalkeeper is not allowed to catch the  |
   |                             |      |                      |ball inside his own penalty area if a     |
   |                             |      |                      |teammate sends the ball to him.           |
   |                             |      |                      |The opposing team will receive an         |
   |                             |      |                      |indirect free-kick at the point of touch  |
   |                             |      |                      |if the goalkeeper makes the mistake.      |
   +-----------------------------+------+----------------------+------------------------------------------+
   |free_kick_fault\_\ *Side*    |      |                      |Players are not allowd to kick the ball   |
   |                             |      |                      |to themselves after a free kick. If a     |
   |                             |      |                      |player does kick the ball to themselves   |
   |                             |      |                      |after a free kick, a free kick is awarded |
   |                             |      |                      |to the opposing team at the point that    |
   |                             |      |                      |the second kick occurred.                 |
   +-----------------------------+------+----------------------+------------------------------------------+
   |indirect_free_kick\_\ *Side* |      |                      |In a direct free kick, the player can     |
   |                             |      |                      |shoot the ball directly towards the goal, |
   |                             |      |                      |but an indirect free kick cannot and      |
   |                             |      |                      |must pass the ball to a teammate.         |
   +-----------------------------+------+----------------------+------------------------------------------+
   |illegal_defense\_\ *Side*    |      |                      |                                          |
   +-----------------------------+------+----------------------+------------------------------------------+

where Side is either the character *l* or *r*, OSide means opponent’s side.
tc is the time (in number of cycles) until the subsequent play mode will be announced

.. table:: Referee Messages
   :name: tab-refereemessages

   +-------------------------+------+----------------------+----------------------------------------+
   |Message                  |tc    | subsequent play mode | comment                                |
   +=========================+======+======================+========================================+
   |goal_*Side*_*n*          | 50   | kick_off_*OSide*     |announce the *n* th goal for a team     |
   +-------------------------+------+----------------------+----------------------------------------+
   |foul_*Side*              | 0    | free_kick_*OSide*    |announce a foul                         |
   +-------------------------+------+----------------------+----------------------------------------+
   |goalie_catch_ball_*Side* | 0    | free_kick_*OSide*    |                                        |
   +-------------------------+------+----------------------+----------------------------------------+
   |time_up_without_a_team   | 0    | time_over	           |sent if there was no opponent until     |
   |                         |      |                      |the end of the second half              |
   +-------------------------+------+----------------------+----------------------------------------+
   |time_up                  | 0    | time_over	           |sent once the game is over	            |
   |                         |      |                      |(if the time is ≥ second half and       |
   |                         |      |                      |the scores for each team are different) |
   +-------------------------+------+----------------------+----------------------------------------+
   |half_time                | 0    | before_kick_off      |                                        |
   +-------------------------+------+----------------------+----------------------------------------+
   |time_extended            | 0    | before_kick_off      |                                        |
   +-------------------------+------+----------------------+----------------------------------------+

where *Side* is either the character `l` or `r`, *OSide* means opponent’s side.
tc is the time (in number of cycles) until the subsequent play mode will be announced.


--------------------------------------------------
Time Referee
--------------------------------------------------

**TODO**

- Judges the game time
- server::half_time
- [12.1.3] server::extra_half_time
- [13.0.0] change a length of overtime

--------------------------------------------------
Offside Referee
--------------------------------------------------

**TODO**

- Judges the offside
- [9.2.1] fix offside rule (kick-in, goal-kick...)
- [15.0.0] improve offside referee
- [15.4.0] improvement of cheking the last kicker

--------------------------------------------------
FreeKick Referee
--------------------------------------------------

**TODO**

- Judges the behavior during a free kick

--------------------------------------------------
Touch Referee
--------------------------------------------------

**TODO**

- Judge the goal
- [14.0.0] golden goal option, server::golden_goal

--------------------------------------------------
Catch Referee
--------------------------------------------------

**TODO**

- Judges the goalie's catch behavior
- [12.0.0 pre-20071217] change the rules of back pass and catch fault
- [12.0.0 pre-20071217] change the rule of goalies' catch vioration
- [12.1.1] fix the back pass rule

--------------------------------------------------
Foul Referee
--------------------------------------------------

**TODO**

- Judges the foul
- [14.0.0] foul model and intentional foul option
- [14.0.0] foul information in sense_body/fullstate
- [14.0.0] red/yellow card message

--------------------------------------------------
Ball Stuck Referee
--------------------------------------------------

**TODO: server::ball_stuck_area. [11.0.0] in NEWS**

--------------------------------------------------
Illegal Defense Referee
--------------------------------------------------
From the server version 16, a new referee module has been added to control the number of defensive players.
We have four new variables in **server_param** to change the parameters of this referee.

::

    server::illegal_defense_duration = 20

This parameter determines the number of cycles that illegal defense situation would have to remain before calling a free kick.

::

    server::illegal_defense_number = 0

This parameter determines how many players would need to be
in the specified zone before the illegal defense situation countdown
starts.
If the value is set to 0, the referee never detects illegal defense situations.

::

    server::illegal_defense_dist_x = 16.5

This parameter determines the distance from the field's goal lines for
detecting defensive players.


::

    server::illegal_defense_width = 40.32

This parameter determines the horizontal distance from the horizontal
symmetry line for detecting defensive players.

--------------------------------------------------
Keepaway Referee
--------------------------------------------------

**TODO**

- [9.1.0] keepaway mode


--------------------------------------------------
Penalty Shootouts Referee
--------------------------------------------------

**TODO**

- [9.3.0] penalty shootouts
- [9.4.0] pen_coach_moves_players


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Rules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If defensive players exists within the rectangle defined by
**illegal_defense_dist_x** and **illegal_defense_width**, they are
marked as an illegal state.
if the number of markerd players becomes greater than or equal to
**illegal_defense_number** and this continues for
**illegal_defense_duration** cycles, then play mode will change to
**free_kick_[lr]** for the offensive team.

A team is considered as the offensive team when their player is the latest player to kick the ball.
If both teams perform a kick on the same cycle, neither team is considered as offensive, and the countdown resets.
The above rule is applied to the tackle action too.
The change of play mode does not affect cycles of illegal defense situations.


==================================================
The Soccer Simulation
==================================================

In Sec. 4.4, we gave a description on how the objects are moved with respect to their accelerations and velocities. In this section, we describe at what point in time acceleration
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

If during a step, several players kick the ball, all the kicks are applied to the ball and a resulting acceleration is calculated. If the resulting acceleration is larger than the maximum acceleration for the ball, acceleration is normalized to its maximum value. After moving the objects, the server checks for collisions and updates velocities if a collision occurred (see also Sec. 4.4.2).

When applying accelerations and velocities to the objects, the order of application is randomized. After changing objects positions, and updating velocities and accelerations, the automated referee checks the situation and changes the play mode or the object positions, if necessary. Changes to the play mode are announced immediately. Finally, stamina for each player is updated.

--------------------------------------------------
Keepaway Mode
--------------------------------------------------

**TODO: [9.1.0] in NEWS**

==================================================
Using Soccerserver
==================================================

To start the server either type::

  ./rcssserver

from the directory containing the executable or::

  rcssserver

if you installed the executables in your PATH.

--------------------------------------------------
Configuration Files
--------------------------------------------------

rcssserver will look in your home directory for the configuration files:

* .rcssserver/server.conf
* .rcssserver/player.conf
* .rcssserver/CSVSaver.conf
* .rcssserver-landmark.xml

If .conf files do not exist, they will be created and populated with
default values.

You can include additional configuration files by using the ``include=FILE``
option to ``rcssserver``.

**TOOD**

- [8.01] landmark reader
- [13.0.0] RCSS_CONF_DIR


--------------------------------------------------
Recording Command Log
--------------------------------------------------

**TODO: description about .rcl file**

--------------------------------------------------
Automatic Mode
--------------------------------------------------

**TODO: [9.0.2]**

--------------------------------------------------
Synchronous Mode
--------------------------------------------------

**TODO: [7.11] in ChangeLog**

--------------------------------------------------
Result Saver
--------------------------------------------------

**TODO**

- [9.4.0] StdOutSaver, MySQLSaver
- [9.4.3] CSVSaver

--------------------------------------------------
The Soccerserver Parameters
--------------------------------------------------

.. list-table:: Parameters adjustable in ``server.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``server.conf``
     - Description
   * - version
     - '16.0.1'
     - soccer server version
   * - catch_ban_cycle
     - 5
     - goalies cannot execute the next catch until this cycle has passed after the successful catch.
   * - clang_win_size
     - 300
     - time window which controls how many messages can be sent (coach language)
   * - clang_advice_win
     - 1
     - number of advice messages per window
   * - clang_define_win
     - 1
     - number of define messages per window
   * - clang_del_win
     - 1
     - number of del messages per window
   * - clang_info_win
     - 1
     - number of info messages per window
   * - clang_mess_delay
     - 50
     - delay between receipt of message and send to players
   * - clang_mess_per_cycle
     - 1
     - maximum number of coach messages sent per cycle
   * - clang_meta_win
     - 1
     - number of meta messages per window
   * - clang_rule_win
     - 1
     - number of rule messages per window
   * - clang_win_size
     - 1
     - The length of clang message window
   * - coach_port
     - 6001
     - (offine) coach port
   * - connect_wait
     - 300
     - maximum cycle to wait for client connections in automatic mode
   * - drop_ball_time
     - 100
     - number of cycles to wait until dropping the ball automatically
   * - extra_half_time
     - 100
     - length of a half time of extra halves in seconds
   * - foul_cycles
     - 5
     - idle cycles of foul charged players
   * - freeform_send_period
     - 20
     - online coaches can send a freeform message during this period after the waiting period
   * - freeform_wait_period
     - 600
     - online coaches can send a freeform message after waiting this period
   * - game_log_compression
     - 0
     - compression level of game log file
   * - game_log_version
     - 5
     - version of game log format
   * - game_over_wait
     - 100
     - maximum cycle to wait for server termination in automatic mode
   * - goalie_max_moves
     - 2
     - goalie max. moves after a catch
   * - half_time
     - 300
     - length of a half time in seconds
   * - hear_decay
     - 1
     - value that reduces the auditory capacity when receiving an auditory message
   * - hear_inc
     - 1
     - value that increases the auditory capacity when the game cycle is updated
   * - hear_max
     - 1
     - maximum value of audiotory capacity
   * - illegal_defense_duration
     - 20
     - threshold count to detect illegal defense behavior
   * - illegal_defense_number
     - 0
     - number of players judged to be illegal illegal defense behavior
   * - keepaway_start
     - -1
     - automatic referee changes playmode to play_on after this
	   seconds elapsed
   * - kick_off_wait
     - 100
     - maximum cycle to wait kick-off in automatic mode
   * - max_goal_kicks
     - 3
     - (actually no effect)
   * - max_monitors
     - -1
     - max number of monitor connections
   * - nr_extra_halfs
     - 2
     - number of extra halves in a game
   * - nr_normal_halfs
     - 2
     - number of normal halves in a game
   * - olcoach_port
     - 6002
     - online coach port
   * - pen_before_setup_wait
     - 10
     - max waiting cycles in penalty_miss_[lr] or penalty_score_[lr]
   * - pen_max_extra_kicks
     - 5
     - max extra kick trials in penalty shootouts
   * - pen_nr_kicks
     - 5
     - number of normal kick trials in penalty shootouts
   * - pen_ready_wait
     - 10
     - max waiting cycles in penalty_ready_[lr]
   * - pen_setup_wait
     - 70
     - max waiting cycles in penalty_setup_[lr]
   * - pen_taken_wait
     - 150
     - max cycles in penalty_taken_[lr]
   * - point_to_ban
     - 5
     - players cannot execute the next pointto until this cycle has passed
   * - point_to_duration
     - 20
     - point to continues automatically for up to this cycle
   * - port
     - 6000
     - player port number
   * - recv_step
     - 10
     - time step of acception of commands [unit: msec]
   * - say_coach_cnt_max
     - 128
     - upper limit of the number of online coach's message
   * - say_coach_msg_size
     - 128
     - upper limit of length of online coach's message
   * - say_msg_size
     - 10
     - string size of say message [unit:byte]
   * - send_step
     - 150
     - time step of visual information [unit:msec]
   * - send_vi_step
     - 100
     - interval of online coach's look
   * - sense_body_step
     - 100
     - time step of player's body information [unit:msec]
   * - simulator_step
     - 100
     - time step of simulation [unit:msec]
   * - slow_down_factor
     - 1
     - coefficient that slows down simulation time
   * - start_goal_l
     - 0
     - initial score of the left team
   * - start_goal_r
     - 0
     - initial score of the right team
   * - synch_micro_sleep
     - 1
     - sleep time to wait clients in synch mode [unit:msec]
   * - synch_offset
     - 60
     - offset time from the beginning of the cycle to send *think* message [unit:msec]
   * - synch_see_offset
     - 0
     - offset time from the beginning of the cycle to send *see* message if players uses *synch_see* mode [unit:msec]
   * - tackle_cycles
     - 10
     - idle cycles of the players that executed *tackle*
   * - text_log_compression
     - 0
     - compression level of text log file
   * - auto_mode
     - false
     - enable auto start of the match
   * - back_passes
     - true
     - enable back pass rule
   * - coach
     - false
     -
   * - coach_w_referee
     - false
     - allows trainer with automatic referee
   * - forbid_kick_off_offside
     - true
     - forbid kick off offside
   * - free_kick_faults
     - true
     - enable free kick fault rule
   * - fullstate_l
     - false
     - enable full state information for left team
   * - fullstate_r
     - false
     - enable full state information for right team
   * - game_log_dated
     - true
     - flag to write date in game log name
   * - game_log_fixed
     - false
     - enable fixed name in game log
   * - game_logging
     - true
     - flag for game logging
   * - golden_goal
     - false
     - flag for the golden goal rule
   * - keepaway
     - false
     - flag for keepaway mode
   * - keepaway_log_dated
     - true
     - flag to write date in keep away log name
   * - keepaway_log_fixed
     - false
     - enable fixed name in keep away log
   * - keepaway_logging
     - true
     - enable logging in keep away mode
   * - log_times
     - false
     -
   * - old_coach_hear
     - false
     -
   * - pen_allow_mult_kicks
     - true
     - Turn on to allow dribbling in penalty shootouts
   * - pen_coach_moves_players
     - true
     - Turn on to have the server automatically position players for peanlty shootouts
   * - pen_random_winner
     - false
     - enable random winner in penalties
   * - penalty_shootouts
     - true
     - Set to true to enable penalty shootouts after normal time and extra time if the game is drawn.
   * - profile
     - false
     -
   * - proper_goal_kicks
     - false
     -
   * - record_messages
     - false
     - enables recording message to game log file
   * - send_comms
     - false
     - enables sending message to monitors
   * - synch_mode
     - false
     - enables synchronous mode
   * - team_actuator_noise
     - false
     - flag whether to use team specic actuator noise
   * - text_log_dated
     - true
     - flag to write date in text log name
   * - text_log_fixed
     - false
     - enable fixed name in text log
   * - text_logging
     - true
     - flag for recording client command log
   * - use_offside
     - true
     - flag for using off side rule
   * - verbose
     - false
     - flag for verbose mode
   * - wind_none
     - false
     - wind factor is none
   * - wind_random
     - false
     - wind factor is random
   * - audio_cut_dist
     - 50.0
     - audio cut off distance
   * - back_dash_rate
     - 0.6
     - dash power date for the backward dash
   * - ball_accel_max
     - 2.7
     - max. ball acceleration
   * - ball_decay
     - 0.94
     - ball decay
   * - ball_rand
     - 0.05
     - noise parameter for the ball movement
   * - ball_size
     - 0.085
     - ball size
   * - ball_speed_max
     - 3.0
     - max. ball velocity
   * - ball_stuck_area
     - 3.0
     - threshold of distance to detect a stucked situation
   * - ball_weight
     - 0.2
     - (not used) weight of the ball
   * - catch_probability
     - 1.0
     - default goalie catch probability
   * - catchable_area_l
     - 1.2
     - goalie's defalut catchable area length
   * - catchable_area_w
     - 1.0
     - goalie's catchable area width
   * - ckick_margin
     - 1.0
     - corner kick margin
   * - control_radius
     - 2.0
     - (not used)
   * - dash_angle_step
     - 1.0
     - minimum angle step for dash command
   * - dash_power_rate
     - 0.006
     - default dash power rate
   * - effort_dec
     - 0.005
     - dash effort decrement
   * - effort_dec_thr
     - 0.3
     - player dash effort decrement threshold
   * - effort_inc
     - 0.01
     - dash effort increment
   * - effort_inc_thr
     - 0.6
     - dash effort increment treshold
   * - effort_init
     - 1.0
     - default effort value
   * - effort_min
     - 0.6
     - min. player dash effort
   * - extra_stamina
     - 50.0
     - default extra stamina
   * - foul_detect_probability
     - 0.5
     - default foul detect probability
   * - foul_exponent
     - 10.0
     -
   * - goal_width
     - 14.02
     - goal width
   * - illegal_defense_dist_x
     - 16.5
     -
   * - illegal_defense_width
     - 40.32
     -
   * - inertia_moment
     - 5.0
     - default intertia moment for turn
   * - keepaway_length
     - 20
     - length of rectangle in keep away mode
   * - keepaway_width
     - 20
     - width of rectangle in keep away mode
   * - kick_power_rate
     - 0.027
     - kick power rate
   * - kick_rand
     - 0.1
     - base parameter for noise added directly to kicks
   * - kick_rand_factor_l
     - 1.0
     - factor to multiply kick rand for left team
   * - kick_rand_factor_r
     - 1.0
     - factor to multiply kick rand for right team
   * - kickable_margin
     - 0.7
     - default kickable margin
   * - max_back_tackle_power
     - 0.0
     - maximum back tackle power
   * - max_dash_angle
     - 180.0
     - maximum dash angle relative to player's body angle
   * - max_dash_power
     - 100.0
     - maximum dash acceleration power
   * - max_tackle_power
     - 100.0
     - maximum tackle power
   * - maxmoment
     - 180.0
     - max. moment
   * - maxneckang
     - 90.0
     - max. neck angle
   * - maxneckmoment
     - 180.0
     - max. neck moment
   * - maxpower
     - 100.0
     - max kick power
   * - min_dash_angle
     - -180.0
     - minimum dash angle relative to player's body angle
   * - min_dash_power
     - -100.0
     - minimum dash acceleration power
   * - minmoment
     - -180.0
     - max. moment
   * - minneckang
     - -90.0
     - max. neck angle
   * - minneckmoment
     - -180.0
     - max. neck moment
   * - minpower
     - -100
     - min kick power
   * - offside_active_area_size
     - 2.5
     - if offside marked players try to kick/tackle command and their distance from the ball is less than this value, referee detects
	   offside
   * - offside_kick_margin
     - 9.15
     -
   * - offside_kick_margin
     - 9.15
     -
   * - pen_dist_x
     - 42.5
     -
   * - pen_max_goalie_dist_x
     - 14
     -
   * - player_accel_max
     - 1.0
     - max. player acceleration
   * - player_decay
     - 0.4
     - default player decay
   * - player_rand
     - 0.1
     - players' movement noise parameter
   * - player_size
     - 0.3
     - player radius
   * - player_speed_max
     - 1.05
     - maxium speed of players
   * - player_speed_max_min
     - 0.75
     - The minumum value of the maximum speed of players
   * - player_weight
     - 60.0
     - (not used) player weight
   * - prand_factor_l
     - 1
     - factor to multiply prand for left team
   * - prand_factor_r
     - 1
     - factor to multiply prand for right team
   * - quantize_step
     - 0.1
     - quantize step of distance for movable objects
   * - quantize_step_l
     - 0.01
     - quantize step of distance for landmarks
   * - recover_dec
     - 0.002
     - player recovery decrement
   * - recover_dec_thr
     - 0.3
     - player recovery decrement threshold
   * - recover_init
     - 1.0
     - player's initial recovery value
   * - red_card_probability
     - 0.0
     - probability of red card in a foul
   * - side_dash_rate
     - 0.4
     - factor to multiply effective power when side dash is performed
   * - slowness_on_top_for_left_team
     - 1
     -
   * - slowness_on_top_for_right_team
     - 1
     -
   * - stamina_capacity
     - 130600
     - max. recovery capacity of each player's stamina
   * - stamina_inc_max
     - 45.0
     - default max. player stamina increment
   * - stamina_max
     - 8000.0
     - max. player stamina
   * - stopped_ball_vel
     - 0.01
     - threshold value to detect ball is moving or not
   * - tackle_back_dist
     - 0.0
     - max. x distance between player and ball that player may perform a tackle when ball is behind the player
   * - tackle_dist
     - 2.0
     - max. x distance between player and ball that player may perform a tackle when ball is in front of the player
   * - tackle_exponent
     - 6.0
     - exponent used in tackle failure probability equation
   * - tackle_power_rate
     - 0.027
     - tackle power rate
   * - tackle_rand_factor
     - 2.0
     -
   * - tackle_width
     - 1.25
     - max. y distance between player and ball that player may perform a tackle when ball is in front of the player
   * - visible_angle
     - 90.0
     - visible angle
   * - visible_distance
     - 3.0
     -
   * - wind_ang
     - 0.0
     -
   * - wind_dir
     - 0.0
     - wind direction
   * - wind_force
     - 0.0
     -
   * - wind_rand
     - 0.0
     -
   * - coach_msg_file
     - ''
     -
   * - fixed_teamname_l
     - ''
     - fixed name of left team's opponent
   * - fixed_teamname_r
     - ''
     - fixed name of right team's opponent
   * - game_log_dir
     - './'
     - path to game log directory
   * - game_log_fixed_name
     - 'rcssserver'
     - fixed name of game log
   * - keepaway_log_dir
     - './'
     - path to keep away log directory
   * - keepaway_log_fixed_name
     - 'rcssserver'
     - fixed name of keep away log
   * - landmark_file
     - '~/.rcssserver-landmark.xml'
     -
   * - log_date_format
     - '%Y%m%d%H%M%S-'
     - date format in game log
   * - team_l_start
     - ''
     - path to start script of left team
   * - team_r_start
     - ''
     - path to start script of right team
   * - text_log_dir
     - './'
     - path to text log directory
   * - text_log_fixed_name
     - ''
     - fixed name of text log

.. list-table:: Parameters adjustable in ``player.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``player.conf``
     - Description
   * - version
     - '16.0.1'
     - soccer server version
   * - player_types
     - 18
     - number of random player types generated at match startup
   * - pt_max
     - 1
     - number of times that online coach can substitute a player to another player of the same type
   * - random_seed
     - -1
     - seed to generate heterogeneous players parameters of a match if it is non zero
   * - subs_max
     - 3
     - maximum number of substitutions in a match
   * - allow_mult_default_type
     - false
     -
   * - catchable_area_l_stretch_max
     - 1.3
     - defines the upper bound of player's catchable_area_l_stretch
   * - catchable_area_l_stretch_min
     - 1
     - defines the lower bound of player's catchable_area_l_stretch
   * - dash_power_rate_delta_max
     - 0
     - defines the upper bound of player's dash power rate when added to default dash power rate
   * - dash_power_rate_delta_min
     - 0
     - defines the lower bound of player's dash power rate when added to default dash power rate
   * - effort_max_delta_factor
     - -0.004
     - controls the upper bound of player's effort amount
   * - effort_min_delta_factor
     - -0.004
     - controls the lower bound of player's effort amount
   * - extra_stamina_delta_max
     - 50
     - defines the upper bound of player's extra stamina when added to default extra stamina
   * - extra_stamina_delta_min
     - 0
     - defines the lower bound of player's extra stamina when added to default extra stamina
   * - foul_detect_probability_delta_factor
     - 0
     - defines the range of heterogeneous player's foul detect probability
   * - inertia_moment_delta_factor
     - 25
     - factor to control the length of inertia moment delta interval
   * - kick_power_rate_delta_max
     - 0
     - defines the upper bound of player's kick power rate when added to default kick power rate
   * - kick_power_rate_delta_min
     - 0
     - defines the lower bound of player's kick power rate when added to default kick power rate
   * - kick_rand_delta_factor
     - 1
     -
   * - kickable_margin_delta_max
     - 0.1
     - defines the upper bound of player's kickable margin when added to default kickable margin
   * - kickable_margin_delta_min
     - -0.1
     - defines the lower bound of player's kickable margin when added to default kickable margin
   * - new_dash_power_rate_delta_max
     - 0.0008
     -
   * - new_dash_power_rate_delta_min
     - -0.0012
     -
   * - new_stamina_inc_max_delta_factor
     - -6000
     -
   * - player_decay_delta_max
     - 0.1
     - defines the upper bound of inertia moment delta when multiplied by inertia moment delta factor
   * - player_decay_delta_min
     - -0.1
     - defines the lower bound of inertia moment delta when multiplied by inertia moment delta factor
   * - player_size_delta_factor
     - -100
     - controls the range of heterogeneous player's size
   * - player_speed_max_delta_max
     - 0
     - defines the upper bound of player's maximum speed when added to server::player_speed_max
   * - player_speed_max_delta_min
     - 0
     - defines the lower bound of player's maximum speed when added to server::player_speed_max
   * - stamina_inc_max_delta_factor
     - 0
     -

.. list-table:: Parameters adjustable in ``CSVSaver.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``CSVSaver.conf``
     - Description
   * - version
     - '16.0.1'
     - soccer server version
   * - save
     - false
     - flag to save matches result in a file
   * - filename
     - 'rcssserver.csv'
     - file to save the results to. If this file does not exist it will be created. Otherwise, the results will be appended to the end.
