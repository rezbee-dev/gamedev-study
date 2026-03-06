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

<details><summary></summary>
  
</details>

<details><summary></summary>
  
</details>
