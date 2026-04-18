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

## 6. Character Setup

<details><summary>Description</summary>
  
  - Two characters: player and AI
  - Both characters should be based on same scene
  - Difference is whether it is player or ai and the direction it will be facing
</details>

<details><summary>6A. Setup Character Scene</summary>
  
  - Create new Node2D and rename to "Character"
    - `Node2D` because we won't be moving the character around or using colliders
    - save as scene "character.tscn" to scene folder
  - Add sprite
    - Drag the "dragon" sprite onto the Character node; rename to "Sprite"
    - Set position to (0, 0) to center it
    - Change origin of sprite
      - Set y offset to -70
  - Add AudioStreamPlayer as child of Character
  - Add script to Character; rename to `character.gd` 
</details>

## 8. Game Manager Script

**8A. Setup "Next Turn" functionality**
- Setup game_manager.gd on main root node (responsible for handling game state, managing turns, and game over screens)
- Create function handles switching turns between player and AI
  - Check if game is over (state)
  - Call `end_turn()` for current character (player or AI)
  - Set the current character to the opposite character
  - Call `begin_turn()` for new current character

  <details><summary>8A. Solution</summary>
    
  
    ```gd
      extends Node2D
      
      @export var player_character : Character
      @export var ai_character : Character
      var current_character : Character
      
      var game_over : bool = false
  
      func next_turn():
        if game_over:
          return
      
        if current_character != null:
          current_character.end_turn()
      
        if current_character == ai_character or current_character == null:
          current_character = player_character
        else:
          current_character = ai_character
      
        current_character.begin_turn()
    ```
  </details>

**8B. Handle player or AI actions during their turns**
- if Player turn
  - Enable player UI (use placeholder for now)
    - cast combat action based on player choice from UI 
- If AI turn
  - add artificial wait times (0.5 - 1.5 seconds) during turn and after turn (0.5) seconds
  - disable player UI if still active
  - cast combat action (placeholder for now)   

  <details><summary>8B. Solution</summary>
    
    ```gd
      # updated from previous game_manager.gd
      func next_turn():
        if game_over:
          return
      
        if current_character != null:
          current_character.end_turn()
      
        if current_character == ai_character or current_character == null:
          current_character = player_character
        else:
          current_character = ai_character
      
        current_character.begin_turn()
      
        if current_character.is_player:
          # Enable and set player UI
          pass # Placeholder for enabling player UI
        else:
          # Disable player UI if still active from the previous turn
          # Generate a wait time between 0.5 and 1.5 seconds
          var wait_time = randf_range(0.5, 1.5)
          await get_tree().create_timer(wait_time).timeout
          # Cast combat action (to be implemented)
          await get_tree().create_timer(0.5).timeout
          next_turn()
     ```
  </details>

**8C. Handle player casting combat action**
- Function that implements the action chosen from player UI during player turn
- Checks if current_character == player; if not, then exit
- Call `.cast_combat_function)` based on the `action` passed to the method
- Disable player UI (placeholder for now)
- Create delay of 0.5 seconds
- call next_turn()

  <details><summary>8C. Solution</summary>
    
    ```gd
      # updated from previous game_manager.gd
      func player_cast_combat_action(action : CombatAction):
        if player_character != current_character:
          return
      
        player_character.cast_combat_action(action, ai_character)
        # Disable player UI
        await get_tree().create_timer(0.5).timeout
        next_turn()
    ```
  </details>

## 9. Turns 

**9A. Setup Characters**
- Add Player and Computer Characters to main scene
  - Player character needs to be marked as "player"
  - both characters need to face each other and be positioned on opposite side and horizontally aligned
- Assign characters to game_manager scripts
  <details><summary>9A. Solution</summary>
    
    - Add Player character to main scene
      - position to -230 on x-axis
      - rename to "player_character"
      - enable "is_player" variable on inspector
    - Add Computer character to main scene
      - position to 230 on x-axis
      - rename to "ai_character"
      - under sprite in inspector, set "flip h" (?)
    - Assign to game manager
      - select game manager
      - drag and drop both characters to the appropiate character fields in the inspector 
  </details>

<details><summary>9B. Modify game_manager script to call `next_turn()` at the start of the game</summary>

  ```gd
    # game_manager.gd

    func _ready():
        next_turn()
  ```
</details>

## 11. Combat Actions 1

Combat actions
- defines various moves (ex: healing, attacking, etc)
- can be assigned to characters

Resources
- piece of data that can be saved to your game files and accessed whenever needed

**11A. Create Combat Action Script for resources**
- display_name: A string that represents the name of the combat action displayed in the UI.
- description: A string that explains what the combat action does.
- melee_damage: An integer that defines how much damage the combat action deals.
- heal_amount: An integer that defines how much health the combat action restores.
- base_weight: An integer that determines the likelihood of the AI choosing this action. Higher values increase the chance.

  <details><summary>11A. Solution</summary>
    
    ```gd
      # combat_action.gd, saved into Scripts folder
      class_name CombatAction
      extends Resource
      
      @export var display_name : String
      @export var description : String
      
      @export var melee_damage : int = 0
      @export var heal_amount : int = 0
      
      @export var base_weight : int = 100
    ```
  </details>

**11B. Create "Slash" combat action resource**
- should deal 8 melee damage

  <details><summary>11B. Solution</summary>
    
    - Right-click on the combat_actions folder.
      - Select New Resource.
      - Search for and select CombatAction.
      - Name the new resource Slash.
    - Double-click on the Slash resource to open it in the inspector. Fill in the properties as follows:
      - display_name: Slash
      - description: Deal 8 damage
      - melee_damage: 8
      - heal_amount: 0
  </details>

<details><summary>11C. Create "Heal" action that heals for 10</summary>
  
  - Right-click on the combat_actions folder.
    - Select New Resource.
    - Search for and select CombatAction.
    - Name the new resource Heal.
  - Double-click on the "Heal" resource to open it in the inspector. Fill in the properties as follows:
    - display_name: Heal
    - description: Heal for 10 hit points
    - melee_damage: 0
    - heal_amount: 10
</details>

<details><summary>11D. Create "Heavy Hit" action that damages for 15 and is more desirable for AI to choose</summary>
  
  - Right-click on the combat_actions folder.
    - Select New Resource.
    - Search for and select CombatAction.
    - Name the new resource HeavyHit.
  - Double-click on the "HeavyHit" resource to open it in the inspector. Fill in the properties as follows:
    - display_name: Heavy Hit
    - description: Deal 15 damage
    - melee_damage: 15
    - heal_amount: 10
    - Base Weight: 150
</details>

<details><summary>11E. Apply combat actions to Characters</summary>
  
  - Open `character.gd` script, and in the variable section, add the following: `@export var combat_actions : Array[CombatAction]`
  - Assign combat action to Player and Computer characters by doing the following,
    - Select each character in the scene.
    - Find the Combat Actions array in the inspector.
    - Set the size of the array to 3.
    - Drag and drop the Heal, HeavyHit, and Slash actions into the array.
</details>

<details><summary>11F. Update `game_manager.gd` script so AI casts combat action</summary>
  
  ```gd
    # updated from previous game_manager.gd
    func next_turn():
      if game_over:
        return
    
      if current_character != null:
        current_character.end_turn()
    
      if current_character == ai_character or current_character == null:
        current_character = player_character
      else:
        current_character = ai_character
    
      current_character.begin_turn()
    
      if current_character.is_player:
        # Enable and set player UI
        pass # Placeholder for enabling player UI
      else:
        # Disable player UI if still active from the previous turn
        # Generate a wait time between 0.5 and 1.5 seconds
        var wait_time = randf_range(0.5, 1.5)
        await get_tree().create_timer(wait_time).timeout
        # new addition:
        var action_to_cast = ai_decide_combat_action()
        ai_character.cast_combat_action(action_to_cast, player_character)
        await get_tree().create_timer(0.5).timeout
        next_turn()
   ```
</details>

## 13. Combat Actions UI

<details><summary>13A. Setup Panel for player to select combat actions, that is positioned to bottom of screen</summary>
  
  - Add `CanvasLayer` node
    - add as child, `Panel` and rename to "CombatActionsUI"
      - set size to 450x155 px 
      - Change anchoring presets to `CenterBottom`  
</details>

**13B. Create UI Panel for Player**
- Left half: list of buttons representing different combat actions
- Right half: a description text that provides details about the selected action

  <details><summary>13B. Solution</summary>
    
    - Add `VBoxContainer` node as child of "CombatActionsUI"
      - rename to "ButtonContainer"
      - Position to be on the left half side
      - Add buttons (as placeholders, they will be changed later)
    - Add `RichTextLabel` as child to "CombatActionsUI"
      - Rename to "Description"
      - Position to be on right half of panel
      - Enable `BBCode` to allow for formatting (ex: bold)
      - Note: In case you find that the default font is blurry, go to the project settings and search for ‘msdf’. Under GUI -> Theme, turn on ‘default_font_multichannel_signed_distance_field’
  </details>


<details><summary>13C. Setup "Combat Action Buttons" in panel w/ code that sets the combat action (?)</summary>

  - Delete the buttons under "ButtonContainer" except one
  - Rename it to "CombatActionButton"
  - Create and attach new script, `combat_action_button.gd`

  ```gd
    extends Button
    
    class_name CombatActionButton
    
    var combat_action : CombatAction
    
    func set_combat_action(ca : CombatAction):
        combat_action = ca
        text = ca.display_name
  ```

  - Duplicate buttons, except for the last one, rename to "PassTurnButton" and remove the script from it
</details>

**13D. Setup script for UI panel**
- Reference UI elements (Buttons, label text)
- Reference game manager

  <details><summary>13D. Solution</summary>
  
    ```gd
      # combat_actions_ui.gd, attached to CombatActionsUI panel
      extends Panel
      
      @onready var button_container = $ButtonContainer
      var ca_buttons : Array[CombatActionButton]
      
      @onready var description_text : RichTextLabel = $Description
      @onready var game_manager = $"../.."
    ```
  </details>

**13E. Implement Combat Action Buttons**
- Select "CombatActionsUI" script, "combat_actions_ui.gd"
- Upon start of game, connect all "CombatActionButton" nodes to functions, that execute when user triggers it (mouse press, mouse enter (hover), mouse exit)
  - `_button_pressed (button : CombatActionButton)` - calls `game_manager.player_cast_combat_action(button.combat_action)`
  - `_button_entered (button : CombatActionButton)` - updates "Description" node text to display combat action name and description
  - `_button_exited (_button : CombatActionButton)` - clears "Description" node text

  <details><summary>13E. Solution</summary>
  
    ```gd
      # inside combat_actions_ui.gd
  
      #.....
      func _ready():
          for child in button_container.get_children():
              if child is not CombatActionButton:
                  continue
      
              ca_buttons.append(child)
              child.pressed.connect(_button_pressed.bind(child))
              child.mouse_entered.connect(_button_entered.bind(child))
              child.mouse_exited.connect(_button_exited.bind(child))
  
      func _button_pressed (button : CombatActionButton):
      	game_manager.player_cast_combat_action(button.combat_action)
      
      func _button_entered (button : CombatActionButton):
      	var ca = button.combat_action
      	description_text.text = "[b]" + ca.display_name + "[/b]\n" + ca.description
      
      func _button_exited (_button : CombatActionButton):
      	description_text.text = ""
    ```
  </details>

<details><summary>13F. Implement "PassTurnButton" pressed signal so it triggers the next turn</summary>

  - Need to select "PassTurnButton" then connect the "pressed" signal from inspector to the "_on_pass_turn_button_pressed()" function, as implement below
  ```gd
    # inside combat_actions_ui.gd

    #.....
    func _on_pass_turn_button_pressed():
      game_manager.next_turn()
  ```
</details>

**13G. Setup function to display correct combat actions for player**
- Need to create function that takes an array of combat actions, and assigns them to one by one to the buttons
- if there are more buttons than combat actions, then the extra buttons should be disabled
- Function will be called by "game_manager" script 
  <details><summary>13G. Solution</summary>
  
    ```gd
      # inside combat_actions_ui.gd
  
      #.....
      # will be called by game_manager.gd
      func set_combat_actions(actions: Array[CombatAction]):
        for i in len(ca_buttons):
            if i >= len(actions):
                ca_buttons[i].visible = false
                continue
    
            ca_buttons[i].visible = true
            ca_buttons[i].set_combat_action(actions[i])
    ```
  </details>

**13H. Connect Player UI to Game Manager**
- Reference combat actions UI panel from game manager code
- Toggle visibility of the panel based on whether it's the players turn or not
  <details><summary>13H. Solution</summary>
  
    ```gd
      # game_manager.gd
      @onready var player_ui = $CanvasLayer/CombatActionsUI
      
      func next_turn():
        if game_over:
          return
      
        if current_character != null:
          current_character.end_turn()
      
        if current_character == ai_character or current_character == null:
          current_character = player_character
        else:
          current_character = ai_character
      
        current_character.begin_turn()
      
        if current_character.is_player:
          player_ui.visible = true
          player_ui.set_combat_actions(player_character.combat_actions)
        else:
          player_ui.visible = false
          var wait_time = randf_range(0.5, 1.5)
          await get_tree().create_timer(wait_time).timeout
      
          var action_to_cast = ai_decide_combat_action()
          ai_character.cast_combat_action(action_to_cast, player_character)
      
          await get_tree().create_timer(0.5).timeout
          next_turn()
      
      func player_cast_combat_action(action: CombatAction):
        if player_character != current_character:
          return
      
        player_character.cast_combat_action(action, ai_character)
        player_ui.visible = false
        await get_tree().create_timer(0.5).timeout
        next_turn()
    ```
  </details>

## 17. Health Bar

<details><summary>17A. Add red Health Bar below character, that displays current hp / total hp</summary>

  - Open "character" scene
  - Add child node, `ProgressBar` and rename to "HealthBar"
  - Disable Show Percentage:
    - In the inspector, uncheck the Show Percentage option.
    - This will remove the percentage display, allowing us to show the actual health value.
  - Resize the Progress Bar:
    - Go to the Layout section and set the size.
    - For example, you can set the X size to 212 and the Y size to 27.
    - Adjust the position to place it below the character’s feet.
  - Change Visual Style:
    - Customize the visual appearance of the health bar.
    - In the inspector, go to Theme Overrides > Styles.
    - Create new style boxes for the background and fill, and set their colors.
    - For example, set the background to a dark gray and the fill to red.
  - Add Health text
    - add as child to "HealthBar" node, `Label` node and rename to "HealthText"
    - In the inspector, go to the Layout section and change the Layout Mode to Anchors.
      - Set the Anchors Preset to Full Rect to make the text fill the bounds of the progress bar.
    - Center the text by setting the Horizontal Alignment and Vertical Alignment to Center.
    - Adjust the font size and add a shadow effect for better visibility.
      - Go to Label Settings, create a new Label Settings resource, and adjust the font size and shadow color.
</details>
<details><summary>17B. Create script for Healthbar that updates the health</summary>

  ```gd
    # inside character_health_bar.gd, attached to HealthBar node
    extends ProgressBar
    
    @onready var health_text : Label = $HealthText
    
    func _ready ():
      var char = get_parent()
      max_value = char.max_health
      _update_value(char.cur_health)

      # connect health bar to when character takes damage or heals
      char.OnTakeDamage.connect(_update_value)
      char.OnHeal.connect(_update_value)
    
    func _update_value (health : int):
      value = health
      health_text.text = str(health) + " / " + str(int(max_value))
  ```
</details>

## 18. Casting Combat Actions

**18A. Implement `cast_combat_action(action: CombatAction, opponent: Character)`**
- function will determine whether to deal damage to opponent or heal the character based on the combat action chosen
- the damage or heal amount must be greater than 0 to take effect
- Implement error handling for `null` combat actions
  
  <details><summary>18A. Solution</summary>
  
    ```gd
      # character.gd
      func cast_combat_action (action : CombatAction, opponent : Character):
        if action == null:
          return
  
        if action.melee_damage > 0:
          opponent.take_damage(action.melee_damage)
      
        if action.heal_amount > 0:
          heal(action.heal_amount)
    ```
  </details>

**18B. Implement `take_damage(amount: int):`**
- subtracts the damage amount from character's current health
- emits a signal to update the health bar
- plays the "take_damage_sfx" sound effect
  <details><summary>18B. Solution</summary>
  
    ```gd
      # character.gd
      func take_damage (amount : int):
        cur_health -= amount
        OnTakeDamage.emit(cur_health)
        _play_audio(take_damage_sfx)
    ```
  </details>

**18C. Implement `heal(amount:int)`**
- add the "heal" amount to the character's current health
- ensure heal amount does not exceed character's max health
- emit signal to update health bar
- plays heal sound effect
  <details><summary>18C. Solution</summary>
  
    ```gd
      # character.gd
      func heal (amount : int):
        cur_health += amount
        cur_health = clamp(cur_health, 0, max_health)
        OnHeal.emit(cur_health)
        _play_audio(heal_sfx)
    ```
  </details>

## 19. AI Combat

- Weighted Randomness
  - allows us to influence the likelihood of certain combat actions being chosen by AI
  - Each action has a base weight that determines its default chance of being selected
  - the higher the base weight, the more likely the action is to be chosen
 
**19A. Implement AI decision Function**
- Establish necessary variables.
  - `weights: Array[int] = []` - keep track of weights
  - `total_weight` 
- Calculate the health percentages of the player and the AI.
  - get the float percentage values of characer healths (ex: `30.53%`) 
- Modify the weights based on health percentages.
- Choose a random combat action based on the modified weights.

  <details><summary></summary>
  
    ```gd
      func ai_decide_combat_action () -> CombatAction:
      	if ai_character != current_character:
      		return null
      	
      	var ai = ai_character
      	var player = player_character
      	
      	var actions = ai.combat_actions
      	
      	var weights : Array[int] = []
      	var total_weight = 0
      	
      	var ai_health_perc = float(ai.cur_health) / float(ai.max_health)
      	var player_health_perc = float(player.cur_health) / float(player.max_health)
      	
      	for action in actions:
      		var weight : int = action.base_weight
      		
      		if player.cur_health <= action.melee_damage:
      			weight *= 3
      		if action.heal_amount > 0:
      			weight *= 1 + (1 - ai_health_perc)
      		
      		weights.append(weight)
      		total_weight += weight
      	
      	var cumulative_weight = 0
      	var rand_weight = randi_range(0, total_weight)
      	
      	for i in len(actions):
      		cumulative_weight += weights[i]
      		
      		if rand_weight < cumulative_weight:
      			return actions[i]
      	
      	return null
    ```
  </details>

<details><summary></summary>

  - 
</details>
<details><summary></summary>

  - 
</details>
