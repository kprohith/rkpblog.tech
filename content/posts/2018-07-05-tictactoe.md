---
toc: true
layout: post
description: TicTacToe in Python
categories: [markdown]
title: TicTacToe in python - Commandline
---


# TicTacToe in Python - Commandline


###  A text-based version of the [popular two player game](https://en.wikipedia.org/wiki/Tic-tac-toe).

###  Size of the game is _dynamically_ set depending on the user's choice.

####  This game is entirely written in [python](https://python.org). 

#### This script uses [colorama](https://pypi.org/project/colorama/) for adding color to the commandline output.
#### Install colorama to see colored output.

```python
# @author = RKP
# python version = 3.7
import itertools
from colorama import Fore, Back, Style, init

init()

def win(current_game):

	def all_same(l):
		if l.count(l[0]) == len(l) and l[0] != 0:
			return True
		else:
			return False
	# horizontal
	for row in game:
		print(row)
		if all_same(row):
			print(f"Player {row[0]} is the winne horizontally!")
			return True
	# vertical
	for col in range(len(game[0])):
		check = []
		for row in game:
			check.append(row[col])
		if all_same(check):
			print(f"Player {check[0]} is the winner vertically!")
			return True

	# / diagonal
	diags = []
	for idx, reverse_idx in enumerate(reversed(range(len(game)))):
		diags.append(game[idx][reverse_idx])

	if all_same(diags):
		print(f"Player {diags[0]} has won diagonally (/)")
		return True

	# \ diagonal
	diags = []
	for ix in range(len(game)):
		diags.append(game[ix][ix])

	if all_same(diags):
		print(f"Player {diags[0]} has won Diagonally (\\)")
		return True

	return False



def game_board(game_map, player=0, row=0, column=0, just_display=False):
	
	try:
		if game_map[row][column] != 0:
			print("This space is occupied, try another!")
			return False
		

		print("   "+"  ".join([str(i) for i in range(len(game_map))]))
		if not just_display:
			game_map[row][column] = player

		for count, row in enumerate(game_map):
			colored_row = ""
			for item in row:
				if item == 0:
					colored_row += "   "
				elif item == 1:
					colored_row += Fore.GREEN + " X " + Style.RESET_ALL
				elif item == 2:
					colored_row += Fore.MAGENTA + " O " + Style.RESET_ALL
		print(count, colored_row)
		return game_map
	except IndexError:
		print("ERROR")
		return False
	except Exception as e:
		print(str(e))
		return False
        


play = True
players = [1, 2]
while play:
	game_size = int(input("What size game TicTacToe?"))
	game = [[0 for i in range(game_size)] for i in range(game_size)]

	game_won = False
	player_cycle = itertools.cycle([1, 2])
	game_board(game, just_display=True)
	while not game_won:
		current_player = next(player_cycle)
		played = False
		while not played:
			
			print(f"Player: {current_player}")
			column_choice = int(input("Which column?"))
			row_choice = int(input("Which row?"))
			played = game_board(game, player=current_player, row=row_choice, column=column_choice)

		if win(game):
			game_won = True
			again = input("The game is over, would you like to play again? (y/n)")
			if again.lower() == "y":
				print("Restarting!")
			elif again.lower() == "n":
				print("Bye!")
				play = False
			else:
				print("Not a valid answer, but see you!")
				play = False

```






