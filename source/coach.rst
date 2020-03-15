.. -*- coding: utf-8; -*-

=================================================
Coach
=================================================
7.1 Introduction
One of the most important parts of soccer 2D is coach which is placed in src file of the server. The reason of the importance of the coach is that the data which it receives from the server are more accurate, they are not dependent on player vision and they are always up to date.

7.2 On-line and off-line coach distinction
In general, the trainer (off-line coach) can exercise more control over the game and may be used only in the development stage, whereas the online coach may connect to official games. 
Off-line coach capabilities: 

1. It can control the play-mode. 

2. It can broadcast audio messages. Such a message can consist of a command or some information intended for one or more of the player-clients. Its syntax and interpretation are user-defined.

3. It can move the players and the ball to any location on the field and set their directions and velocities.
 
4. It can get noise-free information about the movable objects.

 
Online coach capabilities:
 
1. It can communicate with the players.
 
2. It can get noise-free information about the movable object 

In conclusion it must said that the trainer use is better to be in automated learning or game managing whereas the coach usage is better during the game and for advising the players, for applying machine learning methods and also game analyzing.

7.3 commands

The following section includes both coach and trainer command. The ones which can only use for trainer tagged with T, the coach commands with C and the ones that can be used for both are tagged with B.
disable()
setSenders()
sendInit()
sendOKEye()
send()
parseMsg()
parse_command() :
start
move
look
team_names
recover
check_ball
say
ear
eye
change_player_type
compression
sendExternalMsg()
send_visual_info()
check_ball()

awardFreeformMessageCount()
canSendFreeform()
sendPlayerClangVer()
check_message_queue()
team_graphic()
update_messages_left()
7.4 Syntax
The complete grammar of the standard coach language is shown in the below chart. Please note that the symbol ^ refers to ones which are not available in the older version of the server and the ones which are marked with ! have been deprecated.
The new grammar is a follows:

<MESSAGE> : <ADVICE_MESS>
          | <INFO_MESS>
          | <RULE_MESS>^
          | <META_MESS>
          | <DEFINE_MESS>
          | <FREEFORM_MESS>

<ADVICE_MESS> : (advice <RULE_LIST>)^
              | (advice <TOKEN_LIST>)!
<INFO_MESS> : (info <RULE_LIST>)^
            | (info <TOKEN_LIST>)!

<TOKEN_LIST> : <TOKEN_LIST>! <TOKEN>! | <TOKEN>!

<TOKEN> : (<INT> <CONDITION> <DIRECTIVE_LIST>)!
	      | (clear)!

<RULE_MESS> : (delete_rule <ID_LIST>)^
            | (ruleset_on <NAME_LIST>)^
            | (ruleset_off <NAME_LIST>)^
# definition of a ruleset is in the define message

<RULE> : (norm <ID> <CONDITION> <DIRECTIVE_LIST>)^
       | (shared <ID> <CONDITION> <RULE_LIST>)^
       | (ruleset <NAME>) ^ # name of a ruleset
       | <NAME> ^ # name of a rule

<CONDITION> : (true) | (false)
            | (ppos <TEAM> <UNUM_SET> <INT> <INT> <REGION>)
            | (bpos <REGION>)
            | (bowner <TEAM> <UNUM_SET>)
            | (playm <PLAY_MODE>)
            | (and <CONDITION_LIST>)
            | (or <CONDITION_LIST>)
            | (not <CONDITION>)
            | (time <COMP> <INT>) ^
            | (opp_goals <COMP> <INT>) ^
            | (own_goals <COMP> <INT>) ^
            | (goal_diff <COMP> <INT>) ^
            | (<INT> <COMP> time) ^
            | (<INT> <COMP> opp_goals) ^
            | (<INT> <COMP> our_goals) ^
            | (<INT> <COMP> goal_diff) ^
            | (unum <UNUM> <UNUM_SET>) ^
            | <NAME>

<COMP> : < ^ | <= ^ | == ^ | != ^ | >= ^ | > ^ 

<ACTION> : (home <REGION>)
         | (pos <REGION>)
         | (pass <REGION>)^
         | (pass <UNUM_SET>)^
         | (dribble <REGION>)^
         | (clear <REGION>)^
         | (shoot)^
         | (hold)^
         | (intercept)^
         | (tackle <UNUM_SET>)^
         | (mark <UNUM>)
         | (markl <UNUM>)
         | (markl <REGION>)
         | (approach_ball)^
         | (htype <HET_TYPE>)
         | <NAME>
         | (bto <REGION> <BMOVE_SET>)! 
         | (bto <UNUM_SET>)!


<BMOVE_SET> : { <BMOVE_LIST> }!
<BMOVE_LIST> : <BMOVE_LIST> <BMOVE_TOKEN>! 
             | <BMOVE_TOKEN>!
<BMOVE_TOKEN> : p! | d! | c! | s! #pass dribble clear score; note 
                               # these do not need spaces between them


<DIRECTIVE> : (<MODE> <TEAM> <UNUM_SET> <ACTION_LIST>)^ # prevoiusly it was
                                                        # just one action
            | "STRING"

<MODE> : do | dont

#Meta messages
<META_MESS> : (meta <META_TOKEN_LIST>)!
<META_TOKEN_LIST> : <META_TOKEN_LIST>! <META_TOKEN>! | <META_TOKEN>!
<META_TOKEN> : (ver [int])!


#Define messages
<DEFINE_MESS> : (define <DEFINE_TOKEN_LIST>)

<DEFINE_TOKEN> : (definec <NAME> <CONDITION>)
| (defined <NAME> <DIRECTIVE>)
| (definer <NAME> <REGION>)
| (definea <NAME> <ACTION>)
| (definerset <NAME> <ID_LIST>)
| (definerule <NAME> <RULE>)

#Freeform messages
<FREEFORM_MESS> : (freeform "STRING")

#Regions
<REGION> : <POINT>
         | (null)
         | (tri <POINT> <POINT> <POINT>)^
         | (rec <POINT> <POINT>)^
         | (arc <POINT> [real] [real] [real] [real])
         | (reg <REGION_LIST>)
         | "STRING"
         | (quad <POINT> <POINT> <POINT> <POINT>)!

<POINT> : (pt [real] [real])
        | (pt ball)
        | (pt <TEAM> <UNUM>)
        | (<POINT_LIST>) ^
        | (pt [real] [real] <POINT>)!

<POINT_LIST> : <POINT_LIST> <ARITH> <POINT_LIST> ^
             | <POINT> ^

<ARITH> : + ^ | - ^ | * ^ | / ^

<PLAY_MODE> : bko | time_over | play_on
            | ko_our | ko_opp | ki_our | ki_opp 
            | fk_our | fk_opp
            | ck_our | ck_opp | gk_our | gk_opp 
            | gc_our | gc_opp
            | ag_our | ag_opp

<TEAM> : our | opp

7.5 Further resources
The CLang Corpus contains examples of actual CLang messages: http://www-2.cs.cmu.edu/˜ pfr/soccer/clang corpus.html 
The Multi-Agent Modeling Special Interest Group (MAMSIG) provides binaries and sources of coachable teams and online coaches: http://www.cl-ki.uni-osnabrueck.de/˜ tsteffen/mamsig 
The Coach-mailing-list discusses Clang details, competition rules, and coaching methods: http://robocup.biglist.com/coach-1/





