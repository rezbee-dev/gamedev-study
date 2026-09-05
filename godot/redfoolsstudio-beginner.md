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

- Add variables:
  ```
  var game_over = false
  var game_on = false
  var score = 0
  var chosen_ship = 1
  var mute = false 
  ```

- Create function that resets the values of the variables
- Create function that checks for `mute` and mutes or unmutes audio accordingly

### 3. Scrolling background

- Create background scrolling/moving forward effect via animation

**Create Scrolling Background**
- background (`darkPurple.png`)
  - needs to fill entire viewport
  - needs to move forward / scroll
  - size for `darkPurple.png` is 256px by 256px

- Implementation
  - two `TileMapLayer` nodes with the `darkPurple.png` texture
  - one fills up entire viewport, then the other duplicate one sits on top
  - With `AnimationPlayer` node, add the positions of the `TileMapLayer` nodes, move them down an entire viewport's height, and then loop the animation
  - add script to parent node to start the animation when `game_over = true`, and vice-versa
 

### 4. UI Nodes

**Setup nodes**
- Add Node2D to main scene; rename it to "ui"
- add canvaslayer node
- add 4 screens (as node2d): startscreen, choosescreen, ingamescreen, gameoverscreen

**Start Screen**
- Game title in first half of screen (label node)
- Use font from game assets
- Add background/ outline to the title
- Add image of player ship below the title (sprite2d)
- Add play button below image
  - Node2d with Ninepatchrect node as child (we use this because it scales better with resolution than 2d nodes)
  - add texture (button_rectacngle) from texture pack assets
  - add 5px to patch margins
  - add button node
    - may need to adjust button themes to get rid of any appearance
   
**Choose screen**
- Displays pictures of three ships with buttons to select ship and buttons for each of the three ships (with their names)

**Ingame screen**
- Create button with x for muting audio and o for turning audio on
- create label for score

**Game Over screen**
- similar to start screen, except the text reads "Game Over" with red outline
- add another label for score
- button labeled "Menu"

### 5. UI script

**Add script**
- Attach script to UI parent node
- create function for "play" button
  - makes start screen visibility false, and choose screen visible true
- create function for each ship button
  - set global.chosen_ship based on ship selected
  - set visibility of the ship sprite nodes based on ship selected via the button
- create function for choosing ship
  - sets `Global.game_on` to true
  - sets choose screen visibility to false
    - call `queue_free()` on it so lasers are cleared
  - sets ingamescreen visibility to true
- create mute button function
  - set Global.mute to false if true, and true if false
    - set visibility of x and o buttons accordingly 
- set main menu button
  - invoke Global.reset_values()
  - run `get_tree().reload_current_scene()`
- set process function
  - if global.game_over = true, then set ingamescreen visibility to false and game over screen to true
  - if not game over, then set score(s) to str(Global.score)

### 6. Laser Nodes

**Setup Laser**
- Create new scene with CharacterBody2D as root node
- Add sprite2d
- add texture (laser picture)
- add area2d
- add group (laser)
- add collisionshape2d
- add 2 node2ds on the sides of the laser to represent spread shot
- set timer (3.0)
- add script to the root node

**Create Laser2 and Laser3**
- Duplicate the laser scene and rename duplicates laser 2 and 3
- laser three
  - need to make a long laser
  - Use sprite2d to stretch out laser to the entire screen length
  - Also stretch out the collisionshape2d

### 7. Laser Script

**Laser One Script**









