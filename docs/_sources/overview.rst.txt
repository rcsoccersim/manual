.. -*- coding: utf-8; -*-

**************************************************
Overview
**************************************************

==================================================
Getting Started
==================================================

This section is designed to give you a quick introduction to the main
components of the RoboCup simulator.  For each of these components you
will find detailed information (i.e. configuration parameters,
run-time options, etc.) later on in this manual.

---------------------------------------------------
The Server
---------------------------------------------------

The server is a system that enables various teams to compete in a game
of soccer.  Since the match is carried out in a client-server style,
there are no restrictions as to how teams are built.  The only
requirement is that the tools used to develop a team support
client-server communication via UDP/IP.  This is due to the fact that
all communication between the server and each client is done via
UDP/IP sockets.  Each client is a separate process and connects to the
server through a specified port.  After a player connects to the
server, all messages are transferred through this port.  A team can
have up to 12 clients, i.e. 11 players (10 fielders + 1 goalie) and a
coach.  The players send requests to the server regarding the actions
they want to perform (e.g. kick the ball, turn, run, etc.).  The
server receives those messages, handles the requests, and updates the
environment accordingly.  In addition, the server provides all players
with sensory information (e.g. visual data regarding the position of
objects on the field, or data about the player's ressources like
stamina or speed).  It is important to mention that the server is a
real-time system working with discrete time intervals (or cycles).
Each cycle has a specified duration, and actions that need to be
executed in a given cycle, must arrive at the server during the right
interval.  Therefore, slow performance of a player that results in
missing action opportunities has a major impact on the performance of
the team as a whole.  A detailed description of the server can be
found in Chapter :ref:`cha-soccerserver`.

---------------------------------------------------
The Monitor
---------------------------------------------------

The Soccer Monitor is a visualisation tool that allows people to see what is
happening within the server during a game.  Currently the monitor
comes in two flavors, the ``rcssmonitor`` and the
``rcssmonitor_classic``.  The information shown on both monitors
include the score, team names, and the positions of all the players
and the ball.  They also provide simple interfaces to the server.  For
example, when both teams have connected, the "Kick-Off" button on the
monitor allows a human referee to start the game.  The
``rcssmonitor``, which is based on the ``frameview`` by
Artur~Merke, extends the functionality of the classic monitor by
several features.

* It is possible to zoom into areas of the field.  This is especially useful
  for debugging purposes.
* The current positions and velocities of all players and the ball can be
  printed to the console at any time.
* A variety of information can be shown on the monitor, e.g. a player's view
  cone, stamina or (in the case of heterogeneous players) player type.
* Players and the ball can be moved around with the mouse.

As you will discover later on, to run a game on the server, a monitor
is not required.  However, if needed, a number of monitors can be
connected to the server at the same time (for example if you want to
show the same game at different terminals).  For further details on
the monitor please have a look at Chapter :ref:`cha-soccermonitor`.

---------------------------------------------------
The Logplayer
---------------------------------------------------

The logplayer can be thought of as a video player.  It is a tool that
is used to replay matches.  When running the server, certain options
can be used that will cause the server to store all the data for a
given match on the hard drive.  (Pretty much like pressing the record
button on your video).  Then, the program ``rcsslogplayer`` combined
with a monitor can be used to replay that game as many times as
needed.  This is quite useful for doing team analysis and discovering
the strong or weak points of a team.  Much like a video player, the
logplayer is equipped with play, stop, fast forward and rewind
buttons.  Also the logplayer allows you to jump to a particular cycle
in a game (for example if you only want to see the goals).  Finally
the logplayer allows you to edit existing recordings, i.e. you can
save interesting scenes from a match (or several matches) to another
logfile and thus create a presentation easily.

The logplayer can be controlled via a small GUI or a command line
interface.  In addition commands can be read from a file, which adds
limited scripting capabilities to the logplayer.

---------------------------------------------------
The Demo Client
---------------------------------------------------

Bundled with the RoboCup Soccer Simulator is a program called ``rcssclient``,
which implements a very primitive textbased client for the simulation. The
purpose of this program is to give you a first idea of how the whole
affair works.

When ``rcssclient`` is started, it connects to the server. You are
presented with a simple ncurses-based interface. You can then enter
commands that are executed by the server. Any information that is
received by the client will be shown in a different section of the
screen according to its type (visual, sense body or
other).
By entering commands and see what happens you can get
a first idea of the way things work in the simulation.
Even if you are not a newbie any more, the program is handy for simple
tests, e.g. getting a grip on new commands added to the simulation.

===================================================
The Rules of the Game
===================================================

During a game, a number of rules are enforced either by the automated
referee within the server, or by a human referee.  The aim of this
section is to describe how these rules work, and how they affect the
game.

---------------------------------------------------
Rules Judged by the Automated Referee
---------------------------------------------------

.. _sec-overview-referee:

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Kick-Off
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Just before a kick off (either before a half time starts, or after a
goal), all players must be in their own half.  To allow for this to
happen, after a goal is scored, the referee suspends the match for an
interval of 5 seconds. During this interval, players can use the
**move** command to teleport to a position within its own side,
rather than run to this position, which is much slower and consumes
stamina.  If a player remains in the opponent half after the 5-second
interval has expired or tries to teleport there during the interval,
the referee moves the player to a random position within their own
half.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Goal
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a team scores, the referee performs a number of tasks.
Initially, it announces the goal by broadcasting a message to all
players.  It also updates the score, moves the ball to the centre
mark, and changes the play-mode to kick\_off\_x (where x is either
left or right).  Finally, it suspends the match for 5 seconds allowing
players to move back to their own half (as described above in the
"Kick-Off" section).

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Out of Field
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the ball goes out of the field, the referee moves the ball to a
proper position (a touchline, corner or goal-area) and changes the
play-mode to kick_in, corner_kick, or goal_kick. In the case of a
corner kick, the referee places the ball at (1m, 1m) inside the
appropriate corner of the field.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Player Clearance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the play-mode is kick_off, free_kick, kick_in, or corner_kick, the
referee removes all defending players located within a circle centred
on the ball.  The radius of this circle is a parameter within the
server (normally 9.15 meters).  The removed players are placed on the
perimeter of that circle.  When the play-mode is offside, all
offending players are moved back to a non-offside position.  Offending
players in this case are all players in the offside area and all
players inside a circle with radius 9.15 meters from the ball.  When
the play-mode is goal_kick, all offending players are moved outside
the penalty area. The offending players cannot re-enter the penalty
area while the goal kick takes place. The play-mode changes to
play_on immediately after the ball goes outside the penalty area.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Play-Mode Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the play-mode is kick_off, free_kick, kick_in, or
corner_kick, the referee changes the play-mode to play\_on
immediately after the ball starts moving through a **kick**
command.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Offside
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A player is marked offside, if it is
- in the opponent half of the field,
- closer to the opponent goal than at least two defending players,
- closer to the opponent goal than the ball,\\
- closer to the ball than 2.5 meters (this can be changed with the server parameter **server::offside_active_area_size**).

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Backpasses
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Just like in real soccer games, the goalie is not allowed to catch a
ball that was passed to him by a teammate.  If this happens, the
referee calls a **back_pass_l** or **back_pass_r** and
assigns a free kick to the opposing team.  As such a back pass can
only happen within the penalty area, the ball is placed on the corner
of the penalty area that is closest to the position the goalie tried
to catch.  Note, that it is perfectly legal to pass the ball to the
goalie if the goalie does not try to catch the ball.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Free Kick Faults
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When taking a free kick, corner kick, goalie free kick, or kick in, a
player is not allowed to pass the ball to itself.  If a player kicks
the ball again after performing one of those free kicks, the referee
calls a **free_kick_fault_l** or **free_kick_fault_r** and
the oppsing team is awarded a free_kick.

As a player may have to kick the ball more than once in order to
accelerate it to the desired speed, a free kick fault is only called
if the player taking the free kick


1. is the first player to kick the ball again, and
2. the player has moved (= dashed) between the kicks.


So issuing command sequences like
**kick**--**kick**--**dash** or
**kick**--**turn**--**kick** is perfectly
legal.
The sequence **kick**--**dash**--**kick**,
on the other hand, results in a free kick fault.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Half-Time and Time-Up
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The referee suspends the match when the first or the second half
finishes. The default length for each half is 3000 simulation cycles
(about 5 minutes). If the match is drawn after the second half, the
match is extended. Extra time continues until a goal is scored. The
team that scores the first goal in extra time wins the game.  This is
also known as the "golden goal" rule or "sudden death".

---------------------------------------------------
Rules Judged by the Human Referee
---------------------------------------------------


Fouls like "obstruction" are difficult to judge automatically
because they concern players' intentions. To resolve such situations,
the server provides an interface for human-intervention. This way, a
human-referee can suspend the match and give free kicks to either of
the teams. The following are the guidelines that were agreed prior to
the RoboCup 2000 competition, but they have been used since then.

* Surrounding the ball
* Blocking the goal with too many players
* Not putting the ball into play after a given number of
  cycles.
  By now this rule is handled by the automatic referee, as
  well. If a team fails to put the ball back into play for
  **servr::drop_ball_time** cycles, a drop\_ball is issued by the
  referee. However, if a team repeatedly fails to put the ball into
  play, the human referee may drop the ball prematurely.
* Intentionally blocking the movement of other players
* Abusing the goalie **catch** command (the goalie may not
  repeatedly kick and catch the ball, as this provides a safe way
  to move the ball anywhere within the penalty area).
* Flooding the Server with Messages:
  A player should not send more than 3 or 4 commands per simulation
  cycle to the soccer server. Abuse may be checked if the server is
  jammed, or upon request after a game.
* Inappropriate Behaviour:
  If a player is observed to interfere with the match in an
  inappropriate way, the human-referee can suspend the match and give a
  free kick to the opposite team.
