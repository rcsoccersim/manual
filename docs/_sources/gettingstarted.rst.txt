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


=================================================
Getting and installing the server
=================================================

The procedure shown was performed on a computer running SuSE
7.3-GNU/Linux 2.4.10-4GB (check your version with \texttt{uname -sr})
with gcc 2.95.3 and gcc 3.2 (check your version with \texttt{which
  g++}) but any reasonably up-to-date installation should work. In the
commands shown below, \texttt{->} is supposed to be the command-line
prompt.

Get tar.gz files for the version you are after from the Simulator's code
repository:

* rcssserver performs the actual simulation. https://github.com/rcsoccersim/rcssserver/releases
* rcssmonitor allows you to watch game in progress. https://github.com/rcsoccersim/rcssmonitor/releases
* rcsslogplayer allows you to replay logs (\*.rcg files) created by rcssserver. https://github.com/rcsoccersim/rcsslogplayer/releases

At the time of this writing (Oct-12-2018) the latest version is 15.5.0
and will be used in the example below. Please substitute 15.5.0 for the
latest version available.
