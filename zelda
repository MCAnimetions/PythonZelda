import sys
import time

class Player:
    def __init__(self):
        self.hearts = 3
        self.max_hearts = 3
        self.inventory = []
        self.current_room = "link_house"
        self.game_progress = 0  # Percentage completed

    def is_alive(self):
        return self.hearts > 0

class Room:
    def __init__(self, name, description, exits, items=None, enemy=None, progress_value=0):
        self.name = name
        self.description = description
        self.exits = exits  # Dict of direction -> room_key
        self.items = items if items else []  # List of dicts
        self.enemy = enemy  # Dict
        self.progress_value = progress_value

# --- GAME WORLD SETUP ---
WORLD = {
    "link_house": Room(
        name="Link's House",
        description="You wake up to a crackling thunderstorm outside. A cozy bed sits in the corner, and a **wooden chest** rests at the foot of it. Light streams from the **door** leading out into Hyrule.",
        exits={"up": "hyrule_field_south"},
        items=[{"name": "wooden chest", "content": "lamp", "message": "You open the chest and find a **Lamp**!"}],
        progress_value=10
    ),
    "hyrule_field_south": Room(
        name="South Hyrule Field",
        description="Rain pours down on the lush, grassy plains of Hyrule. To the north stands the towering wall of Hyrule Castle. A **sign** is planted near the path.",
        exits={"down": "link_house", "up": "castle_gate"},
        items=[{"name": "sign", "content": "hint", "message": "The sign reads: 'Danger! Hyrule Castle guards are on high alert. Seek weapons before entering!'"}]
    ),
    "castle_gate": Room(
        name="Hyrule Castle Gate",
        description="Massive stone walls block your path. A **patrolling soldier** stands guard by the heavily bolted iron **gate**.",
        exits={
            "bottom center": "hyrule_field_south", 
            "upper right": "castle_courtyard"
        },
        items=[{"name": "patrolling soldier", "content": "fight", "message": "The guard spots you and draws his sword!"}],
        enemy={"name": "Hyrule Soldier", "hearts": 2, "attack": 1},
        progress_value=25
    ),
    "castle_courtyard": Room(
        name="Castle Courtyard",
        description="Overgrown bushes and wet stone pathways make up the courtyard. A secret **drainage pipe** leads into the castle dungeons.",
        exits={
            "lower left": "castle_gate",
            "up": "dungeon_cell"
        },
        items=[{"name": "drainage pipe", "content": "enter", "message": "You pry open the grate and slip inside the damp castle dungeons."}],
        progress_value=40
    ),
    "dungeon_cell": Room(
        name="Dungeon Cell",
        description="You are locked inside a gloomy stone cell. Princess Zelda's telepathic voice echoes in your head. A rusty **cell door** is locked, but a **skeletal guard** dropped a **small key** nearby.",
        exits={"down": "castle_courtyard"},
        items=[
            {"name": "small key", "content": "small key", "message": "You pick up the **small key**."},
            {"name": "cell door", "content": "unlock", "message": "You unlock the cell door and step out into the corridor!"}
        ],
        progress_value=60
    )
}

def display_status(player):
    hearts_display = "❤️ " * player.hearts + "🤍 " * (player.max_hearts - player.hearts)
    print(f"\n[Health: {hearts_display}] | [Inventory: {', '.join(player.inventory) if player.inventory else 'Empty'}]")
    print("-" * 50)

def describe_room(room):
    print(f"\n--- {room.name.upper()} ---")
    print(room.description)
    
    print("\nAvailable Directions:")
    exits = room.exits
    if len(exits) == 1:
        direction = list(exits.keys())[0]
        print(f" -> {direction.capitalize()}")
    else:
        for direction in sorted(exits.keys()):
            print(f" -> {direction.capitalize()}")

def run_tutorial():
    print("==================================================")
    print("               WELCOME TO THE TUTORIAL            ")
    print("==================================================")
    print("Before your adventure begins, let's learn how to interact with the world.\n")
    
    input("Press Enter to continue...")
    print("\n1. **Movement**: When you are in a location, available directions will be listed.")
    print("   If there is only one option, you type 'up', 'down', 'left', or 'right'.")
    print("   If multiple options branch out, you choose specific paths like 'upper right' or 'bottom center'.")
    
    input("\nPress Enter to continue...")
    print("\n2. **Interactive Objects**: Look for items or objects written in **bold text** in room descriptions.")
    print("   To interact with them, simply type the name of the object directly!")
    print("   Example: `wooden chest`")

    input("\nPress Enter to continue...")
    print("\n3. **Special Commands**: You can use these commands at any time during the game or by pausing:")
    print("   - `help`: Lists all available commands and their functions.")
    print("   - `inv`: Displays your current item inventory.")
    print("   - `percentfin`: Shows your overall game completion percentage.")
    print("   - `pause`: Pauses the game so you can check inventory or view help safely.")
    
    print("\n==================================================")
    input("Tutorial complete! Press Enter to start your adventure...\n")

def handle_pause_menu(player):
    print("\n==================================================")
    print("                  GAME PAUSED                     ")
    print("==================================================")
    print("You are in the pause menu. Type any command or type 'resume' to return to the game.")
    
    while True:
        cmd = input("\n[Paused] Enter command: ").strip().lower()
        
        if cmd == "resume":
            print("Resuming game...\n")
            break
        elif cmd == "help":
            print("\n--- AVAILABLE COMMANDS ---")
            print("  help        - Gives you a list of text containing all commands and their functions.")
            print("  inv         - Gives you a list of your current item inventory.")
            print("  percentfin  - Tells you the percentage of the game that you have completed.")
            print("  pause       - Pauses the game and opens this menu.")
            print("  [object]    - Directly type a bolded object name in your surroundings to interact with it.")
            print("  quit / exit - Quits the game.")
        elif cmd == "inv":
            print(f"\nInventory: {', '.join(player.inventory) if player.inventory else 'Your inventory is empty.'}")
        elif cmd == "percentfin":
            print(f"\nGame Completion: {player.game_progress}%")
        elif cmd in ["quit", "exit"]:
            print("Exiting game.")
            sys.exit()
        else:
            print("Unknown command while paused. Type 'help' for options or 'resume' to return.")

def handle_combat(player, enemy):
    print(f"\n⚠️  COMBAT ENCOUNTER: A hostile {enemy['name']} attacks you!")
    while enemy['hearts'] > 0 and player.is_alive():
        print(f"\n{enemy['name']} Hearts: {'❤️ ' * enemy['hearts']}")
        action = input("Combat Command (attack / run / pause): ").strip().lower()
        
        if action == "pause":
            handle_pause_menu(player)
            continue
        elif action == "attack":
            damage = 1 if "Fighter's Sword" in player.inventory else 0.5
            enemy['hearts'] -= damage
            print(f"You slash the {enemy['name']} for {damage} damage!")
            
            if enemy['hearts'] <= 0:
                print(f"✨ You defeated the {enemy['name']}!")
                break
                
            player.hearts -= enemy['attack']
            print(f"The {enemy['name']} strikes back, dealing {enemy['attack']} heart of damage! Your hearts: {player.hearts}")
        elif action == "run":
            print("You managed to escape back to safety!")
            return False
        else:
            print("Invalid combat command! Choose 'attack', 'run', or 'pause'.")
            
    return player.is_alive()

def play_game():
    run_tutorial()
    
    player = Player()
    print("==================================================")
    print(" THE LEGEND OF ZELDA: A LINK TO THE PAST (TEXT EDITION)")
    print("==================================================")
    
    player.inventory.append("Fighter's Sword")

    while player.is_alive():
        current_room = WORLD[player.current_room]
        
        if current_room.progress_value > player.game_progress:
            player.game_progress = current_room.progress_value

        if current_room.enemy and current_room.enemy['hearts'] > 0:
            survived = handle_combat(player, current_room.enemy)
            if not survived:
                break
            if current_room.enemy['hearts'] <= 0:
                current_room.enemy = None 

        describe_room(current_room)
        display_status(player)

        user_input = input("\nEnter command: ").strip().lower()

        if user_input in ["quit", "exit"]:
            print("Thanks for playing!")
            break
            
        if user_input == "pause":
            handle_pause_menu(player)
            continue

        if user_input == "help":
            print("\n--- AVAILABLE COMMANDS ---")
            print("  help        - Gives you a list of text containing all commands and their functions.")
            print("  inv         - Gives you a list of your current item inventory.")
            print("  percentfin  - Tells you the percentage of the game that you have completed.")
            print("  pause       - Pauses the game to open the pause menu.")
            print("  [object]    - Directly type a bolded object name in your surroundings to interact with it.")
            continue

        if user_input == "inv":
            print(f"\nInventory: {', '.join(player.inventory) if player.inventory else 'Your inventory is empty.'}")
            continue

        if user_input == "percentfin":
            print(f"\nGame Completion: {player.game_progress}%")
            continue

        # Handle Movement
        if user_input in current_room.exits:
            player.current_room = current_room.exits[user_input]
            print(f"\nYou move {user_input}.")
            continue
            
        # Handle Direct Object Interactions
        item_found = None
        for item in current_room.items:
            if item['name'] == user_input:
                item_found = item
                break
        
        if item_found:
            print(f"\n{item_found['message']}")
            if item_found['content'] not in ["hint", "fight"]:
                if item_found['content'] not in player.inventory:
                    player.inventory.append(item_found['content'])
            if user_input == "drainage pipe":
                player.current_room = "dungeon_cell"
            continue

        print("\nInvalid command or direction! Type 'help' to see available commands.")

    if not player.is_alive():
        print("\nGAME OVER. You have fallen in battle...")

if __name__ == "__main__":
    play_game()
