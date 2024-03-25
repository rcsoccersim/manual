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

If more messages arrive at the same time than the player can hear, the messages
actually heard are chosen randomly.
This rule does not include messages from the referee, or messages from oneself.
From rcssserver 8.04, players can send ``attentionto`` commands to focus their attention on a particular player.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Focus
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the player focuses on player A from team T (AKA pTA), the player will
hear one message selected randomly from the say messages issued by pTA
in the previous cycle. If pTA did not issue any say commands, the player
will hear one message selected randomly from all the say messages issued
by players in team T. At the same time, the player will hear one message
selected randomly from the other team. If attentionto is off (default)
the player will hear one message from each team selected randomly from
the messages available.

The way to focus is using ``attentionto`` commands.
See :ref:`sec-attentiontomodel` in detail.

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
