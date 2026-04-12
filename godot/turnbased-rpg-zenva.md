# Micro Turn-Based RPG

- Source: https://academy.zenva.com/course/godot-turn-based-rpg-course/

## Course Overview
- Turn-based combat system where the player and AI take turns casting actions.
- Custom combat actions that can be attacks or heals.
- Player choice for actions each turn.
- AI decision-making based on a custom function with various factors and customizations.

## Game Design

**Overview**
- turn-based RPG game divided into turns
- Each turn, a character (player or AI) decides to cast a combat action
  -  If it is the AI’s turn, the action will be automatically determined based on a predefined function. 
- the first character  to defeat their opponent wins
- characters (player and computer) are displayed on the left and right

**Character Visuals**
- Sprite: An animated sprite that will bob up and down and flash red when taking damage, enhancing user engagement.
- Health Bar: A visual representation of the character’s health, which will react based on damage taken and health added. This will be shown as both a bar that changes in size and numerical values.

**Combat Actions**
- Combat actions are custom resources (`.tres` files) that represent various actions a character can take during their turn, such as attacks or heals.
- Name: A custom name for the combat action.
- Description: A detailed description of what the action does.
- Damage: The amount of damage the action will deal.
- Heal: The amount of health the action will restore.
- Base Weight: A value that influences the AI’s decision-making process, making certain actions more likely to be chosen.

**Player turn and UI**
- During the player’s turn, a panel will appear at the bottom of the screen, allowing the player to choose a combat action to cast upon the AI.
- Left Side of panel: Buttons representing the different combat actions assigned to the player, such as “Slash,” “Heavy Hit,” or “Heal.”
- Right Side: A display showing the name and description of the selected combat action.

**AI turn**
- a function will be used to determine the action to be cast, based on the following factors
  - AI’s Health: The current health of the AI character.
  - Player’s Health: The current health of the player character.
  - Action Randomness Weight: A value that influences the likelihood of certain actions being chosen.
 


## 5. Project Setup

<details><summary>5A. Setup Project File Structure</summary>
  
  - Create the following folders, and import assets from project files (see folder)
    - Audio
    - Sprites
    - Scenes
    - Scripts
    - Combat_Actions: This folder will contain custom resources (`.tres`) for different combat actions, such as attacks, healing, etc
</details>

<details><summary>5B. Setup 2D scene and add background_castles.png image as background</summary>
  
  - Create new 2D scene and rename as main; save as "battle_scene"
  - Add Camera2D node
    -  Will be used to control screen size and zoom
    - set position to (0,0,0) so it's centered on screen
  - Add Background Image
    - Add TextureRect node to scene, and rename to background
      - node allows for tiling and repeated textures
    - Drag background image to the texture property of TextureRect node
    - Adjust the node's size and set stretch mode to "Tle"
    - Ensure the background is rendered beneath other elements:
      - set z-index to negative value
      - can also move the texturerect node to the top in the scene hierarchy, which will cause other elements to be appear in front of it
</details>



<details><summary></summary>
  
  - 
</details>












