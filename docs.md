# Project Documentation

## Overview
This is a simple terminal-based slot machine game implemented in Python. The game uses a 3x3 reel matrix of emoji symbols and allows the player to bet on 1 to 3 lines. The player starts with a chosen number of coins and places a bet per line. Wins and losses update the player's coin total until they quit or run out of coins.

The program depends on the colorama package for colored terminal output.

## Requirements
- Python 3.6+
- colorama

Install dependency:

pip install colorama

## Running
Run the game from the repository root:

python main.py

Controls and inputs:
- When prompted for coins, enter a positive integer for your starting coins.
- Choose number of lines to bet: 1, 2, or 3.
- Enter bet per line as a positive integer. The total bet (bet * lines) must be <= current coins.
- Press ENTER to spin; type 'q' (or 'Q') at the prompt to quit the game.

The screen is cleared between steps (uses `clear` on POSIX, `cls` on Windows).

## Gameplay details
- The slot uses a 3 rows x 3 columns matrix.
- Symbols and their counts in the symbol pool:
  - 🥝 (2 copies)
  - 🍓 (4 copies)
  - 🍄 (6 copies)
  - 🍏 (8 copies)
- Each row is filled by randomly sampling symbols from the pool (no replacement per row sampling, rows are sampled independently).
- A "matched" row is one where all three symbols in that row are identical.
- Winning condition: if the number of matched rows is greater than or equal to the number of lines the player bet on, the player wins.

Payouts and losses:
- Win: player receives winnings = bet * 2 (this amount is added to their coins).
- Loss: player loses bet * lines coins (this amount is subtracted from their coins).

When coins reach 0, the game ends.

## Input validation & errors
- The game validates user input and raises friendly error messages for invalid entries (colored using colorama). Examples:
  - Non-digit or non-positive coin/line/bet inputs produce a prompt to enter a valid number.
  - Attempting to bet more than available coins raises an error message.
  - Number of lines must be between 1 and 3.

## API (functions in main.py)
This section documents the main functions in main.py for developers reading the code.

- init(): initializes colorama (called via colorama.init()).

- main(): entry point; initializes colorama and starts the game loop by calling play_slot().

- play_slot(): main interactive loop. Handles:
  - Asking for starting coins (get_coins)
  - Selecting number of lines to bet (get_lines)
  - Entering bet per line (get_bet)
  - Spinning the reels (get_matrix)
  - Displaying the matrix (print_matrix)
  - Determining win/loss (check_win)
  - Applying winnings/losses (get_win)
  - Looping until player quits or coins run out

- get_coins(coins_str) -> int
  - Validates the starting coins input string; converts to int and ensures > 0.
  - Clears the terminal on success and returns the integer amount.
  - Raises ValueError with a colored message on invalid input.

- get_lines(initial_lines, coins) -> int
  - Validates the chosen number of lines (must be digit, 1..3).
  - Ensures player has enough coins to bet the chosen number of lines.
  - Returns the integer number of lines or raises ValueError.

- get_bet(bet_str, coins, lines) -> int
  - Validates bet per line (digit, >0) and that total bet (bet * lines) <= coins.
  - Returns integer bet or raises ValueError.

- get_matrix(rows, cols) -> list[list[str]]
  - Constructs the pool of symbols according to fixed counts and returns a rows x cols matrix.
  - Each row is filled using random.sample from the symbol pool.

- print_matrix(matrix):
  - Nicely prints the matrix to the terminal with separators between columns.

- check_win(matrix, lines) -> bool
  - Checks each row for all-equal symbols and counts matched rows.
  - Returns True if matched_rows >= lines else False.

- get_win(win_lose, bet, coins, lines) -> int
  - If win_lose is True, adds winnings (bet*2) to coins and prints a colored success message.
  - If lose, subtracts bet*lines from coins and prints a colored loss message.
  - Returns the updated coin total.

## Notes / Implementation caveats
- The symbol population and payout rules are hard-coded.
- The win calculation awards a flat bet*2 regardless of how many matched rows are found, as long as matched_rows >= lines.
- The code uses random.sample per row; because sampling is done per row, symbols may repeat across rows.

## Contribution
For changes to gameplay rules (different payouts, symbol counts, matrix dimensions), update the corresponding functions in main.py and reflect those in this documentation.
