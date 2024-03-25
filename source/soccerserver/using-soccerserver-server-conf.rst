
.. list-table:: Parameters adjustable in ``server.conf``
   :widths: 100 30 100
   :header-rows: 1

   * - Name
     - Current Value in ``server.conf``
     - Description
   * - version
     - VERSION
     - soccer server version
   * - catch_ban_cycle
     - 5
     - goalies cannot execute the next catch until this cycle has passed after the successful catch.
   * - clang_win_size
     - 300
     - time window which controls how many messages can be sent (coach language)
   * - clang_advice_win
     - 1
     - number of advice messages per window
   * - clang_define_win
     - 1
     - number of define messages per window
   * - clang_del_win
     - 1
     - number of del messages per window
   * - clang_info_win
     - 1
     - number of info messages per window
   * - clang_mess_delay
     - 50
     - delay between receipt of message and send to players
   * - clang_mess_per_cycle
     - 1
     - maximum number of coach messages sent per cycle
   * - clang_meta_win
     - 1
     - number of meta messages per window
   * - clang_rule_win
     - 1
     - number of rule messages per window
   * - clang_win_size
     - 1
     - The length of clang message window
   * - coach_port
     - 6001
     - (offine) coach port
   * - connect_wait
     - 300
     - maximum cycle to wait for client connections in automatic mode
   * - drop_ball_time
     - 100
     - number of cycles to wait until dropping the ball automatically
   * - extra_half_time
     - 100
     - length of a half time of extra halves in seconds
   * - foul_cycles
     - 5
     - idle cycles of foul charged players
   * - freeform_send_period
     - 20
     - online coaches can send a freeform message during this period after the waiting period
   * - freeform_wait_period
     - 600
     - online coaches can send a freeform message after waiting this period
   * - game_log_compression
     - 0
     - compression level of game log file
   * - game_log_version
     - 5
     - version of game log format
   * - game_over_wait
     - 100
     - maximum cycle to wait for server termination in automatic mode
   * - goalie_max_moves
     - 2
     - goalie max. moves after a catch
   * - half_time
     - 300
     - length of a half time in seconds
   * - hear_decay
     - 1
     - value that reduces the auditory capacity when receiving an auditory message
   * - hear_inc
     - 1
     - value that increases the auditory capacity when the game cycle is updated
   * - hear_max
     - 1
     - maximum value of audiotory capacity
   * - illegal_defense_duration
     - 20
     - threshold count to detect illegal defense behavior
   * - illegal_defense_number
     - 0
     - number of players judged to be illegal illegal defense behavior
   * - keepaway_start
     - -1
     - automatic referee changes playmode to play_on after this
	   seconds elapsed
   * - kick_off_wait
     - 100
     - maximum cycle to wait kick-off in automatic mode
   * - max_goal_kicks
     - 3
     - (actually no effect)
   * - max_monitors
     - -1
     - max number of monitor connections
   * - nr_extra_halfs
     - 2
     - number of extra halves in a game
   * - nr_normal_halfs
     - 2
     - number of normal halves in a game
   * - olcoach_port
     - 6002
     - online coach port
   * - pen_before_setup_wait
     - 10
     - max waiting cycles in penalty_miss_[lr] or penalty_score_[lr]
   * - pen_max_extra_kicks
     - 5
     - max extra kick trials in penalty shootouts
   * - pen_nr_kicks
     - 5
     - number of normal kick trials in penalty shootouts
   * - pen_ready_wait
     - 10
     - max waiting cycles in penalty_ready_[lr]
   * - pen_setup_wait
     - 70
     - max waiting cycles in penalty_setup_[lr]
   * - pen_taken_wait
     - 150
     - max cycles in penalty_taken_[lr]
   * - point_to_ban
     - 5
     - players cannot execute the next pointto until this cycle has passed
   * - point_to_duration
     - 20
     - point to continues automatically for up to this cycle
   * - port
     - 6000
     - player port number
   * - recv_step
     - 10
     - time step of acception of commands [unit: msec]
   * - say_coach_cnt_max
     - 128
     - upper limit of the number of online coach's message
   * - say_coach_msg_size
     - 128
     - upper limit of length of online coach's message
   * - say_msg_size
     - 10
     - string size of say message [unit:byte]
   * - send_step
     - 150
     - time step of visual information [unit:msec]
   * - send_vi_step
     - 100
     - interval of online coach's look
   * - sense_body_step
     - 100
     - time step of player's body information [unit:msec]
   * - simulator_step
     - 100
     - time step of simulation [unit:msec]
   * - slow_down_factor
     - 1
     - coefficient that slows down simulation time
   * - start_goal_l
     - 0
     - initial score of the left team
   * - start_goal_r
     - 0
     - initial score of the right team
   * - synch_micro_sleep
     - 1
     - sleep time to wait clients in synch mode [unit:msec]
   * - synch_offset
     - 60
     - offset time from the beginning of the cycle to send *think* message [unit:msec]
   * - synch_see_offset
     - 0
     - offset time from the beginning of the cycle to send *see* message if players uses *synch_see* mode [unit:msec]
   * - tackle_cycles
     - 10
     - idle cycles of the players that executed *tackle*
   * - text_log_compression
     - 0
     - compression level of text log file
   * - auto_mode
     - false
     - enable auto start of the match
   * - back_passes
     - true
     - enable back pass rule
   * - coach
     - false
     -
   * - coach_w_referee
     - false
     - allows trainer with automatic referee
   * - forbid_kick_off_offside
     - true
     - forbid kick off offside
   * - free_kick_faults
     - true
     - enable free kick fault rule
   * - fullstate_l
     - false
     - enable full state information for left team
   * - fullstate_r
     - false
     - enable full state information for right team
   * - game_log_dated
     - true
     - flag to write date in game log name
   * - game_log_fixed
     - false
     - enable fixed name in game log
   * - game_logging
     - true
     - flag for game logging
   * - golden_goal
     - false
     - flag for the golden goal rule
   * - keepaway
     - false
     - flag for keepaway mode
   * - keepaway_log_dated
     - true
     - flag to write date in keep away log name
   * - keepaway_log_fixed
     - false
     - enable fixed name in keep away log
   * - keepaway_logging
     - true
     - enable logging in keep away mode
   * - log_times
     - false
     -
   * - old_coach_hear
     - false
     -
   * - pen_allow_mult_kicks
     - true
     - Turn on to allow dribbling in penalty shootouts
   * - pen_coach_moves_players
     - true
     - Turn on to have the server automatically position players for peanlty shootouts
   * - pen_random_winner
     - false
     - enable random winner in penalties
   * - penalty_shootouts
     - true
     - Set to true to enable penalty shootouts after normal time and extra time if the game is drawn.
   * - profile
     - false
     -
   * - proper_goal_kicks
     - false
     -
   * - record_messages
     - false
     - enables recording message to game log file
   * - send_comms
     - false
     - enables sending message to monitors
   * - synch_mode
     - false
     - enables synchronous mode
   * - team_actuator_noise
     - false
     - flag whether to use team specic actuator noise
   * - text_log_dated
     - true
     - flag to write date in text log name
   * - text_log_fixed
     - false
     - enable fixed name in text log
   * - text_logging
     - true
     - flag for recording client command log
   * - use_offside
     - true
     - flag for using off side rule
   * - verbose
     - false
     - flag for verbose mode
   * - wind_none
     - false
     - wind factor is none
   * - wind_random
     - false
     - wind factor is random
   * - audio_cut_dist
     - 50.0
     - audio cut off distance
   * - back_dash_rate
     - 0.6
     - dash power date for the backward dash
   * - ball_accel_max
     - 2.7
     - max. ball acceleration
   * - ball_decay
     - 0.94
     - ball decay
   * - ball_rand
     - 0.05
     - noise parameter for the ball movement
   * - ball_size
     - 0.085
     - ball size
   * - ball_speed_max
     - 3.0
     - max. ball velocity
   * - ball_stuck_area
     - 3.0
     - threshold of distance to detect a stucked situation
   * - ball_weight
     - 0.2
     - (not used) weight of the ball
   * - catch_probability
     - 1.0
     - default goalie catch probability
   * - catchable_area_l
     - 1.2
     - goalie's defalut catchable area length
   * - catchable_area_w
     - 1.0
     - goalie's catchable area width
   * - ckick_margin
     - 1.0
     - corner kick margin
   * - control_radius
     - 2.0
     - (not used)
   * - dash_angle_step
     - 1.0
     - minimum angle step for dash command
   * - dash_power_rate
     - 0.006
     - default dash power rate
   * - effort_dec
     - 0.005
     - dash effort decrement
   * - effort_dec_thr
     - 0.3
     - player dash effort decrement threshold
   * - effort_inc
     - 0.01
     - dash effort increment
   * - effort_inc_thr
     - 0.6
     - dash effort increment treshold
   * - effort_init
     - 1.0
     - default effort value
   * - effort_min
     - 0.6
     - min. player dash effort
   * - extra_stamina
     - 50.0
     - default extra stamina
   * - foul_detect_probability
     - 0.5
     - default foul detect probability
   * - foul_exponent
     - 10.0
     -
   * - goal_width
     - 14.02
     - goal width
   * - illegal_defense_dist_x
     - 16.5
     -
   * - illegal_defense_width
     - 40.32
     -
   * - inertia_moment
     - 5.0
     - default intertia moment for turn
   * - keepaway_length
     - 20
     - length of rectangle in keep away mode
   * - keepaway_width
     - 20
     - width of rectangle in keep away mode
   * - kick_power_rate
     - 0.027
     - kick power rate
   * - kick_rand
     - 0.1
     - base parameter for noise added directly to kicks
   * - kick_rand_factor_l
     - 1.0
     - factor to multiply kick rand for left team
   * - kick_rand_factor_r
     - 1.0
     - factor to multiply kick rand for right team
   * - kickable_margin
     - 0.7
     - default kickable margin
   * - max_back_tackle_power
     - 0.0
     - maximum back tackle power
   * - max_dash_angle
     - 180.0
     - maximum dash angle relative to player's body angle
   * - max_dash_power
     - 100.0
     - maximum dash acceleration power
   * - max_tackle_power
     - 100.0
     - maximum tackle power
   * - maxmoment
     - 180.0
     - max. moment
   * - maxneckang
     - 90.0
     - max. neck angle
   * - maxneckmoment
     - 180.0
     - max. neck moment
   * - maxpower
     - 100.0
     - max kick power
   * - min_dash_angle
     - -180.0
     - minimum dash angle relative to player's body angle
   * - min_dash_power
     - -100.0
     - minimum dash acceleration power
   * - minmoment
     - -180.0
     - max. moment
   * - minneckang
     - -90.0
     - max. neck angle
   * - minneckmoment
     - -180.0
     - max. neck moment
   * - minpower
     - -100
     - min kick power
   * - offside_active_area_size
     - 2.5
     - if offside marked players try to kick/tackle command and their distance from the ball is less than this value, referee detects
	   offside
   * - offside_kick_margin
     - 9.15
     -
   * - offside_kick_margin
     - 9.15
     -
   * - pen_dist_x
     - 42.5
     -
   * - pen_max_goalie_dist_x
     - 14
     -
   * - player_accel_max
     - 1.0
     - max. player acceleration
   * - player_decay
     - 0.4
     - default player decay
   * - player_rand
     - 0.1
     - players' movement noise parameter
   * - player_size
     - 0.3
     - player radius
   * - player_speed_max
     - 1.05
     - maxium speed of players
   * - player_speed_max_min
     - 0.75
     - The minumum value of the maximum speed of players
   * - player_weight
     - 60.0
     - (not used) player weight
   * - prand_factor_l
     - 1
     - factor to multiply prand for left team
   * - prand_factor_r
     - 1
     - factor to multiply prand for right team
   * - quantize_step
     - 0.1
     - quantize step of distance for movable objects
   * - quantize_step_l
     - 0.01
     - quantize step of distance for landmarks
   * - recover_dec
     - 0.002
     - player recovery decrement
   * - recover_dec_thr
     - 0.3
     - player recovery decrement threshold
   * - recover_init
     - 1.0
     - player's initial recovery value
   * - red_card_probability
     - 0.0
     - probability of red card in a foul
   * - side_dash_rate
     - 0.4
     - factor to multiply effective power when side dash is performed
   * - slowness_on_top_for_left_team
     - 1
     -
   * - slowness_on_top_for_right_team
     - 1
     -
   * - stamina_capacity
     - 130600
     - max. recovery capacity of each player's stamina
   * - stamina_inc_max
     - 45.0
     - default max. player stamina increment
   * - stamina_max
     - 8000.0
     - max. player stamina
   * - stopped_ball_vel
     - 0.01
     - threshold value to detect ball is moving or not
   * - tackle_back_dist
     - 0.0
     - max. x distance between player and ball that player may perform a tackle when ball is behind the player
   * - tackle_dist
     - 2.0
     - max. x distance between player and ball that player may perform a tackle when ball is in front of the player
   * - tackle_exponent
     - 6.0
     - exponent used in tackle failure probability equation
   * - tackle_power_rate
     - 0.027
     - tackle power rate
   * - tackle_rand_factor
     - 2.0
     -
   * - tackle_width
     - 1.25
     - max. y distance between player and ball that player may perform a tackle when ball is in front of the player
   * - visible_angle
     - 90.0
     - visible angle
   * - visible_distance
     - 3.0
     -
   * - wind_ang
     - 0.0
     -
   * - wind_dir
     - 0.0
     - wind direction
   * - wind_force
     - 0.0
     -
   * - wind_rand
     - 0.0
     -
   * - coach_msg_file
     - ''
     -
   * - fixed_teamname_l
     - ''
     - fixed name of left team's opponent
   * - fixed_teamname_r
     - ''
     - fixed name of right team's opponent
   * - game_log_dir
     - './'
     - path to game log directory
   * - game_log_fixed_name
     - 'rcssserver'
     - fixed name of game log
   * - keepaway_log_dir
     - './'
     - path to keep away log directory
   * - keepaway_log_fixed_name
     - 'rcssserver'
     - fixed name of keep away log
   * - landmark_file
     - '~/.rcssserver-landmark.xml'
     -
   * - log_date_format
     - '%Y%m%d%H%M%S-'
     - date format in game log
   * - team_l_start
     - ''
     - path to start script of left team
   * - team_r_start
     - ''
     - path to start script of right team
   * - text_log_dir
     - './'
     - path to text log directory
   * - text_log_fixed_name
     - ''
     - fixed name of text log
