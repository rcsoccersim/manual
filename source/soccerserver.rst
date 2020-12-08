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
| |     *TeamName* ::= \[-_a-zA-Z0-9\]+                  | |          *Side* ::= ``l`` \| ``r``            |
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
| | (change_view *Width* *Quality*)                                            | No                       |
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
| |               (*ObjName* *Distance* *Diretion* *DistChange* *DirChange* *BodyFacingDir* *HeadFacingDir*                |
|                       [*PointDir*] [t] [k]])                                                                             |
| |               \| (*ObjName* *Distance* *Diretion* *DistChange* *DirChange* [*PointDir*] [t] [k]])                      |
| |               \| (*ObjName* *Distance* *Diretion*)                                                                     |
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

  (hear *Time* *Sender* ''*Message*'')

- *Time* indicates the current time.
- *Sender* is the relative direction to the sender if it is another player,
  otherwise it is one of the following:
   - ``self``: when the sender is yourself.
   - ``referee``: when the sender is the referee.
   - ``online_coach_left`` or ``online_coach_right``: when the sender is one of the online coaches.
- *Message* is the message. The maximum length is **server::say_msg_size** bytes.
  The possible messages from the referee are described in Section :ref:`sec-playmodes`.


The server parameters that affects the aural sensor are described in the
following table:


  Parameters for the aural sensor.
+-------------------------------------------+-----------+
|Parameter in server.conf                   |Value      |
+===========================================+===========+
|autio_cut_dist                             |50.0       |
+-------------------------------------------+-----------+
|hear_max                                   |1          |
+-------------------------------------------+-----------+
|hear_inc                                   |1          |
+-------------------------------------------+-----------+
|hear_decay                                 |1          |
+-------------------------------------------+-----------+


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
actually heard are chosen randomly.
.. (The current implementation choose the messages according to the order of arrival.)
This rule does not include messages from the referee, or messages from oneself.
.. In other words, a player can say a message and hear a message from another
.. player in the same timestep.


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


where :math:`(p_{xt},p_{yt}) is the absolute position of the target object,
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
seen. All of the flags and lines are shown in Fig. :ref:`field-detailed`.

.. figure:: ./images/field-detailed.*
  :align: center
  :name: field-detailed

  The flags and lines in the simulation.


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
use equations :ref:`eq:view-freq` and :ref:`eq:view-angle`.

.. math::
  :label: eq:view-freq

  view\_frequency = sense\_step * view\_quality\_factor * view\_width\_factor

where view_quality_factor is 1 if *ViewQuality* is ``high``
and 0.5 if *ViewQuality* is ``low``;
view_width_factor is 2 if *ViewWidth* is ``narrow``,
1 if *ViewWidth* is ``normal``, and 0.5 if *ViewWidth* is ``wide``.

.. math::
  :label: eq:view-angle

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


The following example and Fig.:ref:`view-example` are taken from [Stone98]_.

The meaning of the view_angle parameter is illustrated in Fig.:ref:`view-example`.
In this figure, the viewing agent is the one shown as two semi-circles.
The light semi-circle is its front.
The black circles represent objects in the world.
Only objects within :math:`view\_angle^\circ/2`, and those within
visible_distance of the viewing agent can be seen.
Thus, objects *b* and *g* are not visible; all of the rest are.

As object *f* is directly in front of the viewing agent, its angle would be
reported as 0 degrees.
Object *e* would be reported as being roughly :math:`-40^\circ`, while object
*d* is at roughly :math:`20^\circ.

Also illustrated in Fig.:ref:`view-example`, the amount of information
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

For example, in Fig.:ref:`view-example`, assume that all of the labeled circles
are players.
Then player *c* would be identified by both team name and uniform number;
player *d* by team name, and with about a 50% chance, uniform number;
player *e* with about a 25% chance, just by team name, otherwise with neither;
and player *f* would be identified simply as an anonymous player.


  Parameters for the visual sensors.
+---------------------------------------+-----------+
|Parameter in ``server.conf``           |Value      |
+=======================================+===========+
|server::sense_step                     |150        |
+---------------------------------------+-----------+
|server::visible_angle                  |90.0       |
+---------------------------------------+-----------+
|server::visible_distance               |3.0        |
+---------------------------------------+-----------+
|unum_far_length :math:`^\dagger`       |20.0       |
+---------------------------------------+-----------+
|unum_too_far_length :math:`^\dagger`   |40.0       |
+---------------------------------------+-----------+
|team_far_length :math:`^\dagger`       |40.0       |
+---------------------------------------+-----------+
|team_too_far_length :math:`^\dagger`   |60.0       |
+---------------------------------------+-----------+
|server::quantize_step                  |0.1        |
+---------------------------------------+-----------+
|server::quantize_step_l                |0.01       |
+---------------------------------------+-----------+
:math:`^\dagger` : Not in ``server.conf``, but compiled into the server.



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Visual Sensor Noise Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to introduce noise in the visual sensor data the values sent from
the server is quantized.
For example, the distance value of the object, in the case where the object
in sight is a ball or a player, is quantized in the following manner:

.. math::

  d' = {\rm Quantize}(\exp({\rm Quantize}(\log(d),quantize\_step)),0.1)


where :math:`d` and :math:`d'` are the exact distance and quantized distance
respectively, and

.. math::

  {\rm Quantize}(V,Q) = {\rm ceiling}(V/Q) \cdot Q


This means that players can not know the exact positions of very far objects.
For example, when distance is about 100.0 the maximum noise is about 10.0,
while when distance is less than 10.0 the noise is less than 1.0.

In the case of flags and lines, the distance value is quantized in the
following manner.

.. math::

  d' = {\rm Quantize}(\exp({\rm Quantize}(\log(d),quantize\_step\_l)),0.1)


--------------------------------------------------
Body Sensor Model
--------------------------------------------------

The body sensor reports the current "physical" status of the
player.
he information is automatically sent to the player every
**server::sense_body_step**, currently 100,  milli-seconds.

The format of the body sensor message is:

+-------------+-----------------------------------------------------------------------------------------+
| (sense_body | | *Time*                                                                                |
|             | | (view_mode *ViewQuality* *ViewWidth*)                                                 |
|             | | (stamina *Stamina* *Effort* *Capacity*)                                               |
|             | | (speed *AmountOfSpeed* *DirectionOfSpeed*)                                            |
|             | | (head_angle *HeadAngle*)                                                              |
|             | | (kick *KickCount*)                                                                    |
|             | | (dash *DashCount*)                                                                    |
|             | | (turn *TurnCount*)                                                                    |
|             | | (say *SayCount*)                                                                      |
|             | | (turn_neck *TurnNeckCount*)                                                           |
|             | | (catch *CatchCount*)                                                                  |
|             | | (move *MoveCount*)                                                                    |
|             | | (change_view *ChangeViewCount*)                                                       |
|             | | (arm (movable *MovableCycles*) (expires *ExpireCycles*) (count *PointtoCount*))       |
|             | | (focus (target {none\|{l\|r} *Unum*}) (count *AttentiontoCount*))                     |
|             | | (tackle (expires *ExpireCycles*) (count *TackleCount*))                               |
|             | | (collision {none\|[(ball)] [(player)] [(post)]})                                      |
|             | | (foul (charged *FoulCycles*) (card {red\|yellow\|none})))                             |
+-------------+-----------------------------------------------------------------------------------------+

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

The semantics of the parameters are described where they are actually
used.
he *ViewQuality* and *ViewWidth* parameters are for example described
in the Section :ref:`sec-visionsensor`.


The server parameters that affects the body sensor are described in
the following table:

 Parameters for the body sensors.
+---------------------------------------+-----------+
|Parameter in ``server.conf``           |Value      |
+=======================================+===========+
|server::sense_body_step                |100        |
+---------------------------------------+-----------+



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

.. math::

  (u_x^{t+1},u_y^{t+1}) = (v_x^{t}, v_y^{t}) + (a_x^{t}, a_y^{t}) + (\tilde{r}_{\rm rmax},\tilde{r}_{\rm rmax})

where :math:`\tilde{r}_{\rm rmax}` is a random number whose distribution
is uniform over the range :math:`[-{\rm rmax},{\rm rmax}]`.
:math:`{\rm rmax}` is a parameter that depends on amount of velocity
of the object as follows:

.. math::

  {\rm rmax} = {\rm rand} \cdot |(v_x^{t}, v_y^{t})|

where :math:`{\rm rand}` is a parameter specified by **server::player_rand**
or **server::ball_rand**.

Noise is added also into the *Power* and *Moment* arguments of a
command as follows:

.. math::

  argument = (1 + \tilde{r}_{\rm rand}) \cdot argument



--------------------------------------------------
Collision Model
--------------------------------------------------

If at the end of the simulation cycle, two objects overlap, then the
objects are moved back until they do not overlap.
Then the velocities are multiplied by -0.1.
Note that it is possible for the ball to go through a player as long
as the ball and the player never overlap at the end of the cycle.


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
direction :math:`\varphi` (see Fig.:ref:`catcharea`).
The ball will be caught with probability
**server::catch_probability**, if it is inside this area (and it will
not be caught if it is outside this area).
For the current values of catch command parameters see
the following table:

 Parameters for the goalie catch command

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
|server::goalie-max_moves                         |2          |
+-------------------------------------------------+-----------+
|player::catchable_area_l_stretch_max             |1.3        |
+-------------------------------------------------+-----------+
|player::catchable_area_l_stretch_min             |1          |
+-------------------------------------------------+-----------+


**TODO** about heterogenous parameters.

First time when goalie has been introduced in Soccer Simulation 2D was with server
version 4.0.0:

When a client connects the server with '(init TEAMNAME (goalie)',
the client becomes a goalies. The goalie can use '(catch DIR)' command
that enable to capture the ball.

With server version 4.0.2 another parameter named `goalie_catch_probability' has
been introduced. This parameter represents the probability that a goalie succeed to
catch the ball by a catch command. (default value: 1.0)

In 2008 a new catch model has been introduced in server version 12.0.0. In the old model 
if the ball would been in the rectangle determined by the position of the goalie and ball, 
catch direction from the catch command, catchable_area_l and catchabale_area_w, the ball 
would been successfully caught. In the new designed model, the catch probability is set to 
unreliable catches. If ball is not within the goalie's reliable catch area, the catch 
probability is calculated according to the ball position, so the goalie's catch command might 
be failed. With this server version, the value of the parameter catchable_area_l has been 
changed from 2.0 to 1.2. If you want to test this rule, you need to change the 
server::catchable_area_l (default value: 1.2) parameter to the value greater than 
server::reliable_catch_area_l (default value: 1.2). 
And server::min_catch_probability (default value: 1) also need to be change to [0, 1]. 
All these parameters are defined in server.conf file.

Later, in server version 14.0.0 a heterogeneous goalie has been introduced. Beginning
with this version online coaches can change the player type of goalie. The
'catchable_area_l_stretch' variable was added to each heterogeneous player type through 
two new parameters: player::catchable_area_l_stretch_min (default value: 1.0) and 
player::catchable_area_l_stretch_max (default value: 1.3)

The following pseudo code shows a trade-off rule of the catch model:

// catchable_area_l_stretch is the heterogeneous parameter, currenlty within [1.0,1.3]

double this_catchable_are_delta = server::catchable_area_l * (catchable_area_l_stretch - 1.0)

double this_catchable_area_l_max = server::catchable_area_l + this_catchable_are_delta

double this_catchable_area_l_min = server::catchable_area_l - this_catchable_are_delta

	if (ball_pos is inside the MINIMAL catch area)
	{
		// the MINIMAL catch area has a length of this_catchable_area_l_min and width server::catchable_area_w goalie 
		catches the ball with probability server::catch_probability (which is 1.0 by default)
	}
	else if (ball_pos is beyond the MAXIMAL (stretched) area)
	{
		// the MAXIMAL catch area has a length of this_catchable_area_l_max and width server::catchable_area_w goalie 
		definitely misses the ball
	}
	else
	{
		double ball_relative_x = (ball_pos - goalie_pos).rotate(-(goalie_body + catch_dir)).x

		double catch_prob = server::catch_probability - server::catch_probability * (ball_relative_x - this_catchable_area_l_min) / 
		(this_catchable_area_l_max - this_catchable_area_l_min)

		// goalie catches the ball with probability catch_prob it holds: catch_prob is in [0.0,1.0]
	}

If a catch command was unsuccessful, it takes
**server::catch_ban_cycle** cycles until another catch command can be
used (catch commands during this time have simply no effect).
If the goalie succeeded in catching the ball, the play mode will
change to ``goalie_catch_ball_[l|r]`` first and ``free_kick_[l|r]``,
after that during the same cycle.
nce the goalie caught the ball, it can use the **move** command to
move with the ball inside the penalty area.
he goalie can use the **move** command **server::goalie_max_moves**
times before it kicks the ball.
dditional **move** commands do not have any effect and the server will
respond with ``(error too_many_moves)``.
Please note that catching the ball, moving around, kicking the ball a
short distance and immediately catching it again to move more than
**server::goalie_max_moves** times is considered as ungentlemanly
play.


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
the following list:

+---------------------------------+----------------------------+-------------------------------------------+------------+
|| Default Parameters             || Default Value (Range)     || Heterogeneous Player Parameters          || Value     |
||  ``server.conf``               ||                           ||   ``player.conf``                        ||           |
+=================================+============================+===========================================+============+
| server::min_dash_power          |-100.0                      |                                           |            |
+---------------------------------+----------------------------+-------------------------------------------+------------+
| server::max_dash_power          |100.0                       |                                           |            |
+---------------------------------+----------------------------+-------------------------------------------+------------+
| server::player_decay            || 0.4 ([0.3, 0.5])          || player::player_decay_delta_min           || -0.1      |
| server::inertia_moment          || 5.0 ([2.5, 7.5])          || player::player_decay_delta_min           || 0.1       |
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
If a player accelerates forward (*power* :math:`> 0`), stamina is
reduced by *power*.
Accelerating backwards (*power* :math:`< 0`) is more expensive for the
player: stamina is reduced by :math:`-2~\cdot~`*power*.
If the player's stamina is lower than the power needed for the
**dash**, *power* is reduced so that the **dash** command does not
need more stamina than available.
Some extra stamina can be used every time the available power is lower
than the needed stamina.
The amount of extra stamina depends on the player type and the
parameters **player::extra_stamina_delta_min** and
player::extra_stamina_delta_max**.

After reducing the stamina, the server calculates the *effective  dash
power* for the **dash** command.
The effective dash power *edp* depends on the **dash_power_rate** and the
current effort of the player.
The effort of a player is a value between **effort_min** and **effort_max**;
it is dependent on the stamina management of the player (see below).

.. math::
  :label: eq:effectivedash

  {\rm edp} = {\rm effort} \cdot {\rm dash_power_rate} \cdot {\rm power}

*edp* and the players current body direction are tranformed into vector and
added to the players current acceleration vector :math:`\vec{a}_n`
(usually, that should be 0 before, since a player cannot dash more than once
a cycle and a player does not get accelerated by other means than dashing).

At the transition from simulation step $n$ to simulation step
:math:`n + 1$, acceleration :math:`\vec{a}_n` is applied:

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
   :math:`[ -|\vec{v}_{n}| \cdot {\rm player\_rand} \ldots |\vec{v}_{n}| \cdot {\rm player\_rand}]`.
4. The new position of the player :math:`\vec{p}_{n+1}` is the old position
   :math:`\vec{p}_{n}` plus the velocity vector :math:`\vec{v}_{n}`
   (i.e.\ the maximum distance difference for the player between two
   simulation steps is **player_speed_max).
5. **player_decay** is applied for the velocity of the player:
   :math:`\vec{v}_{n+1} = \vec{v}_{n} \cdot {\rm player\_decay}`.
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
   dir \in [server::min\_dash\_angle,server::max\_dash\_angle]

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

+---------------------------------+----------------------------+-------------------------------------------+------------+
|| Default Parameters             || Default Value (Range)     || Heterogeneous Player Parameters          || Value     |
||  ``server.conf``               ||                           ||   ``player.conf``                        ||           |
+=================================+============================+===========================================+============+
| server::server::max\_dash\_angle| 180.0                      |                                           |            |
+---------------------------------+----------------------------+-------------------------------------------+------------+
| server::server::min\_dash\_angle|-180.0                      |                                           |            |
+---------------------------------+----------------------------+-------------------------------------------+------------+
| server::side\_dash\_rate        | 0.4                        |                                           |            |
+---------------------------------+----------------------------+-------------------------------------------+------------+
| server::back\_dash\_rate        | 0.6                        |                                           |            |
+---------------------------------+----------------------------+-------------------------------------------+------------+
| server::dash\_angle\_step       | 1                          |                                           |            |
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
    `(stamina <STAMINA> <EFFORT> <CAPACITY>)`


.. _sec-kickmodel:

--------------------------------------------------
Kick Model
--------------------------------------------------

--------------------------------------------------
Move Model
--------------------------------------------------

The *move command* can be used to place a player directly onto a desired position on the field. move exists to set up the team and does not work during normal play. It is available at the beginning of each half (play mode ‘**before_kick_off**’) and after a goal has been scored (play modes ‘**goal_r_n **’ or ‘**goal_l_n** ’). In these situations, players can be placed on any position in their own half (i.e. X < 0) and can be moved any number of times, as long as the play mode does not change. Players moved to a position on the opponent half will be set to a random position on their own side by the server.
A second purpose of the *move command* is to move the goalie within the penalty area after the goalie caught the ball. If the goalie caught the ball, it can move together with the ball within the penalty area. The goalie is allowed to move *goalie_max_moves* times before it kicks the ball. Additional *move commands* do not have any effect and the server will respond with (error too_many_moves).


                 Parameter for the move_command
+-------------------------------------------------+-----------+
|Parameter in ``server.conf``                     | Value     |
+=================================================+===========+
|goalie_max_moves                                 |2          |
+-------------------------------------------------+-----------+


--------------------------------------------------
Say Model
--------------------------------------------------

Using the *say command*, players can broadcast messages to other players. Messages can be say_msg_size characters long, where valid characters for say messages are from the set sth (without the square brackets). Messages players say can be heard within a distance of *audio_cut_dist* by members of both teams . **Say messages** sent to the server will be sent back to players within that distance immediately. The use of the *say command* is only restricted by the limited capacity of the players of hearing messages.


                     Parameter for the say command
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

The tackle command is to accelerate the ball towards the player's body. Players can kick the ball that can not be kicked with the kick command by executing the tackle command. The success of tackle depends on a random probability related to the position of the ball. It can be obtained by the following formula.

The probability of a tackle failure when the ball is in front of the player is:

.. math::

  fail_prob = (player_to_ball.x/tackle_dist)^tackle_exponent + (player_to_ball.y/tackle_width)^tackle_exponent

The probability of a tackle failure when the ball is behind the player is:

.. math::

  fail_prob = (player_to_ball.x/tackle_back_dist)^tackle_exponent + (player_to_ball.y/tackle_back__width)^tackle_exponent

The probability of processing success is:

.. math::

  tackle_prob = 1.0 – fail_prob

In this case, when the ball is in front of the player, it is used to *tackle_dist* (default is 2.0), otherwise it is used to **tackle_back_dist** (default is 0.5); **player_to_ball** is a vector from the player to the ball, relative to the body direction of the player. When the tackle command is successful, it will give the ball an acceleration in its own body direction.

The execution effect of tackle is similar to that of kick, which is obtained by multiplying the parameter **tackle_power_rate** (default is 0.027) with power. It can be expressed by the following formula:

.. math::

  effective_power = power * tackle_power_rate

Once the player executes the tackle command, whether successful or not, the player can no longer move within 10 cycles. The following table shows the parameters used in tackle command.

 Parameters for the tackle command

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
Turn Model
--------------------------------------------------

While *dash* is used to accelerate the player in direction of its body, the *turn command* is used to change the players body direction. The argument for the turn command is the moment; valid values for the moment are between **minmoment** and **maxmoment**. If the player does not move, the moment is equal to the angle the player will turn. However, there is a concept of inertia that makes it more difficult to turn when you are moving. Specifically, the actual angle the player is turned is as follows:
**actual_angle = moment/(1.0 + inertia_moment · player_speed)** (4.22)
*Inertia_moment* is a server.conf parameter with default value 5.0. Therefore (with default values), when the player is at speed 1.0, the *maximum effective* turn he can do is ±30. However, notice that because you can not dash and turn during the same cycle, the fastest that a player can be going when executing a turn is *player_speed_max* · player decay, which means the effective turn for a default player (with default values) is ±60.
For heterogeneous players, the inertia moment is the default inertia value plus a value between min. *player_decay_delta_min* · *inertia_moment_delta_factor and max*. *player_decay_delta_max* · *inertia_moment_delta_factor*.

                           Turn Model Parameter
+----------------------------------+------------------------------------------------+
| Basic Parameter ``server.conf``  |  Parameter for heterofenous ``player.conf``    |
+=====================+============+================================+=======+=======+
|       Name          |   Value    |         Name                   | Value |Range  |
+---------------------+------------+--------------------------------+-------+-------+
|minmoment            || -180      | | player_decay_delta_min       | | 0.0 | | 5   |
+---------------------+------------+--------------------------------+-------+-------+
|maxmoment            ||  180      | | player_decay_delta_max       | | 0.4 | | 1   |
+---------------------+------------+--------------------------------+-------+-------+
|inertia_moment       ||  5.0      | | inertia_moment_delta_factor  | | 25  |       |
+---------------------+------------+--------------------------------+-------+-------+




--------------------------------------------------
TurnNeck Model
--------------------------------------------------

With *turn_neck*, a player can turn its neck somewhat independently of its body. The angle of the head of the player is the viewing angle of the player. The *turn command* changes the angle of the body of the player while turn_neck changes the neck angle of the player relative to its body. The **minimum** and **maximum** relative angle for the player’s neck are given by **minmoment** and **maxmoment** in server.conf. Remember that the neck angle is relative to the body of the player so if the client issues a *turn command*, the viewing angle changes even if no turn_neck command was issued.
Also, *turn_neck commands* can be executed during the same cycle as turn, dash, and *kick commands*. turn_neck is not affected by momentum like turn is. The argument for a *turn_neck command* must be in the range between **minneckmoment** and **maxneckmoment**.


                Parameter for the turn neck command
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



==================================================
Heterogeneous Players
==================================================
Heterogeneous Players are players whose values <200b><200b>of some parameters are different from those of a normal player.
In the case of heterogeneous players, Soccer Server will randomly create a player type on startup.
Player types have different capabilities based on the trade-offs defined in the player.conf file.
Soccer Server generates player types random player types at startup.
Randomly select and combine 10 of the 18 heterogeneous players provided by the server.
Both teams in the match use the same player type. Type 0 is the default type and is always the same.
When players connect to the server, they receive information about the available player types.
Online coaches can change the player type indefinitely in "Before Kick Off" mode, and can use “Player Type Change” to change the player type during play modes other than "Play On".
Each time a player is replaced by another player type, their stamina, recovery, and effort are reset to the initial (maximum) value for that player type.
Heterogeneous players can be replaced by coaches other than the keeper.
The normal player can be used by any number of players, but the use of heterogeneous players is limited, and only three players can be used for each type.
Heterogeneous players has the following parameter differences.


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




+----------------------------+---------+
|Parameter in player.conf    |Value    |
+============================+=========+
|player types                |7        |
+----------------------------+---------+
|subs max                    |3        |
+----------------------------+---------+

Table Parameter for substitutions and heterogeneous player types


==================================================
Referee Model
==================================================

The Automated Referee sends messages to the players, so that players know the actual
play mode of the game. The rules and the behavior for the automated referee are
described in Sec. 2.2.1. Players receive the referee messages as hear messages. A player
can hear referee messages in every situation independent of the number of messages the
player heard from other players.

--------------------------------------------------
Play Modes and referee messages
--------------------------------------------------

The change of the play mode is announced by the referee. Additionally, there are some
referee messages announcing events like a goal or a foul. If you have a look into the
server source code, you will notice some additional play modes that are currently not
used. Both play modes and referee messages are announced using (referee String ),
where String is the respective play mode or message string. The play modes are listed
in Tab. 1, for the messages see Tab. 2.

                		Table 1	Play Modes
+-------------------------+------+----------------------+----------------------------------------+
|Play Mode	 	  |tc    | subsequent play mode | comment  			         |
+=========================+======+======================+========================================+
|before_kick_off          |0     |  kick_off_Side	|at the beginning of a half       	 |
+-------------------------+------+----------------------+----------------------------------------+
|play_on                  |      |       		|during normal play			 |
+-------------------------+------+----------------------+----------------------------------------+
|time_over   		  |      |       		|					 |
+-------------------------+------+----------------------+----------------------------------------+
|kick_off_Side            |      | 	        	|announce start of play 	 	 |
|			  |      |		        |(after pressing the Kick Off button)	 |
+-------------------------+------+----------------------+----------------------------------------+
|kick_in_Side             |      | 	        	|					 |
+-------------------------+------+----------------------+----------------------------------------+
|free_kick_Side           |      |      		|					 |
+-------------------------+------+----------------------+----------------------------------------+
|corner_kick_Side         |      |       		|					 |
+-------------------------+------+----------------------+----------------------------------------+
|goal_kick_Side           |      |  play_on      	|play mode changes once			 |
|			  |	 |			|the ball leaves the penalty area	 |
+-------------------------+------+----------------------+----------------------------------------+
|goal_Side                |      |     			|currently unused			 |
+-------------------------+------+----------------------+----------------------------------------+
|drop_ball                |0     | play_on	        |					 |
+-------------------------+------+----------------------+----------------------------------------+
|offside_Side             |30    | free_kick_Side       |for the opposite side			 |
+-------------------------+------+----------------------+----------------------------------------+
where Side is either the character *l* or *r*, OSide means opponent’s side.
tc is the time (in number of cycles) until the subsequent play mode will be announced



                		Table 2	Referee Messages
+-------------------------+------+----------------------+----------------------------------------+
|Message	 	  |tc    | subsequent play mode | comment  			         |
+=========================+======+======================+========================================+
|goal_Side_n              | 50   | kick_off_OSide       |announce the *n* th goal for a team     |
+-------------------------+------+----------------------+----------------------------------------+
|foul_Side                | 0    | free_kick_OSide      |announce a foul			 |
+-------------------------+------+----------------------+----------------------------------------+
|goalie_catch_ball_Side   | 0    | free_kick_OSide      |					 |
+-------------------------+------+----------------------+----------------------------------------+
|time_up_without_a_team   | 0    | time_over	        |sent if there was no opponent until     |
|			  |      |		        |the end of the second half		 |
+-------------------------+------+----------------------+----------------------------------------+
|time_up                  | 0    | time_over	        |sent once the game is over		 |
|			  |	 |			|(if the time is ≥ second half and	 |
|			  |	 |			|the scores for each team are different) |
+-------------------------+------+----------------------+----------------------------------------+
|half_time                | 0    | before_kick_off      |					 |
+-------------------------+------+----------------------+----------------------------------------+
|time_extended            | 0    | before_kick_off      |					 |
+-------------------------+------+----------------------+----------------------------------------+

where Side is either the character *l* or *r*, OSide means opponent’s side.
tc is the time (in number of cycles) until the subsequent play mode will be announced.

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


==================================================
Using Soccerserver
==================================================

--------------------------------------------------
The Soccerserver Parameters
--------------------------------------------------

.. list-table:: Parameters adjustable in ``server.conf``
   :widths: 100 20 30 100
   :header-rows: 1

   * - Name
     - Default Value
     - Current Value  in ``server.conf``
     - Description
   * - goal_width
     - 7.32
     - 14.0
     - goal width
   * - player_size
     -
     - 0.3
     - player size
   * - player_decay
     -
     - 0.4
     - player decay
   * - player_rand
     -
     - 0.1
     -
   * - playerـweight
     -
     - 60.0
     - player weight
   * - playerـspeedـmax
     -
     - 1.0
     - max. player velocity
   * - player-accelـmax
     -
     - 1.0
     - max. player acceleration
   * - playerـaccelـmax
     -
     - 1.0
     - max. player acceleration
   * - staminaـmax
     -
     - 4000.0
     - max. player stamina
   * - staminaـincـmax
     -
     - 45.0
     - max. player stamina increment
   * - recoverـdecـthr
     -
     - 0.3
     - player recovery decrement threshold
   * - recover_min
     -
     - 0.5
     - min. player recovery
   * - recover_dec
     -
     - 0.002
     - player recovery decrement
   * - effort_dec_thr
     -
     - 0.3
     - player dash effort decrement threshold
   * - effort_min
     -
     - 0.6
     - min. player dash effort
   * - effort_dec
     -
     - 0.005
     - dash effort decrement
   * - effort_inc_thr
     -
     - 0.6
     - dash effort increment treshold
   * - effort_inc
     -
     - 0.01
     - dash effort increment
   * - kick_rand
     -
     - 0.0
     - noise added directly to kicks
   * - team_actuator_noise
     -
     -
     - flag whether to use team specic actuator noise
   * - prand_factor_l
     -
     -
     - factor to multiply prand for left team
   * - prand_factor_r
     -
     -
     - factor to multiply prand for right team
   * - kick_rand_factor_l
     -
     -
     - factor to multiply kick rand for left team
   * - kick_rand_factor_r
     -
     -
     - factor to multiply kick rand for right team
   * - ball_size
     -
     - 0.085
     - ball size
   * - ball_decay
     -
     - 0.94
     - ball decay
   * - ball_rand
     -
     - 0.05
     -
   * - ballـweight
     -
     - 0.2
     - weight of the ball
   * - ballـspeedـmax
     -
     - 2.7
     - max. ball velocity
   * - ballـaccelـmax
     -
     - 2.7
     - max. ball acceleration
   * - dashـpowerـrate
     -
     - 0.006
     - dash power rate
   * - kickـpowerـrate
     -
     - 0.027
     - kick power rate
   * - kickableـmargin
     -
     - 0.7
     - kickable margin
   * - controlـradius
     -
     -
     - control radius
   * - catchـprobability
     -
     - 1.0
     - goalie catch probability
   * - catchableـareaـl
     -
     - 2.0
     - goalie catchable area length
   * - catchableـareaـw
     -
     - 1.0
     - goalie catchable area width
   * - goalieـmaxـmoves
     -
     - 2
     - goalie max. moves after a catch
   * - maxpower
     -
     - 100
     - max power
   * - minpower
     -
     - -100
     - min power
   * - maxmoment
     -
     - 180
     - max. moment
   * - minmoment
     -
     - -180
     - min. moment
   * - maxneckmoment
     -
     - 180
     - max. neck moment
   * - minneckmoment
     -
     - -180
     - min. neck moment
   * - maxneckang
     -
     - 90
     - max. neck angle
   * - minneckang
     -
     - -90
     - min. neck angle
   * - visibleـangle
     -
     - 90.0
     - visible angle
   * - visibleـdistance
     -
     -
     - visible distance
   * - audioـcutـdist
     -
     - 50.0
     - audio cut off distance
   * - quantize_step
     -
     - 0.1
     - quantize step of distance for movable objects
   * - quantize_step_l
     -
     - 0.01
     - quantize step of distance for landmarks
   * - quantize_step_dir
     -
     -
     -
   * - quantize_step_dist_team_l
     -
     -
     -
   * - quantize_step_dist_team_r
     -
     -
     -
   * - quantize_step_dist_l_team_l
     -
     -
     -
   * - quantize_step_dist_l_team_r
     -
     -
     -
   * - quantize_step_dir_team_l
     -
     -
     -
   * - quantize_step_dir_team_r
     -
     -
     -
   * - ckick_margin
     -
     - 1.0
     - corner kick margin
   * - wind_dir
     - 0.0
     - 0.0
     - wind direction
   * - wind_force
     - 10.0
     - 0.0
     -
   * - wind_rand
     - 0.3
     - 0.0
     -
   * - wind_none
     -
     -
     - wind factor is none
   * - wind_random
     -
     - false
     - wind factor is random
   * - inertia_moment
     -
     - 5.0
     - intertia moment for turn
   * - half_time
     -
     - 300
     - length of a half time in seconds
   * - drop_ball_time
     -
     - 200
     - number of cycles to wait until dropping the ball automatically
   * - port
     -
     - 6000
     - player port number
   * - coach_port
     -
     - 6001
     - (offine) coach port
   * - olcoach_port_online
     -
     -
     - coach port
   * - say_coach_cnt_max
     -
     - 128
     - upper limit of the number of online coach's message
   * - say_coach_msg_size
     -
     - 128
     - upper limit of length of online coach's message
   * - simulator_step
     -
     - 100
     - time step of simulation [unit:msec]
   * - send_step
     -
     - 150
     - time step of visual information [unit:msec]
   * - recv_step
     -
     - 10
     - time step of acception of commands [unit: msec]
   * - sense_body_step
     -
     - 100
     -
   * - say_msg_size
     -
     - 512
     - string size of say message [unit:byte]
   * - clang_win_size
     -
     - 300
     - time window which controls how many messages can be sent (coach language)
   * - clang_define_win
     -
     - 1
     - number of messages per window
   * - clang_meta_win
     -
     - 1
     -
   * - clang_advice_win
     -
     - 1
     -
   * - clang_info_win
     -
     - 1
     -
   * - clang_mess_delay
     -
     - 50
     - delay between receipt of message and send to players
   * - clang_mess_per_cycle
     -
     - 1
     - maximum number of coach messages sent per cycle
   * - hear_max
     -
     - 2
     -
   * - hear_inc
     -
     - 1
     -
   * - hear_decay
     -
     - 2
     -
   * - catch_ban_cycle
     -
     - 5
     -
   * - coach
     -
     -
     -
   * - coach_w_referee
     -
     -
     -
   * - old_coach_hear
     -
     -
     -
   * - send_vi_step
     -
     - 100
     - interval of online coach's look
   * - use_offside
     -
     - on
     - flag for using off side rule
   * - offside_active_area_size
     -
     - 5
     - offside active area size
   * - forbid_kick_off_offside
     -
     - on
     - forbid kick off offside
   * - log_file
     -
     -
     -
   * - record
     -
     -
     -
   * - record_version
     -
     - 3
     - flaag for record log
   * - record_log
     -
     - on
     - flag for record client command log
   * - record messages
     -
     -
     -
   * - send_log
     -
     - on
     - flag for send client command log
   * - log_times
     -
     - off
     - flag for writing cycle lenth to log file
   * - verbose
     -
     - off
     - flag for verbose mode
   * - replay
     -
     -
     -
   * - offside_kick_margin
     -
     - 9.15
     - offside kick margin
   * - slow_down_factor
     -
     -
     -
   * - start_goal_l
     -
     -
     -
   * - start_goal_r
     -
     -
     -
   * - fullstate_l
     -
     -
     -
   * - fullstate_r
     -
     -
     -
