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

rcssserver sends dispinfo\_t structs to the soccer monitor.
dispinfo\_t contains a union with three different types of information:

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
(See :ref:`sec-settingsvariables` Parameters and Configurations)

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

-------------------------------------------------
How to record and playback a game
-------------------------------------------------


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Version 1 Protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Logfiles of version 1 (server versions up to 4.16) are a stream of
consecutive dispinfo_t chunks.
Due to the structure of dispinfo_t as a union, a lot of bytes have been
wasted leading to impractical logfile sizes.
This lead to the introduction of a new logfile format 2.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Version 2 Protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Version 3 Protocol
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The version 3 protocol contains player parameter information for
heterogenous players and optimizes space. The format is as follows:

* head of the file: Just like version 2, the file starts with the magic
  characters 'ULG'.
* char version : version of the logfile format, i.e. 3
* body: The rest of the file contains shorts that specify which data
  structures will follow.
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
Settings and Parameters
-------------------------------------------------
