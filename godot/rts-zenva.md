# Zenva - 2D Godot RTS Course

- https://academy.zenva.com/course/godot-rts-course/

## 1 - Intro

- Key Features
  - A player team consisting of multiple units that can be selected and controlled.
  - An AI team that will search for and engage with the player’s units.
  - Real-time strategy mechanics where the last surviving team wins.
- Game Overview
  - RTS consisting of multiple units, each assigned to a team.
  - Two teams: the player team and the AI team.
  - Objective: defeat all units of the opposing team. The last team standing wins the game. 
- Base Unit Scene
  - Health bar
  - Movement
  - Attacking
- Unit Movement
  - Includes obstacle avoidance
  - Includes "wobble" (moving side to side) when walking
- Unit Targeting
  - Player Targeting
    -  Select a unit with the left mouse button.
    -  Right-click on empty ground to move the unit to that position.
    -  Right-click on an enemy unit to set it as the target.
  - Enemy Targeting
    - Enemy units will automatically search for player units within a detect range and attack the closest one.
    - This process is dynamic, meaning the AI will prioritize the closest enemy.
- Visuals and effects
  - Health bar indicating the unit’s health.
  - Wobbling effect when units move.
  - Units facing their moving direction.
  - Damage flash and sound effect when units take damage.
- Game end and menu
  - The game ends when only one team remains.
  - An end screen will display the winning team.
  - Additionally, the game will launch into a menu screen where players can choose to play or quit the game.

## 5. Project Setup
- Import [assets](https://academy.zenva.com/wp-content/uploads/2025/02/Assets-Godot-RTS.zip)
- Create folders: scripts and scenes
- Create main scene with 2D scene as root node, and renamed to `Main`
- Add camera2D
  - set zoom property to 2 (sidebar)
- Go to Project Settings and change default texture filter from Linear to Nearest (fix blurry texture problem)

## 6. Tilemap

<details><summary>6A. Create tile map for game environment</summary>

  - Add as child TileMapLayer
  - Create TileSet Resource
    - Save to file system: Create New > Resource > TileSet
    - Open it
    - Drag over tilemap image into tile sources window (click yes on Atlas)
    - May need to adjust margins, separations, etc. from the setup menu
  - Add TileSet to TileMapLayer node (in the inspector)
  - Create arena based on tileset and make it fit within camera bounds  
</details>

## 7. Navigation Region

- Navigation region
  - defines areas where navigation agents can move while avoiding obstacles
- Navigation Agents
  - entities that need to travel from one point to another while avoiding obstacles  

<details><summary>7A. Create Navigation Region</summary>

  - Add NavigationRegion2D node
  - In inspector, set NavigationPolygon proprty with reource
</details>

<details><summary>7B. Define walkable areas</summary>

  - Click NavigationNode and define points on map to represent walkable area
</details>


<details><summary>7C. Create Obstacles</summary>

  - Add NavigationObstacle2D as child of NavigationRegion2D node
  - Select the node and define the obstacle area by clicking and dragging to create points around the obstacle
    - be sure to include padding around obstacle to account for unit sprite size (to prevent clipping) 
  - In the inspector, enable "Effect Navigation Mesh" and "Carve Navigation Mesh" under "Navigation Mesh" collapsible menu
  - Click on "NavigationRegion2D" and Click "Bake Navigation Polygon" (carves out space within navigation mesh for obstacle)
</details>

## 9. Unit Scene

- Why Area2D node is used here?
  - since characters will be utiizing navmesh, setting up collisions is not needed so no need to use rigidbody based node
  - Just need area2d to _detect_ collision rather than act upon it

<details><summary>9A. Setup Base Unit scene</summary>

  - Add Area2D node and rename to unit_base
  - Add sprite to unit_base and rename to Sprite
  - Center position of sprite to (0,0)
  - Save scene, then open it
  - Add ColisionShape2D and set shape to cover the sprite
  - Add NavigationAgent2D node
</details>

<details><summary>9B. Setup Health Bar</summary>

  - Add progresbar node and rename to healthbar
  - In inspector, disable "Show Percentage"
  - Adjust the scale and size of health bar so it appears aobve characters head
    - Go to Layout > Transform and set scale to (0.1,0.1)
    - Set the size to (200,40) and positiion it above unit's head
  - Change colors and add border
    - Go to Theme Overrides > Styles
    - Create new "StyleBoxFlat" for background and set to dark grey (#202020)
    - Create new "StyleBoxFlat" for the fill, and set color to red (#fa1e1e)
    - Add border by setting width to (5,5,5,5) and match border color to background color  
</details>

## 10. Unit Script
- Setup unit script that will control things lilke moving to a target, attacking, managing health, etc.
- Script features
  - Includes `class_name Unit` so code can be used as reference for other code
  - Signal for when unit takes damage (int value)
  - Signal for when unit dies (Unit reference value)
  - Unit variables that can be adjusted in inspector
    - move_speed
    - cur_hp
    - max_hp
    - attack_range (how far away unit must be away from target before it can begin attacking)
    - attack_rate (minimum time allowed between attacks)
    - attacke_damage (how much damage unit deals when it attacks)
    - team (PLAYER or AI)
    - agent (reference to navigationagent)
  - Other variables
    - last_attack_time (to track when unit last attacked)
    - enum Team {PLAYER, AI} (defines which team unit belongs to)
    - attack_target: Unit (which unit this unit is pursuing)
  - Functions
    - _process(delta) (gets called each frame)
    - _target_check (called each frame, and detects how far away from target - if too far, then move towards target and if in range, then begin attacking)
    - _try_attack_target() (try to attack target, and check if possible)
    - set_move_to_target(target: Vector2)
    - set_attack_target (target: Unit)
    - take_damage (amount: int) (what happens when unit takes damage)
    - _die() (what happens when HP reaches 0)

<details><summary>10A. Setup base unit script that includes the features above.</summary>
  - In unit base scene, create scrupt for root area2d node and name as "unit"


  ```gd
  extends Area2D
  class_name Unit

    signal OnTakeDamage (health : int)
    signal OnDie (unit : Unit)
    @export var move_speed : float = 10.0
    @export var cur_hp : int = 20
    @export var max_hp : int = 20
    @export var attack_range : float = 20.0
    @export var attack_rate : float = 0.5
    var last_attack_time : float
    @export var attack_damage : int = 5
    enum Team { PLAYER, AI }
    @export var team : Team
    var attack_target : Unit

    @onready var agent : NavigationAgent2D = $NavigationAgent2D
    func _process (delta):
      pass

    func _target_check ():
      pass

    func _try_attack_target ():
      pass

    func set_move_to_target (target : Vector2):
      pass

    func set_attack_target (target : Unit):
      pass

    func take_damage (amount : int):
      pass

    func _die ():
      pass
  ```
</details>

## 11. Unit Movement
- Implement unit movement and ensure smooth navigation around obstacles
- Includes Move function
  - calculates the next position along the navigation path and moves the unit towards it
  - Uses NavigationAgent2D to get the next path position, which is the point the unit should move towards
  - Based on the next point, calculates direction and movement vector unit should move
  - Applies it to unit via translate

<details><summary>11A. Modify unit script to include move function</summary>

  - 
  - In `unit.gd`,

  ```gd
    func _process(delta):
      _move(delta)

    func _move(delta):
      # get_next_path_position generates path based on navigable area, accounting for obstacles, and gives next point along the path 
      var move_pos = agent.get_next_path_position()
      var move_dir = global_position.direction_to(move_pos)
      # adding delta changes it from "per second" movement to "per frame" movement
      var movement = move_dir * move_speed * delta

      translate(movement)

    # temp function, for demonstration purposes
    # sets target position when scene is loaded
    func _ready():
      set_move_to_target(Vector2.ZERO)

    # sts target position on NavigationAgent2D and tells it to calculate a path to that target and resets the attack target, since unit is being commanded to move
    func set_move_to_target(target: Vector2):
      agent.target_position = target
      attack_target = null
  ```
</details>

<details><summary>11B. Ensure smooth movement</summary>

  - Select NavigationAgent2D, go to inspector, and adjust the "Path Desired Distance" and "Target Desired Distance" properties to lower values (5.0 px)
    - "Path Desired Distance"
      - the distance threshold before a path point is considered to be reached, path position switches to the next point
    - "Target Desired Distance"
      - distance upon which it's considered reaching the target position 
  - This will make unit switch to the next path position closer to the current position, resulting in smoother movement
</details>

<details><summary>11C. Prevent Jitterness during movement</summary>

  - Unit may still jitter when reaching target position
    - happens because unit continues to try to move; so need to stop movement when reaching target position
  - In Unit.GD script,

  ```gd
    func _process(delta):
      # returns true if unit reached target position, and stops movement
      if not agent.is_navigation_finished():
        _move(delta)
  ```
</details>

## 12. Player Unit
- Create units that can be selected by player
- Inherited scene
  - allows us to create new scene based on an existing one, inheriting all its properties and nodes

<details><summary>12A. Create player unit scene inherited from unit base scene</summary>

  - Right-click on "unit_base" scene and select "New Inherited Scene"
  - Save new scene as "unit_player"
  - Open it and rename the root node as "Unit_Player"
  - Set the "Team" property in inspector to "Player"
</details>

<details><summary>12B. Setup unit "selection" visual (so we know unit is selected; green rect. bar underneath unit)</summary>
  
  - Add Sprite2D and rename to "SelectionVisual" (will serve as visual indicator when unit is selected)
  - In the inspector, set "Texture" property to "New GradientTexture1D"
  - Open the texture and remove any existing color stops to create flat color
  - Set color to green
  - In the editor, resize the node to desired size (should be rectangular box underneath unit)
</details>

<details><summary>12C. Implement unit selection</summary>

  - Add `Node` as child of "Unit_Player" and rename to "PlayerUnit" (this will hold player-specific script)
  - Add script, "player_unit.gd"
  - Add code that has function for referencing the SelectionVisual sprite2d node and toggles it on and off

  ```gd
    extends Node
    @onready var selection_visual = $"../SelectionVisual"

    func toggle_selection_visual(toggle: bool):
      selection_visual.visible = toggle
  ```
</details>

## 13. Unit Controller 1

- UnitController
  - responsible for selecting and commanding player units for movement and attacking
  - handles mouse click detection
  - variables & functions
    - `selected_unit`: Unit (keeps track of currently selected unit)
    - `_input(event)`: handles input events (such as mouse clicks)
    - `_try_select_unit()`: attempts to select a unit under the mouse cursor
    - `_select_unit(unit: Unit)`: selects the specified unit
    - `_unselect_unit()`: deselects currently selected unit
    - `_try_command_unit()`: commands selected unit to move or attack
    - `_get_selected_unit()`: return the unit under mouse cursor, if any
     
<details><summary>13A. Setup Unit Controller</summary>

  - Add `Node2D` to main scene and rename to `UnitController`
    - Using `Node2D` because we need access to 2D functionality for detecting mouse clicks 
  - Add script to `UnitController`, labeled `unit_controller.gd`

  ```gd
    extends Node2D

    var selected_unit : Unit

    # get's called whenever input event (ex: mouse click, key press, etc) occurs
    func _input (event):
      pass

    func _try_select_unit ():
      pass

    func _select_unit (unit : Unit):
      pass

    func _unselect_unit ():
      pass

    func _try_command_unit ():
      pass

    func _get_selected_unit () -> Unit:
      return null
  ```
</details>

<details><summary>13B. Implement function, `_get_selected_unit()` to return unit under mouse cursor, if any</summary>

  ```gd
    func _get_selected_unit() -> Unit:
      # space = world that contains all 2D physics info and state
      var space = get_world_2d().direct_space_state
      # Defines object for performing query
      var query = PhysicsPointQueryParameters2D.new()
      # Sets position of query to current mouse position
      query.position = get_global_mouse_position()
      # by default, query does not include Area2D nodes, so set to true to allow detection of Area2D nodes (unit)
      query.collide_with_areas = true
      # perform the query and store its result in a variable
      # returns the collider mouse is hovering over
      var intersection = space.intersect_point(query, 1)

      if intersection.is_empty():
          return null

      if intersection[0].collider is not Unit:
          return null

      # returns unit if query resolves to it
      return intersection[0].collider
  ```
</details>

<details><summary>13C. Implement left mouse for select unit</summary>

  ```gd
    func _input(event):
      if event is InputEventMouseButton and event.pressed:
          if event.button_index == MOUSE_BUTTON_LEFT:
              _try_select_unit()
          elif event.button_index == MOUSE_BUTTON_RIGHT:
              _try_command_unit()

    func _try_select_unit():
      var unit = _get_selected_unit()

      if unit == null or unit.team != Unit.Team.PLAYER:
          _unselect_unit()
      else:
          _select_unit(unit)
  ```
</details>

## 14. Unit Controller 2
- Implement Unit Selection and Command functionality
- Selecting Unit
  - unselects currently selected unit
  - set the selected unit to the unit that was clicked
  - Enable selection visual
- Unselecting Unit
  - Check if there is a currently selected unit
  - If there is one, then disable it's selction visual
  - Set `selected_unit` variable to null
- Commanding Unit (attack target or move to mouse position)
  - triggered when player right clicks with mouse
  - check if there is a selected unit; if mot then exit function
  - get the target unit (that was right-clicked on)
  - either command unit to attack target or move to mouse position

<details><summary>14A. Implement Selecting Unit</summary>

  ```gd
    func _select_unit(unit: Unit):
      _unselect_unit()
      selected_unit = unit
      unit.get_node("PlayerUnit").toggle_selection_visual(true)
  ```
</details>

<details><summary>14B. Implement Unselecting Unit</summary>

  ```gd
    func _unselect_unit():
      if selected_unit != null:
          selected_unit.get_node("PlayerUnit").toggle_selection_visual(false)
      selected_unit = null
  ```
</details>

<details><summary>14C. Implement Commanding Unit</summary>

  ```gd
    func _try_command_unit():
      if selected_unit == null:
          return

      var target = _get_selected_unit()

      if target != null:
          if target.team != Unit.Team.PLAYER:
              selected_unit.set_attack_target(target)
      else:
          selected_unit.set_move_to_target(get_global_mouse_position())
  ```
</details>

## 16. Attacking - 1
- Attack functionality
  - Handle movement towards target and initiate attacks when within range
  - Ensure units can only attack enemies, not their own teammates
  - Ensure attacks matches the defined attack rate (so attacks don't happen every single frame)

<details><summary>16A. Implement targeting</summary>

  ```gd
  # inside unit.gd
  func _process(delta):
    # using nav.finished instead of is_target_reached, to prevent buggy movement
    if not agent.is_navigation_finished():
        _move(delta)

    _target_check()

  func _target_check():
    if attack_target == null:
        return

    # calculate distance between unit and target positions
    var dist = global_position.distance_to(attack_target.global_position)

    # if within attack range, set nav agent target position to own unit position (to stop movement?) and then try to attack
    if dist <= attack_range:
        agent.target_position = global_position
        _try_attack_target()
    # else, move nav agent towards target enemy position
    else:
        agent.target_position = attack_target.global_position
  ```
</details>

<details><summary>16B. Prevent attacking of own teammate (set_attack_target())</summary>

  ```gd
    func set_attack_target(target):
      if target.team == team:
          return

      attack_target = target
  ```
</details>

<details><summary>16C. Implement attacking according to attack rate</summary>

  ```gd
    func _try_attack_target():
      var time = Time.get_unix_time_from_system()

      # if not enough time has passed since last attack, then don't attack
      if time - last_attack_time < attack_rate:
         return

      last_attack_time = time
      attack_target.take_damage(attack_damage)
  ```
</details>

## 17. Attacking - 2
- features
  - "taking damage" when attacked
  - updating health
  - checking when unit should be destroyed 

<details><summary>17A. Implement "take_damage" function</summary>

  ```gd
  # in unit.gd

  func take_damage(amount: int):
    cur_hp -= amount
    # notify other parts of game that unit has taken damage and send current health as parameter
    OnTakeDamage.emit(cur_hp)

    if cur_hp <= 0:
        _die()
  ```
</details>

<details><summary>17B. Implement unit "dying"</summary>

  ```gd
  # unit.gd
  func _die():
    OnDie.emit(self)
    queue_free() # remove entity from game
  ```
</details>

## 18. Health Bar
- Objectives
  - Setup code for health bar and attach it to the "healthbar" node in "unit_base" scene
  - Ensure health bar is only visible when unit has taken damage
  - Healthbar should reflect unit's current health and reduce when unit takes damage
  - variables & functions
    - "unit: Unit" - references the Unit health bar is attached to
    - "_ready()" called at start of game when node is initialized
    - "_update_value(health: int)" updates health bar's value based on unit's health

<details><summary>18A. Implement health bar</summary>

  ```gd
  # inside unit_healthbar.gd, attached to health bar in unit_base scene

  extends ProgressBar

  @onready var unit : Unit = get_parent()

  func _ready():
    max_value = unit.max_hp
    value = max_value
    visible = false

    unit.OnTakeDamage.connect(_update_value)

  func _update_value(health: int):
    value = health
    visible = value < max_value
  ```
</details>

## 19. Enemy AI
- Objectives
  - Create enemy scene based on unit_base scene
  - Adjust parameters to make it look and behave differently than player units
    - Move Speed: 30
    - Cur Hp: 20
    - Max Hp: 20
    - Attack Range: 30
    - Attack Rate: 0.7 (slower than player)
    - Attack Damage: 5
    - Team: AI
  - implement functionality for detecting and pursuing the player
  - Variables and functions
    - detect_range : float
    - detect_rate : float
    - last_detect_time: float
    - enemy_list: Array[Unit]
    - _process(delta) - invoked every frame to update and call detect functions
    - _detect() - detects enemies within range
    - _update_enemy_list() - updates the list of enemy units
 
<details><summary>19A. Setup Enemy Unit and set its parameters</summary>

  - Right click on "unit_base" scene and select "New Inherited Scene"; save new scene as "unit_ai"
  - Select sprite node and change texture to enemy texture
  - Adjust unit params in the inspector
    - Move Speed: Increase to 30.
    - Health: Keep at 20.
    - Attack Range: Set to 30.
    - Attack Rate: Set to 0.7 (slower than the player).
    - Attack Damage: Set to 5.
    - Team: Change to AI.
  - Create & add new node to scene
    - Rename to AI
    - create new script and attach to it, "unit_ai.gd" 
</details>

<details><summary>19B. Add Scene group (tags) for Unit, UnitPlayer, and UnitAI and apply to unit scenes</summary>

  - Go to "unit_base" scene
    - Select "Node" then "Groups" where inspector window is
    - Click the "+" button
    - Add group "Unit"
  - Go to "unit_player" scene
    - Add group "UnitPlayer"
  - Go to "unit_ai" scene
    - Add group "UnitAI"   
</details>

<details><summary>19C. Implement enemy script (implement detect player)</summary>

  ```gd
    # unit_ai.gd from "unit_ai" scene
    extends Node

    @export var detect_range : float = 100.0
    # prevent detection happening every frame, saving performance costs
    @export var detect_rate : float = 0.2
    var last_detect_time : float
    var enemy_list : Array[Unit] = []

    @onready var unit : Unit = get_parent()

    # checks if enough time has elapsed since last detection & invokes other functions
    func _process(delta):
      var time = Time.get_unix_time_from_system()

      if time - last_detect_time > detect_rate:
          last_detect_time = time
          _update_enemy_list()
          _detect()

    # Clears current enemy list, and adds all player units to it
    # searches for all units and adds the ones that are "player"
    func _update_enemy_list():
      enemy_list.clear()

      var raw_list = get_tree().get_nodes_in_group("UnitPlayer")

      # check if node is unit or not
      for node in raw_list:
          if node is not Unit:
              continue

          enemy_list.append(node)

    # determines whether unit in enemy list is within range
    func _detect ():
      pass
  ```
</details>

## 20. Enemy Unit - 2

<details><summary>20A. implement enemy detection of player units and find the closest one to attack</summary>

  ```gd
    # inside unit_ai.gd

    func _detect():
      var closest_enemy = null
      var closest_dist = 999999

      for enemy in enemy_list:
        var dist = unit.global_position.distance_to(enemy.global_position)
  
        if dist > detect_range:
          continue 
  
        if dist < closest_dist:
          closest_enemy = enemy
          closest_dist = dist

      if closest_enemy != null:
        unit.set_attack_target(closest_enemy)
  ```
</details>

## 22-23. Unit Visual

- Objectives
  - Add damage flash effect
  - Add slight rotation effect to simulate movement
  - flip sprite based on direction of movement
  - Variables & Functions
    - unit: Unit - unit entity
    - unit_pos_last_frame: Vector2
    - _ready() - connect damage flash function to onTakeDamage signal
    - _process(delta)
    - _damage_flash(health: int) - cause sprite to flash red briefly    

<details><summary>22A. Change unit origin point from center to feet</summary>

  - Select "Unit_Base" sprite from "unit_base" scene
  - Over in inspector, change Y value in "Offset" to be -8.0px and set Y value in Position to 8.0px
</details>

<details><summary>22B. Implement damage flash effect and rotational movement</summary>

  - Select sprite node in "unit_base" scene and add script, "unit_visual.gd"

  ```gd
    extends Sprite2D

    @onready var unit : Unit = get_parent()

    var unit_pos_last_frame : Vector2

    func _ready():
        unit.OnTakeDamage.connect(_damage_flash)

    func _process(delta):
      var time = Time.get_unix_time_from_system()

      # calc the rotation value using a sine wave
      # multiplication causes rotation to be quicker
      var r = sin(time * 10) * 5

      # stops rotation if unit not moving
      if unit.global_position.distance_to(unit_pos_last_frame) == 0:
          r = 0

      # applies rotation to sprite & convert rotation from degrees to radians
      rotation = deg_to_rad(r)

      # updates units last frame position
      unit_pos_last_frame = unit.global_position

    func _damage_flash(health : int):
      modulate = Color.RED
      await get_tree().create_timer(0.05).timeout
      modulate = Color.WHITE
  ```
</details>

<details><summary>22C. Implment sprite flip based on direction of movement</summary>

  ```gd
    # inside unit_visual.gd
    extends Sprite2D

    @onready var unit : Unit = get_parent()

    var unit_pos_last_frame : Vector2

    func _ready ():
        unit.OnTakeDamage.connect(_damage_flash)

    func _process (delta):
        var time = Time.get_unix_time_from_system()
        var r = sin(time * 10) * 5

        if unit.global_position.distance_to(unit_pos_last_frame) == 0:
            r = 0

        rotation = deg_to_rad(r)

        # flip sprite based on move direction
        var dir = unit.global_position.x - unit_pos_last_frame.x

        if dir > 0:
            flip_h = false
        elif dir < 0:
            flip_h = true

        unit_pos_last_frame = unit.global_position

    func _damage_flash (health : int):
        modulate = Color.RED
        await get_tree().create_timer(0.05).timeout
        modulate = Color.WHITE
  ```
</details>

## 24. Audio

<details><summary>24A. Implement sound effect for when unit takes damage</summary>

  - Add AudioStreamPlayer to unit_base scene
  - Create script "unit_audio.gd" and attach it to the player

  ```gd
    extends AudioStreamPlayer

    # this will make the variable show up in the inspector
    # drag and drop the "take_damage_sfx" sound file to it
    @export var take_damage_sfx : AudioStream

    func _play_sound(audio : AudioStream):
      stream = audio
      play()

    func _play_take_damage_sfx(health : int):
      _play_sound(take_damage_sfx)

    func _ready():
      var unit = get_parent()
      unit.OnTakeDamage.connect(_play_take_damage_sfx)
  ```
</details>

## 25. Camera Controller

<details><summary>25A. Setup Input Map for controlling camera</summary>

  - Go to Project > Project Settings.
  - Navigate to the Input Map tab.
  - Add the following new actions:
    - cam_left
    - cam_right
    - cam_up
    - cam_down
    - zoom_in
    - zoom_out
  - Assign the following keys to the actions:
    - cam_left: A
    - cam_right: D
    - cam_up: W
    - cam_down: S
    - zoom_in: Mouse Wheel Up
    - zoom_out: Mouse Wheel Down
</details>

<details><summary>25B. Setup camera contollable by player (w/ zoom)</summary>

  - Add camera node to main scene (if not already present)
  - Create and attach script "camera_controller.gd" to it

  ```gd
    extends Camera2D

    @export var move_speed : float = 70.0
    @export var zoom_amount : float = 0.2

    func _process(delta):
      _move(delta)
      _zoom(delta)

    # add input vector to the camera’s global position, scaled by the delta value and the move_speed variable
    # smoothly moves the camera in the desired direction, taking into account the time elapsed since the last frame
    func _move(delta):
      var input = Input.get_vector("cam_left", "cam_right", "cam_up", "cam_down")
      # zoom mod for allowing camera movement to be slow when zoomed in, and fast when zoomed out 
      var zoom_mod = 6.0 - zoom.x
      global_position += input * delta * move_speed * zoom_mod

    func _zoom(delta):
      var z = zoom.x
      if Input.is_action_just_released("zoom_in"):
          z += zoom_amount
      elif Input.is_action_just_released("zoom_out"):
          z -= zoom_amount
      # restricts zoom level to 1.0 to 5.0
      z = clamp(z, 1.0, 5.0)
      zoom.x = z
      zoom.y = z
  ```
</details>

## 27. Winning/ Ending Game

<details><summary>Objectives</summary>

  - Set up "game manager" that will be responsible for detecting when game has been won
    - Win condition: when one team is sole remaining team with units in the game
  - Game manager will also keep track of units and teams
    - Team name
    - Unit count 
</details>

<details><summary>27A. Setup "game manager" for detecting when game ends</summary>

  - Select main node in main scene (Node2D)
  - Attach script, "game_manager.gd"
</details>

<details><summary>27B. Track units in game </summary>

  ```gd
    # inside game_manager.gd

    extends Node2D

    var units = {
        Unit.Team.PLAYER: 0,
        Unit.Team.AI: 0
    }

    func _ready():
      for unit in get_tree().get_nodes_in_group("Unit"):
          if unit is not Unit:
              continue
  
          units[unit.team] += 1
          unit.OnDie.connect(_on_unit_die)
  ```
</details>

<details><summary>27C. Modify unit counts on unit deaths</summary>

  ```gd
    # game_manager.gd

    #....

    func _on_unit_die(unit: Unit):
      units[unit.team] -= 1
      _check_win_condition()
  ```
</details>

<details><summary>27C. Check if game is won/over</summary>

  ```gd
    # game_manager.gd

    #....

    func _check_win_condition():
      var winner = 0
      var teams_alive = 0
  
      for team in units:
          if units[team] > 0:
              teams_alive += 1
              winner = team
  
      if teams_alive > 1:
          return
  
      var team_name = Unit.Team.keys()[winner]
      print(team_name + " team has won!")
  ```
</details>

## 28. End Screen

<details><summary>28A. Create End Screen menu overlay</summary>

  - Add `CanvasLayer` node to main scene
  - Add as child, `Panel` and rename to "EndScreen"
  - Make panel fill entire screen
    - Go to Layout
    - Anchors Preset
    - Full Rect
  - Style panel to Green color
    - Thenes Overrides
    - Style
    - Add `StyleBoxFlat`
    - Change color to `#4faf79`
  - Add text to display winning team
    - To `EndScreen`, add as child `Label` node renamed as "HeaderText"
    - Set text to "PLAYER team has won!"
  - Center the Text
    - With "HeaderText" selected, go to "Layout" in inspector
    - Change "Layout Mode" to "anchors"
    - Set "achors preset" to "center"
    - Set both horizontal and vertical alignment to Center
  - Alter font
    - Make new "LabelSettings"
    - Set fontsize to 32
    - Add shdow to text (1px size with 2px offset on both axes
  - Add Menu Button
    - Add button as child of "Endscreen" panel, and rename to "MenuButton"
    - Set text to Menu
    - Resize and position button under text
  - Center the button
    - Go to Layout
    - Change Layout mode to Anchors
    - Set "Anchors Preset" to Center  
</details>

<details><summary>28B. Add script for implementing end screen functionality</summary>

  - Create new script "end_screen.gd" and attach it to "EndScreen" Panel
  - After creating script, connect menu button
    - Select Menu Button
    - Go to "Node" tab in inspector
    - Under signals, conenct "pressed()" signal to `_on_menu_button_pressed()"` function in `end_screen.gd` script
  - Update game manager script, to display end screen when game is won or lost
    - Open `game_manager.gd` script
    - add variable,  `@onready var end_screen = $CanvasLayer/EndScreen`
    - In `check_win_condition()`, instead of priniing win to console, put the following, `end_screen.set_screen(team_name)`
  - Test end screen
    - Toggle `EndScreen` panel visibility off in node menu
    - Play game and lose or win, to see if end screen appears 

  ```gd
    extends Panel

    @onready var header_text : Label = $HeaderText
    
    func set_screen (winning_team : String):
      visible = true
      header_text.text = winning_team + " team has won!"

    func_on_menu_button_pressed():
      pass
  ```
</details>

## 29. Menu

<details><summary>29A. Create Game Menu</summary>

  - Go to the Scene menu and select New Scene.
    - Set the scene type to User Interface.
    - Rename the root node to Menu.
    - Save the scene as Menu.tscn in your project folder.
  - Add background image
    - Add as child, TextureRect and rename as Background
    - Drag and Drop "menu_background.png" into Texture property
    - If background image does not fill the screen,
      - Change Layout Mode to Anchors
      - Set the Anchor Presets to Full Rect
  - Add Game Title
    - Add as child to Menu, `Label`, and rename as "Title"
    - Set text to "Real Time Strategy Game"
    - Create new Label settings
      - Increase font size to 42
      - Enable Shadow (1px, 2px offset both axes) and Outline (3px) options
      - Set horizontal and vertical alignments to center
      - Set Layout mode to Anchors
      - Set Anchors Preset to Center
      - Resize and position the text as needed  
</details>

<details><summary>29B. Add Play & Quit buttons</summary>

  - Add to `Menu`, a Button node and rename to "PlayButton"
    - set text to "Play"
    - Change Layout mode to Anchors, and set anchor presets to Center
    - Resize and position the button under the title
  - Duplicate the button and rename to "QuitButton"
    - Set text to "Quit"
    - Adjust the position to be below the "Play" button  
</details>

<details><summary>29C. Implement button functionalities</summary>

  - Select "Menu" node and attach script, "menu.gd"
  - Connect the button signals
    - Select the PlayButton node.
    - Go to the Node tab and find the pressed() signal.
    - Connect the signal to the Menu node and select the _on_play_button_pressed function.
    - Repeat the process for the QuitButton and connect it to the _on_quit_button_pressed function.
   
  ```gd
    extends Control

    func _on_play_button_pressed():
      get_tree().change_scene_to_file("res://Scenes/main.tscn")
    
    func _on_quit_button_pressed():
      get_tree().quit()
  ```
</details>

<details><summary>29D. Add "Menu" functionality to End screen</summary>

  ```gd
    # inside end_screen.gd

    func _on_menu_button_pressed():
      get_tree().change_scene_to_file("res://Scenes/menu.tscn")
  ```
</details>


<details><summary></summary>

  - 
</details>
