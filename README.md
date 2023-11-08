# learn
import pygame
import sys

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 600, 600
LINE_WIDTH = 15
BOARD_ROWS, BOARD_COLS = 3, 3
SQUARE_SIZE = WIDTH // BOARD_ROWS

# Colors
WHITE = (193,205,205)
CYAN = (0,139,139)
RED = (255, 0, 0)

# Setup Window
win = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Tic Tac Toe')

# Fonts
END_FONT = pygame.font.SysFont('comicsans', 40)

# Variables
turn = 'X'
board = [['' for _ in range(BOARD_ROWS)] for _ in range(BOARD_COLS)]
game_over = False
score = {'X': 0, 'O': 0}

def draw_lines():
    for i in range(BOARD_ROWS - 1):
        pygame.draw.line(win, CYAN, (0, (i + 1) * SQUARE_SIZE), (WIDTH, (i + 1) * SQUARE_SIZE), LINE_WIDTH)
        pygame.draw.line(win, CYAN, ((i + 1) * SQUARE_SIZE, 0), ((i + 1) * SQUARE_SIZE, HEIGHT), LINE_WIDTH)

def draw_board():
    for row in range(BOARD_ROWS):
        for col in range(BOARD_COLS):
            if board[row][col] == 'X':
                x = col * SQUARE_SIZE
                y = row * SQUARE_SIZE
                pygame.draw.line(win, RED, (x + 10, y + 10), (x + SQUARE_SIZE - 10, y + SQUARE_SIZE - 10), 5)
                pygame.draw.line(win, RED, (x + SQUARE_SIZE - 10, y + 10), (x + 10, y + SQUARE_SIZE - 10), 5)
            elif board[row][col] == 'O':
                x = col * SQUARE_SIZE + SQUARE_SIZE // 2
                y = row * SQUARE_SIZE + SQUARE_SIZE // 2
                pygame.draw.circle(win, RED, (x, y), SQUARE_SIZE // 2 - 10, 5)

def check_winner():
    for i in range(BOARD_ROWS):
        if all(board[i][j] == turn for j in range(BOARD_COLS)) or all(board[j][i] == turn for j in range(BOARD_ROWS)):
            return True
    if all(board[i][i] == turn for i in range(BOARD_ROWS)) or all(board[i][BOARD_COLS - 1 - i] == turn for i in range(BOARD_ROWS)):
        return True
    return False

def restart_game():
    global board, turn, game_over
    board = [['' for _ in range(BOARD_ROWS)] for _ in range(BOARD_COLS)]
    turn = 'X'
    game_over = False

def display_message(message):
    pygame.time.delay(500)
    win.fill(WHITE)
    end_text = END_FONT.render(message, True, CYAN)
    win.blit(end_text, ((WIDTH - end_text.get_width()) // 2, (HEIGHT - end_text.get_height()) // 2))
    pygame.display.update()
    pygame.time.delay(2000)

def draw_score():
    score_text = END_FONT.render(f"X: {score['X']}  O: {score['O']}", True, CYAN)
    win.blit(score_text, (10, 10))

# Game Loop
run = True
while run:
    win.fill(WHITE)
    draw_lines()
    draw_board()
    draw_score()

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False

        if not game_over and event.type == pygame.MOUSEBUTTONDOWN:
            x, y = event.pos
            col = x // SQUARE_SIZE
            row = y // SQUARE_SIZE

            if board[row][col] == '':
                board[row][col] = turn
                if check_winner():
                    display_message(f"Player {turn} wins!")
                    score[turn] += 1
                    game_over = True
                    display_message(f"Press space to restart")
                    game_over = True
                elif all(board[i][j] != '' for i in range(BOARD_ROWS) for j in range(BOARD_COLS)):
                    display_message("It's a tie!")
                    game_over = True
                    display_message(f"Press space to restart")
                    game_over = True
                else:
                    turn = 'O' if turn == 'X' else 'X'
                    

        if game_over and event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                restart_game()

    pygame.display.update()

pygame.quit()
