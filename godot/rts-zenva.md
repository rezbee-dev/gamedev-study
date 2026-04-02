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

<details><summary></summary>
  
</details>
