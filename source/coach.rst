.. -*- coding: utf-8; -*-
.. _cha-coach:

*************************************************
Coach
*************************************************


=================================================
Online coach
=================================================


-------------------------------------------------
Introduction
-------------------------------------------------

The online coach is a privileged client that can connect to the server
in offcial games.It has the capability of receiving global and noise-free
information about the objects on the field.In order to encourage research
in this area there are special coach contests since 2001. Thisway, research
groups that do not want to develop a team of player clients can participate
in the RoboCup challenge by focusing on the online coach. Additionally,
in order to make it possible to use a single coach with a variety of teams,
a standard coach language (CLang) has been developed that can be used to communicate
with the players.
See Section 7.4 and 7.5 for details about the commands that can
be used by the online coach and messages that will be sent by the server.

-------------------------------------------------
Communication with the players
-------------------------------------------------

Prior to version 7.00, the online coach could say  short (128characters,
say_coach_msg_size) alphanumeric (plus the symbols().+*/?<>) messages when the play-modeis
not`play on'.  This type of message still exists as a "freeform" message, but there are now
other standard message types. Since version 8.05 there are also certain intervalls in which
freeform-messages can be sent even during `playon'. Every 600 cycles (specified by freeform_wait_period)
of `playon' the coach can send freeform-messages for 20 cycles (specifed by freeform_send_period).
For example, if the play mode changes to `play on' at cycle 420 and stays in `playon' till
the end of this example, the coach can send freeform-messages between 1020 and 1040, 
1620 and 1640, etc. The coach can send say_coach_cnt_max freeform messages per game.
The length of these messages has to be less than say_coach_msg_size. If the game continues into
extended time,the online coaches are given an additional say_coach_cnt_max messages
to say every additional 6000 cycles (or whatever the normal length of a game is). Allowed
messages are cumulative, so if the coach does not use all its allowed messages, it can use
them in the extended time. The server will send (error said_too_many_messages) if the coach
tries to send messages after it reached the maximum number.
It should be noted that freeform-messages are not allowed in coach-competition-games,
and are only supported by CLang for compatibility reasons.
In the standard coach language there are three other types of messages: rule-, define-,
and delete-messages. To prevent coaches from micro-controlling every single action
of the players communication is restricted in the following ways.
Every 300 cycles (specified by clang_win_size) the coach can send one of each. Note that
the number of allowed messages can be changed by setting the clang_define_win, clang_del_win,
and clang_rule_win parameters (see Section B.1). The messages are heard by the players 50
(specified by clang_mess_delay) cycles later. If the play-mode is not `playon',
one (specified by clang_mess_per_cycle) message is sent to the players in each cycle,
even if the delay time has not elapsed. Messages that are sent while the play mode
is not `playon' do not count towards the message number restrictions. For example,
if the default values are used the coach can send one message per cycle during
breaks that will be heard by the players without delay. The server guarantees that
messages of each type will be sent to the players in the same order in which
they were received from the coach.
The language grammar developed below does not place restrictions on the length of
the messages which can be sent to the server. How ever, for very practical reasons,
any message in the standard language can not be longer than 8154 characters (this is so
the maximum message which should be sent to the player is 8K).
The first version of the coach language (Clang) was developped for server version 7.x.
For server version 8.x the language has been extended. Because of this, clients that want
to receive messages from their coach have to explicitly advise the server, which version of
CLang they support. This is done by sending
			(clang(ver MINMAX))
where MIN and MAX are unsigned integers denoting the earliest and latest supported version
of CLang, respectively. Clients that do not send such a message will not receive coach messages.
The server is able to determine the version number of coach messages and will filter out any
messages that are not supported by the player. If a message has been filtere dout, the players
will receive
			(clang(ver (PLAYERNAME)MINMAX))
The coach will receive a message for each player which informs it about the supported versions:
			(clang(ver (PLAYERNAME)MINMAX))
This means that you have to add the sending of (clang(ver 7 7)), if you use version 7 source code
of players with newer server versions. The standard coach language will be described in detail in Section 7.7.

-------------------------------------------------
Changing Player Types
-------------------------------------------------

Using the change_player_type-command (described in in Section 7.4) the online coach can change player types
unlimited times in `before_kick_off' play-mode. Of course these changes have to comply with the general
rules about heterogeneous players (see Section 4.6). After kick-of player types can be changed three (subs_max)
times during play-modes that are not `play-on'.
See the description of the change_player_type-command in Section 7.4 for details about the possible
replies from the server.
Note: A player client will be informed about substitutions that occurred before the client connected
by the message (change_player_type UNUMT YPE) for substitutions in it own team and (change_player_type UNUM)
for substitutions in the opponent team.

