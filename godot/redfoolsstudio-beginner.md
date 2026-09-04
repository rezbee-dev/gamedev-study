# Red Fools Studio - Beginner Godot Course

- https://www.redfoolsstudio.com/challenge-page/3f85f6d1-d913-48d7-bf19-1b50ce06ac1c?programId=3f85f6d1-d913-48d7-bf19-1b50ce06ac1c

## Space Shooter Game

### 1. Setup
- UI: https://kenney.nl/assets/ui-pack
- Sprites: https://kenney.nl/assets/space-shooter-remastered
- Set game screen size to 720 (width) and 1280 (height)
- Set stretch mode so that screen stretches to any actual screen size (hint: viewport)
- Create folders with their own colors
  - assets
  - scenes
  - components
  - scripts
 
### 2. Global Script

**Create global script named `global.gd`"**

Add variables for
- tracking game over
- tracking game on
- tracking score
- setting `chosen_ship` (int)
- sound mute

Create function that resets values of the variables

Add `_process(delta)` function
- mute audio if mute is set to true and vice-versa

### 3. Scrolling background

Need to create background that loops 

**Create Background**
- Add Node2D to main scene
- Add as child, Node2D with TileMapLayer as child with darkpurple.png as tileset
- Do not allow automatic atlas region thingy
- Select it so the entire image is a single tile
- Set Texture Region to 256 by 256 (in tileset)
- Set Tile Size inspector to 256x256
- Fill out the viewport (purple box) with the tiles
- Duplicate the Node2D with the tilemaplayer
- Reposition it above the original Node2D with the tilemaplayer

**Create Animation**
- Add animationplayer to the Node2D parent whose children are the background
- Create new animation, labeled loop
- Select first node w/ background and add position keyframe to the animation
- 
