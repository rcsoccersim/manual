.. -*- coding: utf-8; -*-

*************************************************
Coach
*************************************************

=================================================
Introduction
=================================================

Coaches are privileged clients used to provide assistance to the players.
There are two kinds of coaches, the online coach and the trainer. The latter is often
called 'off-line coach' as well, but for clarity sake we will use the term 'trainer'.

=================================================
Distinction Between Trainer and Online Coach
=================================================

In general,the trainer can exercise more control over the game and may be used only in
the development stage,where as the online coach may connect to official games. The trainer is
useful during development for such tasks as running automated learning or managing games.
The on-line coach is used during games to provide additional advice and information to the players.

While developing player clients,for example when applying machine learning methods to learn skills like
dribbling or kicking, it might be useful to create training sessions in an automated way. Therefore,
the trainer has the following capabilities:

* It can control the play-mode
* It can broad cast audio messages. Such a message can consist of a command or some information intended for one or more of the player-clients. Its syntax and interpretation are user-defined.
* It can move the players and the ball to any location on the field and set their directions and velocities.
* It can get noise-free information about the movable objects.

For details on these capabilities see Section 7.3.

The online coach is intended to observe the game and provide advice and information to the players. Therefore, it's capabilities are somewhat limited:

* It can communicate with the players.
* It can get noise-free information about the movable objects.

To prevent the coach from controlling each client in a centralized way, communication is restricted
in several ways as described in Section 7.7. The online coach is a good tool for opponent modelling,
game analysis, and giving strategic tips to its teammates. Since the coach gets a noise-free, global
view over the field and has less real-time demands, it is expected that it can spend more time
deliberating over strategies. See Section 7.6 for more details about the online coach.

=================================================
Trainer
=================================================

---------------------------------------------------------
Connecting with and without the Soccerserver Referee
---------------------------------------------------------

By default, an internal referee module is active within the soccerserver that controls the
match (see Section 4.7). If the trainer should have complete control over the match,
the soccerserver must be instructed to deactivate the referee module. This means for
example, that the play-mode will not change and players will not be moved back to their
sides after a goal. The trainer has to react to these events by its own rules.

The soccerserver must be informed at startup-time that a trainer-client will be used.
Add the option *-coach* to thecommand arguments of the soccerserver application when
a coach-client is used and the internal referee module of the server must be deactivated.
You can also add the line *coach* to the server.conf.

If you want to connect a trainer but let the server referee remain activated, add the
option *-coach_w_referee* to the command arguments of the server or add *coach_w_referee*
to the server conguration file.

If the server is invoked with one of the trainer modes, it prepares a UDP socket to
which the trainer-client can connect. The default port number is 6001.  If a different
port number is needed the new port can be set by assigning its value to the *coach_port*
parameter (see Section B.1).

.. _sec-coachcommand:

=================================================
Commands
=================================================

The trainer and the online coach can use the following set of commands. The items are
listed in three categories. The first category includes commands that can be used only
by the trainer, the second includes commands that can be used also by the online coach
with certain restrictions, and the third lists commands that can be used by both trainer
and online coach.

---------------------------------------------------------
Commands that can be used only by the trainer
---------------------------------------------------------

* **(change_mode PLAY_MODE)**

Change the play-mode to PLAY_MODE. PLAY_MODE must match one of the
modesde nedin Section 4.7.1. Note that for most play-mode requests the
soccerserver will only change the play-mode. The position of the ball usually remains
unchanged, but in some cases players will be moved. E.g. in free-kick and kick-in
playmodes they will be moved away from the ball if they stand within a certain
radius. When changing to 'before_kick_off' they will be even moved to their
own side.

Possible replies by the soccerserver:

    * **(ok change_mode)**
        The command succeded.
    * **(error illegal_mode)**
        The specified mode was not valid.
    * **(error illegal_command_form)**
        The PLAY_MODE argument was omitted

* **(move OBJECT X Y [VDIR [VELx VELy]])**

This command will move OBJECT, which may be a player or the ball (see
Section Sensor models for format information),to absolute position(X,Y).
If VDIR is specified, it will also change its absolute facing direction to VDIR (this
only matters for players). Additionally, if VELx and VELy are specified, the object's
velocity will be set accordingly.

The trainer always uses left-hand coordinates.

Possible replies by the soccerserver:

    * **(ok move)**
        The command succeded.
    * **(error illegal_object_form)**
        The OBJECT specification was not valid.
    * **(error illegal_command_form)**
        The position, direction, and/or velocity specification was not valid.

* **(check_ball)**

Ask the soccerserver to check the position of the ball. Four positions are defined:

    * **in_field**
        The ball is whithin the boundaries of the field.
    * **(goal_l)**
        The ball is whithin the area assigned to the goal at the left side of the field.
    * **(goal_r)**
        The ball is whithin the area assigned to the goal at the right side of the field.
    * **(out_of_field)**
        The ball is somewhere else.

Note that the states 'goal_l' and 'goal_r' do not necessary imply that the ball
actually crossed the goal line.

Possible replies by the soccerserver:

    * **(ok check_ball TIME BALLPOSITION)**
        BALLPOSITION will be one of the states specified above.

* **(start)**

This commands starts the server, e.g. sets the play-mode to 'kick_off_l'.  This
essentially simulates pressing the kick off button on the monitor.

If the trainer does not send an init command, then the first commands of any type
received from the trainer will cause the server to start, e.g. set the play-mode to
'kick_off_l'.

Possible replies by the soccerserver:

    * **(ok start)**
        The command succeeded.

* **(recover)**

This command resets players' stamina, recovery, effort and hear capacity to the
values at the beginning of the game.

Possible replies by the soccerserver:

    * **(ok recover)**
        The command succeeded.

* **(ear MODE)**

It turns on or off the sending of auditory information to the trainer. MODE must
be one of **on** and **off**. If **(ear on)** is sent, the server sends *all* auditory information
to the trainer. See Table 7.3 for the format. If **(ear off)** is sent, the server stops
sending auditory information to the trainer.

Possible replies by the soccerserver:

    * **(ok ear on)** and **(ok ear on)**
        Both replies indicate that the command succeeded.
    * **(error illegal_mode)**
        MODE did not match **on** or **off**.
    * **(error illegal_command_form)**
        The MODE argument was omitted.

---------------------------------------------------------
Commands that can be used only by the online coach
---------------------------------------------------------

* **(init (version VERSION))** for the trainer and
* **(init TEAMNAME (version VERSION))** for the online coach.

These commands tell the server which protocol version should be used to
communicate with the trainer or coach. In the case of the online coach TEAMNAME has
to be specified to indicate which team the coach belongs to. Note that the coach
must connect after at least one player from its team.

The trainer is *not* required to issue an init command. However, it is recommended
that the trainer does so. Otherwise, the server will communicate with an older
protocol.

It should be mentioned that the default port is 6001 for the trainer and 6002 for
the online coach.

Possible replies by the soccerserver:

    * **(init ok)**
        The command succeeded in case of the trainer.
    * **(init SIDE ok)**
        The command succeeded in case of the online coach. SIDE is either 'l' or 'r'.

* **(say MESSAGE)**

Note that the online coach can use this command with the same syntax, but there
are more restrictions. See Section 7.6.2 for details.

This command broadcasts the message MESSAGE to all clients in the case of the
trainer and only to teammates in the case of the online coach. For the trainer
the format of MESSAGE is the same as for a player-client. It must be a string
whose length is less than *say_coach_msg_size*(see Section B.1) and it must consist
of alphanumeric characters and/or the symbols().+*/?<>_

The format which the players hear these messages can be found in Section 4.3.1.

Possible replies by the soccerserver:

    * **(ok say)**
        The command succeeded.
    * **(error illegal_command_form)**
        MESSAGE did not match the required format.

* **(change_player_type TEAM_NAME UNUM PLAYER_TYPE)** for the trainer and
* **(change_player_type UNUM PLAYER_TYPE)** for the online coach.

These commands can be used to change the heterogeneous player type (see
Section 4.6) of the player with the number UNUM of team TEAM_NAME to the type
PLAYER_TYPE. PLAYER_TYPE is a digit between 0 and 6, where 0 denotes
the default player type. Note that in the case of the online coach the argument
TEAM_NAME is missing, because it can only change player types in its own team.

The trainer does not have to comply to the rule that a maximum of three (specified
by *subs_max*) players of each type can be on the field.

See Section 7.6.3 for details about the restrictions as to when and how the online
coach may substitute players.

Possible replies by the soccerserver:

    * **(warning no_team_found)**
        The team does not exist.
    * **(error illegal_command_form)**
        If **change_player_type** is not followed by a string, two integers and a close bracket.
    * **(warning no_such_player)**
        If there is no player with that uniform number on that team.
    * **(ok change_player_type TEAM UNUM TYPE)**
        The command succeeded.

Additionally, the soccerserver can send the following replies to the online coach:

    * **(warning cannot_sub_while_playon)**
        If the play-mode is **'play-on'**.
    * **(warning no_subs_left)**
        If the coach has already made its three (specified by *subs_max*) subs for the game.
    * **(warning max_of_that_type_on_field)**
        If the player-type is not the default and there are three (specified by *subs_max*) of that type already on the field.
    * **(warning cannot_change_goalie)**
        If the coach tries to change the player type of the goalie.

The server responds to the teammates with:

    * **(change_player_type UNUM TYPE)**

and opponents (including opponent coach) with:

    * **(change_player_type UNUM)**

**TODO: team_graphic**

-------------------------------------------------------------
Commands that can be used by both trainer and online-coach
-------------------------------------------------------------

* **(look)**

This command provides information about the positions of the following objects on the field:

    * The left and right goals.
    * The ball.
    * All active players.

Note that the trainer and online coach for *both* sides receive left hand coordinates.
That is, the coaches receive information in the global coordinates that the left hand
team uses. In general,the players receive no global information (the one exception
being the **move** command), but it is common for teams to localize themselves so
that the negative *x* direction is towards the goal they defend.

Possible replies by the soccerserver:

    * **(ok look TIME (OBJ1 OBJDESC1) (OBJ2 OBJDESC2) ... )**
        OBJj can be any of the objects mentioned above.  See Section 4.3 for information about the way the names for those objects are composed. OBJDESCj have the following form:

        * For goals: X Y
        * For the ball: X Y DELTAx DELTAy
        * For players: X Y DELTAx DELTAy BODYANGLE NECKANGLE [POINTING_DIRECTION]

The coordinates are always in left-hand orientation, no matter whether a trainer
or online coach is used.

If the trainer/coach should receive visual information periodically, use the
**(eye on)** command.

* **(eye MODE)**

MODE must be one of **on** and **off** . If **(eye on)** is sent, the server starts sending
**(see_global ... )** information (see Section 7.5) every 100ms (the interval  is
specified by the *send_vi_step* parameter automatically to the client.  If **(eye off)**
is sent, the server stops to send visual information automatically. In this case the
trainer/coach has to ask actively with **(look)**, if it needs visual information.

Possible replies by the soccerserver:

    * **(ok eye on)** and **(ok eye off)**
        Both replies indicate that the command succeeded.
    * **(error illegal_mode)**
        MODE id not match **on** or **off**.
    * **(error illegal_command_form)**
        The MODE argument was omitted.

* **(team_names)**

This command makes the trainer/coach receive information about the names of
both teams and which side they are playing on.

Possible replies by the soccerserver:

    * **(ok team_names [(team l TEAMNAME1) [(team r TEAMNAME2)]])**
        Depending on whether the teams already connected no, one, or both team name(s) will be supplied. Recall that the first team that connects will be on the left side.

-------------------------------------------------------------
Commands that can be used only by the online-coach
-------------------------------------------------------------

* **(team_graphic (X Y "XPM line" ... "XPM line"))**

The online coach can send teams-graphics as 256x64 XPM to the server. Each
**team_graphic**-command sends a 8x8 tile. X and Y are the coordinates of this tile,
so they range from 0 to 31 and 0 to 7 respectively. Each XPM line is a line from the
8x8 XPM tile. Monitors that are connected to the server will receive the following
message on the message-board after each of the coach's **team_graphic**-commands:
**(team_graphic_l|r (X Y "XPM line" ... "XPM line"))**

Possible replies by the soccerserver:

    * **(ok team_graphic X Y)**
        For each tile the server sends this string in order to signal its arrival.

=================================================
Messages from the Server
=================================================

Apart from the replies to the commands mentioned above the server also sends some
messages to the trainer and online coach. If the clients connect to the server with a
version >= 7.0 (using the **init**-command),they will receive the following parameter
messages just like player clients:

    * **(server_param ...)** once
    * **(player_param ...)** once
    * **(player_type ...)** once for each player type

See Section 4.2.2 for details on the parameter messages.

If the client chooses to receive visual information in each cycle by sending **(eye on)**
it will receive messages in the following format every 100ms *(send_vi_step)*:

.. class:: center

(*see_global* (OBJ1 OBJDESC1)(OBJ2 OBJDESC2) ...)

OBJj denotes the name of the object. See Table 4.3 for information about the way the
names for those objects are composed. OBJDESCj have the following form:

    * For goals: X Y
    * For the ball: X Y DELTAx DELTAy
    * For players: X Y DELTAx DELTAy BODYANGLE NECKANGLE [POINT-ING_DIRECTION]

The syntax is the same as in the reply to the **(look)** command, so coordinates are
always in left-hand orientation.

If the client wants to receive auditory information and sent **(ear on)** to the server,
it will receive all auditory information, from both the referees and all of the players.
There are two kinds of hear messages:

    * **(hear TIME referee MESSAGE)** for all referee messages, such as "play_on" and "free_kick_left". See Section 4.7 for a list of the valid messages from the referee.
    * **(hear TIME (p "TEAMNAME" NUM) "MESSAGE")** for all player messages. Note the quotes around the message.

See Section 4.3.1 for more details about the players speaking and listening abilities.

=================================================
Online Coach
=================================================

-------------------------------------------------
Introduction
-------------------------------------------------

The online coach is a privileged client that can connect to the server in official games. It
has the capability of receiving global and noise-free information about the objects on the
field. In order to encourage research in this area there are special coach contests since
2001. This way, research groups that do not want to develop a team of player clients
can participate in the RoboCup challenge by focusing on the online coach. Additionally,
in order to make it possible to use a single coach with a variety of teams, a standard
coach language (CLang) has been developed that can be used to communicate with the
players.

See Section 7.4 and 7.5 for details about the commands that can be used by the online
coach and messages that will be sent by the server.

-----------------------------------------------
Communication with the players
-----------------------------------------------

Prior to version 7.00, the online coach could say short (128 characters,
*say_coach_msg_size*) alphanumeric (plus the symbols().+*/?<>) messages when the
play-mode is not 'play_on'.  This type of message still exists as a "freeform" message,
but there are now other standard message types. Since version 8.05 there are also certain
intervalls in which freeform-messages can be sent even during 'play_on'. Every 600 cycles
(specified by *freeform_wait_period*) of 'play_on' the coach can send freeform-messages
for 20 cycles (specified by *freeform_send_period*). For example, if the playmode changes
to 'play_on' at cycle 420 and stays in 'play_on' till the end of this example,the coach can
send freeform-messages between 1020 and 1040, 1620 and 1640, etc. The coach can
send *say_coach_cnt_max* freeform messages per game. The length of these messages has
to be less than *say_coach_msg_size*. If the game continues into extended time, the online
coaches are given an additional *say_coach_cnt_max* messages to say every additional 6000
cycles (or whatever the normal length of a game is). Allowed messages are cumulative,
so if the coach does not use all its allowed messages, it can use them in the extended
time. The server will send **(error said_too_many_messages)** if the coach tries to send
messages after it reached the maximum number.

It should be noted that freeform-messages are not allowed in coach-competition-games,
and are only supported by CLang for compatibility reasons.

In the standard coach language there are three other types of messages: rule-, define-,
and delete-messages. To prevent coaches from micro-controlling every single action of
the players communication is restricted in the following ways.

Every 300 cycles (specified by *clang_win_size*) the coach can send one of each. Note
that the number of allowed messages can be changed by setting the *clang_define_win*,
*clang_del_win*, and *clang_rule_win* parameters (see SectionB.1). The messages are heard
by the players 50 (specified by *clang_mess_delay*) cycles later. If the play-mode is not
'play_on', one (specified by *clang_mess_per_cycle*) message is sent to the players in each
cycle, even if the delay time has not elapsed. Messages that are sent while the play mode
is not 'play_on' do not count towards the message number restrictions. For example, if
the default values are used the coach can send one message per cycle during breaks that
will be heard by the players without delay. The server guarantees that messages of each
type will be sent to the players in the same order in which they were received from the
coach.

The language grammar developed below does not place restrictions on the length of
the messages which can be sent to the server. However, for very practical reasons, any
message in the standard language cannot be longer than 8154 characters (this is so the
maximum message which should be sent to the player is 8K).

The first version of the coach language (Clang) was developped for server version7.x.
For server version 8.x the language has been extended. Because of this, clients that want
to receive messages from their coach have to explicitly advise the server, which version
of CLang they support. This is done by sending

* **(clang (ver MIN MAX))**

where MIN and MAX are unsigned integers denoting the earliest and latest supported
version of CLang, respectively. Clients that do not send such a message will not receive
coach messages. The server is able to determine the version number of coach messages
and will filter out any messages that are not supported by the player. If a message has
been filtered out, the players will receive

* **(hear TIME online_coach_left|right (unsupported_clang))**

The coach will receive a message for each player which informs it about the supported
versions:

* **(clang (ver (PLAYER_NAME) MIN MAX))**

This means that you have to add the sending of **(clang (ver 7 7))**, if you use version
7 source code of players with newer server versions.

The standard coach language will be described in detail in Section7.7.

-----------------------------------------------
Changing Player Types
-----------------------------------------------

Using the **change_player_type**-command (described in Section7.4) the online coach
can change player types unlimited times in **'beforekickoff'** play-mode. Of course
these changes have to comply with the general rules about heterogeneous players (see
Section 4.6). After kick-off player types can be changed three (*subs_max*) times during
play-modes that are not 'play_on'.

See the description of the **change_player_type**-command in Section 7.4 for details
about the possible replies from the server.

Note: A player client will be informed about substitutions that occurred before the
client connected by the message **(change_player_type UNUM TYPE)** for
substitutions in it own team and **(change_player_type UNUM)** for substitutions in the
opponent team.


-----------------------------------------------
Team Graphic
-----------------------------------------------

**TODO**

================================================
The Standard Coach Language
================================================

------------------------------------------------
General Properties
------------------------------------------------

The standard coach language was developed to enable coaches to work together with
teams from different research groups. One of the design goals was to have clear semantics
that should prevent misinterpretation from both the players and the coach. The language
is based on low-level concepts that can be combined to construct new high level concepts.

Additionally, coaches cancommunicate a certain number of freeform messages that
may be arbitrary strings to the players during non-*'play_on'*-modes. See Section 7.6.2
for details. Be aware though, that freeform messages probably will not be understood
by other teams if you plan to use your coach with other teams.

The language description below is the improved and extended version of the language
developed by the community, as it is supported by server version8.x. While the first
version of CLang is still supported by the server, its use is not encouraged. A complete
description of this first version can be found in them anual for server version 7. It is
hoped that all interested researchers will continue to develop CLang in order to make it
a useful tool for multi-agent research.

Some concepts were derived from Unilang [14] (e.g. definitions and several actions)
and SFL[12] (e. g. variables and point arithmetic).

Note that the server itself parses all the coach messages using flex and bison (the GNU
replacements for lex and yacc) and constructs a simple representation based on a C++
class hierarchy. Please feel free to use and modify this code from the server to handle
the parsing of the coach messages. In particular, look at the *coach_lang** files.

------------------------------------------------
Example Language Utterance
------------------------------------------------

The general idea of CLang is to describe tactics and behaviours as rules which map
directives to conditions. Each rule consists of a component which denotes a situation
(the *condition*) and a list of *directives* which are applicable if the situation-description
is truein the given worldstate. Rules can either be used as advise which tells the player
how to actor as information which for example describes how the opponent behaves in
certain situations. In CLang rules also have an ID, so that the coach can refer to them
later.

A simple rule which advises the player number 5 to pass to his teammate with the
number 11 if it has the ball and is in the middle of the field can be defined as follows:

    (define
        (definerule

            MyRule1

            direc (

            (and

                (bowner our 5)

                (bpos (rec (pt -10 -10) (pt 10 10))))

            (do our 5 (pass 11)))))

Each of the primitives will be explained in detail later. For now it should suffice to
get the idea that the rule is assigned the ID "MyRule1" and is defined as a directive (as
compared to a model-rule which describes observed behavior). **bowner** determines that
player 5 of the coach's team is the ballowner. **bpos** specifies the ballposition by means
of a rectangle. Finally, the directive advises player number 5 to pass to his teammate
11. In CLang lingo **(pass 11)** is an *action* and **(do our5 (pass 11))** is a *directive*.

Rules are off by default. So the coach has to turn them off by sending a message like
**(rule (on MyRule1))**

Now the language concepts will be looked at in more detail.

------------------------------------------------
Overview of the Five Message Types
------------------------------------------------

There are four types of coach messages in the standard coach language: Rule, Define,
Delete, and Freeform. Their purpose and format will be described in this section,and
some examples will be given.

In the following format description elements in capitals denote non-terminal symbols
which are defined in section 7.7.7.

**Define-message**: Define messages are the most complex messages in CLang, because
they define and combine the components which the coach wants to share with
the players, like conditions, directives, regions, actions, and rules. By defining
acomponent its is assigned an ID which the coach can use to refer to it in later
messages.


    **Conditions**: Formatfor defining a condition: **(definec CLANG_STR CONDITION)**

        Example: **(definec "Defense" (bowner opp 0))** This defines the condition
        in which any player of the opponent team owns the ball.

    **Actions**: Format for defining an action:**(definea CLANG_STR ACTION)**

        Example: **(definea "Pass7" (pass 7))**

    **Directives**: Format for defining a directive:**(defined CLANG_STR DIRECTIVE)**

        Example: **(defined "Pass10to11" (doour 10 (pass 11)))** This directives
        denotes player 10 passing to player 11.

    **Regions**: Format for defining a region:**(defined CLANG_STR REGION)**

        Example: **(defined "OURHALF" (rec (pt -52.5 -34) (pt 0 34)))** A
        rectangle which covers the team's own half is defined.

    **Rules**: Formatfor defining a rule:**(definerule CLANG_VAR model RULE)** or
    **(definerule CLANG_VAR direc RULE)**

        Example: **(definerule Rule1 direc ((playm bko) (do our 7 (pos (pt -20 20)))))**
        This rule states that player 7 should position itself at the given
        point before kick-off.
        See also section7.7.4 about defining rules.

**Rule-message**: Rule messages are used to turn previously defined rules on or off. After
defining a rule, it is off by default.

    Format: **(rule ACTIVATION_LIST)**

    Example: **(rule (on rule2) (off rule1))**

**Delete-message**: The delete message tells a player that a rule will not be used again and
can be removed from the memory. This also means that after deleting a rule, its
ID should not appear in other nested rule-definitions (see section 7.7.4) anymore.

    Format: **(delete ID_LIST)**

    Examples: **(delete Rule1) (delete (Rule1 Rule2)) (delete all)** Deletes one
    rule, a list of two rules, or all rules, respectively.

**Freeform-message**: Free form messages are arbitrary strings and can be sent according
to the afore-mentioned restrictions in section7.6.2.

    Format: **(freeform "STRING")**
    Note that STRING must be included in quotes.

------------------------------------------------
Defining Rules
------------------------------------------------

The definition of rules is an important part in CLang, so it will be looked at in more
detail in this section. Remember that a rule consists of a condition and a list of directives,
which again contain actions.

As stated above the format for defining a rule is **(definerule DEFINE_RULE)** using
the following components:

.. code-block::

    <DEFINE_RULE>: <CLANG_VAR> model <RULE>
                | <CLANG_VAR> direc <RULE>

.. code-block::

    <RULE>: (<CONDITION> <DIRECTIVE_LIST>)
            | (<CONDITION> <RULE_LIST>)
            | <ID_LIST>

Each rule is assigned a name complying the definition of **CLANG_VAR**. Additionally,
rules are in one of two modes, either **model** which states that the rule is a description
of observed behavior, or **direc** which states that the rule is a directive to behave in a
certain way.

Now,the actual content of a rule can be specified in several ways:

* (CONDITION DIRECTIVE_LIST)

This is the straight-forward way. The example in section 7.7.3 complies to this
format. The CONDITION denotes a situation, and DIRECTIVE_LIST denotes
the appropriate directives. Note that the list can contain directives for one, several,
or **all** players, or even several directives for the same player. In the latter case it is
up to the player to decide which directive is to be followed.

* (CONDITION RULE_LIST)

This is a very powerful format for combining rules to larger tactics. Since each
rule in RULE_LIST already contains a condition, a definition of this form results
in nested rules. It can for example be used to activate several rules simultaneously.
Suppose, there are already several rules specifying the home positions of the
defenders: pos2a and pos2b for player 2, and pos3a and pos3b for player 3. Now, by
using

(definerule defenseformation direc ((bowner our {0}) (pos2a pos3a)))

and

(definerule offenseformation direc ((bowner opp {0}) (pos2b pos3b)))

it can be specified when the rules are supposed to be active (depending on which
team owns the ball). For evaluating such definitions, the outer condition is assumed
to be distributed into the inner conditions, being combined with logical **and**. E.g.
assume that pos2a was specified as

((time > 20) (do our {2} (pos (pt -40 10))))

then the above definition would create

((and (bowner our {0}) (time > 20)) (do our {2} (pos (pt -40 10))))

* ID_LISTS

Similar to the above format, this way several existing rules can be combined.
Suppose, there have been defined two rules:

(definerule position2 direc ((true) (home (pt -40 -10))))

(definerule mark2 direc ((bowner opp {10}) (mark 10)))

These can be combined into a behavior for player 2:

(definerule player2 direc (position2 mark2))

------------------------------------------------
Semantics and Syntax Details of the Components
------------------------------------------------

In the following the syntax and semantics of the non-terminal symbols which were used
in the format outlines above will be described.
Rules have a condition on the left-hand side, and a set of actions on the right hand
side. Thus each rule can be thought of as essentially specifying an if-then statement:

.. code-block::

    if CONDITION
    then { DIRECTIVE_1 DIRECTIVE_2 ... }

In the player’s programs, it is easy to represent all the advice given by the coach as
a small rule-base. Following the advice would be easy by matching the current world
state against the condition, and trying to act on the directives. Note: If more than one
condition applies to the current situation and the corresponding directives differ, it is
up to the player to choose the directive. Note that the player should also exercise some
discretion in following directives. For example, if the only directive which matches is to
pass to player 5, but player 5 is well-covered by opponents, the player with the ball may
choose to ignore the directive for now.

* Conditions:

    A condition is made from the logical connectives over atomic state description
    propositions:

        * **(ture)**
            Always true.

        * **(false)**
            Always false.

        * **(ppos TEAM UNUM SET INT INT REGION)**
            The first INT is the MINIMUM and the second is the MAXIMUM At least
            MINUMUM but no more than MAXIMUM players in UNUM SET from team
            TEAM are in region REGION. Regions and unum sets are more precisely
            defined below. TEAM is either ”our” or ”opp”. There is no ambiguity since
            the coach can only be heard by its own players.

        * **(bpos REGION)**
            The ball is in region REGION.

        * **(bowner TEAM UNUM SET)**
            The ball is controlled by some player in UNUM SET of team TEAM. The
            ball-owner is the last player that had ball contact (i.e. the ball was in his
            kickable area), even if the ball left his control after that.

        * **(playm PLAY MODE)**
            The play-mode is PLAY MODE. See Section 7.7.7 for the valid values of
            PLAY MODE.

        * **(COND COMP)**
            The time, goal-difference, number of own or opponent goals can be compared
            with constants, using the operators < > <= == != >=.
            Examples: (time > 20) (2 >= opp goals)

        * **unum CLANG VAR UNUM SET**
            If CLANG VAR is instantiated, it is checked whether the unum denoted by
            the variable CLANG VAR is in the set UNUM SET. If the variable is still
            unbound, it is bound to the specific set.

    The logical connectives are:

        * **(and CONDITION_1 CONDITION_2 . . . CONDITION_n )**
        * **(or CONDITION_1 CONDITION_2 . . . CONDITION_n )**
        * **(not CONDITION)**

    An example condition: ”When opponent player 3 is in region X and controls the
    ball” would be
    **(and (ppos opp {3} X) (bowner opp {3}))**

* Directives:

    Directives are basically lists of actions for individual sets of players and come in
    two forms:

        * **(do TEAM UNUM SET ACTION LIST)** (affirmative mode: players should take thess actions)

        * **(dont TEAM UNUM SET ACTION LIST)** (negative mode: players should avoid taking these actions)

    If the actions in the affirmative mode are mutually exclusive, it is up to the player to
    decide which one is to be followed. In rules which are in the model-mode, directives
    convey knowledge about the plans/behaviors of the players or their opponents.

* Actions:

    * **(pos REGION)**
        The player should position itself in REGION.

    * **(home REGION)**
        The player’s default position should be in REGION. This directive is intended
        largely to specify formations for the team.

    * **(mark UNUM SET)**
        The player should mark some opponent player in UNUM SET.

    * **(markl REGION)**
        The passing lane from the current ball position to REGION should be marked.

    * **(markl UNUM SET)**
        The passing lane from the current ball position to some opponent player in
        UNUM SET should be marked.
    * **(oline REGION)**
        The offside-trap line for the player/team should be set at REGION.
    * **(htype TYPE)**
        The player is of heterogeneous type TYPE. The TYPE number is as described
        in Section 4.6. A value of -1 should clear the player’s idea of the heterogeneous
        type.
    * **(pass REGION)**
        The ball should be passed to some player in REGION.
    * **(pass UNUM SET)**
        The ball should be passed to some player in UNUM SET.
    * **(dribble REGION)**
        The ball should be dribbled to REGION.
    * **(clear REGION)**
        The ball should be cleared from REGION, which means to shoot the ball to
        a point outside of REGION.
    * **(shoot)**
        The ball should be shot at the goal.
    * **(hold)**
        The player should hold the ball, i. e. stand at his position and keeping the
        ball away from opponents.
    * **(intercept)**
        The player should go to the ball and try to control it.
    * **(tackle UNUM SET)**
        The player should tackle some player in UNUM SET (or the ballowner?).

* Regions:

    Any REGION token can be any of the following:
        * a POINT
            This is defined more precisely below
        * **(rec POINT 1 POINT 2 )**
            Defines a rectangle with its sides parallel to the pitch-lines, respectively.
        * **(tri POINT 1 POINT 2 POINT 3 )**
            Defines a triangle made up of the given points.
        * **(arc POINT RADIUS SMALL RADIUS LARGE ANGLE BEGIN ANGLE SPAN)**
            Defines a donut-arc: the area between two circles co-centered at point POINT,
            having the given radii, with the arc defined starting at the beginning angle
            and covering the spannign angle. For example a, a circle with radius r could
            be defined as “(arc (pt 0 0) 0 r 0 360)”, and a U-shaped region could be
            defined as “(arc (pt 0 0) 5 10 0 180)”
        * **(null)**
            The null (empty) region.
        * **(reg REG_1 REG_2 . . . REG_n )**
            Defines a region made up from the union of the given regions.
    A POINT is any of the following:
        * **(pt X Y)**
            X and Y are reals and in global coordinates. This is the absolute position
            (X,Y);
        * **(pt ball)** The current global position of the ball.
        * **(pt TEAM UNUM)** The current position of player number UNUM on team
            TEAM (either ’our’ or ’opp’). Remember that UNUM can be a variable.
        * **(POINT 1 OP POINT 2 )**
            This arithmetically combines two points to a new point. POINT i can be
            made up of arithmetic operators, resulting in a recursive structure.
            The operators are defined in the natural way, for example:
            **(pt** :math:`X_1Y_1` **) OP (pt** :math:`X_2Y_2` **)** :math:`=` **(pt** :math:`X_1` **OP** :math:`X_2` :math:`Y_1`**OP** :math:`Y_2` **)**
            where **OP** is one of :math:`+ − * /`

    The use of these relative points makes it easy to express ideas such as “Move to
    the ball”, “If there are 2 teammates within 10m of the ball”, etc.
    Remember that the online coach receives visual information alway in left-hand
    orientation, no matter which side its team plays on. Yet, when sending messages
    to a team that plays on the right side, the coach must use right-hand orientation
    in the messages. Transforming coordinates from left- to right-hand orientation is
    done by negating them.

* UNUM SETS:

    Unum sets are sets of player numbers. These are sets in the sense that order does
    not matter and may be changed by the server. If 0 is included anywhere in the
    set, then the set contains all players 1 - 11. The set can contain variables.

    Format: { :math: `NUM_1 NUM_2 ... NUM_n` }

* Variables:

    Technically, everywhere where UNUM occurs, a variable can be used. Yet, it is
    important to make sure that the variables are instantiated or ground. The scope
    is the innermost spanning rule, e.g. in

    .. code-block::

        1   (definerule rule1 model
        2       (bowner our {0})
        3       ((true)             (do our {5} (mark 11)))
        4       ((bowner our {X}) (do our {X} (shoot)))
        5   )

    the scope of **X** is the complete line 4. This also shows how variables can be instan-
    tiated: Only in conditions which have UNUMs as fixed argument (i. e. UNUMs
    in POINTs do not count as condition UNUMS) a variable may be introduced. Its
    value is set by checking which unums make the condition true. In the example **X**
    is instantiated with the uniform number of the ballowner. In a condition like **ppos**
    it can be necessary to instantiate the variable as a set of unums:

        (ppos our {X} 1 11 REGION)
        In this example **X** has to be instantiated as the set of unums which are in **REGION**.
        Note that an instantiation as in
        (ppos our {5} 1 1 (rec (pt ball) (pt our {X}))) is not supported.


------------------------------------------------
Futher Resources
------------------------------------------------

* The CLang Corpus contains examples of actual CLang messages:
    http://www-2.cs.cmu.edu/ ̃ pfr/soccer/clang corpus.html

* The Multi-Agent Modeling Special Interest Group (MAMSIG) provides binaries
    and sources of coachable teams and online coaches:

    http://www.cl-ki.uni-osnabrueck.de/ ̃ tsteffen/mamsig

* The Coach-mailing-list discusses Clang details, competition rules, and coaching
    methods:
    http://robocup.biglist.com/coach-l/

------------------------------------------------
Syntax
------------------------------------------------

The complete grammar of the standard coach language:

| <MESSAGE> : <FREEFORM_MESS> | <DEFINE_MESS> | <RULE_MESS> | <DEL_MESS>
|
| <RULE_MESS> : (rule <ACTIVATION_LIST>)
|
| <DEL_MESS> : (delete <ID_LIST>)
|
| <DEFINE_MESS> : (define <DEFINE_TOKEN_LIST>)
|
| <FREEFORM_MESS> : (freeform <CLANG_STR>)
|
| <DEFINE_TOKEN_LIST> : <DEFINE_TOKEN_LIST> <DEFINE_TOKEN>
|                      \| <DEFINE_TOKEN>
|
| <DEFINE_TOKEN> : (definec <CLANG_STR> <CONDITION>)
|                 \| (defined <CLANG_STR> <DIRECTIVE>)
|                 \| (definer <CLANG_STR> <REGION>)
|                 \| (definea <CLANG_STR> <ACTION>)
|                 \| (definerule <DEFINE_RULE>)
|
| <DEFINE_RULE> : <CLANG_VAR> model <RULE>
|                \| <CLANG_VAR> direc <RULE>
|
| <RULE> : (<CONDITION> <DIRECTIVE_LIST>)
|                 \| (<CONDITION> <RULE_LIST>)
|                 \| <ID_LIST>
|
| <ACTIVATION_LIST> : <ACTIVATION_LIST> <ACTIVATION_ELEMENT>
|                    \| <ACTIVATION_ELEMENT>
|
| <ACTIVATION_ELEMENT> : (on|off <ID_LIST>)
|
| <ACTION> : (pos <REGION>)
|               \| (home <REGION>)
|               \| (mark <UNUM_SET>)
|               \| (markl <UNUM_SET>)
|               \| (markl <REGION>)
|               \| (oline <REGION>)
|               \| (htype <INTEGER>)
|               \| <CLANG_STR>
|               \| (pass <REGION>)
|               \| (pass <UNUM_SET>)
|               \| (dribble <REGION>)
|               \| (clear <REGION>)
|               \| (shoot)
|               \| (hold)
|               \| (intercept)
|               \| (tackle <UNUM_SET>)
|
| <CONDITION> : (true)
|               \| (false)
|               \| (ppos <TEAM> <UNUM_SET> <INTEGER> <INTEGER> <REGION>)
|               \| (bpos <REGION>)
|               \| (bowner <TEAM> <UNUM_SET>)
|               \| (playm <PLAY_MODE>)
|               \| (and <CONDITION_LIST>)
|               \| (or <CONDITION_LIST>)
|               \| (not <CONDITION>)
|               \| <CLANG_STR>
|               \| (<COND_COMP>)
|               \| (unum <CLANG_VAR> <UNUM_SET>)
|               \| (unum <CLANG_STR> <UNUM_SET>)
|
| <COND_COMP> : <TIME_COMP>
|               \| <OPP_GOAL_COMP>
|               \| <OUR_GOAL_COMP>
|               \| <GOAL_DIFF_COMP>
|
| <TIME_COMP> : time <COMP> <INTEGER>
|               \| <INTEGER> <COMP> time
|
| <OPP_GOAL_COMP> : opp_goals <COMP> <INTEGER>
|               \| <INTEGER> <COMP> opp_goals
|
| <OUR_GOAL_COMP> : our_goals <COMP> <INTEGER>
|               \| <INTEGER> <COMP> our_goals
|
| <GOAL_DIFF_COMP> : goal_diff <COMP> <INTEGER>
|               \| <INTEGER> <COMP> goal_diff
|
| <COMP> : < | <= | == | != | >= | >
|
| <PLAY_MODE> : bko | time_over | play_on | ko_our | ko_opp
|               \| ki_our | ki_opp | fk_our | fk_opp
|               \| ck_our | ck_opp | gk_opp | gk_our
|               \| gc_our | gc_opp | ag_opp | ag_our
|
| <DIRECTIVE> : (do|dont <TEAM> <UNUM_SET> <ACTION_LIST>)
|               \| <CLANG_STR>
|
| <REGION> : (null)
|           \| (arc <POINT> <REAL> <REAL> <REAL> <REAL>)
|           \| (reg <REGION_LIST>)
|           \| <CLANG_STR>
|           \| <POINT>
|           \| (tri <POINT> <POINT> <POINT>)
|           \| (rec <POINT> <POINT>)
|
| <POINT> : (pt <REAL> <REAL>)
|           \| (pt ball)
|           \| (pt <TEAM> <INTEGER>)
|           \| (pt <TEAM> <CLANG_VAR>)
|           \| (pt <TEAM> <CLANG_STR>)
|           \| (<POINT_ARITH>)
|
| <POINT_ARITH> : <POINT_ARITH> <OP> <POINT_ARITH>
|                \|  <POINT>
|
| <OP> : + | - | * | /
|
| <REGION> : <REGION_LIST> <REGION>
|           \| <REGION>
|
| <UNUM_SET> : { <UNUM_LIST> }
|
| <UNUM_LIST> : <UNUM>
|              \| <UNUM_LIST> <UNUM>
|
| <UNUM> : <INTEGER> | <CLANG_VAR> | <CLANG_STR>
|
| <ACTION_LIST> : <ACTION_LIST> <ACTION>
|                \| <ACTION>
|
| <DIRECTIVE_LIST> : <DIRECTIVE_LIST> <DIRECTIVE>
|                   \| <DIRECTIVE>
|
| <CONDITION_LIST> : <CONDITION_LIST> <CONDITION>
|                   \| <CONDITION>
|
| <RULE_LIST> : <RULE_LIST> <RULE>
|              \| <RULE>
|
| <ID-LIST> : <CLANG_VAR>
|            \| (<ID_LIST2>)
|            \| all
|
| <ID-LIST2> : <ID_LIST2> <CLANG_VAR>
|             \| <CLANG_VAR>
|
| <CLANG_STR> : "[0-9A-Za-z\(\)\.\+\-\*\/\?\<\>\_ ]+"
|
| <CLANG_VAR> : [abe-oqrt-zA-Z\_]+[a-zA-Z0-9\_]\*

+----------------------+-------------+----------------+------------------------------------------------+
| Parameter name       |Used value   | Default value  | Explanation                                    |
+======================+=============+================+================================================+
| coach_port           | 6001        | 6001           | The port number the trainer connects to.       |
+----------------------+-------------+----------------+------------------------------------------------+
| say_msg_size         | 512         | 256            | Maximum length of a freeform message a         |
|                      |             |                | player, trainer, or coach can say.             |
+----------------------+-------------+----------------+------------------------------------------------+
| say_coach_cnt_max    | 128         | 128            | Upper limit of freeform messages an online     |
|                      |             |                | coach can say                                  |
+----------------------+-------------+----------------+------------------------------------------------+
| send_vi_step         | 100         | 100            | Interval of online coach’s look.               |
+----------------------+-------------+----------------+------------------------------------------------+
| clang_win_size       | 100         | 100            | Number of cycles that lie between online coach |
|                      |             |                | messages                                       |
+----------------------+-------------+----------------+------------------------------------------------+
| clang_define_win     | 1           | 1              | Number of define messages that can be sent in  |
|                      |             |                | the aforementioned interval.                   |
+----------------------+-------------+----------------+------------------------------------------------+
| clang_rule_win       | 1           | 1              | Number of rule messages that can be sent in    |
|                      |             |                | the aforementioned interval.                   |
+----------------------+-------------+----------------+------------------------------------------------+
| clang_del_win        | 1           | 1              | Number of delete messages that can be sent in  |
|                      |             |                | the aforementioned interval.                   |
+----------------------+-------------+----------------+------------------------------------------------+
| clang_mess_delay     | 50          | 50             | Number of cycles messages from the online      |
|                      |             |                | coach will be delayed.                         |
+----------------------+-------------+----------------+------------------------------------------------+
| clang mess per cycle | 1           | 1              | Number of messages that will be sent to the    |
|                      |             |                | players during non-play on modes.              |
+----------------------+-------------+----------------+------------------------------------------------+


.. table:: Trainer Interactions with the Server

    +----------------------------------------+----------------------------------+
    | From trainer to server                 | From server to trainer           |
    +========================================+==================================+
    | (init (version VERSION))               | trainer: (init ok)               |
    |    VERSION ::= a real number           |                                  |
    +----------------------------------------+----------------------------------+
    | (change mode PLAY_MODE)                | (ok change_mode)                 |
    |    PLAY MODE ::= one of the play-modes | (error illegal_mode)             |
    |                                        | (error illegal_command_form)     |
    +----------------------------------------+----------------------------------+
    | | (move OBJECT X Y                     | | (ok move)                      |
    | |       [VDIR [DELTA_X DELTA_Y]])      | | (error illegal_object_form)    |
    | |   OBJECT ::= One of object names     | | (error illegal_command_form)   |
    | |   X ::= -52–52                       |                                  |
    | |   Y ::= -32–32                       |                                  |
    | |   VDIR ::= -180–180                  |                                  |
    | |   DELTA_X, DELTA_Y ::= [float]       |                                  |
    +----------------------------------------+----------------------------------+
    | (check_ball)                           | | (ok check_ball TIME BPOS)      |
    |                                        | |    TIME ::= sim. time of server|
    |                                        | |    BPOS ::= in_field |         |
    |                                        | |       goal SIDE |              |
    |                                        | |       out of field             |
    |                                        | |    SIDE ::= l | r              |
    +----------------------------------------+----------------------------------+
    | | (start)                              | | (ok start)                     |
    | | (recover)                            | | (ok recover)                   |
    +----------------------------------------+----------------------------------+
    | | (change_player_type                  |  |  (warning no_team_found)      |
    | |       TEAM_NAME UNUM                 |  |  (error illegal_command_form) |
    | |       PLAYER_TYPE)                   |  |  (warning no_such_player)     |
    | |   TEAM_NAME ::= string               |  |  (ok change_player_type       |
    | |   UNUM ::= 1–11                      |  |       TEAM UNUM TYPE)         |
    | |   PLAYER_TYPE ::= 0–6                |  |                               |
    +----------------------------------------+----------------------------------+
    | | (ear MODE)                           | | (ok ear on)                    |
    | |    MODE ::= on | off                 | | (ok ear off)                   |
    |                                        | | (error illegal mode)           |
    |                                        | | (error illegal_command_form)   |
    +----------------------------------------+----------------------------------+


.. table:: Online Coach Interactions with the Server

    +----------------------------------------+--------------------------------------+
    | From trainer to server                 | From server to online coach          |
    +========================================+======================================+
    | | (init TEAMNAME                       | | (init SIDE ok)                     |
    | |        (version VERSION))            | |    SIDE ::= l | r                  |
    | |    VERSION ::= a real number         |                                      |
    | |    TEAMNAME ::= string               |                                      |
    +----------------------------------------+--------------------------------------+
    | | (change_player_type                  | | (warning no_team_found)            |
    | |    UNUM PLAYER_TYPE)                 | | (error illegal_command_form)       |
    | |   UNUM ::= 1–11                      | | (warning no_such_player)           |
    | |   PLAYER TYPE ::= 0–6                | | (ok change_player_type             |
    |                                        | |      TEAM UNUM TYPE)               |
    |                                        | | (warning cannot_sub_while_playon)  |
    |                                        | | (warning no_subs_left)             |
    |                                        | | (warning max_of_that_type_on_field)|
    |                                        | | (warning cannot_change_goalie)     |
    +----------------------------------------+--------------------------------------+


.. table:: Server Interactions with Trainer/Coach

    +----------------------------------------+--------------------------------------------------------+
    | From client to server                  | From server to client                                  |
    +========================================+========================================================+
    || (say MESSAGE)                         || (ok say)                                              |
    ||        (see Section 7.4.2)            || (error illegal command form)                          |
    +----------------------------------------+--------------------------------------------------------+
    | (look)                                 || (ok look TIME                                         |
    |                                        ||      (:math:`OBJ_1` :math:`OBJDESC_1` )               |
    |                                        ||      (:math:`OBJ_2` :math:`OBJDESC_2` )..)            |
    |                                        ||   :math:`OBJ_j` ::= object name                       |
    |                                        ||              (see Section :ref:`sec-sensormodels`)    |
    |                                        ||          :math:`OBJDESC_j` ::= X Y |                  |
    |                                        ||       X Y :math:`DELTA_x` :math:`DELTA_y`\|           |
    |                                        ||       X Y :math:`DELTA_x` :math:`DELTA_y`             |
    |                                        ||           BODYANG NECKANG                             |
    +----------------------------------------+--------------------------------------------------------+
    | | (eye MODE)                           | | (ok eye on)                                          |
    | |    MODE ::= on | off                 | | (ok eye off)                                         |
    |                                        | | (error illegal mode)                                 |
    |                                        | | (error illegal command form)                         |
    +----------------------------------------+--------------------------------------------------------+
    | This message is sent automatically ev- | |                                                      |
    | ery send_vi_step milliseconds when the | | (see_global TIME                                     |
    | coach/trainer eye is on (see the “eye” | |    (:math:`OBJ_1` :math:`OBJDESC_1` )                |
    | commands below).                       | |    (:math:`OBJ_2` :math:`OBJDESC_2` )...)            |
    |                                        |                                                        |
    +----------------------------------------+--------------------------------------------------------+
    | The trainer must use the ‘ear’ command | |                                                      |
    | to get these messages. The online coach| | (hear TIME referee MESSAGE)                          |
    | always gets these messages.            | | (hear TIME                                           |
    |                                        | |        (p ”TEAMNAME” NUM)                            |
    |                                        | |    ”MESSAGE”)                                        |
    |                                        | |   TIME ::= time message was sent                     |
    |                                        | |   TEAMNAME ::= string                                |
    |                                        | |   NUM ::= 1–11                                       |
    |                                        | |   MESSAGE ::= string                                 |
    +----------------------------------------+--------------------------------------------------------+
    | (team_names)                           | | (ok team_names                                       |
    |                                        | |    [(team l TEAMNAME1)                               |
    |                                        | |    [(team r TEAMNAME2)]])                            |
    +----------------------------------------+--------------------------------------------------------+
