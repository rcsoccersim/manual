.. -*- coding: utf-8; -*-

=================================================
Soccer Server
=================================================


--------------------------------------------------
Objects
--------------------------------------------------

.. figure:: ./images/objects.*
  :align: center
  :scale: 80%
  :name: objects

  UML diagram of the objects in the simulation

--------------------------------------------------
Protocols
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Player Command Protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

""""""""""""""""""""""""""""""""""""""""""""""""""
Connecting, reconnecting, and disconnecting
""""""""""""""""""""""""""""""""""""""""""""""""""

+--------------------------------------------------------+-------------------------------------------------+
|From player to server                                   |From server to client                            |
+========================================================+=================================================+
| | (init *TeamName* [(version *VerNum* )] [(goalie)])   | | (init *Side* *Unum* *PlayMode*)               |
| |     *TeamName* ::= \[-_a-zA-Z0-9\]+                  | |          *Side* ::= l\|r                      |
| |       *VerNum* ::= the protocol version (e.g. 15)    | |          *Unum* ::= 1~11                      |
|                                                        | |      *PlayMode* ::= one of play modes         |
|                                                        | | (error no_more_team_or_player)                |
|                                                        | | (error reconnect)                             |
+--------------------------------------------------------+-------------------------------------------------+

If your client connects or reconnects sucessfully with a protocol version ≥ 7.0, the
server will additionally send following messages: a message containing the server
parameters, a message containing the player parameters and a message containing the player
types. The format is given below. Finally, the player will receive a message on changed
players (see Sec. 4.6).

* server_param
* player_param
* player_type


""""""""""""""""""""""""""""""""""""""""""""""""""
Player Control
""""""""""""""""""""""""""""""""""""""""""""""""""
+-------------------------------------------------------------------------+--------------------------+
|From player to server                                                    |Only once per cycle       |
+=========================================================================+==========================+
| | (catch *Direction*)                                                   | Yes                      |
| |     *Direction* ::= ``minmoment`` ~ ``maxmoment`` degrees             |                          |
+-------------------------------------------------------------------------+--------------------------+
| | (change_view *Width* *Quality*)                                       | No                       |
| |     *Width* ::= narrow \| normal \| wide                              |                          |
| |     *Quality* ::= high \| low                                         |                          |
+-------------------------------------------------------------------------+--------------------------+
| | (dash *Power* [*Direction*])                                          | Yes                      |
| |     *Power* ::= ``min_dash_power`` ~ ``max_dash_power``               |                          |
| |     *Direction* ::= ``min_dash_angle`` ~ ``max_dash_angle`` degrees   |                          |
| | Note: backward dash (negative power) consumes double stamina.         |                          |
+-------------------------------------------------------------------------+--------------------------+
| | (kick *Power* *Direction*)                                            | Yes                      |
| |     *Power* ::= ``minpower`` ~ ``maxpower``                           |                          |
| |     *Direction* ::= ``minmoment`` ~ ``maxmoment`` degrees             |                          |
+-------------------------------------------------------------------------+--------------------------+
| | (move *X* *Y*)                                                        | Yes                      |
| |     *X* ::= real number                                               |                          |
| |     *Y* ::= real number                                               |                          |
+-------------------------------------------------------------------------+--------------------------+
| | (say "*Message*")                                                     | No                       |
| |     *Message* ::= \[-0-9a-zA-Z ().+\*/?<>_\]\*                        |                          |
+-------------------------------------------------------------------------+--------------------------+
| | (sense_body)                                                          | No                       |
| | The server returns sense_body message to the player immediately.      |                          |
+-------------------------------------------------------------------------+--------------------------+



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Player Sensor Protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



--------------------------------------------------
Sensor Models
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Aural Sensor Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

""""""""""""""""""""""""""""""""""""""""""""""""""
Capacity of the Aural Sensor
""""""""""""""""""""""""""""""""""""""""""""""""""

""""""""""""""""""""""""""""""""""""""""""""""""""
Range of Communication
""""""""""""""""""""""""""""""""""""""""""""""""""

""""""""""""""""""""""""""""""""""""""""""""""""""
Aural Sensor Example
""""""""""""""""""""""""""""""""""""""""""""""""""

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Vision Sensor Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

""""""""""""""""""""""""""""""""""""""""""""""""""
Range of View
""""""""""""""""""""""""""""""""""""""""""""""""""

""""""""""""""""""""""""""""""""""""""""""""""""""
Visual Sensor Noise Model
""""""""""""""""""""""""""""""""""""""""""""""""""

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Body Sensor Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



--------------------------------------------------
Movement Models
--------------------------------------------------

In each simulation step, movement of each object is calculated as following manner:

.. math::
  :label: eq:u-t

   (u_x^{t+1},u_y^{t+1}) &= (v_x^t,v_y^t)+(a_x^t,a_y^t) : accelerate \\
   (p_x^{t+1},p_y^{t+1}) &= (p_x^t,p_y^t)+(u_x^{t+1},u_y^{t+1}) : move \\
   (v_x^{t+1},v_y^{t+1}) &= decay \times (u_x^{t+1},u_y^{t+1}) : decay speed \\
   (a_x^{t+1},a_y^{t+1}) &= (0,0) : reset acceleration

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
of a ``kick`` command.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Movement Noise Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Collision Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


--------------------------------------------------
Action Models
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Catch Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Dash Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



.. _sec-kickmodel:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Kick Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Move Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Say Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Tackle Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Turn Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
TurnNeck Model
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

--------------------------------------------------
Heterogeneous Players
--------------------------------------------------

--------------------------------------------------
Referee Model
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Play Modes and referee messages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

--------------------------------------------------
The Soccer Simulation
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Description of the simulation algorithm
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



--------------------------------------------------
Using Soccerserver
--------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The Soccerserver Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
