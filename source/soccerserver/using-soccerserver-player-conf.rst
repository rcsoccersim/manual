
.. list-table:: Parameters adjustable in ``player.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``player.conf``
     - Description
   * - version
     -
     - soccer server version
   * - player_types
     - 18
     - number of random player types generated at match startup
   * - pt_max
     - 1
     - number of times that online coach can substitute a player to another player of the same type
   * - random_seed
     - -1
     - seed to generate heterogeneous players parameters of a match if it is non zero
   * - subs_max
     - 3
     - maximum number of substitutions in a match
   * - allow_mult_default_type
     - false
     -
   * - catchable_area_l_stretch_max
     - 1.3
     - defines the upper bound of player's catchable_area_l_stretch
   * - catchable_area_l_stretch_min
     - 1
     - defines the lower bound of player's catchable_area_l_stretch
   * - dash_power_rate_delta_max
     - 0
     - defines the upper bound of player's dash power rate when added to default dash power rate
   * - dash_power_rate_delta_min
     - 0
     - defines the lower bound of player's dash power rate when added to default dash power rate
   * - effort_max_delta_factor
     - -0.004
     - controls the upper bound of player's effort amount
   * - effort_min_delta_factor
     - -0.004
     - controls the lower bound of player's effort amount
   * - extra_stamina_delta_max
     - 50
     - defines the upper bound of player's extra stamina when added to default extra stamina
   * - extra_stamina_delta_min
     - 0
     - defines the lower bound of player's extra stamina when added to default extra stamina
   * - foul_detect_probability_delta_factor
     - 0
     - defines the range of heterogeneous player's foul detect probability
   * - inertia_moment_delta_factor
     - 25
     - factor to control the length of inertia moment delta interval
   * - kick_power_rate_delta_max
     - 0
     - defines the upper bound of player's kick power rate when added to default kick power rate
   * - kick_power_rate_delta_min
     - 0
     - defines the lower bound of player's kick power rate when added to default kick power rate
   * - kick_rand_delta_factor
     - 1
     -
   * - kickable_margin_delta_max
     - 0.1
     - defines the upper bound of player's kickable margin when added to default kickable margin
   * - kickable_margin_delta_min
     - -0.1
     - defines the lower bound of player's kickable margin when added to default kickable margin
   * - new_dash_power_rate_delta_max
     - 0.0008
     -
   * - new_dash_power_rate_delta_min
     - -0.0012
     -
   * - new_stamina_inc_max_delta_factor
     - -6000
     -
   * - player_decay_delta_max
     - 0.1
     - defines the upper bound of inertia moment delta when multiplied by inertia moment delta factor
   * - player_decay_delta_min
     - -0.1
     - defines the lower bound of inertia moment delta when multiplied by inertia moment delta factor
   * - player_size_delta_factor
     - -100
     - controls the range of heterogeneous player's size
   * - player_speed_max_delta_max
     - 0
     - defines the upper bound of player's maximum speed when added to server::player_speed_max
   * - player_speed_max_delta_min
     - 0
     - defines the lower bound of player's maximum speed when added to server::player_speed_max
   * - stamina_inc_max_delta_factor
     - 0
     -
