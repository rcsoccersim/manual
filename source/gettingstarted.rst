.. -*- coding: utf-8; -*-
.. _cha-gettingstarted:

*************************************************
Getting Started
*************************************************

This section contains all the information necessary to get the RoboCup Soccer
Simulator source files and to install the software.  Since you are reading this
manual, you probably already know where to find the RoboCup Soccer Simulator
related software and documentation.  But we will tell you just in
case. :-)

=================================================
The Homepage
=================================================

The official homepage of the RoboCup Soccer Simulator can be found at
https://rcsoccersim.github.io/ .
This page contains (links to) useful information about RoboCup in general and
the RoboCup Soccer Simulator.

This is a short description of what hides behind the buttons:

Home: The RoboCup Soccer Simulator homepage.

SSIL: The Simulated Soccer Internet League is a continuous tournament hosted at the University of Koblenz. On a slightly modified (slowed down) server the participating teams have a series of tournaments When a tournament is finished, the next one is started. 
The SSIL provides continuous feedback to the developers of a team and thus offers valuable help in improving the abilities of the soccer playing agents. So the emphasis in this event is not laid on winning tournaments or being the best team,but on testing ideas and collecting data for.Once you have your own team, you might want to join in. In this case just submit a request by following the link New User Request on the SSIL page.

Clients: Since the RoboCup World Cup 2002 it is compulsory for participants to release a binary version of their team. Many teams have released binaries and sometimes (parts of) their source code even before that. The client repository collects the released teams for download. You should definitely have a look at the page if you need an opponent to test your team against.

Libraries: This page contains some useful libraries for working with the simulator. Usually such a library is for handling the lower levels of a team, e.g. the connection and synchronization with the server, a world model, and some basic skills like dribbling or passing.

Getting Started
Downloads: This page very briefly explains the different downloadable packages and leads you to the actual download page. You may want to read this page in order to find out more about the different packages you can download and install. However, to get started quickly, you better follow the instructions in Section 3.3.

Events: A list of past and upcoming local and international RoboCup events.

Links: A collection of links to sites related to the RoboCup initiative.

About us: This page shows the organizational structure of the RoboCup Federation in general. More detailed information are available about the RoboCup Simulation League and the RoboCup Soccer Simulator Maintainance Committee.

RoboCup: The official homepage of the RoboCup Federation. On this page you can get all information about RoboCup, the different leagues, its aims and history.

SIGs: A listing of the RoboCup Special Interest Groups. As the RobCup has a scientific background, a certain scientific infrastructure exists in addition to the annual competitions. In the RoboCup special interest groups scientists focus on certain
research aspects.

Old Site: This link takes you to the old RoboCup simulation site. It dates back to the days before the RoboCup Soccer Simulator was hosted by SourceForge. It was not even called RoboCup Soccer Simulator but Soccer Server in these days.

The rest of the page contains the latest news about releases and several shortcuts to
project management tools provided by SourceForge.
Finally, if you would like to make a donation to the RoboCup Soccer Simulator Maintenance Group, click on the PayPal button at the bottom of the page.


=================================================
Getting and installing the server
=================================================

The procedure shown was performed on a computer running SuSE
7.3-GNU/Linux 2.4.10-4GB (check your version with ``uname -sr``)
with gcc 2.95.3 and gcc 3.2 (check your version with ``which g++``)
but any reasonably up-to-date installation should work.In the commands
shown below, -> is supposed to be the command-lineprompt.

Get source files or RPMs from the Soccer Server site: 
<http://sserver.sf.net>
This is done by clicking the Released Files link and getting the tar.gz files or the .rpm files for the version you are after.
At the time of this writing (Jan-22-2003) the latest version is 9.2.2 and will be used in the example below. Please substitute 9.2.2 for the latest version available.
The entire simulator can be downloaded as the rcsoccersim-9.2.2.tar.gz file, or you can download each module individually.
rcssbase is base code used by the other modules. 
rcssserver performs the actual simulation. 
rcsslogplayer allows you to replay logs (*.rcg files) created by rcsserver. 
rcssmonitor and rcssmonitorclassic allow you to watch game in progress or game being replayed by the log player.
Get tar.gz files for the version you are after from the Simulator's code
repository:

* `rcssserver <https://github.com/rcsoccersim/rcssserver/releases>`_ performs the actual simulation.
* `rcssmonitor <https://github.com/rcsoccersim/rcssmonitor/releases>`_ allows you to watch game in progress.
* `rcsslogplayer <https://github.com/rcsoccersim/rcsslogplayer/releases>`_ allows you to replay logs (\*.rcg files) created by rcssserver. 

At the time of this writing (May-1-2021) the latest version is 16.0.0
and will be used in the example below. Please substitute 16.0.0 for the
latest version available.


If you have downloaded rcssserver-\*.tar.gz, then first extract the
source files by running::

  $ tar zxvf rcssserver-16.0.0.tar.gz

directory to **rcssserver-16.0.0**. This directory contains the following
files::

  $ cd rcssserver-16.0.0
  $ ls
  acinclude.m4    compile        configure       install-sh
  Makefile.in     README.md      aclocal.m4      config.guess
  configure.ac    ltmain.sh      missing         src
  AUTHORS         config.h.in    COPYING.LESSER  m4          
  NEWS            ylwrap 	       ChangeLog       config.sub 
  depcomp         Makefile.am    rcssbase

Always read the **README** file first::

  $ more README

The **COPYING** file contains details about the license under which you
may use and modify the software. Please, make sure you read it in your
own time::

  $ more COPYING

=================================================
Quick Start
=================================================

From the rcssserver-\* directory execute::

  $ ./configure
  $ make

This will build the necessary binaries to get you up and running.
The monitor and logplayer can be built with same procedure.

**rcssserver-\*/src/rcssserver** is the binary for the simulator server.
The simulator server manages the actual simulation and communicates with
client programs that control the simulated robots.
A sample client can be found at **rcssserver-\*/src/rcssclient**.

To see what is actually happening in the simulator, you will need to
start a simulator monitor, which can be found at
**rcssmonitor-\*/src/rcssmonitor**.

To playback games that you have recorded or downloaded, you will need
to start the log player, **rcsslogplayer-\*/src/rcsslogplayer**.
The log player will control what part of the game you see, but you will need
to start a monitor (like rcssmonitor) to see the actual playback.

=================================================
Full installation
=================================================

-------------------------------------------------
Configuring
-------------------------------------------------

Before you can build the RoboCup Soccer Simulator you will need to run the
**configure** script located in the root of the distribution directory.

The default configuration will set up to install the simulator components in
the following locations:

* /usr/local/bin
    for the executables
* /usr/local/include
    for the headers
* /usr/local/lib
    for the libraries


You may need administrator privileges to install the simulator into
the default location.
This location can be modified by using configure's ``--prefix=DIR`` and
related options.
See **configure --help** for an overview over the available options.

There are a number of features specific to the package. Some of them
are enabled by default.
If you want to enable a feature, use the option ``--enable-FEATURE[=yes]``.

Disabling a feature can be done by using either ``--disable-FEATURE`` or
``--enable-FEATURE=no``.

-------------------------------------------------
Building
-------------------------------------------------

Once you have successfully configured the simulator, simply run
``make`` to build the sources.

-------------------------------------------------
Installing
-------------------------------------------------

When you have completed building the simulator, its components can be
installed into their default locations or the locations specified
during configuration by running ``make install``.
Depending on where you are installing the simulator, you may need special
permissions.

-------------------------------------------------
Uninstalling
-------------------------------------------------

The simulator can also be easily removed by entering the distribution
directory and running ``make uninstall``.
This will remove all the files that where installed, but not any directories
that were created during the installation process.


=================================================
Using the Simulator
=================================================

To start the server either type::

  ./rcssserver

from the directory containing the executable or::

  rcssserver

if you installed the executables in your PATH.

rcssserver will look in your home directory for the configuration files:

* .rcssserver/server.conf
* .rcssserver/player.conf
* .rcssserver/CSVSaver.conf
* .rcssserver-landmark.xml

If .conf files do not exist, they will be created and populated with
default values.

You can include additional configuration files by using the ``include=FILE``
option to \Com{rcssserver}.

You can then see what's happening in the simulator by using
**./rcssmonitor** or **rcssmonitor** as above.

If you installed the executables in your PATH, you can start both the
server and the monitor by using the **rcsoccersim** script which
would have also been installed in your PATH.
This script will start the server and the monitor and automatically stop
the server when you close the monitor.

In order to actually start a match on the simulation server, the user must
connect some clients to the server (maximum of 11 per side plus
coaches).
When these clients are ready, the user can click the **Kick Off** button on
the monitor to start the game.
It is likely that you have not yet programmed your own clients, in which case,
you can read section ??? for instructions how to set up
a whole match with the available teams that other RoboCuppers have
contributed.

Also, there is a sample client **rcssclient** included with every
distribution of the server.
.. It has either an ncurses interface or a
.. command line interface (CLI) if ncurses is not available, or it it's
.. started with the \Com{-nogui} option.
Running **rcssclient** attempts to connect to the server using
default parameters (host=localhost, port=6000). Of course, these
server parameters can be changed using the arguments that the server
accepts when it is started. When the client is started, you need to
initialise its connection to the server.  This is done by manually
typing in an init command and hitting enter.  So, to initialise the
connection::

  (init MyTeam (version 15))

You will notice that one of the two teams is now named "MyTeam" and
one of the players that are standing by the side-line is active.
This player corresponds to the client you've just initialised.
Also, notice the information that the client writes on the terminal.
This is what the client receives from the server.

In the following text (which has line breaks added for clarity), the
first eleven lines correspond to the initialisation [#f1]_ and the other data
is the sensor information that the server sends to this client::

  (init MyTeam (version 15))
  (init l 2 before_kick_off)
  (server_param (catch_ban_cycle 5)(clang_advice_win 1)
    (clang_define_win 1)(clang_del_win 1)(clang_info_win 1)
    (clang_mess_delay 50)(clang_mess_per_cycle 1)
    (clang_meta_win 1)(clang_rule_win 1)(clang_win_size 300)
    (coach_port 6001)(connect_wait 300)(drop_ball_time 0)
    (freeform_send_period 20)(freeform_wait_period 600)
    (game_log_compression 0)(game_log_version 3)
    (game_over_wait 100)(goalie_max_moves 2)(half_time -10)
    (hear_decay 1)(hear_inc 1)(hear_max 1)(keepaway_start -1)
    (kick_off_wait 100)(max_goal_kicks 3)(olcoach_port 6002)
    (point_to_ban 5)(point_to_duration 20)(port 6000)
    (recv_step 10)(say_coach_cnt_max 128)
    (say_coach_msg_size 128)(say_msg_size 10)
    (send_step 150)(send_vi_step 100)(sense_body_step 100)
    (simulator_step 100)(slow_down_factor 1)(start_goal_l 0)
    (start_goal_r 0)(synch_micro_sleep 1)(synch_offset 60)
    (tackle_cycles 10)(text_log_compression 0)
    (game_log_dir "/home/thoward/data")
    (game_log_fixed_name "rcssserver")keepaway_log_dir "./")
    (keepaway_log_fixed_name "rcssserver")
    (landmark_file "~/.rcssserver-landmark.xml")
    (log_date_format "%Y%m%d%H%M-")(team_l_start "")
    (team_r_start "")(text_log_dir "/home/thoward/data")
    (text_log_fixed_name "rcssserver")(coach 0)
    (coach_w_referee 1)(old_coach_hear 0)(wind_none 0)
    (wind_random 0)(auto_mode 0)(back_passes 1)
    (forbid_kick_off_offside 1)(free_kick_faults 1)
    (fullstate_l 0)(fullstate_r 0)(game_log_dated 1)
    (game_log_fixed 1)(game_logging 1)(keepaway 0)
    (keepaway_log_dated 1)(keepaway_log_fixed 0)
    (keepaway_logging 1)(log_times 0)(profile 0)
    (proper_goal_kicks 0)(record_messages 0)(send_comms 0)
    (synch_mode 0)(team_actuator_noise 0)(text_log_dated 1)
    (text_log_fixed 1)(text_logging 1)(use_offside 1)
    (verbose 0)(audio_cut_dist 50)(ball_accel_max 2.7)
    (ball_decay 0.94)(ball_rand 0.05)(ball_size 0.085)
    (ball_speed_max 2.7)(ball_weight 0.2)(catch_probability 1)
    (catchable_area_l 2)(catchable_area_w 1)(ckick_margin 1)
    (control_radius 2)(dash_power_rate 0.006)(effort_dec 0.005)
    (effort_dec_thr 0.3)(effort_inc 0.01)(effort_inc_thr 0.6)
    (effort_init 0)(effort_min 0.6)(goal_width 14.02)
    (inertia_moment 5)(keepaway_length 20)(keepaway_width 20)
    (kick_power_rate 0.027)(kick_rand 0)(kick_rand_factor_l 1)
    (kick_rand_factor_r 1)(kickable_margin 0.7)(maxmoment 180)
    (maxneckang 90)(maxneckmoment 180)(maxpower 100)
    (minmoment -180)(minneckang -90)(minneckmoment -180)
    (minpower -100)(offside_active_area_size 2.5)
    (offside_kick_margin 9.15)(player_accel_max 1)
    (player_decay 0.4)(player_rand 0.1)(player_size 0.3)
    (player_speed_max 1)(player_weight 60)(prand_factor_l 1)
    (prand_factor_r 1)(quantize_step 0.1)(quantize_step_l 0.01)
    (recover_dec 0.002)(recover_dec_thr 0.3)(recover_min 0.5)
    (slowness_on_top_for_left_team 1)
    (slowness_on_top_for_right_team 1)(stamina_inc_max 45)
    (stamina_max 4000)(stopped_ball_vel 0.01)
    (tackle_back_dist 0.5)(tackle_dist 2.5)(tackle_exponent 6)
    (tackle_power_rate 0.027)(tackle_width 1.25)
    (visible_angle 90)(visible_distance 3)(wind_ang 0)
    (wind_dir 0)(wind_force 0)(wind_rand 0))
  (player_param (player_types 7)(pt_max 3)(random_seed -1)
    (subs_max 3)(dash_power_rate_delta_max 0)
    (dash_power_rate_delta_min 0)
    (effort_max_delta_factor -0.002)
    (effort_min_delta_factor -0.002)
    (extra_stamina_delta_max 100)
    (extra_stamina_delta_min 0)
    (inertia_moment_delta_factor 25)
    (kick_rand_delta_factor 0.5)
    (kickable_margin_delta_max 0.2)
    (kickable_margin_delta_min 0)
    (new_dash_power_rate_delta_max 0.002)
    (new_dash_power_rate_delta_min 0)
    (new_stamina_inc_max_delta_factor -10000)
    (player_decay_delta_max 0.2)
    (player_decay_delta_min 0)
    (player_size_delta_factor -100)
    (player_speed_max_delta_max 0.2)
    (player_speed_max_delta_min 0)
    (stamina_inc_max_delta_factor 0))
  (player_type (id 0)(player_speed_max 1)(stamina_inc_max 45)
    (player_decay 0.4)(inertia_moment 5)(dash_power_rate 0.006)
    (player_size 0.3)(kickable_margin 0.7)(kick_rand 0)
    (extra_stamina 0)(effort_max 1)(effort_min 0.6))
  (player_type (id 1)(player_speed_max 1.1956)(stamina_inc_max 30.06)
    (player_decay 0.4554)(inertia_moment 6.385)(dash_power_rate 0.007494)
    (player_size 0.3)(kickable_margin 0.829)(kick_rand 0.0645)
    (extra_stamina 9.4)(effort_max 0.9812)(effort_min 0.5812))
  (player_type (id 2)(player_speed_max 1.135)(stamina_inc_max 33.4)
    (player_decay 0.4292)(inertia_moment 5.73)(dash_power_rate 0.00716)
    (player_size 0.3)(kickable_margin 0.8198)(kick_rand 0.0599)
    (extra_stamina 31.3)(effort_max 0.9374)(effort_min 0.5374))
  (player_type (id 3)(player_speed_max 1.1964)(stamina_inc_max 31.24)
    (player_decay 0.4664)(inertia_moment 6.66)(dash_power_rate 0.007376)
    (player_size 0.3)(kickable_margin 0.88)(kick_rand 0.09)
    (extra_stamina 47.1)(effort_max 0.9058)(effort_min 0.5058))
  (player_type (id 4)(player_speed_max 1.151)(stamina_inc_max 37.8)
    (player_decay 0.45)(inertia_moment 6.25)(dash_power_rate 0.00672)
    (player_size 0.3)(kickable_margin 0.8838)(kick_rand 0.0919)
    (extra_stamina 44.1)(effort_max 0.9118)(effort_min 0.5118))
  (player_type (id 5)(player_speed_max 1.1544)(stamina_inc_max 34.68)
    (player_decay 0.4352)(inertia_moment 5.88)(dash_power_rate 0.007032)
    (player_size 0.3)(kickable_margin 0.8052)(kick_rand 0.0526)
    (extra_stamina 47.1)(effort_max 0.9058)(effort_min 0.5058))
  (player_type (id 6)(player_speed_max 1.193)(stamina_inc_max 36.7)
    (player_decay 0.4738)(inertia_moment 6.845)(dash_power_rate 0.00683)
    (player_size 0.3)(kickable_margin 0.885)(kick_rand 0.0925)
    (extra_stamina 92)(effort_max 0.816)(effort_min 0.416))
  (sense_body 0 (view_mode high normal) (stamina 4000 1) (speed 0 0)
    (head_angle 0) (kick 0) (dash 0) (turn 0) (say 0) (turn_neck 0)
    (catch 0) (move 0) (change_view 0) (arm (movable 0) (expires 0)
    (target 0 0) (count 0)) (focus (target none) (count 0)) (tackle
    (expires 0) (count 0)))
  (see 0 ((f c t) 6.7 27 0 0) ((f r t) 58.6 3) ((f g r b) 73 37)
    ((g r) 69.4 32) ((f g r t) 66 27) ((f p r c) 55.7 41)
    ((f p r t) 45.2 22) ((f t 0) 6.3 -18 0 0)
    ((f t r 10) 16.1 -7 0 0) ((f t r 20) 26 -4 0 0)
    ((f t r 30) 36.2 -3) ((f t r 40) 46.1 -2)
    ((f t r 50) 56.3 -2) ((f r 0) 73.7 30) ((f r t 10) 68.7 23)
    ((f r t 20) 66 15) ((f r t 30) 64.1 6) ((f r b 10) 79 37)
    ((f r b 20) 85.6 42))
  (sense_body 0 (view_mode high normal) (stamina 4000 1) (speed 0 0)
    (head_angle 0) (kick 0) (dash 0) (turn 0) (say 0) (turn_neck 0)
    (catch 0) (move 0) (change_view 0) (arm (movable 0) (expires 0)
    (target 0 0) (count 0)) (focus (target none) (count 0)) (tackle
    (expires 0) (count 0)))
  (see 0 ((f c t) 6.7 27 0 0) ((f r t) 58.6 3) ((f g r b) 73 37)
    ((g r) 69.4 32) ((f g r t) 66 27) ((f p r c) 55.7 41)
    ((f p r t) 45.2 22) ((f t 0) 6.3 -18 0 0)
    ((f t r 10) 16.1 -7 0 0) ((f t r 20) 26 -4 0 0)
    ((f t r 30) 36.2 -3) ((f t r 40) 46.1 -2)
    ((f t r 50) 56.3 -2) ((f r 0) 73.7 30) ((f r t 10) 68.7 23)
    ((f r t 20) 66 15) ((f r t 30) 64.1 6) ((f r b 10) 79 37)
    ((f r b 20) 85.6 42))
  ...


You can still type commands (such as ``(move 0 0)`` or ``(turn 45)``)
that the player will then send to the server. You should be able to
see the result of these commands on the monitor window.

=================================================
How to stop the server
=================================================

The correct procedure for stopping the server is:

#. Stop all clients (players)
#. Stop all monitors by clicking on the quit button
#. ``ctrl-c`` at the terminal window where you started the server in order to terminate it

If you follow this procedure, you will not only stop all visible
running processes but also make sure that all those processes that may
be running in the background (such as the server) are also stopped.
The problem that arises when you don't properly shut down the server
is that you may not be able to start another process unless you start
it with different parameters.

Also, if you don't stop the simulator with a ctrl-c, then the logfiles
will no be closed properly (only important if you are using compressed
logging) and they will not be renamed correctly.

*NOTE:* It is sometimes useful and convenient to terminate
processes using their name. Using the **kill** operating system
command involves finding the process number of the process you want to
stop using the **ps** command. A simpler way to eradicate all
processes that have a specific name is by means of the **killall**
command, for example: ``killall rcssserver`` is
sufficient to kill all processes with the name **rcssserver**.

=================================================
Supported platforms
=================================================

The Soccer Server supports quite a few unix style platforms but we haven’t actually
compiled a list. The simulator (grouped by version numbers) is known to work on the
following platforms [#f2]_:

- 9.2.2
  
  - SuSE 7.3 with gcc 2.95.3 or 3.2 (Tom Howard)
  - Windows 2000 with Cygwin with gcc 2.95.3 (Tom Howard)
  - SuSE 8.1 with gcc 3.2 (Jan Murray)
  - Debian 3.0 (woody) with gcc 2.95.4 (Jan Murray)
  - SuSE 7.0 Linux with gcc 2.95.2 (Kernel 2.4.16) (Goetz Schwandtner)

- 9.1.5

  - SuSE 8.1 with gcc 3.2 (Jan Murray)
  - Debian 3.0 (woody) with gcc 2.95.4 (Jan Murray)
  - SuSE 7.3 with gcc 2.95.3 or 3.2 (Tom Howard)
  - Windows 2000 with Cygwin with gcc 2.95.3 (Tom Howard)

If you have a platform not listed above for a particular simulator version and
you have managed to get the simulator running on it, please let us know at
<sserver-admin@lists.sf.net>.

=================================================
Troubleshooting
=================================================

In this section we list known problems and try to give some solutions
or at least point you in the right direction.

If you run into any errors in configuring, building or running the
simulator, which are not reported here please submit a bug report via
the RoboCup Soccer Simulator website, https://rcsoccersim.github.io/,
especially if you can provide a patch or hint to the solution of the
problem.

 Libtool and Sed
 
Some versions of libtool are broken, which may result in build errors. A fix to the
problem is to manually set the environment variable SED to point to the location of the
sed stream editor on your machine. This is now checked by the configure script. If the
SED variable is not set, configure exits with this error:
creating libtool
*************** ERROR *****************
The SED environment variable is not set.
Please set it to the sed excutable on your system.
Depending on the type of shell you use you have to do the following to fix this error: If
you use (t)csh, set the variable using the setenv shell builtin before running configure:
-> setenv SED sed
If you use bash, please set the variable like this:
-> export SED=sed
Now run configure again
-> ./configure
If you still get an error, your PATH probably does not include the location of the sed
binary. In this case replace sed in the above instructions with the absolute path to your
sed binary (usually /bin/sed).
To set the SED variable permanently, add the above lines to your .cshrc for (t)csh
or .bashrc for bash.

ncurses and solaris

Todo

old gcc (< 2.95.3) and sstream

Todo
====================================================
The process of a match
====================================================
Todo
----

.. [#f1] The response from the server means that the client plays for the left
         side, has the number one and the play mode is before_kick_off.
         The other lines correspond the the current server parameters and
         player types.
.. [#f2] The names listed are the names of the people who have verified the platform.
