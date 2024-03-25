
To start the server either type::

  ./rcssserver

from the directory containing the executable or::

  rcssserver

if you installed the executables in your PATH.

--------------------------------------------------
Configuration Files
--------------------------------------------------

rcssserver will look in your home directory for the configuration files:

* .rcssserver/server.conf
* .rcssserver/player.conf
* .rcssserver/CSVSaver.conf
* .rcssserver-landmark.xml

If .conf files do not exist, they will be created and populated with
default values.

You can include additional configuration files by using the ``include=FILE``
option to ``rcssserver``.

**TOOD**

- [8.01] landmark reader
- [13.0.0] RCSS_CONF_DIR


--------------------------------------------------
Recording Command Log
--------------------------------------------------

**TODO: description about .rcl file**

--------------------------------------------------
Automatic Mode
--------------------------------------------------

**TODO: [9.0.2]**

--------------------------------------------------
Anonyous Mode
--------------------------------------------------
Anonymous Mode,which was introduced in server version 16.0.0 allows the server
to hide team names from opponents. There are two parameters inside server.conf, which
allow each side's name to be set to a fixed string. If the parameter is empty, the
real name of the team will be reported to the opponent.

.. table:: Server parameters for Anonymous mode
   :name: tab-anonymous

   +-------------------------+-------------------------------------------------------------+
   |Parameter                |Description                                                  |
   +=========================+=============================================================+
   |server::fixed_teamname_l |Fixed name of the left team, which is sent to the right team.|
   |                         |Leave empty for real name.                                   |
   +-------------------------+-------------------------------------------------------------+
   |server::fixed_teamname_r |Fixed name of the left team, which is sent to the right team.|
   |                         |Leave empty for real name.                                   |
   +-------------------------+-------------------------------------------------------------+

--------------------------------------------------
Synchronous Mode
--------------------------------------------------

**TODO: [7.11] in ChangeLog**

--------------------------------------------------
Result Saver
--------------------------------------------------

**TODO**

- [9.4.0] StdOutSaver, MySQLSaver
- [9.4.3] CSVSaver

--------------------------------------------------
The Soccerserver Parameters
--------------------------------------------------

.. include:: soccerserver/using-soccerserver-server-conf.rst

.. include:: soccerserver/using-soccerserver-player-conf.rst

.. list-table:: Parameters adjustable in ``CSVSaver.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``CSVSaver.conf``
     - Description
   * - version
     -
     - soccer server version
   * - save
     - false
     - flag to save matches result in a file
   * - filename
     - 'rcssserver.csv'
     - file to save the results to. If this file does not exist it will be created. Otherwise, the results will be appended to the end.
