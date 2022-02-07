.. -*- coding: utf-8; -*-

.. _cha-soccermonitor:

*************************************************
Soccer Monitor
*************************************************

=================================================
Introduction
=================================================

Soccer monitor provides a visual interface.
Using the monitor we can watch a game vividly and control the proceeding of
the game.
.. By cooperating with logplayer, soccermonitor can replay games, so that it
.. becomes very convenient to analyze and debug clients.

=================================================
Getting started
=================================================

To connect the soccer monitor with the soccer server, you can use the command
following::

  $ rcssmonitor

By specifying the options, you can modify the parameters of soccer monitor
instead of modifying monitor configuration file.
You can find available options by::

  $ rcssmonitor --help

If you use script **rcsoccersim** to start the server, a monitor will be
automatically started and connected with the server::


  $ rcsoccersim


**TODO: [12.0.0 pre-20071217] server::max_monitor**

=================================================
Communication from Server to Monitor
=================================================

Soccer monitor and rcssserver are connected via UDP/IP on port 6000 (default).
When the server is connected with the monitor, it will send information to
the monitor every cycle.
rcssserver-15 provides four different formats (version 1 ~ 4).
The server will decide which format is used according to the initial command
sent by the monitor (see :ref:`sec-commandsfrommonitor`).
The detailed data structure information can be found in
appendix :ref:`sec-appendixmonitorstructs`.

-------------------------------------------------
Version 1
-------------------------------------------------

rcssserver sends dispinfo_t structs to the soccer monitor.
dispinfo_t contains a union with three different types of information:

* showinfo_t: information needed to draw the scene
* msginfo_t : contains the messages from the players and the referee shown
  in the bottom windows
* drawinfo_t: information for monitor to draw circles, lines or points
  (not used by the server)

The size of dispinfo_t is determined by its largest subpart (msg) and is
2052 bytes (the union causes some extra network load and may be changed
in future versions).
In order to keep compatibility between different platforms, values in
dispinfo_t are represented by network byte order.
Which information is included is determined by the mode information.
NO_INFO indicates no valid info contained (never sent by the server),
BLANK_MODE tells the monitor to show a blank screen (used by logplayer)
(see rcssserver-\*/src/types.h)::

  NO_INFO    0
  SHOW_MODE  1
  MSG_MODE   2
  DRAW_MODE  3
  BLANK_MODE 4


Following is a description of these structs and the ones contained:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Showinfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A showinfo_t struct is passed every cycle (100 ms) to the monitor and
contains the state and positions of players and the ball::

  typedef struct {
     char   pmode ;
     team_t team[2] ;
     pos_t  pos[MAX_PLAYER * 2 + 1] ;
     short  time ;
  } showinfo_t ;


* pmode: currently active playmode of the game (see rcssserver-\*/src/types.h)::

    PM_Null,
    PM_BeforeKickOff,
    PM_TimeOver,
    PM_PlayOn,
    PM_KickOff_Left,
    PM_KickOff_Right,
    PM_KickIn_Left,
    PM_KickIn_Right,
    PM_FreeKick_Left,
    PM_FreeKick_Right,
    PM_CornerKick_Left,
    PM_CornerKick_Right,
    PM_GoalKick_Left,
    PM_GoalKick_Right,
    PM_AfterGoal_Left,
    PM_AfterGoal_Right,
    PM_Drop_Ball,
    PM_OffSide_Left,
    PM_OffSide_Right,
    PM_PK_Left,
    PM_PK_Right,
    PM_FirstHalfOver,
    PM_Pause,
    PM_Human,
    PM_Foul_Charge_Left,
    PM_Foul_Charge_Right,
    PM_Foul_Push_Left,
    PM_Foul_Push_Right,
    PM_Foul_MultipleAttacker_Left,
    PM_Foul_MultipleAttacker_Right,
    PM_Foul_BallOut_Left,
    PM_Foul_BallOut_Right,
    PM_Back_Pass_Left,
    PM_Back_Pass_Right,
    PM_Free_Kick_Fault_Left,
    PM_Free_Kick_Fault_Right,
    PM_CatchFault_Left,
    PM_CatchFault_Right,
    PM_IndFreeKick_Left,
    PM_IndFreeKick_Right,
    PM_PenaltySetup_Left,
    PM_PenaltySetup_Right,
    PM_PenaltyReady_Left,
    PM_PenaltyReady_Right,
    PM_PenaltyTaken_Left,
    PM_PenaltyTaken_Right,
    PM_PenaltyMiss_Left,
    PM_PenaltyMiss_Right,
    PM_PenaltyScore_Left,
    PM_PenaltyScore_Right,
	PM_Illegal_Defense_Left,
    PM_Illegal_Defense_Right,
    PM_MAX

* team: information about the teams. Index 0 is for team playing
  from left to right::

    typedef struct {
      char  name[16];  /* name of the team */
      short score;     /* current score of the team */
    } team_t;

* pos: position information of ball and players. Index 0 represents the ball,
  indices 1 to 11 is for team[0] (left to right) and 12 to 22 for team[1]::

    typedef struct {
      short enable;
      short side;
      short unum;
      short angle;
      short x;
      short y;
    } pos_t;

* time: current game time.


Values of the elements can be

* enable: state of the object.
  Players not on the field (and the ball) have state DISABLE.
  The other bits of enable allow monitors to draw the state and action of
  a player more detailed (see rcssserver-\*/src/types.h)::

    DISABLE         0x00000000
    STAND           0x00000001
    KICK            0x00000002
    KICK_FAULT      0x00000004
    GOALIE          0x00000008
    CATCH           0x00000010
    CATCH_FAULT     0x00000020
    BALL_TO_PLAYER  0x00000040
    PLAYER_TO_BALL  0x00000080
    DISCARD         0x00000100
    LOST            0x00000200
    BALL_COLLIDE    0x00000400
    PLAYER_COLLIDE  0x00000800
    TACKLE          0x00001000
    TACKLE_FAULT    0x00002000
    BACK_PASS       0x00004000
    FREE_KICK_FAULT 0x00008000
    POST_COLLIDE    0x00010000
    FOUL_CHARGED    0x00020000
    YELLOW_CARD     0x00040000
    RED_CARD        0x00080000
	ILLEGAL_DEFENSE 0x00100000

* side: side the player is playing on. LEFT means from left to right,
  NEUTRAL is the ball (rcssserver-\*/src/types.h)::

    LEFT     1
    NEUTRAL  0
    RIGHT   -1

* unum: uniform number of a player ranging from 1 to 11
* angle: angle the agent is facing ranging from -180 to 180 degrees,
  where -180 is view to the left side of the screen, -90 to the top,
  0 to the right and 90 to the bottom.
* x, y: position of the ball or player on the screen. (0, 0) is the midpoint
  of the field, x increases to the right, y to the bottom of the screen.
  Values are multiplied by SHOWINFO_SCALE (16) to reduce aliasing, so field
  size is PITCH_LENGTH * SHOWINFO_SCALE in x direction
  and PITCH_WIDTH * SHOWINFO_SCALE in y direction.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Messageinfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Information containing the messages of players and the referee::

  typedef struct {
    short board ;
    char  message[2048] ;
  } msginfo_t;

* board: indicates the type of message.
  A message with type MSG_BOARD is a message of the referee, LOG_BOARD are
  messages from and to the players.
  (rcssserver-\*/param.h)::

    MSG_BOARD 1
    LOG_BOARD 2

* message: zero terminated string containing the message.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Drawinfo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Allows to specify information for the monitor to draw circles, lines or points.


-------------------------------------------------
Version 2
-------------------------------------------------

rcssserver sends dispinfo_t2 structs to the soccer monitor instead of
dispinfo_t structs which is used in version 1.
dispinfo_t2 contains a union with five different types of information
(the data structures are printed in appendix :ref"`sec-appendixmonitorstructs`:

* showinfo_t2: information needed to draw the scene.
  It includes all information on coordinates and speed of players and
  the ball, teamnames, scores, etc.
* msginfo_t : contains the messages from the players and the referee.
  It also contains information on team's images and information on
  player exchanges.

 - team graphic: The team graphic format requires a 256x64 image to
   be broken up into 8x8 tiles and has the form::

		  (team_graphic_{l|r} (<X> <Y> "<XPM line>" ... "<XPM line>"))

   Where X and Y are the co-ordinates of the 8x8 tile in the complete 256x64
   image, starting at 0 and ranging upto 31 and 7 respectively.
   Each XPM line is a line from the 8x8 xpm tile.
 - substitutions: substitutions are now explicitly recorded in the
   message board in the form::

		  (change_player_type {l|r} <unum> <player_type>)

* player_type_t: information describing different player's abilities and tradeoffs
* server_params_t: parameters and configurations of soccerserver
* player_params_t: parameters of players

Which information is contained in the union is determined by the mode field.
NO_INFO indicates no valid info contained (never sent by the server).
BLANK_MODE tells the monitor to show a blank screen::

  NO_INFO     0
  SHOW_MODE   1
  MSG_MODE    2
  BLANK_MODE  4
  PT_MODE     7
  PARAM_MODE  8
  PPARAM_MODE 9

-------------------------------------------------
Version 3
-------------------------------------------------


-------------------------------------------------
Version 4
-------------------------------------------------


.. _sec-commandsfrommonitor:

=================================================
Communication from Monitor to Server
=================================================

The monitor can send to the server the following commands
(in all commands, *<variable>* has to be replaced with proper values)::

  (dispinit) | (dispinit version <version>)

sent to the server as first message to register as monitor (opposed to
a player, that connects on port 6000 as well) .
"(dispinit)" is for information version 1, while "(dispinit version 2)" is
for version 2.
You can change the version by setting the according monitor parameter.
(See :ref:`sec-settingsvariables`)

::

  (dispstart)

sent to start (kick off) a game, start the second half or extended time.
Ignored, when the game is already running.

::

  (dispfoul <x> <y> <side>)

sent to indicate a foul situation. x and y are the coordinates of the foul,
side is LEFT (1) for a free kick for the left team, NEUTRAL (0) for
a drop ball and RIGHT (-1) for a free kick for the right team.

::

  (dispdiscard <side> <unum>)

sent to show a player the red card (kick him out). side can be LEFT or RIGHT, unum is the number of the player (1 - 11).

::

  (dispplayer <side> <unum> <posx> <posy> <ang>)

sent to place player at certain position with certain body angle, side
can be LEFT (1) or RIGHT (-1), unum is the number of the player(1 - 11).
Posx and posy indicate the new position of the player, which will
be divided by SHOWINFO_SCALE.
And ang indicate the new angle of a player in degrees.
This command is added in the server 7.02.

::

  (compression <level>)

The server supports compression of communication with its clients and
monitors (since version 8.03). A monitor can send the above compression
request to the server to start compressed communication.
If the server is compiled without ZLib, the server
will respond with ``(warning compression_unsupported)``
else *<level>* is not a number between 0 and 9 inclusive, the server
will respond with ``(error illegal_command_form)``
else the server will respond with ``(ok compression <level>)``
and all subsequent messages to that client will be compressed at that
level, until a new compression command is received.
If a compression level above zero is selected, then the monitor is
expected to compress its commands to the server.
Specifying a level of zero turns off compression completely (default).

**TODO: [12.0.0 pre-20071217] accept some coach commands from monitor**

=================================================
How to record and playback a game
=================================================

To record games, you can call server with the argument:

::

  server::game_logging = true

This parameter can be set in ``server.conf`` file.
The logfile is recorded under **server::game_log_dir** directory.
The default logfile name contains the datetime and the result of
the game.
You can use the fixed file name by using **server::game_log_fixed**
and **server::game_log_fixed_name**.

::

  server::game_log_fixed : true
  server::game_log_fixed_name : 'rcssserver'

To specify the logfile version, you can call server with the argument:

::

  server::game_log_version [1/2/3/4/5]

or set the parameter in server.conf file:

::

  server::game_log_version : 5

You can replay recorded games using logplayer applications.
The latest rcssmonitor (version 16 or later) can work as a logplayer.
To replay logfiles just call rcssmonitor with the logfile name as argument,
and then use the buttons on the window to start, stop, play backward, play stepwise.


.. _sec-version1protocol:

-------------------------------------------------
Version 1 Protocol
-------------------------------------------------

Logfiles of version 1 (server versions up to 4.16) are a stream of
consecutive dispinfo_t chunks.
Due to the structure of dispinfo_t as a union, a lot of bytes have been
wasted leading to impractical logfile sizes.
This lead to the introduction of a new logfile format 2.

-------------------------------------------------
Version 2 Protocol
-------------------------------------------------

Version 2 logfile protocol tries to avoid redundant or unused data for
the price of not having uniform data structs.
The format is as follows:

* head of the file:
  the head of the file is used to autodetect the version of the logfile.
  If there is no head, Unix-version 1 is assumed.
  3 chars 'ULG' : indicating that this is a Unix logfile (to distinguish
  from Windows format)
* char version : version of the logfile format
* body: the rest of the file contains the data in chunks of
  the following format:

 * short mode:
   this is the mode part of the dispinfo_t struct
   (see :ref:`sec-version1protocol` Version 1) SHOW_MODE for showinfo_t
   information MSG_MODE for msginfo_t information

  * If mode is SHOW_MODE, a showinfo_t struct is following.
  * If mode is MSG_MODE, next bytes are
     * short board: containing the board info
     * short length: containing the length of the message (including zero terminator)
     * string msg: length chars containing the message

Other info such as DRAW_MODE and BLANK_MODE are not saved to log files.
There is still room for optimization of space.
The team names could be part of the head of the file and only stored once.
The unum part of a player could be implicitly taken from array indices.

Be aware of, that information chunks in version 2 do not have the same size,
so you can not just seek SIZE bytes back in the stream when playing log files
backward.
You have to read in the whole file at once or (as is done) have at least
to save stream positions of the showinfo_t chunks to be able to play
log files backward.

In order to keep compatibility between different platforms, values are
represented by network byte order.

-------------------------------------------------
Version 3 Protocol
-------------------------------------------------

The version 3 logfile protocol contains player parameter information for
heterogenous players and optimizes space. The format is as follows:

* head of the file: Just like version 2, the file starts with the magic
  characters 'ULG'.
* char version : version of the logfile format, i.e. 3
* body: The rest of the file contains shorts that specify which data structures will follow.
   - If the short is PM_MODE,
      * a char specifying the play mode follows.
        This is only written when the playmode changes.
   - If the short is TEAM_MODE,
      * a team_t struct for the left side and
      * a team_t struct for the right side follow.
        Team data is only written if a new team connects or the score changes.
   - If the short is SHOW_MODE,
      * a short_showinfo_t2 struct specifying ball and player positions and
        states follows.
   - If the short is MSG_MODE,
      * a short specifying the message board,
      * a short specifying the length of the message,
      * a string containing the message will follow.
   - If the short is PARAM_MODE,
      * a server_params_t struct specifying the current server parameters follows.
        This is only written once at the beginning of the logfile.
   - If the short is PPARAM_MODE,
      * a player_params_t struct specifying the current hetro player parameters.
        This is only written once at the beginning of the logfile.
   - If the short is PT_MODE,
      * a player_type_t struct specifying the parameters of a specific player type
        follows.
        This is only written once for each player type at the beginning of the logfile.


Data Conversion:

* Values such as x, y positions are meters multiplied by SHOWINFO_SCALE2.
* Values such as deltax, deltay are meters/cycle multiplied by SHOWINFO_SCALE2.
* Values such as body_angle, head_angle and view_width are in radians
  multiplied by SHOWINFO_SCALE2.
* Other values such as stamina, effort and recovery have also been multiplied
  by SHOWINFO_SCALE2.

-------------------------------------------------
Version 4 Protocol
-------------------------------------------------

The version 4 logfile protocol is a text-based format, that may be readable for humans, adopted in rcssserver version 12 or later.
Each line contains one data in S-expression like sensory messages.
Its grammar is almost the same as monitor protocol version 3.

* head of the file: Just like older versions, the file starts with the magic
  characters 'ULG'.
* char version : version of the logfile format, i.e. 4
* new line
* body: In the rest of the file, one of the following data is recorded on each line:
   - server_param
   - player_param
   - player_type
   - msg
   - playmode
   - team
   - show

``msg`` may contain various string data, such as ``team_graphic``, the result of the game, and so on.

- **TODO: detail for each data type.**
- **TODO: [12.1.0] record the game result as a msg info in the game log**

-------------------------------------------------
Version 5 Protocol
-------------------------------------------------

The version 5 logfile protocol is adopted in rcssserver version 13 or later.
Its grammar is almost the same as the version 4 protocol, except adding stamina_capacity information to each player data.

.. _sec-settingsvariables:

-------------------------------------------------
Settings and Parameters
-------------------------------------------------

rcssmonitor has various modifiable parameters.
You can check available options by calling rcssmonitor with ``--help`` argument:

::

  rcssmonitor --help


Several parameters can be modified from ``View`` menu after invoking rcssmonitor.

Some parameters are recorded in ``~/.rcssmonitor.conf``, and rcssmonitor will reuse them in the next execution.
Of course, you can directly edit this configuration file.



=================================================
Team Graphic
=================================================

**TODO**

=================================================
What’s New
=================================================

16.0:
 * Support illegal defense information.
 * Integrate a log player feature.
 * Implement a time-shift reply feature.
 * Remove a buffering mode.
 * Change the default tool kit to Qt5.
 * Support CMake.

15.0:
 * Support v15 server parameters.

14.1:
 * Support an auto reconnection feature.

14.0:
 * Reimplement using Qt4.
 * Support players' card status.
 * Implement a buffering mode.

13.1:
 * Support a team_graphic message.

13.0:
 * Support the monitor protocl version 4.
 * Support a stamina capacity information.

12.1:
 * Support pointto information.
 * Implement an auto reconnection feature.

12.0:
 * Support the monitor protocl version 3.

11.0.2:
 * Support the penalty kick scores.

11.0:
 * Support 64bits OS.

10.0:
 * Ported to OS X.

9.1:
 * Support a keepaway field.

8.03:

* The server supports compressed communication to monitors as described in section 5.4
* Player substitution information is added to the message log
* Team graphics information is added to the message log

7.07:

* The logplayer did not send server param, player param, and player type
  messages. This has been fixed.

* The monitor would crash on some logfiles because stamina max seemed to be
  set to 0. The monitor will no longer crash this way.


+---------------------------------+----------------------------+---------------------+---------------------------------------+
|| Parameter Name                 || Used Value                || Default            || Explanation                          |
+=================================+============================+=====================+=======================================+
| host                            | localhost                  |Localhost            | hostname of soccerserver              |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| port                            |6000                        |6000                 | port number of soccerserver           |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| version                         | 2                          | 1                   | monitor protocol version              |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| length magnify                  | 6.0                        | 6.0                 | magnification of size of field        |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| goal width                      | 14.02                      | 7.32                | goal width                            |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| print log                       | off                        | On                  | flag for display log of               |
|                                 |                            |                     | communication [on/off]                |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Log line                        | 6                          | 6                   | size of log window                    |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Print mark                      | on                         |On                   | flag for display mark on field        |
|                                 |                            |                     | [on/off]                              |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| mark file name                  | mark.RoboCup.grey.xbm      | Mark.xbm            | mark on field use file name           |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| ball_file_name                  | ball-s.xbm                 | Ball.xbm            | ball use file name                    |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| player_widget_size              | 9.0                        | 1.0                 | size of player widget                 |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| player_widget_font              | 5x8                        | Fixed               | font(uniform number) of player widget |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Uniform_num_pos_x               |2                           | 2                   | position (X) of player uniform number |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Uniform_num_pos_y               | 8                          | 8                   | position (Y) of player uniform number |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| team_l_color                    | Gold                       | Gold                | Team_L color                          |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| team_r_color                    | Red                        | Red                 | Team_R color                          |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| goalie_l_color                  | Green                      | Green               | Team_L Goalie color                   |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| goalie_r_color                  | Purple                     | Purple              | Team_R Goalie color                   |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| neck_l_color                    | Black                      | Black               | Team_L Neck color                     |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| neck_r_color                    | Black                      | Black               | Team_R Neck color                     |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Goalie_neck_l_color             | Black                      | Black               | Team_L Goalie Neck color              |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Goalie_neck_r_color             | Black                      | Black               | Team_R Goalie Neck color              |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| status_font                     | 7x14bold                   | Fixed               | status line font [team name and       |
|                                 |                            |                     | score, time, play_mode]               |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| popup_msg                       | off                        | Off                 | flag for pop up and down “GOAL!!” and |
|                                 |                            |                     | “Offside!” [on/off]                   |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Goal_label_width                | 120                        | 120                 | pop up and down “GOAL!!” label width  |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Goal_label_font                 | -adobe-times               | Fixed               | pop up and down “GOAL!!” label font   |
|                                 | bold-r-*-*-34-*-*-         |                     |                                       |
|                                 | *-*-*-*-*                  |                     |                                       |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Goal_score_width                | 40                         | 40                  | pop up and down “GOAL!!” score width  |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Goal_score_font                 | -adobe-times               | Fixed               | pop up and down “GOAL!!” score font   |
|                                 | bold-r-*-*-25-*-*-         |                     |                                       |
|                                 | *-*-*-*-*                  |                     |                                       |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Offside_label_width             | 120                        | 120                 | pop up and down“Offside!” label width |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| Offside_label_font              | -adobe-times               | Fixed               | pop up and down “Offside!” label font |
|                                 | bold-r-*-*-34-*-*-         |                     |                                       |
|                                 | *-*-*-*-*                  |                     |                                       |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| eval                            | off                        | Off                 | flag for evaluation mode              |
+---------------------------------+----------------------------+---------------------+---------------------------------------+
| redraw_player                   | on                         | Off                 | always redraw player (needed          |
|                                 |                            |                     | for RH 5.2)                           |
+---------------------------------+----------------------------+---------------------+---------------------------------------+


7.05:

* For quite some time, the logplayer has occasionally “skipped” so that certain cycles were never displayed by the logplayer. This seems to be caused
  by the logplayer sending too many UDP packets for the monitor to receive. Therefore, a new parameter has been added to the logplayer ’message delay interval’. After sending that many messages, the logplayer sleeps
  for 1 microsecond, giving the monitor a chance to catch up. This is not a guaranteed to work, but it seems to help significantly. If you still have a problem
  with the logplayer/monitor “skipping”, try reducing message delay interval
  from it’s default value of 10. Setting message delay interval to a negative
  number causes there to be no delay.
* The server used to truncate messages received from the players and coach to
  128 characters before recording them in the logfile. This has been fixed.

7.04:

* If a client connects with version > 7.0, all angles sent out by the server are
  rounded instead of truncated (as they were previously) This makes the error
  from quantization of angles (i.e. conversion of floats to ints) both uniform
  throughout the domain and two sided. This change was also made to all
  values put into the dispinfo t structure for the monitors and logfiles.

7.02:

* A new command has been added to the monitor protocol::

    (dispplayer side unum posx posy ang)

  (contributed by Artur Merke) See :ref:`sec-commandsfrommonitor`.

7.00:

* Included the head angle into the display of the soccermonitor. (source contributed by Ken Nguyen)
* Included visualization effect when the player collided with the ball or the
  player collided with another player. The monitor displays both cases with a
  black circle around the player.
* Introduced new monitor protocol version 2. (See 5.5.2 Version 2 and 5.4
  Commands From Monitor to Server)
* Introduced new logging protocol version 3. (See 5.5.3 Version 3 Protocol)
* Fixed logging so that the last cycle of a game is logged.
