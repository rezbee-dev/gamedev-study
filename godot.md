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

### 13. Player Animation 1

- Setup animations for idle, moving, and jumping

- Misc
  - While this tutorial uses "AnimationPlayer", a more simple one would be "AnimatedSprite2D" which allows you to flip through Sprite Frames for animation

<details><summary>13. Setup Player Animation</summary>

  - Add "AnimationPlayer" node to root player node
  - Select and then press on "Animation" in bottom bar "animation" window
  - Create Animation labelled "Idle"
  - Change animation duration to 0.5 (edit duration field near top right, next to timeline)
  - Set animation for "texture" property: Select "Sprite" from scene hiearchy and then click on the white "key" icon next to "Texture" in right sidebar menu
    - This should add a "texture" track to the Animation window in the bottom
</details>




<details><summary></summary>

  - 
</details>
