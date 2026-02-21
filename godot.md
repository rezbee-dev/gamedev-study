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
- Add TileMapLayer as child node of root
- Adjust rendering settings
  - Project > Project Settings > Rendering > Textures > Default Texture Filter, change from "linear" to "nearest"
- Create tileset resource
  - add "tiles_packed.png" to the tileset resource
  - Change texture region to 18x by 18x in "tileset (bottom) > setup > texture region"
  - Change tile size to 18x by 18x in "Tileset (side) > Tile Size"
  - Add collision to the platform tiles
    - Go to "TileSet (side) > Physics Layers > + Add element", then go to "TileSet (bottom) > Paint > Physics > Physics_Layer_0", then "paint" the platform tiles in the tileset that should have collisions (so player doesn't fall through when touched)
- Paint scene
  - Assign tile set to TileMapLayer
  - Select tiles and create a level and paint the scene (creating platforms)
  - Drag and Drop backgroundForest.png
    - Rename it to "BackgroundSprite" in Scene hierarchy

Misc
- Tileset
  - Defines what "tiles" we can use
  - palette of building blocks
  - resource that stores collection of textures (sprites, images, etc) and defines how those textures should behave
    - allows for defining physics, collisions, naviations, etc.
- TileMap
  - Canvas for drawing using tiles from tileset
  - Must be assigned tileset
<details>
  <summary></summary>

  - 
</details>
