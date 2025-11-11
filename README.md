# Implement a Sudoku Solver From Scratch

## Introduction

Generate a valid 9×9 Sudoku solution, remove some cells to make a puzzle, and solve that same puzzle using a backtracking solver. This program demonstrates how to (1) build a full valid board, (2) make a playable puzzle by removing cells, and (3) solve the puzzle with the same backtracking algorithm.

## Problem statement

Write a Python program that:

Generates a complete, valid 9×9 Sudoku grid (a full solution).

Removes a chosen number of cells (keeps clues filled cells) to create a puzzle.

Solves the puzzle using backtracking and prints:

the generated full solution,

the puzzle (with empty cells shown as .),

the solved puzzle.

Constraints and notes:

The generator uses randomized backtracking to produce a valid solution.

The puzzle creation step removes cells randomly and does not guarantee a unique solution (simple approach).

The solver uses a classic row/column/3×3-box safety check and backtracking.

## Code
## sudoku_gen_solve.py
```
import random
import copy

N = 9  # 9x9 Sudoku

def print_grid(grid):
    for r in range(N):
        row = ""
        for c in range(N):
            val = grid[r][c]
            row += f"{val if val != 0 else '.'} "
            if (c + 1) % 3 == 0 and c < N - 1:
                row += "| "
        print(row)
        if (r + 1) % 3 == 0 and r < N - 1:
            print("-" * 21)
    print()

def find_empty(grid):
    for r in range(N):
        for c in range(N):
            if grid[r][c] == 0:
                return (r, c)
    return None

def used_in_row(grid, row, num):
    return any(grid[row][c] == num for c in range(N))

def used_in_col(grid, col, num):
    return any(grid[r][col] == num for r in range(N))

def used_in_box(grid, box_start_row, box_start_col, num):
    for r in range(3):
        for c in range(3):
            if grid[box_start_row + r][box_start_col + c] == num:
                return True
    return False

def is_safe(grid, row, col, num):
    return (not used_in_row(grid, row, num) and
            not used_in_col(grid, col, num) and
            not used_in_box(grid, row - row % 3, col - col % 3, num))

def solve_sudoku(grid):
    empty = find_empty(grid)
    if not empty:
        return True
    row, col = empty
    for num in range(1, 10):
        if is_safe(grid, row, col, num):
            grid[row][col] = num
            if solve_sudoku(grid):
                return True
            grid[row][col] = 0
    return False

def fill_grid_randomly(grid):
    empty = find_empty(grid)
    if not empty:
        return True
    row, col = empty
    nums = list(range(1, 10))
    random.shuffle(nums)
    for num in nums:
        if is_safe(grid, row, col, num):
            grid[row][col] = num
            if fill_grid_randomly(grid):
                return True
            grid[row][col] = 0
    return False

def generate_full_solution():
    grid = [[0] * N for _ in range(N)]
    fill_grid_randomly(grid)
    return grid

def remove_cells(grid, clues=30):
    puzzle = copy.deepcopy(grid)
    cells = [(r, c) for r in range(N) for c in range(N)]
    random.shuffle(cells)
    to_remove = 81 - max(1, min(81, clues))
    for i in range(to_remove):
        r, c = cells[i]
        puzzle[r][c] = 0
    return puzzle

if __name__ == "__main__":
    random.seed()  # use a fixed int for reproducible results, e.g. random.seed(42)
    # 1) Generate full valid solution
    full = generate_full_solution()
    print("Full solution (generated):")
    print_grid(full)

    # 2) Make a puzzle with a chosen number of clues (filled cells)
    clues = 30  # change to 35/40 for easier puzzles
    puzzle = remove_cells(full, clues=clues)
    print(f"Puzzle with {clues} clues:")
    print_grid(puzzle)

    # 3) Solve the same puzzle
    puzzle_copy = copy.deepcopy(puzzle)
    if solve_sudoku(puzzle_copy):
        print("Solved puzzle:")
        print_grid(puzzle_copy)
    else:
        print("No solution found.")
```

