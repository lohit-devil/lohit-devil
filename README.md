 import pygame
import random

# Initialize pygame
pygame.init()

# Define constants
GRID_SIZE = 8
CELL_SIZE = 80
WIDTH = GRID_SIZE * CELL_SIZE
HEIGHT = GRID_SIZE * CELL_SIZE
FPS = 60

# Define colors
WHITE = (255, 255, 255)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
YELLOW = (255, 255, 0)
PURPLE = (128, 0, 128)
ORANGE = (255, 165, 0)

COLORS = [RED, GREEN, BLUE, YELLOW, PURPLE, ORANGE]

# Initialize the game window
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Candy Crush-like Game")
clock = pygame.time.Clock()

# Create the grid
grid = [[random.choice(COLORS) for _ in range(GRID_SIZE)] for _ in range(GRID_SIZE)]

# Function to draw the grid
def draw_grid():
    for row in range(GRID_SIZE):
        for col in range(GRID_SIZE):
            color = grid[row][col]
            pygame.draw.rect(screen, color, (col * CELL_SIZE, row * CELL_SIZE, CELL_SIZE, CELL_SIZE))
            pygame.draw.rect(screen, WHITE, (col * CELL_SIZE, row * CELL_SIZE, CELL_SIZE, CELL_SIZE), 3)

# Function to handle mouse clicks
def get_cell_from_mouse(pos):
    x, y = pos
    return x // CELL_SIZE, y // CELL_SIZE

# Swap two cells
def swap_cells(cell1, cell2):
    r1, c1 = cell1
    r2, c2 = cell2
    grid[r1][c1], grid[r2][c2] = grid[r2][c2], grid[r1][c1]

# Check if there's a match of three or more candies
def check_matches():
    matches = []
    # Check horizontal matches
    for row in range(GRID_SIZE):
        for col in range(GRID_SIZE - 2):
            if grid[row][col] == grid[row][col+1] == grid[row][col+2]:
                matches.append(((row, col), (row, col+1), (row, col+2)))
    # Check vertical matches
    for col in range(GRID_SIZE):
        for row in range(GRID_SIZE - 2):
            if grid[row][col] == grid[row+1][col] == grid[row+2][col]:
                matches.append(((row, col), (row+1, col), (row+2, col)))
    return matches

# Function to clear matched candies
def clear_matches(matches):
    for match in matches:
        for cell in match:
            row, col = cell
            grid[row][col] = random.choice(COLORS)

# Function to drop candies after clearing matches
def drop_candies():
    for col in range(GRID_SIZE):
        empty_cells = []
        for row in range(GRID_SIZE-1, -1, -1):
            if grid[row][col] == None:
                empty_cells.append(row)
        for i in range(len(empty_cells)):
            for row in range(empty_cells[i]-1, -1, -1):
                grid[row+1][col] = grid[row][col]
                grid[row][col] = None

        for row in range(GRID_SIZE):
            if grid[row][col] == None:
                grid[row][col] = random.choice(COLORS)

# Main game loop
selected_cell = None
while True:
    screen.fill((0, 0, 0))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            quit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse_pos = pygame.mouse.get_pos()
            clicked_cell = get_cell_from_mouse(mouse_pos)

            if selected_cell:
                if clicked_cell != selected_cell:
                    swap_cells(selected_cell, clicked_cell)
                    matches = check_matches()
                    if matches:
                        clear_matches(matches)
                        drop_candies()
                    else:
                        # Undo the swap if no match is found
                        swap_cells(selected_cell, clicked_cell)
                selected_cell = None
            else:
                selected_cell = clicked_cell

    draw_grid()
    pygame.display.update()
    clock.tick(FPS)

