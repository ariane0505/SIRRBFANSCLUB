RED = '\033[0;31m'             
FAINT = '\033[2m'               
BOLD = '\033[1m'               
BLINK = "\033[5m"
LIGHT_RED = "\033[1;31m"
YELLOW = "\033[1;33m"
ITALIC = '\033[3m'              
END = '\033[0m'                

def boxed_text(title, subtitle):
    # Calculate the box width
    title_length = len(title)
    subtitle_length = len(subtitle)
    
    # Determine the width of the box (based on the longest line)
    max_width = max(len(title), len(subtitle)) + 4  # Add padding for borders

    # Create the box
    top_border = f"{RED}{BOLD}╔{"═" * (max_width - 2)}╗"
    title_line = f"║ {title.center(max_width - 4)} ║"
    subtitle_line = f"║ {subtitle.center(max_width - 4)} ║"
    bottom_border = f"╚{"═" * (max_width - 2)}╝{END}"
    
    # Print the box
    print(top_border)
    print(title_line)
    print(subtitle_line)
    print(bottom_border)

# Display the title and subtitle
boxed_text("HANGMAN ODYSSEY", "an immersive word-guessing game with a thrilling mystery to solve!")

print(f"\n{RED}Here’s how to play:{END}")

print("═" * 150)
from tabulate import tabulate
# Define the headers for the table
col_names = [f"{BOLD}{FAINT}Objective", "Gameplay Instructions", "Guessing the Word", f"Winning and Losing{END}"]

# Define the content for 2 rows and 4 columns
instructions = [[
    f"""{FAINT}You are Detective Arby, tasked with solving riddles to uncover clues and save John from a sinister kidnapper. Solve each case by guessing the correct words and piecing together the story. 
        Beware: incorrect guesses bring the hangman closer to doom!""",
    f"""{FAINT}Each case consists of multiple levels with riddles to solve. For every riddle:
                     - A prompt will be displayed.
                     - You must guess the word that answers the riddle.
                     - Each word is represented by underscores (_) showing the number of letters.""",
    f"""{FAINT}You have 5 attempts per word. Type your guess and press Enter:
                - If correct: Progress to the next level and reveal part of the story.
                - If incorrect: You’ll lose an attempt. 
                        After 5 wrong attempts, the hangman advances a stage.""",
    f"""{FAINT}⁍Win: Solve all riddles in a case to uncover the full story and unlock the next case.
                ⁍Lose: If the hangman reaches the final stage, the case ends in failure. You’ll need to start again.{END}"""
]]

# Display the table with formatting
print(tabulate(instructions, headers=col_names, tablefmt="grid", maxcolwidths=35))

print("═" * 150)



# A function to display the current stage of the hangman bases on the number of incorrect guesses
def hangman(parts):
    # List of hangman stages, corresponding to the number of incorrect guesses
    hangman_stages = [
        ''' 
         ------ 
         |    |
              |
              |
              |
              |
        ---------
        ''',
        ''' 
         ------ 
         |    |
         O    |
              |
              |
              |
        ---------
        ''',
        ''' 
         ------ 
         |    |
         O    |
         |    |
              |
              |
        ---------
        ''',
        ''' 
         ------ 
         |    |
         O    |
        /|    |
              |
              |
        ---------
        ''',
        ''' 
         ------ 
         |    |
         O    |
        /|\\   |
              |
              |
        ---------
        ''',
        ''' 
         ------ 
         |    |
         O    |
        /|\\   |
        /     |
              |
        ---------
        ''',
        ''' 
         ------ 
         |    |
         O    |
        /|\\   |
        / \\   |
              |
        ---------
        '''
    ]
    
    # Ensure the number of parts does not exceed the maximum hangman stages to prevent "IndexError"
    # Prevents accessing an out-of-bounds index in the 'hangman_stages' list
    parts = min (parts, 6)
    
    # Print the hangman visual corresponding to the current hangman stage
    print(hangman_stages[parts])


# A function to display the game's homepage and menu
def homepage():
    print("\n--- WELCOME TO DETECTIVE HANGMAN! ---")
    print(
    """
    You are a private investigator tasked with solving a high-profile criminal case.
    Your client has been wrongfully accused of a heinous crime, and the gallows loom.
    Each word you guess reveals crucial evidence to clear their name.
    But be careful: five wrong guesses, and the hangman inches closer to doom!
    Can you solve all the mysteries? Let’s find out!
    
    Options:
    1. Start Case 1: The Vanishing Act
    2. Start Case 2: The Serpent's Wrath (Locked until Case 1 is completed)
    3. Start Case 3: The Final Showdown (Locked until Case 2 is completed)
    Q. Quit the game
    """
    )


# A function to display the specific case
def play_story(story_introduction, story_set, case_number, story_texts):
    print(f"\n\n{RED}--- CASE {case_number} BEGINS ---{END}\n")
    print (story_introduction)
    hangman_parts = 0  # Track the number of incorrect guesses
    max_tries = 3 # Maximum number of guesses per level

    # A for loop through each level in the story
    for level, (prompt, correct_word) in enumerate(story_set):
        print(f"\n{RED}Level {level + 1}:{END}") # Indicate the current level
        print(prompt) # Display the riddle/prompt for the current level

        # Display the word to guess as blanks
        current_state = ["_" for _ in range(len(correct_word))]
        print(f"{YELLOW}Word to guess: {' '.join(current_state)}{END}") # Show the word as underscores

        guessed_correctly = False # Flag to indicate if the word was guessed correctly
        tries = 0 # Counter for the number of attempts made for the curren word

        # Allow the user to guess the word until they ran out of tries or guess correctly
        while tries < max_tries:
            guess = input(f"Attempt {tries + 1}/{max_tries}. Guess the word: ").lower().strip()
            if guess == correct_word: # If the guess matches the correct word
                print("Correct! You've solved this level!")
                print(story_texts[level])  # Display the text for this level
                guessed_correctly = True # Set the flag to True
                break # Exit the existing loop
            else: # If the guess is incorrect
                tries += 1 # Increment the attemp counter
                if tries < max_tries:
                    print("Incorrect! Try again.") # Encourage the player to try again

        # If the user failed to guess correctly after the maximum number of tries
        if not guessed_correctly:
            hangman_parts += 1 # Increment the hangman stage
            hangman(hangman_parts) # Shows updated hangman
            print(f"You've used all your attempts! The correct word was '{correct_word}'.")

        # Check if the hangman is fully displayed
        if hangman_parts == 6:  # After 6 wrong guesses, the hangman "dies"
            print("\nGAME OVER! You've completed the hangman. Try again.")
            return False  # Indicate that the case was lost

    # If the user compeletes the story successfully
    print("\nCongratulations! You’ve completed this story.")
    print("\n--- FULL STORY RECAP ---")
    # Recap all riddles in the story
    for prompt, _ in story_set:
        print(prompt)
    return True


# Main game function
def main_game():
    # Narrative introductions for each story/case
    story_introductions = ["""CASE 1: THE VANISHING ACT

Arby receives a chilling call in the dead of night: Detective, your friend John is in my hands. 
Solve my riddles, or you'll never see him again. If you fail to save one, the clock starts to tick and John will end up hanging on a stick.
The voice doesn’t identify himself, leaving Arby with only one option: follow the clues left behind. Detective Arby 
races against time in hopes to save John, who could the assailant be?""", 

"""CASE 2: THE SERPENT'S WRATH
                       
Python’s past and motivations come to light as Arby digs into the history of his vengeful adversary.""", 

"""CASE 3:THE FINAL SHOWDOWN

Arby uses his detective skills to track Python and save John before it’s too late. He goes back to the city and through Python’s 
journal he was instructed to go to the clock tower. The Hangman’s Game begins, will Arby solve all the riddles and save John?"""
]
    story_sets = [
        # Story 1 riddles and answers
        [
            ("Arby arrives at John’s ransacked apartment. Scrawled on the wall is:\n \n 'I shatter and fall, but I reflect.'\n", "glass"),
            ("At the base of the fire escape, Arby finds a torn piece of fabric with a note: \n \n 'You wear me when it's cold, \n I'm made of leather or so I'm told.'\n", "jacket"),
            ("Tracing the taxi, Arby finds a receipt leading to a café. On the café table, there’s a riddle: \n \n 'I’m often held though I have no hands, \n Filled up but not with sands.'\n", "cup"),
            ("At the docks, Arby finds a locker with a riddle taped to it: \n \n 'I hold and guard secrets within me. \n If you wish for the answer, you must need a key.'\n", "lock"),
            ("The photo includes a handwritten message: \n \n 'I carry a tune or a thought to share, \n A reminder of something important, with messages I bear.'\n", "note"),
            ("Arby rushes to an old house on the west end of town. Inside, a clue reads: \n \n 'Sometimes I’m thin, sometimes I’m thick. \n I am filled with knowledge yet I cannot speak.'\n", "book"),
            ("A serpent symbol leads Arby to an underground passage. At the entrance is a final clue: \n \n 'I slither and slide without any feet. \n In deserts or jungles, we’re likely to meet.'\n", "snake"),
            ("The carvings confirm the name of the kidnapper. Scrawled below is: 'To save your friend, you must utter my name: \n \n Neither a serpent nor cobra, we are not the same. \n I'm a serpent that's large, with a mighty squeeze.'\n", "python"),
        ],
        # Story 2 riddles and answers
        [
            ("Arby investigates the prison Python ecscaped from. In Python’s cell, a message reads: \n \n 'I set prisoners free but require the right shape. \n If you are locked in, you need me to escape.'\n", "key"),
            ("The sewer entrance has a clue etched on the wall: \n \n 'Laces or straps, I help you choose. \n What am I? You call me ____.'\n", "shoes"),
            ("In the hideout, Arby finds a hint that Python was an alcoholic. \n \n 'I hold liquids, from water to wine. \n With a cap or a cork, I’m sealed just fine.'\n", "bottle"),
            ("Arby finds a note inside a broken bottle: \n \n 'I tell you the way, left or right, \n Straight ahead or out of sight. \n Follow me carefully, step by step, \n What am I, guiding your trek?' \n", "map"),
            ("At the bar, Arby was greeted by the waiter and was handed an old photograph. On the back, it says: \n \n 'I capture moments, both still and bright, \n With a flash or focus, I freeze the sight.'\n", "photo"),
            ("The journal contains Python’s memories and vengeful plans. It reads: \n \n 'I hold your thoughts, your dreams, your day, \n With pages to fill in a personal way. \n Write down your secrets, both big and small, \n What am I, that holds it all?'\n", "journal"),
            ("Inside the journal was written that Python wants revenge for being in prison and the hanging of John was just a start of the horrors to come. \n \n 'I mark your name with a flourish or pen, \n A symbol of you, again and again.'\n", "signature"),
            ("The journal ends with a note that taunts the detective: 'The chase was a bit odd, wasn’t it? Welcome to Hangman’s Game: \n \n Hangman’s toy, within the ropes, \n Through twists, ties, and loops.'\n", "knot"),
        ],
        # Story 3 riddles and answers
        [
            ("At the clock tower, Arby finds a note: \n \n 'I mark time but never stop. Pointing to hours as the minutes go. \n Ticking onward, a guiding band,\n  What am I, known as the clock's _'\n", "hands"),
            ("Inside the cathedral, Arby finds an altar with a riddle: \n \n 'I light the way but can be snuffed out.'\n", "candle"),
            ("In the tunnel, a message is scratched into the stone: \n n 'I pierce, I slash, with a deadly allure. \n What am I, sharp and swift, yet unsure?'\n", "knife"),
            ("A door with a strange lock has the clue: \n \n 'A symbol of faith, through pain and loss. \n What am I, known as the holy __?'\n", "cross"),
            ("Arby enters the room and is immediately ambushed by Python. He wakes up and finds himself tied to a chair, and across the room, John is in the gallows with a rope around his neck. Python says: \n \n 'I’m a rope that twists, in a fateful design. \n A loop that holds, in a sorrowful line. Tied with intent, my purpose is loose,\n What am I, known as a ___?'\n", "noose"),
            ("Arby: NOOSE! THE ANSWER IS NOOSE! \nPython: That’s right Arby, the answer is noose, however you are too late and now you lose! \nJust as Python was about to drop John, Arby throws a punch and sent him away, thus saving John. \n \nArby: You might be wondering how I escaped just as you were about to take John's life?, \n 'I was just in luck because from the sewers earlier I kept this ____!' \n", "knife"),
            ("Python tries to escape, but Arby catches him and says: \n \n 'You twist the truth, you cheat, you claw, \n But none escape my steady draw. \n I bring the mighty down to crawl, \n What am I? I am the ____.'\n", "law"),
            ("As dawn breaks, John thanks Arby. Python, now defeated, mutters: \n \n 'I’m always moving, yet I stand still. \n I can’t be bought, nor bent to your will.'\n", "time"),
        ],
    ]

    # Story-specific texts for solved riddles
    story_texts = [
        # Story 1 texts
        [
            "\nThe broken window leads to the fire escape.",
            "\nThe jacket bears a logo of a taxi company.",
            "\nIn a daze, Arby asks around, and a barista recalls a suspicious man heading toward the docks.",
            "\nInside is a photo of John tied to a chair.",
            "\nThe note says: 'Find me where the sun sets.'",
            "\nA bookmark points to a page detailing a serpent.",
            "\nThe passage reveals a name carved into the wall.",
            "\nArby realizes his enemy is Python and swears to bring him down.",
        ],
        # Story 2 texts
        [
            "\nThe key was used to escape through the sewer. Arby then ventures into the sewer to find more clues.",
            "\nThe detectives find a set of footprints within the sewers, the trail leads to an abandoned safe house.",
            "\nFor what use could the bottle be?", 
            "\nThe note entails a location to a bar.",
            "\nThe photo shows the jail cell that Python went to.",
            "\nThe journal contains Python’s memories and vengeful plans.",
            "\nIt is final! The motives of Python are as clear as day!",
            "\nTick tock, tick tock, \n Can Detective Arby save John against the clock?", 
        ],
        # Story 3 texts
        [
            "\nArby used his intellect to make use of the clues. Where do the hands point to? It was 10:30 and it doesn’t make sense. Suddenly it came to Arby that when the clock strikes 12:00, the hands point to the north, which indicates the location of the Cathedral.",
            "\nAfter lighting the candles in the cathedral, the light shows a path that connects to the back of the altar and behind it, a hidden tunnel.",
            "\nArby finds a knife at the end of the tunnel; the knife then points to a room.",
            "\nArby looks around and finds a cross; he notices that the cross and the lock match, thus making it the key.",
            "\nIn an intense scenario will Arby be able to answer? The story continues…",
            "\nTHE CLIMAX! The detective saves John but what of the enemy?",            
            "\nPython: Law! \n Arby: It’s over now, Python! This shall be the end of your journey, the end of The Hangman’s Odyssey!",
            '\n"Until next time, Detective," says Python. Arby knows his work is never truly done. Will Python break free again?',
        ],
    ]

    unlocked_stories = 1  # Start with only the first story unlocked

    # Main game loop
    while True:
        # shows the main menu
        homepage()
        choice = input("Choose an option (1, 2, 3, Q): ").lower()

        # Case 1
        if choice == "1":
            success = play_story(story_introductions [0], story_sets[0], 1, story_texts[0])
            if success: # If the player completes Case 1 successfully
                print("\n--- CONGRATULATIONS! YOU HAVE UNLOCKED THE NEXT STORY! ---")
                unlocked_stories = max(unlocked_stories, 2)  # Ensure Story 2 is unlocked

        # Case 2
        elif choice == "2":
            if unlocked_stories < 2: # Check if Case 2 is locked
                print("Story 2 is locked. Solve Story 1 first!")
            else: # If Case 2 is unlocked
                success = play_story(story_introductions [1],story_sets[1], 2, story_texts[1])
                if success:
                    print("\n--- WELL DONE! YOU'VE SOLVED BOTH STORIES! ---")
                    unlocked_stories = max(unlocked_stories, 3)  # Ensure Story 3 is unlocked

        # Case 3
        elif choice == "3":
            if unlocked_stories < 3: 
                print("Story 3 is locked. Solve Story 2 first!")
            else: # If Case 3 is unlocked
                success = play_story(story_introductions [2],story_sets[2], 3, story_texts[2])
                if success:
                    print("\n--- WELL DONE! YOU'VE SOLVED ALL THREE STORIES! ---")

        elif choice == "q": # Quit the game
            print("Thank you for playing Detective Hangman! Goodbye!")
            break

        # Handle invalid input
        else:
            print("Invalid choice. Please try again.")

# Calls the function to start the game
main_game()
