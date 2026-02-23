# Godot Notes

## 2D Platformer
- [src: zenva](https://academy.zenva.com/lesson/introduction-458/?zva_less_compl=3579381)

### Features
- 2D, sidescroller platformer game
- Player has to collect coins, avoid enemies, and reach end flag
- Movement
  - left, right, and jump
  - Acceleration & braking for smooth movement
- Enemies
  - Move back and forth between two points
  - Damages player when hit
- Player Damage
  - health goes down by 1 (1 less heart)
  - Screenflashes red
  - Screenshake
  - Damage sound
- Coins
  - increases score by 1 when collected
  - plays sound effect when collected
  - has rotating effect
  - bobs up and down
- UI
  -  Hearts for health
  -  Score for coins collected
- End flag
  - placed at the end of each level
  - when touched, it loads up next level
- Menu
  - play button
    - Loads level 1
  - quit button

### 5. Project Setup

<details><summary>5A. Setup Project Folder</summary>

- Project assets: [link](https://academy.zenva.com/wp-content/uploads/2025/02/Assets-Godot-2D-Platformer.zip)
- Create Folders: Audio, Sprites, Scripts, and Scenes
</details>

<details><summary>5B. Create Level 1 Scene</summary>

- Initialize 2D scene by selecting "2D Scene"
- Rename root node to "main"
- Save as "level_1" into scenes folder
</details>

### 6. Tilemap

- Setting up environment (background & tile map)
- Misc
  - Tileset
    - Defines what "tiles" we can use
    - palette of building blocks
    - resource that stores collection of textures (sprites, images, etc) and defines how those textures should behave
      - allows for defining physics, collisions, naviations, etc.
  - TileMap
    - Canvas for drawing using tiles from tileset
    - Must be assigned tileset
      
<details><summary>6A. Fix blurry pixelated sprites</summary>
  
  - Project > Project Settings > Rendering > Textures > Default Texture Filter, change from "linear" to "nearest"
</details>

<details><summary>6B. Create TileSet Resource (platforms) with collision detection</summary>

  - Create new tileset resource (save to tiles folder)
  - Add "tiles_packed.png" to the tileset resource
  - Change texture region to 18x by 18x in "tileset (bottom) > setup > texture region"
  - Change tile size to 18x by 18x in "Tileset (side) > Tile Size"
  - Add physics layer: Go to "TileSet (side) > Physics Layers > + Add element"
  - Apply physics to platform tiles:
    - Go to "TileSet (bottom) > Paint > Physics > Physics_Layer_0"
    - "Paint" the platform tiles in the tileset that should have collisions (so player doesn't fall through when touched)
</details>

<details><summary>6C. Create level platform + background</summary>

  - Drag and Drop backgroundForest.png
    - Rename it to "BackgroundSprite" in Scene hierarchy
  - Add "TileMapLayer" node as child of root scene
  - Set the created tileset as tileset for "TileMapLayer"
  - From the bottom, select tiles and paint the scene (creating platforms)
</details>

### 7. Player Scene

- Setup "Player" scene for player movement, etc.
- Misc
  - CharacterBody2D
    - Node well-suited for 2D player movement, as it comes with built-in features (such as collision detection)
    - Script-driven, allows for velocity to be defined precisely (unlike RigidBody2D where its physics driven, handled by Godot)
  - Origin (Sprites)
    - AKA Anchor point or pivot
    - Its the coordinates in a sprite that movement and transformation is relative to
    - For platformers and top-down rpg, its recommended to set origin at "feet" of character to make it easier to snap to floor (ex: setting position.y to floor's height puts character perfectly on ground)
    - For top-down shooters, its recommended to put in the center to make rotation feel more natural (as its around the center)
    - Represented by the "crosshair" when selecting `CharacterBody2D` node

<details><summary>7A. Create player (scene)</summary>

  - Add "CharacterBody2D" Node
  - Rename to "Player" and save as scene
  - Open "Player" scene
  - Add "character_0000.png" to root as childe and rename to "Sprite"
</details>

<details><summary>7B. Set origin so it is a bottom/feet of character</summary>

  - Select "Sprite" node
  - Adjust "Offset" in sidebar menu
  - Sprite should be above the "crosshairs" 
</details>

<details><summary>7C. Add Collision to Player</summary>

  - Add "CollisionShape2D" node
  - Select the node and click on "Shape" in sidebar menu
  - Choose "CapsuleShape2D"
  - Adjust position/size of "CollisionShape2D" to be aligned with "Sprite"
</details>

<details><summary>7D. Add camera that follows player</summary>

  - Add "Camera2D"
  - Click on "Zoom" in sidebar menu and adjust to ensure it's not too zoomed out or in
  - Adjust "Purple" bounds box to depict what player should see
</details>

### 8. Input Map
- Set up "actions" game listens for (that corresponds to number of keys) to know when and how to move the character
  - Instead of listening for specific keys directly, you listen for the "action" and assign keys to the action to trigger it

<details><summary>Set actions for left, right, and jump </summary>

  - Project > Project Settings > Input Map
  - In "Add new Action", type and enter "move_left"
  - Click on "+" icon and click on the corresponding key for the action (example, "A" and "Left arrow" keys)
  - Do the same for "move_right" and "jump" actions
</details>
 
### 10. Player Movement 1

- Create script for player movement and jumping (simple)
- Misc
  - Exporting variables
    - Allows variables to be modified outside of code, in editor
   
<details><summary>10A. Create Player Script</summary>

  - Select "Player" scene
  - In sidebar menu, add script, and save as "player.gd"
</details>

<details><summary>10B. Export variables for move_speed, acceleration, braking, gravity, and jump_force</summary>

  ```gd
  # Maximum movement speed
  @export var move_speed : float = 100

  # Rate at which player movement speed increases until reaching move_speed
  # Allows for smooth movement
  @export var acceleration : float = 50

  # Rate at which player slows down when movement keys no longer pressed
  @export var braking : float = 20

  # How much downward force to apply to player
  @export var gravity : float = 500

  # How much upward force to apply when player jumps
  @export var jump_force : float = 200
  ```
</details>


<details><summary>10C. Add functionality for moving left and right and falling</summary>

  ```gd
  extends CharacterBody2D

  ##############################################
  # Export variables from 10B
  # .....
  # .....
  # .....
  ##############################################

  # represents whether player is moving left (-1), right (1), or stopped (0)
  var move_input : float

  # function that runs at a fixed, consistent rate, allowing for frame-rate independent movement
  # Allows for logic to sync with Godot's physics engine (which calculates collisions and gravity)
  #   otherwise, doing physics stuff in _process() can cause jitter
  func _physics_process(delta):
      # Determine whether player is falling or not, depending on whether character is on floor or not
      # "is_on_floor()" is provided by CharacterBody2D
      if not is_on_floor():
          # move player down by gravity per delta 
          velocity.y += gravity * delta

      # Input.get_axis() returns -1.0 for left movement, +1.0 for right movement, and 0.0 for no movement
      move_input = Input.get_axis("move_left", "move_right")

      # Variable from `CharacterBody2D` (inherited)
      # Sets left and right direction and movement
      # move_speed allows for movement to be 100 pixels for second rather than 1 (which is sloooow)
      velocity.x = move_input * move_speed

      # takes the velocity, determines whether any collisions, and moves character
      # moves based on velocity; so should be placed after velocity is modified
      # returns true if collision or false if otherwise
      move_and_slide()
  ```
</details>


### 11. Player Movement 2

- Adds acceleration and jumping to Player script

- Misc
  - `lerp(from, to, weight)`
    - Linear Interpolation
    - Finds a value that is some percentage between two other values
    - "Smoothing" function that allows for value to slide gracefully from one to the other, without snapping instantly from 0 to 100

<details><summary>11A. Implement Jumping</summary>

  - Player should only be able to jump if jump action is triggered and player is on the ground

  ```gd
  # player.gd, inside _physics_process(delta) function,
  #
  # Jumping
  if Input.is_action_pressed("jump") and is_on_floor():
    # negative because positive y-values mean down and negative y-values means up
    velocity.y = -jump_force 
  ```
</details>

<details><summary>11B. Implement Movement smoothing</summary>

  - Currently, player movement is rigid and snappy. There is no accelerating to speed or braking down to stopping, making movement feel too rigid

  ```gd
  # player.gd, inside _physics_process(delta) function,

  # movement
  if move_input != 0:
        # multiply by delta to make smoothing frame-independent
        velocity.x = lerp(velocity.x, move_input * move_speed, acceleration * delta)
    else:
        velocity.x = lerp(velocity.x, 0.0, braking * delta)
  ```
</details>

### 12. Facing Movement Direction
- Flip sprite so it faces the direction its moving

<details><summary>12A. Ensure player sprite faces the direction of movement</summary>

  - In `player.gd` script, need to create reference to `Sprite2D` node, then use it to invoke `.flip_h` (found in sidebar menu Sprite2D > Offset > Flip H)

  ```gd
  extends CharacterBody2D

  # Variables...

  @onready var sprite : Sprite2D = $Sprite

  # def _physics_process(delta)....

  func _process(delta):
    # if statement ensures sprite is flipped only when player is moving
    if velocity.x != 0:
      # velocity.x > 0 means moving to right, otherwise means moving to left
      # depending on default sprite face direction, set flip_h to velocity.x > 0 or velocity.x < 0
      sprite.flip_h = velocity.x > 0
  ```
</details>

### 13. Player Animation 1 & 2

- Setup animations for idle, moving, and jumping

- Misc
  - While this tutorial uses "AnimationPlayer", a more simple one would be "AnimatedSprite2D" which allows you to flip through Sprite Frames for animation
  - Reset Track
    - Allows for returning animation back to default state after an animation
    - Contains default values for all modified properties
    - Can be found in dropdown menu next to the animation button in the animation window

<details><summary>13A. Setup Player Animation and create IDLE and RESET animation tracks</summary>

  - Add "AnimationPlayer" node to root player node
  - Select and then press on "Animation" in bottom bar "animation" window
  - Create Animation labelled "Idle"
  - Change animation duration to 0.5 (edit duration field near top right, next to timeline)
  - Set animation for "texture" property: Select "Sprite" from scene hiearchy and then click on the white "key" icon next to "Texture" in right sidebar menu
    - Ensure "Create RESET Track(s)" is selected and click create
    - This should add a "texture" track to the Animation window in the bottom
</details>

<details><summary>13B. Add "Move" animation track</summary>

  - In animationplayer window, click "Animation" and create new animation labelled "move"
  - Add texture property key frame to animation (ensure RESET is selected)
  - Replace texture value (in sidebar window) for initial key with "character_0001.png" (legs are spread apart more)
  - Add new key halfway through the duration, and set texture value back to "character_0000.png"
  - Enable "animation looping" by clicking on double arrow circle bottom in top right side in animation window, above timeline
  - Change animation duration to 0.25 - 0.50 (make movement animate faster)
</details>


<details><summary>13C. Add "Jump" animation track</summary>

  - In animationplayer window, click "Animation" and create new animation labelled "jump"
  - Add texture property key frame to animation (ensure RESET is selected)
  - Replace texture value (in sidebar window) for initial key with "character_0001.png" (legs are spread apart more)
  - Note: duration not needed since jump is simply a single frame that is held while in the air
</details>


<details><summary>13D. Implement scripting for the animation</summary>

  ```gd
  extends CharacterBody2D

  # Variables...

  @onready var anim : AnimationPlayer = $AnimationPlayer
  
  # def _physics_process(delta)....

  func _process(delta):
    # prev. code...

    _manage_animation()

  # plays jump animation if not on floor
  # plays move animation if velocity not 0
  # plays idle animation if not moving
  func _manage_animation():
    if not is_on_floor():
        anim.play("jump")
    elif move_input != 0:
        anim.play("move")
    else:
        anim.play("idle")
  ```
</details>

### 16. Enemy 1 & 2

- Setup Enemy scene that moves from one point to another, back and forth
  - Player can go through it, but when touched, it triggers "damage"
  - Add enemy to level whereever it makes sense
    - Organize under "empty node"
- Misc
  - Area2D
    - Can detect collisions, but does not physically collide with other objects
      - "Ghostly"
    - Ex: Coins, health packs, detect when player enters room, etc. 


<details><summary>16A. Setup Enemy Scene (sprite, collision, animation, etc nodes)</summary>

  - Add Area2D node to level scene, rename to "Enemy" and save as scene
  - Add "character_0024.png" to it and rename to "Sprite"
  - Add "CollisionShape2D" and set to circle shape over sprite
  - Add "AnimationPlayer" 
</details>


<details><summary>16B. Setup Enemy Script so enemy moves between two points</summary>

  - Need to set variables for move direction (up, down, left, right) and move speed
  - Need to set start and target positions (so enemy can move back and forth on set path)

  ```gd
  extends Area2D

  @export var move_direction : Vector2
  @export var move_speed : float = 20

  @onready var start_pos : Vector2 = global_position
  @onready var target_pos : Vector2 = global_position + move_direction

  func _physics_process(delta):
    # move_toward(to, delta) will return vector moved towards "to" by fixed delta amount (doesn't go past "to")
    # so every physics frame, global position of Enemy is updated to be closer to "target_pos"
    global_position = global_position.move_toward(target_pos, move_speed * delta)

    # once enemy reaches target_pos, reset target_pos to be start_pos
    if global_position == target_pos:
      if target_pos == start_pos:
        target_pos = start_pos + move_direction
      else:
        target_pos = start_pos
  ```
</details>


<details><summary>16C. Setup signal for when player enters enemy collider (touches enemy)</summary>

  - Specifically, enemy will detect when player's collider overlaps or enters the enemy's collider
  - Click on Enemy node
  - Go to Node panel on right sidebar menu (top tab)
  - Select Signals > body_entered(body: Node2D), and double click and connect to enemy node
    - this should add new method to enemy script
</details>


<details><summary>16D. Assign player to Group so enemy can identify Player</summary>

  - Click on Player Scene
  - Go to Node panel (right hand side side bar menu, top tab)
  - Select Groups (next to Signal tab)
  - Click the "+" icon and add new group labelled "Player"
  - Now that Player is tagged as being part of "Player" group, we can check for this group in enemy "body_entered" function
</details>


<details><summary>16D. Write collision detection script for detecting when player touches enemy</summary>

  ```gd
  # inside enemy.gd
  ## Variables...
  ## _physics_process(delta):...

  func _on_body_entered(body):
    # if not player, then ignore
    if not body.is_in_group("Player"):
        return

    print("Deal damage to player")
  ```
</details>


<details><summary>16E. Animate Enemy (so wings flap when moving)</summary>

  - Click on AnimationPlayer, add new animation (click on Animation in animation player window), and name as "fly"
  - Click on enemy sprite and then add texture key to animation (ensure "create RESET track(s)" is set)
  - Change duration to 0.3
  - Add new texture key at 0.1 seconds in timeline
    - set to character_0025.png
  - Add new texture at 0.2
    - set to character_0026.png
  - Set animation to loop in animation window
  - In script, play the "fly" animation:
  ```gd
  # inside enemy.gd
  ## Variables...

  func _ready():
    # initializes animation player to play "fly" animation upon startup
    $AnimationPlayer.play("fly")

  ## _physics_process(delta):...

  ## _on_body_entered(body):
  ```
</details>

### 18. Damage
- Add feature where player takes damage and damage reduces health and causes game over

<details><summary>18A. Implement player health & game over if health is <= 0</summary>

  ```gd
  # inside player.gd

  ## Variables...
  @export var health : int = 3

  ## _physics_process(delta)....
  ## _process()...

  func take_damage(amount: int):
    health -= amount
    if health <= 0:
        # call_deferred needed, otherwise you get an error "removing collision node during physics call back not allowed"
        # call_deferred delays call to game_over until end of physics frame, avoiding physics callback error
        # the reason this happens, is because in enemy.gd, we call this method inside of _on_body_entered() which runs during physics process
        call_deferred("game_over")

  func game_over():
    get_tree().change_scene_to_file("res://Scenes/level_1.tscm")
  ```
</details>


<details><summary>18B. Implement enemy damage player</summary>

  ```gd
  # inside enemy.gd

  ## previous code...

  func _on_body_entered(body):
    if not body.is_in_group("Player"):
      return
    body.take_damage(1)
  ```
</details>


### 19. Coin 1 & 2 
- Implement coin where when player collect it, it increases player score
- Coin should have animation (bobbing up/down and rotation)

<details><summary>19A. Create coin scene</summary>

  - Create Area2D node and rename it "Coin" and save
  - Add as child, Sprite2D node renamed as Sprite with "tile_0151.png"
  - If necessary, set to (0,0) in Node2D (sidebar) > Transform > Position
  - Add CollisionShape2D with CircleShape2D
  - Add "coin.gd" script
  - Add coins to level wherever it makes sense
    - Organize under "empty node"
</details>


<details><summary>19B. Create coin animation (bobbing up/down and rotation)</summary>

  ```gd
  extends Area2D

  var rotate_speed : float = 3.0
  var bob_height : float = 5.0
  var bob_speed : float = 5.0

  @onready var start_pos : Vector2 = global_position

  @onready var sprite : Sprite2D = $Sprite

  # using physics since Area2D has collision detection stuff 
  func _physics_process(delta):
    # gets current time in seconds
    var time = Time.get_unix_time_from_system()

    # rotate
    # modifying scale.x mimics rotation appearance
    # sin() allows us to keep values between two values
    # time allows for value to change 
    sprite.scale.x = sin(time * rotate_speed)

    # bob up and down
    # the 1+ and /2 is to keep range of sin() between start y and 1, so coin does not go below starting y position (ex: 0 and 1 instead of -1 and 1)
    var y_pos = ((1 + sin(time * bob_speed)) / 2) * bob_height
    global_position.y = start_pos.y - y_pos
  ```
</details>


<details><summary>19C. Implement coin collect functionality (when player touches coin, increase score and despawn coin)</summary>

  - Add placeholder function, "increase_score()" to player.gd script

  ```gd
  # inside player.gd

  func increase_score(amount: int):
    # will implement scorring system later
    print("increase score")
  ```

  - Add `body_entered` signal into `coin.gd` (click on coin scene, then select signals over in sidebar menu right, and select "body_entered" then connect)
  - in coin.gd script, add the following code

  ```gd
  # checks if player touched coin
  func _on_body_entered(body):
    if not body.is_in_group("Player"):
        return
    body.increase_score(1)
    # causes coin to disappear; queues the coin node for deletion at the end of current frame after it has finished processing
    queue_free()
  ```
</details>


### 21. Scoring
- Setup scoring system
- Misc
  - [Autoload](https://docs.godotengine.org/en/stable/tutorials/scripting/singletons_autoload.html)
    - aka Singleton
    - Used to load a scene or script that will be attached to a node and will be added to the root node before any other scenes are loaded
    - Allows for it to be always loaded, no matter the current running scene
    - Can store global variables/state
    - Must be added to "Globals" in: Project > Project SEttings > Globals > Autoload
      - if "Global Variable" is checked in the "Autoload" window, then it will be able to be accessed directly in GDScript

<details><summary>21A. Define global variable "score"</summary>

  - Cannot just be added to "Player" or "Level" scene since we want score to persist across levels, and if attached to "Player", it would just get reset when switching levels (or scenes)
  - Go to Project > Project Settings > Global, then enter "PlayerStats" in Node name field and click add
    - save to scripts folder when prompted
  - Inside "playerstats.gd":

  ```gd
  extends Node

  var score : int = 0
  ```
</details>


<details><summary>21B. Modify "increase_score()" to update the global score variable</summary>

  ```gd
  # inside player.gd

  func increase_score(amount : int):
    PlayerStats.score += amount
    print(PlayerStats.score)
  ```
</details>


### 22. End Flag
- Add "end flag" to level so that touching it will end the level (switch scene)

<details><summary>22A. Create End Flag Scene</summary>

  - Create Area2D node, rename to EndFlag, add "tile_0112.jpg" as Sprite, and set position to 0,0
  - Add collisionshape2d node with circle shape
  - Add script "endflag.gd"
  - Include "body_entered" signal from right sidebar menu (Node > Signals > body_entered(...)" to "endflag.gd" script
  - Code:

  ```gd
  extends Area2D

  @export var scene_to_load : PackedScene

  func _on_body_entered(body):
    if not body.is_in_group("Player"):
      return

    get_tree().change_scene_to_packed(scene_to_load)
  ```
</details>


### 24. UI 1 & 2

- Add hearts for HP and text for score
- Misc
  - CanvasLayer
    - Node that adds a separate 2D rendering layer for all its children
    - While "Viewport" children draws by default at layer "0", CanvasLayer will draw at any other numeric layer
    - Layers with a greater number will be drawn above those with a smaller number

<details><summary>24A. Setup UI and create Heart display (health)</summary>

  - Open Player scene and add "CanvasLayer" node to it
    - Add script "player_ui.gd" to it
  - Add "TextureRect" node to the "CanvasLayer" node as child
  - Rename it to "Heart"
  - Assign "tile_0044.png" texture to the "Heart" texturerect node in the sidebar "texture" field
  - In sidebar, go to "Layout" > "Transform" then set size to 80px by 80px
  - Add "HBoxContainer" to CanvasLayer as child
    - Rename to "HealthContainer"
    - In sidebar menu, go to Layouts > Transform > Size, and set y to 80px
    - Drag Heart texturerect node to be child of healthcontainer node
    - In Heart node, in sidebar menu, set "expand mode" from "keep size" to "fit width"
    - Duplicate the hearts so there are three, arranged horizontally
    - Place heartcontainer where it makes sense on the UI
</details>


<details><summary>24B. Create Score Display</summary>

  - Add node "Label" as child of "CanvasLayer" node
  - Rename to "ScoreText"
  - In sidebar menu, set "Text" to "Score: 500"
  - In sidebar menu, set font to 38px and shadow to 6px
    - Shadow is to make score easier to read against light backgrounds
</details>


<details><summary>24C. Setup UI update via scripting and signals</summary>

  ```gd
  # inside player.gd

  # define two signals at top of script
  signal OnUpdateHealth (health : int)
  signal OnUpdateScore (score : int)

  # modify take_damage()
  func take_damage (amount : int):
    health -= amount
    OnUpdateHealth.emit(health)

    if health <= 0:
        call_deferred("game_over")

  # modify increase_Score()
  func increase_score (amount : int):
    PlayerStats.score += amount
    OnUpdateScore.emit(PlayerStats.score)
  ```
</details>


<details><summary>24D. Update UI elements</summary>

  ```gd
  # inside player_ui.gd script,

  extends CanvasLayer

  @onready var health_container = $HealthContainer
  # stores values of the hearts in an array
  var hearts : Array = []

  @onready var score_text : Label = $ScoreText

  # references the player itself
  @onready var player = get_parent()

  # inits variables and sets up the necesary connections to update UI via signals
  func _ready():
    hearts = health_container.get_children()

    # connect the custom signals defined in player.gd to the callback functions _update_hearts and _Update_score
    # the values "emitted" by the signals are passed into the callback functions when invoked
    player.OnUpdateHealth.connect(_update_hearts)
    player.OnUpdateScore.connect(_update_score)

    _update_hearts(player.health)
    _update_score(PlayerStats.score)

  # only updating visibility; not deleting the hearts
  # hearts updated based on current health
  func _update_hearts(health : int):
    for i in range(len(hearts)):
        # hearts are visible if index is less than player's health, otherwise false
        hearts[i].visible = i < health

  # Update's score text label with player's current score
  func _update_score(score : int):
    score_text.text = "Score: " + str(score)
  ```
</details


<details><summary></summary>

</details>
