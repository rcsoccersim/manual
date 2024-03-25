--------------------------------------------------
Say Model
--------------------------------------------------

Using the *say command*, players can broadcast messages to other players. Messages can be say_msg_size characters long, where valid characters for say messages are from the set sth (without the square brackets). Messages players say can be heard within a distance of *audio_cut_dist* by members of both teams . **Say messages** sent to the server will be sent back to players within that distance immediately. The use of the *say command* is only restricted by the limited capacity of the players of hearing messages.

.. table:: Parameter for the say command

   +-------------------------------------------------+-----------+
   |Parameter in ``server.conf``                     | Value     |
   +=================================================+===========+
   |say_msg_size                                     |10         |
   +-------------------------------------------------+-----------+
   |audio_cut_dist                                   |50         |
   +-------------------------------------------------+-----------+
   |hear_max                                         |1          |
   +-------------------------------------------------+-----------+
   |hear_inc                                         |1          |
   +-------------------------------------------------+-----------+
   |hear_decay                                       |1          |
   +-------------------------------------------------+-----------+
