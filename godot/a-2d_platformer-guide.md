# 2D Platformer


## 1. Project Setup

**1A. Create project folders:** 
- Audio, Sprites, Scripts, and Scenes
- Import assets into appropriate folders
  - https://mega.nz/folder/tOdyiITJ#HHo_NvrW6vjup-Wh5bCyzQ
  <details><summary>image</summary>

    ![alt text](images/prjsetup1.png)
  </details>

**1B. Setup main scene (level 1)**
- Select "2D Scene"
  <details><summary>image</summary>

    ![alt text](images/prjsetup2.png)
  </details>
  
- Rename "Node2D" to "Main"
  <details><summary>image</summary>

    ![alt text](images/prjsetup3.png)
  </details>
- Save scene as "level_1" into scenes folder
  <details><summary>image</summary>

    ![alt text](images/prjsetup4.png)
  </details>

## 2. Create platforms

**2A. Create Tileset Resource**
- Add `TileSet` resource to `Tiles` folder (name it whatever)
  <details><summary>image</summary>

    ![alt text](images/2-platform-1.png)

    ![alt text](images/2-platform-2.png)

    ![alt text](images/2-platform-3.png)
  </details>
- Double-click the tileset resource to open it in the bottom window (if not already open)
- Drag `tiles_packed.png` into the ‘Tile Sources’ section.
- Click yes to the 'atlas' popup
  <details><summary>image</summary>

    ![alt text](images/2-platform-4.png)
  </details>

**2B. Fix tileset texture region size**
- Select "Setup" tab in `TileSet` bottom window (if not already selected)
  - Adjust "texture region" from 16px to 18px
- In "inspector" panel on right side, adjust tile size to 18px
  <details><summary>image</summary>

    ![alt text](images/2-platform-5.png)
  </details>

**2C. Add Collision properties to TileSet**
- In "inspector" pannel on right side, click on "+ Add Element" under "Physics Layer"
  <details><summary>image</summary>

    ![alt text](images/2-platform-6.png)
  </details>
- In `TileSet` bottom window, select `Paint` tab, then "Select a Property Editor", then "physics layer 0"
  <details><summary>image</summary>

    ![alt text](images/2-platform-7.png)
  </details>
- Select all the tiles that would need "physics" (aka "solid ground, so player doesn't fall through")
  <details><summary>image</summary>

    ![alt text](images/2-platform-8.png)
  </details>

**2D. Setup `TileMapLayer` node**
- Add `TileMapLayer` node to the scene
  <details><summary>image</summary>

    ![alt text](images/2-platform-9.png)
  </details>
- Add tileset resource to `TileMapLayer` node
  <details><summary>image</summary>

    ![alt text](images/2-platform-10.png)
  </details>

**2E. Fix blurry textures**
- Go to Project > Project Settings
- Then Select Rendering > Textures
- Then in Default Texture Filter, change from "linear" to "nearest"      

**2F. Add background to the level**
- Drag the "backgroundForest.png" image from project folder on bottom left onto the scene
- Move the newly created "BackgroundForest" sprite node to the top, but below Main so that it appears behind the platforms


## 3. Create Player

**3A. Setup Player scene**
- Add to current scene, `CharacterBody2D` node
- Rename to "Player"
- Save it as scene, into scenes folder
- Open the "Player" scene
- Add character sprite to the scene
  - Rename the resulting node to "Sprite"
- Center the sprite by setting position to (0,0) in transform properties (inspector panel on right)
- Adjust offset of the sprite, so origin is at character's feet
  - Set "Y-Offset" to negative value
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3a-1.png)
  </details>
- Add `CollisionShape2D` node to scene
  - In `CollisionShape2D` inspector window, add `New CapsuleShape2D`
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3a-2.png)
  </details>
  - Adjust collision shape so it fits the sprite, ensuring it touches the base of character's feet
- Add `Camera2D` node to scene
  - In inspector panel on right, adjust zoom property to (3,3)
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3a-3.png)
  </details>
  - Reposition camera as necessary

**3B. Setup Input Actions**
- Go to "Project" in top menu and select "Project Settings..."
- Select "Input Map" tab in the "Project Settings" window
- Create the actions: "move_left, move_right, jump"
- Add the following input keys for each (need to click on '+' icon on action)
  - move_left: Left Arrow Key, A Key
  - move_right: Right Arrow Key, D Key
  - jump: Up Arrow Key, Spacebar, W Key
    <details><summary>image</summary>

      ![alt text](images/2dplt-g-3a-4.png)

      ![alt text](images/2dplt-g-3a-5.png)
    </details>

**3C. Setup Player script for left & right movement**
- Select root node of `Player` scene
- In inspector panel on right, click on "Attach Script" icon at the bottom, and select "New Script" 
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3a-6.png)
  </details>
- Save script as `player.gd` to the Scripts folder
- Implement code for player left and right movement

  <details><summary>code</summary>

    ```gd
    extends CharacterBody2D

    # The maximum speed at which the player can move
    @export var move_speed : float = 100

    # player’s input for movement
    var move_input : float

    func _physics_process(delta):
      # Get the move input
      move_input = Input.get_axis("move_left", "move_right")

      # Movement
      velocity.x = move_input * move_speed
      move_and_slide()
    ```
  </details>



**3D. Add gravity to player so player falls instead of floating in mid-air**
- Modify code to include velocity.y movement if player is not on floor
  <details><summary>code</summary>

    ```gd
    extends CharacterBody2D


    @export var move_speed : float = 100

    # NEW
    @export var gravity : float = 500 

    var move_input : float

    func _physics_process(delta):

      # NEW
      if not is_on_floor():
        velocity.y += gravity * delta
      
      move_input = Input.get_axis("move_left", "move_right")

      
      velocity.x = move_input * move_speed
      move_and_slide()
    ```
  </details>


**3E. Add jumping**
  <details><summary>code</summary>

    ```gd
    extends CharacterBody2D


    @export var move_speed : float = 100
    @export var gravity : float = 500 
    # NEW
    @export var jump_force : float = 200

    var move_input : float

    func _physics_process(delta):

      if not is_on_floor():
        velocity.y += gravity * delta
      
      move_input = Input.get_axis("move_left", "move_right")

      
      velocity.x = move_input * move_speed

      # NEW
      if Input.is_action_pressed("jump") and is_on_floor():
        velocity.y = -jump_force

      move_and_slide()
    ```
  </details>

**3F. Smooth out character's movement**
  <details><summary>code</summary>

    ```gd
    extends CharacterBody2D


    @export var move_speed : float = 100
    @export var gravity : float = 500 
    @export var jump_force : float = 200

    # NEW
    @export var acceleration : float = 50
    @export var braking : float = 20

    var move_input : float

    func _physics_process(delta):

      if not is_on_floor():
        velocity.y += gravity * delta
      
      move_input = Input.get_axis("move_left", "move_right")

      # NEW
      if move_input != 0:
        velocity.x = lerp(velocity.x, move_input * move_speed, acceleration * delta)
      else:
        velocity.x = lerp(velocity.x, 0.0, braking * delta)

      if Input.is_action_pressed("jump") and is_on_floor():
        velocity.y = -jump_force

      move_and_slide()
    ```
  </details>

**3G. Flip sprite so it faces the direction player is moving in**
  <details><summary>code</summary>

    ```gd
    extends CharacterBody2D


    @export var move_speed : float = 100
    @export var gravity : float = 500 
    @export var jump_force : float = 200

    @export var acceleration : float = 50
    @export var braking : float = 20

    var move_input : float

    # NEW
    @onready var sprite : Sprite2D = $Sprite

    func _physics_process(delta):

      if not is_on_floor():
        velocity.y += gravity * delta
      
      move_input = Input.get_axis("move_left", "move_right")

      if move_input != 0:
        velocity.x = lerp(velocity.x, move_input * move_speed, acceleration * delta)
      else:
        velocity.x = lerp(velocity.x, 0.0, braking * delta)

      if Input.is_action_pressed("jump") and is_on_floor():
        velocity.y = -jump_force

      move_and_slide()

    # NEW
    func _process(_delta):
      if velocity.x != 0:
        sprite.flip_h = velocity.x > 0
    ```
  </details>

**3H. Setup character movement animation**
- Add `AnimationPlayer` node to scene
- Select it to display the animation window at the bottom
- Create new animation, called "IDLE"
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3-7.png)
  </details>
- Change duration of animation to 0.5
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3-8.png)
  </details>
- Add "IDLE" animation to animation timeline
  - select the `Sprite` node, then in the inspector panel on the right, click the white "key" icon next to "texture" property
  - Select "Create RESET Track(s)" in the pop-up window and then "Create"
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3-9.png)
  </details>
- Add "MOVE" animation to animation track
  - do the same as above except, name it as "move"
  - then select the "keyframe" (the blue dot on the timeline); it should bring up the `animationtrackeyedit` property in the inspector panel
  - drag over the "movement character state" sprite (where legs are open), onto the "value" in the inspector
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3-10.png)
  </details>
  - then in the animation timeline, click somewhere in the middle and insert a key
  - then drag over the "idle character state" sprite (hwere legs are closed) over to the `AnimationTrackKeyEdit` in the inspector panel 
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3-11.png)

    ![alt text](images/2dplt-g-3-12.png)
  </details>
  - enable the "loop" effect by clicking on the loop icon (two arrows in a circle) on the top right of animation window
  <details><summary>image</summary>

    ![alt text](images/2dplt-g-3-13.png)
  </details>  
  - adjust the animation timeline duration and keyframes positions as necessary to make the animation look good

**3I. Setup character jump animation**
- Do the same as above, except name the animation as "jump" and change the texture for the key frame to the one "movement character state" sprite, where the characters legs are open

**3J. Implement animations in code**
  <details><summary>code</summary>

    ```gd
    # player.gd
    extends CharacterBody2D


    @export var move_speed : float = 100
    @export var gravity : float = 500 
    @export var jump_force : float = 200

    @export var acceleration : float = 50
    @export var braking : float = 20

    var move_input : float

    @onready var sprite : Sprite2D = $Sprite
    # NEW
    @onready var anim : AnimationPlayer = $AnimationPlayer

    func _physics_process(delta):

      if not is_on_floor():
        velocity.y += gravity * delta
      
      move_input = Input.get_axis("move_left", "move_right")

      if move_input != 0:
        velocity.x = lerp(velocity.x, move_input * move_speed, acceleration * delta)
      else:
        velocity.x = lerp(velocity.x, 0.0, braking * delta)

      if Input.is_action_pressed("jump") and is_on_floor():
        velocity.y = -jump_force

      move_and_slide()

    func _process(_delta):
      if velocity.x != 0:
        sprite.flip_h = velocity.x > 0

      # NEW
      _manage_animation()

    # NEW
    func _manage_animation ():
      if not is_on_floor():
        anim.play("jump")
      elif move_input != 0:
        anim.play("move")
      else:
        anim.play("idle")
    ```
  </details>

## 4. Create Enemy

**4A. Setup Enemy Scene**
- Select "level_1" scene (main scene)
- Add `Area2D` node and rename to "Enemy"
- Save scene as "enemy.tscn" into scenes folder
- Open "enemy.tscn" scene
- Drag over enemy sprite into the scene
  - rename the resulting node to "Sprite"
  - center the sprite by setting (0,0) to the position property in the inspector
- Add `CollisionShape2D` node to the scene
  - Add `New CircleShape2D` shape to the node (like with Player)
  - Adjust the shape over the sprite

**4B. Code enemy automatic patrol between two points**
- Select root node in "enemy" scene
- Create and attach new script, named "enemy.gd".
- Save to scripts folder
  <details><summary>code</summary>

      ```gd
      extends Area2D

      @export var move_direction : Vector2
      @export var move_speed : float = 20

      @onready var start_pos : Vector2 = global_position
      @onready var target_pos : Vector2 = global_position + move_direction

      func _physics_process(delta):
        global_position = global_position.move_toward(target_pos, move_speed * delta)
        
        if global_position == target_pos:
          if target_pos == start_pos:
            target_pos = start_pos + move_direction
          else:
            target_pos = start_pos
      ```
  </details>
- Back in "level_1" scene, position enemy somewhere
  - set "move direction" property values in inspector (example, set y to -50, to have enemy move up and down)
  - confirm enemy movement by playing the game


**4?. Implement Enemy animations**
- Add "AnimationPlayer" to the "Enemy" scene