
.. _sec-playercommandprotocol:

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
| |                                                      | |      *PlayMode* ::= one of play modes         |
| |                                                      | | (error no_more_team_or_player_or_goalie)      |
+--------------------------------------------------------+-------------------------------------------------+
| | (reconnect *TeamName* *Unum*)                        | | (error *Side* *PlayMode*)                     |
| |     TeamName := \[+-_a-zA-Z0-9\]+                    | |          *Side* ::= ``l`` \| ``r``            |
| |                                                      | |          *Unum* ::= 1~11                      |
| |                                                      | |      *PlayMode* ::= one of play modes         |
| |                                                      | | (error reconnect)                             |
+--------------------------------------------------------+-------------------------------------------------+
| (bye)                                                  |                                                 |
+--------------------------------------------------------+-------------------------------------------------+


If your client connects or reconnects successfully with a protocol version >= 7.0, the
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
| | (dash (l *Power* [*Direction*]) (r *Power* [*Direction*]))                 |                          |
| | (dash (r *Power* [*Direction*]) (l *Power* [*Direction*]))                 |                          |
| | (dash (l *Power* [*Direction*]))                                           |                          |
| | (dash (r *Power* [*Direction*]))                                           |                          |
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
| |                      : if client version >= 12                             |                          |
| |     *PowerOrAngle* ::= **-max_back_tackle_power** ~ **max_tackle_power**   |                          |
| |                      : if client version <  12                             |                          |
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

