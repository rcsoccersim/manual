.. _sec-turnneckmodel:

--------------------------------------------------
TurnNeck Model
--------------------------------------------------

With *turn_neck*, a player can turn its neck somewhat independently of
its body.
The angle of the head of the player is the viewing angle of the
player.
The *turn command* changes the angle of the body of the player while
turn_neck changes the neck angle of the player relative to its body.
The **minimum** and **maximum** relative angle for the player’s neck
are given by **server::minneckang** and **server::maxneckang** in
server.conf.
Remember that the neck angle is relative to the body of the player so
if the client issues a *turn command*, the viewing angle changes even
if no turn_neck command was issued.
Also, *turn_neck commands* can be executed during the same cycle as
turn, dash, and *kick commands*.
turn_neck is not affected by momentum like turn is.
The argument for a *turn_neck command* must be in the range between
**server::minneckmoment** and **server::maxneckmoment**.

.. table:: Parameter for the turn neck command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |minneckang                                       | -90       |
   +-------------------------------------------------+-----------+
   |maxneckang                                       |  90       |
   +-------------------------------------------------+-----------+
   |minneckmoment                                    | -180      |
   +-------------------------------------------------+-----------+
   |maxneckmoment                                    |  180      |
   +-------------------------------------------------+-----------+
