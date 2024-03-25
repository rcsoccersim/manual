
.. _sec-attentiontomodel:

--------------------------------------------------
Attentionto Model
--------------------------------------------------

Version 8 and above players can send ``attentionto`` commands to focus their attention on a particular player.
The command has the form:

  (attentionto <TEAM> <UNUM>) | (attentionto off)

Where ``<TEAM>`` is

  ``opp`` | ``our`` | ``l`` | ``r`` | ``left`` | ``right`` | <TEAM_NAME>

and ``<UNUM>`` is integer identifying a member of the team specified.
Players can only focus on one player at a time (each attentionto command
overrides the previous) and cannot focus on themselves.

See :ref:`sec-sensormodels` in detail about the aural sensor.
