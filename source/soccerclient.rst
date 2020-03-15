.. -*- coding: utf-8; -*-

*************************************************
6 Soccer Client
*************************************************

In this section, we’re going to talk about the client and its useful commands. After explaining the exact concept of client and server, we will describe the connection between the server and the player in sec. 6.1.
Client’s concept in computer science is defined as a transmitter that sends information and requests to Server.
The server generally is responsible to process the client’s information or act as a network interface and it’s a center for saving all information.
Simulations we have are based on the connection between client and server.
At the beginning of each game players have to make connections with the server so they can send their commands.
Players can send their commands to the server in cycles (which are 0.01 seconds) repeatedly (Each game will take 6000 cycles)

=================================================
6.1 Protocols
=================================================

In a simple word, a protocol is a standard set of rules that allow different devices to communicate with each other (refer to sec. 4.2 for more information).
In the following, each control and communication command which were used in the server protocols will be described.

=================================================
6.1.2 Control Commands
=================================================

During each cycle of the game, some action commands will be sent to the server by each player, which causes the server to simulate the next cycle using the previous cycle’s data and received commands.

-------------------------------------------------
Body Commands
-------------------------------------------------

Each action that players can do is based on some commands known as body commands that we’re going to mention more details about each of them specifically on below.
What result would each of these commands cause is dependent on many simulation factors which you can find execution details of them in Soccer Server Section. 

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Turn Moment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Will change the player’s body direction degrees by a degree from 
-180 to 180

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Dash Power
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command accelerates the player in the direction of its body (not direction of the current speed). The Power is between “minpower” and “maxpower” (which will be an Interval of -100 and 100).

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Kick Power Direction(Goalie special command)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Accelerates the ball with the given Power in the given Direction. The direction is relative to the Direction of the body of the player and the power is again between “minpower” and “maxpower”.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Catch Direction
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Goalie special command: Tries to catch the ball in the given Direction relative to its body direction. If the catch is successful the ball will be in the goalie's hand until kicked away.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Move X Y
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command will move the player to position of X and Y in one cycle for two situations of before kick off and after a goal and is useful for arrangements. (X can be between -54 and 54, Y can be between -32 and 32)

\*In each simulation cycle only one of the above body commands can be executed and if server receive more than one command only one of them will be executed (usually the first received one)

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Turn Neck Angle
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command can be sent (and will be executed) each cycle independently, along with other action commands. The neck will rotate with the given Angle relative to previous Angle. Note that the resulting neck angle will be between minneckang (default: -90) and maxneckang (default: 90) relative to the player's body direction.

-------------------------------------------------
Communication Commands
-------------------------------------------------

The only way of communication between two players is broadcasting of messages through the say command and hearing through the hear sensor.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
say Message
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command broadcasts the Message through the field, and any player near enough (specified with audio cut dist, default: 50:0 meters), with enough hearing capacity will hear the Message. The message is a string of valid characters.
After using say Message there will be two different possible responses which you could get from the server:
	``ok say``:
		Command succeeded 
	``error illegal command form``:
		This will appear in case of error

=================================================
Misc. Commands
=================================================

-------------------------------------------------
Data Request Commands
-------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Sense body
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Requests the server to send sense body information. Note the server sends sense body information every cycle if you connect with version 6:00 or higher.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Score
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Request the server to send score information. The server's reply will be in this format:
``“score Time OurScore OpponentScore”``

-------------------------------------------------
Mode Change Commands
-------------------------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Change view Width Quality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Changes the view parameters of the player. Width is one of narrow, normal or wide and Quality is one of high or low. The amount and detail of the information returned by the visual sensor depend on the width of the view and the quality. But note that the frequency of sending information also depends on these parameters
(e.g. if you change the quality from high to low, the frequency doubles, and the time between two see sensors will be cut to half).

=================================================
6.1.3 Sensor Information
=================================================

Sensor information is the messages that are sent to all players regularly (e.g. each cycle or each one and half a cycle). There is no need to send any request to the server to get this information. It’s just like the data that the five senses give the player in the real world. 
All returned information of the sensors have a time label, indication the cycle number of the game when the data has been sent (indicated by Time). This time is very useful.

-------------------------------------------------
Class Visual Sensor
-------------------------------------------------

The visual sensor is one of the most important sensors, which returns information to the players, including some details about the objects such as the ball and the players that can be seen from the player’s view. There is a limited amount for the view angle and the distance between the player and object (The current default values are 150 milli-seconds and 90 degrees) but the visual information only includes relative locations of landmarks and moving objects on the field, so the client needs to parse the given data after receiving information.

-------------------------------------------------
Class Audio Sensor
-------------------------------------------------

The online coach, referee and other players can send messages which can be heard through the field. The audio sensor returns these messages. There are three senders :
	1) self: when You are the sender
	2) referee: when the referee of the game sends the message
	3) online-coach- or online-coach-r
Whenever a player other than you sends a message its relative direction is returned instead.
::
		AudioSensor::AudioSensor()
		: M_teammate_message_time( -1, 0 )
		, M_opponent_message_time( -1, 0 )
		, M_freeform_message_time( -1, 0 )
		, M_trainer_message_time( -1, 0 )
		{
		M_freeform_message.reserve( 256 );
		}

-------------------------------------------------
Class body Sensor
-------------------------------------------------

The body sensor reports the physical information of the player; like the head angle, remaining stamina, current speed, etc. at the beginning of every cycle these body information get updated.
Default sense body information messages in version 8.0 and more::  
	GameTime M_time; //!< updated game time
	ViewQuality M_view_quality; //!< sensed view quality
	ViewWidth M_view_width; //!< sensed view width
	double M_stamina; //!< sensed stamina value
	double M_effort; //!< sensed effort value
	double M_stamina_capacity; //!< sensed stamina capacity
	double M_speed_mag; //!< sensed speed magnitude. this is quantized by 0.01.
	double M_speed_dir_relative; //!< speed dir. this is relative to face angle.
	double M_neck_relative; //!< neck angle. this is relative to body angle
	int M_kick_count; //!< sensed command count
	int M_dash_count; //!< sensed command count
	int M_turn_count; //!< sensed command count
	int M_say_count; //!< sensed command count
	int M_turn_neck_count; //!< sensed command count
	int M_catch_count; //!< sensed command count
	int M_move_count; //!< sensed command count
	int M_change_view_count; //!< sensed command count


