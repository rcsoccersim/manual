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
| |              (focus_point *FocusDist* *FocusDir*))                                           |
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

**TODO: add descriptions about values. arm [8.03], focus [8.04], tackle [8.04], collision [12.0.0_pre-20071217], foul [14.0.0], focus_point [18.0.0] in NEWS**

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
