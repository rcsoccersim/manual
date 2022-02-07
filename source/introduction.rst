.. -*- coding: utf-8; -*-

**************************************************
Introduction
**************************************************


We are in the early days of RoboCup [Kitano95IJCAI]_, with half a
century to go before we can "...build a team of robot soccer players,
which can beat a human world cup champion team" [RoboCup97]_.
The challenge posed by the goal is enormous and inspires hundreds of
researchers yearly throughout the world to engage themselves and their
students in RoboCup.
RoboCup has been used as a research challenge in parallel with a usage
for educational purposes, and to stimulate the interest of the public
for robotics and artificial intelligence(AI).
Each year since 1997, researchers from different countries have
gathered to play the world cup.
The event has drawn an increasing amount of interest from the public,
as robotics is still not commonplace.

The intention of this manual [#f1]_ is to guide the developers of
simulated league teams in the beginning steps, and also serve as a
reference manual for the experienced users.

=================================================
Background
=================================================

Mackworth [Mackworth93]_ introduced the idea of using soccer-playing
robots in research. Unfortunately, the idea did not get the proper
response until the idea was further developed and adapted by Kitano,
Asada, and Kuniyoshi, when proposing a Japanese research programme,
called Robot J-League [#f2]_. During the autumn of 1993, several
American researchers took interest in the Robot J-League, and it
thereafter changed name to the Robot World Cup Initiative or RoboCup
for short. RoboCup is sometimes referred to as the RoboCup challenge
or the RoboCup domain.

In 1995, Kitano et al. [Kitano95IJCAI]_ proposed the first Robot World
Cup Soccer Games and Conferences to take place in 1997. The aim of
RoboCup was to present a new standard problem for AI and robotics,
somewhat jokingly described as the life of AI after Deep Blue [#f3]_
. RoboCup differs from previous research in AI by focusing on a
distributed solution instead of a centralised solution, and by
challenging researchers from not only traditionally AI-related fields,
but also researchers in the areas of robotics, sociology, real-time
mission critical systems, etc.

To co-ordinate the efforts of all researchers, the RoboCup Federation
was formed. The goal of RoboCup Federation is to promote RoboCup, for
example by annually arranging the world cup tournament. Members of the
RoboCup Federation are all active researchers in the field, and
represent a number of universities and major companies. As the body of
researchers is quite large and widespread, local committees are formed
to promote RoboCup-related events in their geographical area.


=================================================
The Goals of RoboCup
=================================================

The RoboCup Federation has set goals and a timetable for the
research. Setting goals and a timetable are means of pushing the
state-of-the-art further, in conjunction with formalised test-beds. In
resemblance with John F. Kennedy’s national goal of “landing a man
on the moon and returning him safely to earth” ([4], p. 8276), the
main accomplishment was not to land a man on the moon and returning
him safely, but the overall technological advancement. Therefore, the
most important goal of RoboCup is to advance the overall technological
level of society, and as a more pragmatic goal to achieve the
following:

    By mid-21st century, a team of fully autonomous humanoid robot soccer
    players shall win the soccer game, comply with the official rule of the FIFA 4 ,
    against the winner of the most recent World Cup.

There will be several technological advancements, even if the goal of
the robotic soccer team is not reached, starting with
Team-Partitioned, Opaque-Transition Reinforcement Learning (TPOT-RL)
[Stone98]_ which has found application in the domain of packet routing in
computer networks. TPOT-RL is a distributed learning method in domains
where “agents have limited information about environmental state
transitions” ([Stone98]_, p. 22).

In most RoboCup leagues, the teams consist of either robots or
programs that cooperate in order to defeat the opponent team. RoboCup
Rescue and the commentator exhibition diverge from the other RoboCup
leagues. The goal of defeating an opponent would raise ethical issues
in RoboCup Rescue, since we cannot assign comparable utilities to
human lives and buildings. Hence, the focus in RoboCup Rescue is on
the co-operative efforts between heterogeneous agents. In the
commentator exhibition, the goal is to observe and comment.

Besides the commentator exhibition and RoboCup Rescue, the main body
of the RoboCup challenge consists of several leagues for soccer
playing. However, as this manual is about the simulated league we will
only focus on it.


-------------------------------------------------
Simulated League
-------------------------------------------------

The RoboCup simulator league is based on the RoboCup simulator called
the soccer server [Noda97RoboCup97]_, a physical soccer simulation system.
All games are visualised by displaying the field of the simulator by the
soccer monitor on a computer screen. The soccer server is written to support
competition among multiple virtual soccer players in an uncertain
multi-agent environment, with real-time demands as well as
semi-structured conditions. One of the advantages of the soccer server
is the abstraction made, which relieves the researchers from having to
handle robot problems such as object recognition [9], communications,
and hardware issues, e.g., how to make a robot move. The abstraction
enables researchers to focus on higher level concepts such as
cooperation and learning.

Since the soccer server provides a challenging environment, i.e., the
intentions of the players cannot mechanically be deduced, there is a
need for a referee when playing a match. The included artificial
referee is only partially implemented and can detect trivial
situations, e.g., when a team scores. However, there are several
hard-to-detect situations in the soccer server, e.g., deadlocks, which
brings the need for a human referee. All participating teams are also
obliged to play according to a gentlemen’s agreement, e.g., not to
use loopholes.

Since the first version of the soccer server was completed in 1995,
there have been four world cups and one pre-world cup event, not to
mention all other RoboCup-related events. The 1996 pre-RoboCup event
[PreRoboCup96]_ was held in Osaka, with only seven entrants in the
competition which ended with a Japanese victory by the team Ogalets from
Tokyo University. In Nagoya the following year, the first formal competition
was held in conjunction with the IJCAI’97 conference. The competition
had 29 teams participating, and the winner was AT Humboldt [Burkhard97]_.
The RoboCup world cup of 1998 was played in conjunction with the human
world cup in Paris, and the winning team was CMUnited98 [CMUnited98]_.
During the world cup, media was heavily covering the event, as it was public
in a museum in the suburbs of Paris. The year after, the world cup was
held in conjunction with IJCAI’99 in Stockholm, and the winners (once
again) were CMUnited99 [CMUnited99]_. An unchanged version of the champion
team must participate, as a benchmark, in the next world cup. The
benchmarking teams have always been able to win their group, but only
in 2000 did the benchmark team advance further than the first game
after group play.

-------------------------------------------------
What is the Soccerserver
-------------------------------------------------

Soccer Server is a system that enables autonomous agents consisting of
programs written in various languages to play a match of soccer
(association football) against each other.

A match is carried out in a client/server style: A server,
soccerserver, provides a virtual field and simulates all movements of
a ball and players. Each client controls movements of one
player. Communication between the server and each client is done via
UDP/IP sockets. Therefore users can use any kind of programing systems
that have UDP/IP facilities.

The soccerserver consists of 2 programs, soccerserver and
soccermonitor. Soccer Server is a server program that simulates
movements of a ball and players, communicates with clients, and
controls a game according to rules. Soccermonitor is a program that
displays the virtual field from the soccerserver on the monitor using
the X window system. A number of soccermonitor programs can connect
with one soccerserver, so we can display field-windows on multiple displays.

A client connects with soccerserver by an UDP socket. Using the
socket, the client sends commands to control a player of the client
and receives information from sensors of the player. In other words, a
client program is a brain of the player: The client receives visual
and auditory sensor information from the server, and sends
control-commands to the server.

Each client can control only one player 56 . So a team consists of the
same number of clients as players. Communications between the clients
must be done via soccerserver using say and hear protocols. (See
section :ref:`sec-playercommmandprotocol`.) One of the purposes of soccerserver
is evaluation of multi-agent systems, in which efficiency of communication between
agents is one of the criteria. Users must realize control of multiple
clients by such restricted communication.


=================================================
History
=================================================

In this section we will first describe the history of the soccerserver
and thereafter the history of the RoboCup Simulation League. To end
the section we will also describe the history of the manual effort.

-------------------------------------------------
History of the Soccer Server
-------------------------------------------------

The first, preliminary, original system of soccerserver was written in
September of 1993 by Itsuki Noda, ETL. This system was built as a
library module for demonstration of a programming language called MWP,
a kind of Prolog system that has multi-threads and high level program
manipulation. The module was a closed system and displayed a field on
a character display, that is VT100.

The first version (version 0) of the client-server style server was
written in July of 1994 on a LISP system. The server shows the field
on an X window system, but each player was shown in an alphabet
character. It used the TCP/IP protocol for connections with
clients. This LISP version of soccerserver became the original style
of the current soccerserver. Therefore, the current soccerserver uses
S-expressions for the protocol between clients and the server.

The LISP version of soccerserver was re-written in C++ in August of
1995 (version 1). This version was announced at the IJCAI workshop on
Entertainment and AI/Alife held in Montreal, Canada, August 1995.

The development of version 2 started January of 1996 in order to
provide the official server of preRoboCup-96 held at Osaka, Japan,
November 1996. From this version, the system is divided into two
modules, soccerserver and soccerdisplay (currently,
soccermonitor). Moreover, the feature of coach mode was introduced
into the system. These two features enabled researchers on machine
learning to execute games automatically. Peter Stone at Carnegie
Mellon University joined the decision-making process for the
development of the soccerserver at this stage. For example, he created
the configuration files that were used at preRoboCup-96.

After preRoboCup-96, the development of the official server for the
first RoboCup, RoboCup-97 held at Nagoya, Japan, August 1997, started
immediately, and the version 3 was announced in February
of 1997. Simon Ch’ng at RMIT joined decisions of regulations of
soccerserver from this stage. The following features were added into
the new version:

* logplayer
* information about movement of seen objects in visual information
* capacity of hearing messages

The development of version 4 started after RoboCup-97, and announced
November 1997. From this version, the regulations are discussed on the
mailing list organized by Gal Kaminka. As a result, many contributers
joined the development. Version 4 had the following new features:

* more realistic stamina model
* goalie
* handling offside rule
* disabling players for evaluation
* facing direction of players in visual information
* sense body command

Version 4 was used in JapanOpen 98, RoboCup98 and Pacific Rim
Series 98.

Version 5 was used in JapanOpen 99, and will also be used in
RoboCup99 in Stockholm during the summer of 1999.

In Melbourne 2000, version 6 was used, and for the world cup in 2001
version 7 will be used.

-------------------------------------------------
History of the RoboCup Simulation League
-------------------------------------------------

The RoboCup simulation league has had 26 main official events:
Starting with a preRoboCup96 in 1996 event, from 1997 onward an official 
world championship tournament was held each year (from RoboCup97 to 
RoboCup 2021). 
Research results have been reported extensively in the
proceedings of the workshops and conferences associated with these
competitions. In this section, we focus mainly on the competitions
themselves.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
preRoboCup96
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

preRoboCup96 was the first robotic soccer competition of any sort. It
was held on November 5–7, 1996 in Osaka, Japan [5]. In conjunction
with the IROS-96 conference, preRoboCup96 was meant as an informal,
small-scale competition to test the RoboCup soccerserver in
preparation for RoboCup97. 5 of the 7 entrants were from the Tokyo
region. The other 2 were from Ch'ng at RMIT and Stone and Veloso from
CMU. The winning teams were entered by:

1. Ogawara (Tokyo University)
2. Sekine (Tokyo Institute of Technology)
3. Inoue (Waseda University)
4. Stone and Veloso (Carnegie Mellon University)

In this tournament, team strategies were generally quite
straightforward. Most of the teams kept players in fixed locations,
only moving them towards the ball when it was nearby.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
RoboCup97
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The RoboCup97 simulator competition was the first formal simulated
robotic soccer competition. It was held on August 23–29, 1997 in
Nagoya, Japan in conjunction with the IJCAI-97 conference [6]. With 29
teams entering from all around the world, it was a very successful
tournament. The winning teams were entered by:

1. Burkhard et al. (Humboldt University)
2. Andou (Tokyo Institute of Technology)
3. Tambe et al. (ISI/University of Southern California)
4. Stone and Veloso (Carnegie Mellon University)

In this competition, the champion team exhibited clearly superior
low-level skills. One of its main advantages in this regard was its
ability to kick the ball harder than any other team. Its players did
so by kicking the ball around themselves, continually increasing its
velocity so that it ended up moving towards the goal faster than was
imagined possible. Since the soccerserver did not (at that time)
enforce a maximum ball speed, a property that was changed immediately
after the competition, the ball could move arbitrarily fast, making it
almost impossible to stop. With this advantage at the low-level
behavior level, no team, regardless of how strategically
sophisticated, was able to defeat the eventual champion.

At RoboCup97, the RoboCup scientific challenge award was
introduced. Its purpose is to recognize scientific research results
regardless of performance in the competitions. The 1997 award went to
Sean Luke [10] of the University of Maryland "for demonstrating the
utility of evolutionary approach by co-evolving soccer teams in the
simulator league."


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
RoboCup98
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The second international RoboCup championship, RoboCup-98, was held on
July 2–9, 1998 in Paris, France [1]. It was held in conjunction with
the ICMAS-98 conference.

The winning teams were entered by:

1. Stone et al. (Carnegie Mellon University)
2. Burkhard et al. (Humboldt University)
3. Corten and Rondema (University of Amsterdam)
4. Tambe et al. (ISI/University of Southern California)


Unlike in the previous year's competition, there was no team that
exhibited a clear superiority in terms of low-level agent
skills. Games among the top three teams were all quite closely
contested with the differences being most noticeable at the strategic,
team levels.

One interesting result at this competition was that the previous
year's champion team competed with minimal modifications and finished
roughly in the middle of the final standings. Thus, there was evidence
that as a whole, the field of entries was much stronger than during
the previous year: roughly half the teams could beat the previous
champion.

The 1998 scientific challenge award was shared by Electro Technical
Laboratory (ETL), Sony Computer Science Laboratories, Inc., and German
Research Center for Artificial Intelligence GmbH (DFKI) for
"development of fully automatic commentator systems for RoboCup
simulator league."

To encourage the transfer of results from RoboCup to the scientific
community at large, RoboCup98 was the first to host the Multi-Agent
Scientific Evaluation Session. 13 different teams participated in the
session, in which their adaptability to loss of team-members was
evaluated comparatively. Each team was played against the same fixed
opponent (the 1997 winner, AT Humboldt’97) four half-games under
official RoboCup rules. The first half-game (phase A) served as a
base-line. In the other three half- games (phases B-D), 3 players were
disabled incrementally: A randomly chosen player, a player chosen by
the representative of the fixed opponent to maximize "damage" to the
evaluated team, and the goalie. The idea is that a more adaptive team
would be able to respond better to these.

Very early on, even during the session itself, it became clear that
while in fact most participants agreed intuitively with the evaluation
protocol, it wasn’t clear how to quantitatively, or even
qualitatively, analyse the data. The most obvious measure of the
goal-difference at the end of each half may not be sufficient: some
teams seem to do better with less players, some do worse. Performance,
as measured by the goal-difference, really varied not only from team
to team, but also for the same team between phases. The evaluation
methodology itself and analysis of the results became open research
problems in themselves. To facilitate this line of research, the data
from the evaluation was made public at: http://www.isi.edu/~galk/Eval/


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
RoboCup99
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The third international RoboCup championship, RoboCup-99, was held in
late July and early August, 1999 in Stockholm, Sweden [3]. It was held
in conjunction with the IJCAI-99 conference.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
RoboCup2000
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The fourth international RoboCup championship, RoboCup 2000, was held
in early September, 2000 in Melbourne, Australia [16]. It was held in
conjunction with the PRICAI-2000 conference.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
RoboCup 2004
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The eigth international RoboCup championship, RoboCup 2004, was held
in Lisbon, Portugal. It was accompanied by the RoboCup 2004 Symposium,
held at the Instituto Superior Tecnico and was co-located with the 
5th IFAC/EURON International Symposium on Intelligent Autonomous
Vehicles (IAV 2004).

The main novelty in the Soccer Simulation League in 2004 was the 
introduction of the 3D soccer simulator, where players are spheres
in a three-dimensional environment with a full physical model. This 
sub-competition was the spawning point for the Soccer Simulation 
3D League in later years.

The winning teams in the Soccer Simulation 2D competition,
for which 24 teams were qualified, were:

1. STEP (ElectroPult Plant Company, Russia)
2. Brainstormers (University of Osnabrueck, Germany)
3. Mersad (Allameh Helli High School, Iran)

The winning teams in the coach competition were:

1. MRL (Azad University of Qazvin, Iran)
2. FC Portugal (Universities of Porto and Aveiro, Portugal)
3. Caspian (Iran University of Science and Technology, Iran)

The winning teams in the 3D competition were:

1. Aria (Amirkabir University of Technology, Iran)
2. AT-Humboldt (Humboldt University Berlin, Germany)
3. UTUtd 2004 (University of Tehran, Iran)


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
RoboCup 2005
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The tenth international RoboCup championship, RoboCup 2005, was held
in July 2005 in Osaka, Japan [16]. It was accompanied
by the RoboCup Symposium.
Since, for the first time, the 3D sub-league of soccer simulation
had its own tournament, the number of teams that were maximally 
allowed to qualify for the Soccer Simulation 2D competitions at
RoboCup 2005 was reduced to 16 (though a 
17th team was permitted for reasons of the qualifying procedure).

The winning teams in the Soccer Simulation 2D competition were:

1. Brainstormers (University of Osnabrueck, Germany)
2. WrightEagle (University of Science and Technology of China, China)
3. TokyoTech SFC (Tokyo Institute of Technology, Japan)

An interesting observation, quite similar to the related remark 
for RoboCup98, could be made in this year: Last year's champion
(STEP, Russia) entered the competition without any modifications
made to their team and finished the tournament on rank 4. 


---------------------------------------------------
History of the Soccer Manual Effort
---------------------------------------------------

The first versions of the manual were written by Itsuki Noda, while
developing the soccerserver, and around version 3.00 there were
several requests on an updated manual, to better correspond to the
server as well as enable newcomers to more easily participate in the
RoboCup World Cup Initiative. In the fall of 1998 Peter Stone
initiated the Soccer Manual Effort, over which Johan Kummeneje took
responsibility to organize and as a result the Soccer Server Manual
version 4.0 was released on the 1st of November 1998.

In 1999, the manual for the soccerserver version 5.0 was
released. Unfortunately the manual lost part of its pace, and there
was no release of the manual for soccerserver version 6.0.

Since 1999, the soccerserver has changed major version to 7 and is
continuously developed. Therefore the Soccer Manual Effort has
developed a new version, which resulted in a PDF version of the 
Soccer Manual (available on Sourceforge) that has been the main 
reference document for many years.

In 2009 and 2010 (soccerserver versions 12 and 14), significant changes 
were introduced to the way the soccerserver simulates soccer, including 
a changed tackle model and a sideward dash model to mention just a few.
The corresponding changes of those times were, unfortunately, not 
incorporated into the existing soccerserver manual, but were reflected
only in the NEWS text file as part of the soccerserver software
package. 

In 2019, a joint effort was started to migrate the existing 
Latex-based soccerserver manual to the Github-hosted version 
that is based on reStructured text and that you are reading here.


=================================================
About This Manual
=================================================

This manual is the joint effort of the authors from a diverse range of
universities and organizations, which build upon the original work of
Itsuki Noda. If there are errors, inconsistencies, or oddities, please
notify johank@dsv.su.se or fruit@uni-koblenz.de with the location of
the error and a suggestion of how it should be corrected.

We are always looking for anyone who has an idea on how to improve the
manual, as well as proofread or (re)write a section of the manual. If
you have any ideas, or feel that you can contribute with anything to
the SoccerServer Manual Effort.
.. please mail johank@dsv.su.se or fruit@uni-koblenz.de.

.. The latest manual can be downloaded at http://www.dsv.su.se/~johank/RoboCup/manual.



=================================================
Reader's Guide to the Manual
=================================================

The thesis is written for a wide range of readers, and therefore the
chapters are not equally important to all readers. We shortly describe
the remaining chapters to give an overview of the thesis.

*Chapter 2* introduces the concepts of the simulated league and will
help the newcomer to get to terms with the different parts.

*Chapter 3* helps the beginners to start compiling and running the software.

*Chapter 4* describes the soccerserver.

*Chapter 5* describes the soccermonitor.

*Chapter 6* describes the soccerclient and how to create one.

*Chapter 7* describes the coachclient.

*Chapter 8* suggests some further reading.



----

.. [#f1] Parts of this chapter is taken directly from [Kummeneje01PhL]_
.. [#f2] The J-League is the professional soccer league in Japan.
.. [#f3] In reference to Deep Blue and its games with Kasparov, see http://www.chess.ibm.com.
