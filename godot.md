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
 
- 8A. Set InputMap
  - Project > Project Settings > Input Map
  - In "Add new Action", type and enter "move_left"
  - Click on "+" icon and click on the corresponding key for the action (example, "A" and "Left arrow" keys)
  - Do the same for "move_right" and "jump" actions

<details><summary></summary>

  - 
</details>
