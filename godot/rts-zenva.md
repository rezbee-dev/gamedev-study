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
    -  
</details>

<details><summary></summary>
  
</details>
