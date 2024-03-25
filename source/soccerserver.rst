.. -*- coding: utf-8; -*-

Last update: |today|

.. _cha-soccerserver:

*************************************************
Soccer Server
*************************************************


==================================================
Objects
==================================================

.. figure:: ./images/objects.*
  :align: center
  :scale: 80%
  :name: objects

  UML diagram of the objects in the simulation

==================================================
Protocols
==================================================

.. include:: soccerserver/protocols.rst


.. _sec-sensormodels:

==================================================
Sensor Models
==================================================

.. include:: soccerserver/sensor-models.rst


.. _sec-movementmodels:

==================================================
Movement Models
==================================================

.. include:: soccerserver/movement-models.rst


==================================================
Action Models
==================================================

.. include:: soccerserver/action-models-catch-model.rst

.. include:: soccerserver/action-models-dash-model.rst

.. include:: soccerserver/action-models-kick-model.rst

.. include:: soccerserver/action-models-move-model.rst

.. include:: soccerserver/action-models-say-model.rst

.. include:: soccerserver/action-models-tackle-model.rst

--------------------------------------------------
Foul Model
--------------------------------------------------

**TODO**

- [14.0.0] foul model and intentional foul option
- [14.0.0] trade off between foul detect probability and kick power rate
- [15.0.0] improve foul model (red_card_probability)

.. include:: soccerserver/action-models-turn-model.rst

.. include:: soccerserver/action-models-turnneck-model.rst

.. include:: soccerserver/action-models-change-focus-model.rst

.. include:: soccerserver/action-models-pointto-model.rst

.. include:: soccerserver/action-models-attentionto-model.rst


.. _sec-heterogeneousplayers:

==================================================
Heterogeneous Players
==================================================

.. include:: soccerserver/heterogeneous-players.rst

==================================================
Referee Model
==================================================

.. include:: soccerserver/referee-model.rst

==================================================
The Soccer Simulation
==================================================

.. include:: soccerserver/the-soccer-simulation.rst

==================================================
Using Soccerserver
==================================================

.. include:: soccerserver/using-soccerserver.rst
