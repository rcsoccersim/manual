.. -*- coding: utf-8; -*-

*************************************************
Soccer Client
*************************************************


=================================================
Protocols
=================================================

This section provides a brief overview of the protocol between the Soccer Client and the
Soccer Server. More details on these protocols can be found in the Soccer Server section.
Note that the init and reconnect commands should be send to the player’s UDP port
(default: 6000) of the Soccer Server machine, and after the response they should be sent
to the port assigned to your player by the server, in a valid format. The server sends
the init response from this port (refer to section 1.2.1) . All the commands sent to or
received from the server are strings of common character and are in a pair of parenthesis.


-------------------------------------------------
Initialization and Reconnection
-------------------------------------------------
Every player wanting to connect to the server should introduce himself. This is like a
handshake and is done only at the beginning and optionally in the half time when you
want to reconnect.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Initialization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Your client should send an init command to the server in the following format

  (init *TeamName* [(version *VerNum*)] [(goalie)])

The goalie should include the ”(goalie)” in the init command to be allowed by the
server to catch the ball or do another special goalie action. Note there can only be one
or no goalie in each team. (You are not obliged to use a goalie)
The Server welcomes you with a response to your init message in the following format

  (init *Side* *UniformNumber* *PlayMode*)

Or by an error message (if there is an error, i.e. you have initiated more than two
team, more than 11 players in a team or more than one goalie in a team)

  (error no_more_team_or_player_or_goalie)

Side is your team’s side of play, a character, l(left) or r(right). UniformNumber is the
player’s uniform number (the players of each team are known by their uniform number).
PlayMode is a string representing one of the valid play modes.

If you connect to server with versions 7.00 or higher you will receive additional server
parameters, player parameters and player types information ( the last two are related
to the hetero players feature ). For the exact format refer to the appendix.

  (server_param *Parameters* . . . )

  (player_param *Parameters* . . . )

  (player_type *id* *Parameters* . . . )

Here the hand shaking is finished and your client is known as a valid player.



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Reconnection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Reconnection is useful for changing the client program of a player without restarting the
game. It can only be done in a non-PlayOn playing mode (e.g. in the half time).
For reconnection you should send a reconnect command in the following format

  (reconnect *TeamName* *UniformNumber*)

And you will receive a response in the following format

  (reconnect *Side* *PlayMode*)

Or one of the following errors

  (can’t reconnect)

if the game is in the PlayOn mode.

  (error reconnect)

when no client reconnected due to an error. You may also receive the following error
if the team name is invalid **(error no_more_team_or_player_or_goalie)**
Here again if you are connecting to the server with version 7.00 or higher you will
receive additional server parameters, player parameters and player types information.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Disconnection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Before you disconnect, you can send a bye command to the server. This command will
remove the player from the field.

  (bye)

There will be no answers from the server.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Version Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Due to the progressive development of the Soccer Server, new features have been added
every year and this resulted in changes and improvements in the protocols to support
these features. In order to keep compatibility with the older clients and making it easier
to work with (specially for researchers), a system has been implemented for the Protocols
Version Control. Every client should tell the server the version of its communication
protocol in the **init** command so that the server would be able to send the messages in
the proper format.
But note that although the communication protocol remains unchanged, the judgment
and the simulation rules may change and this will affect the whole game.


--------------------------------------------------
Control Commands
--------------------------------------------------
During the game each player can send action commands. The server executes the commands at the end of the cycle and simulates the next cycle regarding the received commands and the previous cycles data.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Body Commands
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
All the playing and movement behaviors of the player are consisted from a few commands
known as body commands that are presented briefly below.
The results of these commands are a little complicated and depend on many simulation
factors. For the details of the execution of each command refer to the Soccer Server
Section.

  (turn *Moment*)

The Moment is in degrees from −180 to 180. This command will turn the
player’s body direction Moment degrees relative to the current direction.

  (dash *Power*)

This command accelerates the player in the direction of its body (not direction of the current speed). The Power is between **minpower** (used value:
−100) and **maxpower** (used value: 100).

  (kick *Power Direction*)

Accelerates the ball with the given Power in the given Direction. The direction is relative to the the Direction of the body of the player and the power
is again between **minpower** and **maxparam**.

  (catch *Direction*)

Goalie special command: Tries to catch the ball in the given Direction relative
to its body direction. If the catch is successful the ball will be in the goalie’s
hand until kicked away.

  (move *X* *Y*)

This command can be executed only before kick off and after a goal. It
moves the player to the exact position of X (between −54 and 54) and Y
(between −32 and 32) in one simulation cycle. This is useful for before kick
off arrangements.

Note that in each simulation cycle, only one of the above five commands can be
executed (i.e. if the client sends more than one command in a single cycle, one of them
will be executed randomly, usually the one received first)

  (turn_neck *Angle*)

This command can be sent (and will be executed) each cycle independently, along with
other action commands. The neck will rotate with the given Angle relative to previous
Angle. Note that the resulting neck angle will be between **minneckang** (default: −90)
and **maxneckang** (default: 90) relative to the player’s body direction.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Communication Commands
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The only way of communication between two players is broadcasting of messages through
the **say** command and hearing through the **hear** sensor.

  (say *Message*)

This command broadcasts the Message through the field, and any player near enough
(specified with **audio_cut_dist**, default: 50.0 meters), with enough hearing capacity will
hear the Message. The message is a string of valid characters.

  (ok say)

Command succeeded.
In case of error there will be the following response from the Server

  (**error illegal_command_form**)


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Misc. Commands
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Other commands are usually of two forms:

* Data Request Commands



  (sense_body)

  Requests the server to send sense body information. Note the server sends sense
  body information every cycle if you connect with version 6.00 or higher.

  (score)

  Request the server to send score information. The server’s reply will be in this
  format

  (score *Time* *OurScore* *OpponentScore*)


* Mode Change Commands

  (change_view *Width* *Quality*)

  Changes the view parameters of the player. Width is one of narrow, normal or
  wide and Quality is one of high or low. The amount and detail of the information
  returned by the visual sensor depends on the width of the view and the quality. But
  note that the frequency of sending information also depends on these parameters
  (e.g. if you change the quality from high to low, the frequency doubles, and the
  time between two see sensors will be cut to half).

------------------------------------
Sensor Information
------------------------------------
Sensor information are the messages that are sent to all players regularly (e.g. each cycle
or each one and half a cycle). There is no need to send any message to the server to get
these information.
All the returned information of the sensors have a time label, indication the cycle
number of the game when the data have been sent (indicated by Time). This time is
very useful.

^^^^^^^^^^^^^^^^^^
Visual Sensor
^^^^^^^^^^^^^^^^^^
Visual Sensor is the most important sensor, and is a little bit complicated. This sensor
returns information about the objects that can be seen from the player’s view (i.e.
objects that are in the view angle and not very far).

The main format of the information is

   (see *Time* *ObjInfo* *ObjInfo* . . . )

The ObjInfos are of the format below

   (*ObjName* *Distance* *Direction* [*DistChange* *DirChange* [*BodyFacingDir* *HeadFacingDir*]])

or

   (*ObjName* *Direction*)

Note that the amount of information returned for each object depends
on its distance.
The more distant the object is the less information you get.
For more detailed information regarding ObjInfo refer to Appendix.

ObjName is in one of the following formats:

  (p [*TeamName* [*Unum*]])

  \(b\)

  (f *FlagInfo*)

  (g *Side*)

**p** stands for player, **b** stands for ball, **f** stands for flag and **g** stands for goal.
Side is one of **l** for left or **r** for right. For more information
on FlagInfo refer to Appendix.

^^^^^^^^^^^^^^^^^^
Audio Sensor
^^^^^^^^^^^^^^^^^^

Audio sensor returns the messages that can be heard through the field. They may come
from the online coach, referee, or other players.

The format is as follows:

  (hear *Time* *Sender* *Message*)

Sender is one of the followings:
 - **self**: when the sender is yourself.
 - **referee**: when the sender is the referee of the game.
 - **online_coach_l** or **online_coach_r**
 - *Direction*: when the sender is a player other than yourself the relative direction of the sender is returned instead.

^^^^^^^^^^^^^^^^^^
Body Sensor
^^^^^^^^^^^^^^^^^^

Body sensor returns all the states of the player such as remaining stamina, view mode
and the speed of the player at the beginning of each cycle:


  (sense_body *Time* (view_mode { high | low } { narrow | normal | wide })
  (stamina *Stamina* *Effort*) (speed *Speed* *Angle*) (head_angle *Angle*)
  (kick *Count*) (dash *Count*) (turn *Count*) (say *Count*)
  (turn_neck *Count*) (catch *Count*) (move *Count*) (change_view *Count*))

The last eight parameters are counters of the received commands. Use the counters
to keep track of lost or delayed messages.

======================
How to Create Clients
======================

This section provides a brief description to write a first-step program of soccer client.

----------------------
Sample Client
----------------------

The Soccer Server distribution includes a very simple program for soccer clients, called
sampleclient. It is under the ”sampleclient” directory of the distribution, and is
automatically compiled when you make the Soccer Server.
The sampleclient is not a stand-alone client: It is a simple ‘pipe’ that redirects
commands from its standard input to the server, and information from the server to its
standard output. Therefore, nothing happens when users invoke the sampleclient. The
users must type-in commands from keyboards, and read the sensor information displayed
on the terminal. (Actually it is impossible to read sensor information, because the server
sends about 17 sensor informations (see information and sense_body information) per
second.)
The sampleclient is useful to understand what clients should do, and what the clients
will receive from the server.


**How to Use** sampleclient
Here is a typical usage of the sampleclient.

  #. Invoke client under sampleclient directory of the Soccer Server.

      ::

      % ./client SERVERHOST

      Here, SERVERHOST is a hostname on which Soccer Server is running.
      Then the program awaits user input.
      If the Soccer Server uses an unusual port, for example 6005, instead of the standard
      port (6000), the users should use the following form.
      ::

      % ./client SERVERHOST 6005

  #. Type in init command from the keyboard.


      (init MYTEAMNAME (version 7))

      Here MYTEAMNAME is a team name the users want to use.
      Then a player appears on the field. In the same time, the program starts to
      output the sensor information sent from the server to the terminal. Here is a
      typical output
      ::

        send 6000 : (init foo (version 7))
        recv 1567 : (init r 1 before_kick_off)
        recv 1567 : (server_param 14.02 5 0.3 0.4 0.1 60 1 1 4000 45 0 0.3 0.5 ...
        recv 1567 : (player_param 7 3 3 0 0.2 -100 0 0.2 25 0 0.002 -100 0 0.2 ...
        recv 1567 : (player_type 0 1 45 0.4 5 0.006 0.3 0.7 0 0 1 0.6)
        recv 1567 : (player_type 1 1.16432 28.5679 0.533438 8.33595 0.00733326 ...
        recv 1567 : (player_type 2 1.19861 25.1387 0.437196 5.92991 0.00717675 ...
        recv 1567 : (player_type 3 1.04904 40.0956 0.436023 5.90057 0.00631769 ...
        recv 1567 : (player_type 4 1.1723 27.7704 0.568306 9.20764 0.00746072 ...
        recv 1567 : (player_type 5 1.12561 32.4392 0.402203 5.05509 0.00621539 ...
        recv 1567 : (player_type 6 1.02919 42.0812 0.581564 9.53909 0.00688457 ...
        recv 1567 : (sense_body 0 (view_mode high normal) (stamina 4000 1) ...
        recv 1567 : (see 0 ((g r) 61.6 37) ((f r t) 49.4 3) ((f p r t) 37 27) ...
        recv 1567 : (sense_body 0 (view_mode high normal) (stamina 4000 1) ...

      The first line, “send 6000 : (init foo (version 7))”, is a report what
      the client sends to the server. The second line,”recv 1567 : (init r 1
      before_kick_off) is a report of the first response from the server. Here, the
      server tells the client that the assigned player is the right side team (r), its uniform number is 1, and the current playmode is before_kick_off. The next 9
      lines are server_param and player_param, which tells various parameters used in
      the simulation. Finally, the server starts to send the normal sensor informations,
      sense_body and see. Because the server sends these sensor information every
      100ms or 150ms, the client continues to output the information endlessly.

  #. Type in move command to place the player to the initial position. The player
      appears on a bench outside of the field. Users need to move it to its initial position
      by move command like:

        (move -10 10)

      Then the player moves to the point (-10,10).
      Because, as mentioned before, the client program outputs sensor information
      endlessly, users can not see strings they type in. So, they must type-in commands
      blindly. [#f1]_


  #. Click ‘Kick-Off’ button on the Soccer Server. Then the game starts. The users
      can see that the time data in each sensor information (the first number of see and
      sense_body information) are increasing.

  #. After then, users can use any normal command, turn, dash, kick and so on. For
      example, users can turn the player to the right by typing:

        (turn 90)

      The player can dash forward with full power by typing:

        (dash 100)

      When the player is near enough to the ball, it can kick the ball to the left with
      power 50 by:

        (kick 50 -90)

      Note again that because of endless sensor output, users must type-in these commands blindly.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Overall Structure of Sample Client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The structure of the sampleclient is simple. The brief process the client does is as
follows:

  #. Open a UDP socket and connect to the server port. (init_connection())
  #. Enter the read-write loop (message_loop), in which the following two processes are executed in parallel.

    * read user’s input from the standard input (usually a keyboard) and send it
      to the server (send_message()).

    * receive the sensor information from the server (receive_message()) and output it to the standard output (usually a console).

In order to realize the parallel execution, sampleclient uses the select() function.
The function enables to wait for multiple input from sockets and streams in a single
process. When select() is called, it waits until one of the sockets and streams gets
input data, and tells which sockets or streams got the data. For more details of the
usage of select(), please refer to the man page or manual documents.

An important tip in the sampleclient is that the client must change the server’s port
number when it receives sensor informations from the server. This is because the server
assign a new port to a client when it receives an init command. This is done by the
following statement in ”client.c” (around line 147)

    ::

        printf( "recv %d : ", ntohs(serv_addr.sin_port));
        sock->serv_addr.sin_port = serv_addr.sin_port ;
        buf[n] = ’\0’


----------------
Simple Clients
----------------

In order to develop complete soccer clients, what users must do is to write code of a
‘brain’ part, which performs the same thing as users do with the sampleclient described
in the previous section. In other words, users must write a code to generate command
strings to send to the server based on received sensor information.

Of course it is not a simple task (so that many researchers tackle RoboCup as a
research issue), and there are various ways to implement it. Simply saying, in order to
develop player clients, users need to realize the following functions

**[Sensing]** To analyze sensor information: As shown in the previous section, the server
sends various sensor information in S-expressions. Therefore, a client needs to
parse the S-expressions. Then, the client must analyze the information to get a
certain internal representation. For example, the client needs to analyze a visual
information to estimate player’s location and field status, because the visual information only include relative locations of landmarks and moving objects on the
field.

**[Action Interval]** To control interval of sending commands: Because the server accepts
a body command (turn, dash and kick) per 100ms, the client needs to wait appropriate interval before sending a command.

**[Parallelism]** To execute sensor and action processes in parallel: Because the Soccer
Server processes sensor information and command asynchronously, clients need
to execute a sensor process, which deals with sensor information, and an action
process, which controls to send commands, in parallel.

**[Planning]** To make a plan of play: Using sensor information, the client needs to generate appropriate command sequences of play. Of course, this is the final goal of developing soccer clients!!

Here are two simple examples of stand-alone players, sclient1 and sclient2, which
just chase the ball and kick it to the opponent goal. The sources are available from

  ftp://ci.etl.go.jp/pub/soccer/client/noda-client-2.0.tar.gz

In the examples, the functions listed above are realized as follows:


  * For Sensing function, both examples use common facilities of class BasePlayer, class FieldState, and estimatePos functions. By these facilities, the example programs do:
      * receive data from a socket connected with the server,
      * parse the data as S-expression,
      * interpret the expression into internal data format (class SensorInfo),
      * and in the case the received data is visual sensor information, estimate player’s and other object’s positions.

    For more detail, please read the source code.

  * For Action Interval and Parallelism functions, the two examples use different methods. The first example, sclient1 uses timeout of select() function. The second
    one, sclient2 uses the multi-thread (pthread) facility. These are described below.

  * For Planning function, both examples have very simple planners as follows:
      * If the player does not see the ball in recent 10 steps, or if the player can not
        estimate its position in recent 10 steps, it looks around.
      * If the ball is in kickable area, it kicks the ball to the opponent goal.
      * Otherwise, the player rushes to the ball (turns to the ball and dashes).

sclient1

The sclient1 uses the timeout facility of select() function to realize Action Interval
and Parallelism.

The key part of the program is in MyPlayer::run(). Here is the part of the source
code

    .. code-block:: c

      //----------------------------------------
      // enter main loop

      SocketReadSelector selector ;

      TimeVal nexttic ; // indicate the timestamp for next command send
      nexttic.update() ; // set nexttic to the current time.

      while(True) {
          //-------------------------------------------------
          // setup selector

          selector.clear() ;
          selector.set(socket) ;

          //-------------------------------------------------
          // wait socket input or timeout (100ms) ;

          Int r = selector.selectUntil(nexttic) ;

          if(r == 0) { // in the of timeout. (no sensor input)
              doAction() ; // enter action part
              nexttic += TimeVal(0,100,0) ; // increase nexttimetic 100ms
          } else { // got some input
              doSensing() ; // enter sensor part
          }
      }

Here, class SocketReadSelector is a class to abstract facilities of select() and is
defined in ”itk/Socket.h”. In the line “Int r = selector.selectUntil(nexttic)
;”, the program awaits the socket input or timeout indicated by nexttic, which holds
the timestamp of the next tic (simulation step). The function returns 0 if timeout, or
the number of receiving sockets. In the case of timeout, the program calls doAction() in
which a command is generated and sent to the server, or otherwise, it calls doSensing()
in which a sensor information is processed.

sclient2

The sclient2 uses the POSIX thread (pthread) facilities to realize Action Interval and
Parallelism.

The key part of the program is also in MyPlayer::run(). Here is the part of the
source code:

    .. code-block:: c

      //----------------------------------------
      // fork sensor thread

      forkSensor() ;

      //----------------------------------------
      // main loop

      while(True) {
          if (!isBallSeenRecently(10)) {
              //------------------------------
              // if ball is not seen recently
              // look around by (turn 60)
              for(UInt i = 0 ; i < 6 ; i++) {
              turn(60) ;
              }
          } else if (kickable()) {
              ...
          }
      }

The statement “forkSensor() ;” invokes a new thread for receiving and analyzing the
sensor information. (The behavior of the sensor thread are defined in ”SimpleClient.*”
and ”ThreadedClient.*”.) Then the main thread enters the main loop in which action
sequences of “chasing the ball and kick to the goal” are generated. Because Sensing
function is handled in the sensor thread in parallel, the main thread needs not take care
of the sensor input.

In order to keep action interval to be 100ms, the sclient2 waits for the next
simulation step by the function ThreadedPlayer::sendCommandPre() defined in
”ThreadedPlayer.cc” as follows:

    .. code-block:: c

        Bool ThreadedPlayer::sendCommandPre(Bool bodyp) {
            cvSend.lock() ;

            if(bodyp) {
                while(nextSendBodyTime.isFuture())
                    cvSend.waitUntil(nextSendBodyTime) ;
            }
            while(nextSendTime.isFuture()) {
                cvSend.waitUntil(nextSendTime) ;
            }
            return True ;
        } ;

In this function, MutexCondVar cvSend provide a similar timeout facility of select()
function used in sclient1 described above. (MutexCondVar is a combination of
condition variable (pthread_cond_t) and mutex (pthread_mutex\_ ), and is defined in
”itk/MutexCondVar.h”.) Because the function is called just before the player sends a
command to the server, and nextSendBodyTime is controlled to indicate the timestamp
of the next simulation step, the thread waits to send a command in the next tic.

--------------------------
Tips
--------------------------
Here we collect tips to develop soccer client programs.

  * Debugging is the main problem in developing your own team. So try to find easy
    debuging methods.

  * A nice and simple way to see your program’s variables in a condition is to use
    an **abort()** command or some **asserts** to force the program to core-dump; And
    debug the core using gbd.

  * Log every message received from the server and sent to the server. It is very useful
    for debugging.

  * Using ready to use libraries for socket and parsing problems is useful if you are a
    beginner.

  * Remember to pass the version number to the server in the init command. Although
    it is optional, the default is 3.00 which usually is not desired.

  * Even if the catch probability is 1.00 your catch command may be unsuccessful
    because of errors in returned sensors about the positions.

  * The first serious problem you may encounter is the timing problem. There are
    many methods to synchronize your client’s time with server. One simple methods
    is to use received sense body information.

  * Beware of slow networks. If your timing is not very powerful your client’s will
    behave abnormaly in a crowded or slow network or if they are out of process
    resources (e.g. you run many clients on one slow machine). In this case they may
    see older positions and will try to act in these positions and this will result in
    confusion (e.g. they will turn around themselves)

  * The main usage of flags are for the player to find the position of himself in the field.
    Your very first clients may ignore flags and play with relative system of positions.
    But you may need a positioning module in the near future. There are many of the
    in the ready to use libraries.

  * The program should check the end of buffer in analyzing sensor information. The
    sensor information uses S-expressions. But the expression may not be completed
    when the sensor data is longer than the buffer, so that some closing parentheses are
    lost. In this case, the program may core-dump if it parses the expression naively.

----

.. [#f1] Users can redirect the output to any file or program. For example, you can redirect it to /dev/null
         to discard the information by invoking “% client SERVERHOST > /dev/null”. Then, the users can
         see the string they type-in.
