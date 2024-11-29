import pygame
import random
import sys

# Initialize pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Initialize screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Escape the Carnival")

# Clock for controlling frame rate
clock = pygame.time.Clock()

# Player properties
player_size = 30
player_pos = [100, 100]
player_speed = 5

# Clown properties
clown_size = 40
clown_pos = [random.randint(50, SCREEN_WIDTH - 50), random.randint(50, SCREEN_HEIGHT - 50)]
clown_speed = 3

# Exit properties
exit_size = 50
exit_pos = [SCREEN_WIDTH - 70, SCREEN_HEIGHT - 70]

# Lives
lives = 3

# Font
font = pygame.font.Font(None, 36)

def draw_text(text, color, x, y):
    """Helper function to draw text on the screen."""
    label = font.render(text, True, color)
    screen.blit(label, (x, y))

def detect_collision(player_pos, obj_pos, obj_size):
    """Check for collision between the player and another object."""
    px, py = player_pos
    ox, oy = obj_pos
    return (ox < px < ox + obj_size or ox < px + player_size < ox + obj_size) and \
           (oy < py < oy + obj_size or oy < py + player_size < oy + obj_size)

def clown_movement(player_pos, clown_pos, speed):
    """Simple AI for the clown to follow the player."""
    px, py = player_pos
    cx, cy = clown_pos

    if cx < px:
        cx += speed
    elif cx > px:
        cx -= speed

    if cy < py:
        cy += speed
    elif cy > py:
        cy -= speed

    return [cx, cy]

# Game loop
running = True
while running:
    screen.fill(BLACK)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_UP] and player_pos[1] > 0:
        player_pos[1] -= player_speed
    if keys[pygame.K_DOWN] and player_pos[1] < SCREEN_HEIGHT - player_size:
        player_pos[1] += player_speed
    if keys[pygame.K_LEFT] and player_pos[0] > 0:
        player_pos[0] -= player_speed
    if keys[pygame.K_RIGHT] and player_pos[0] < SCREEN_WIDTH - player_size:
        player_pos[0] += player_speed

    # Clown movement
    clown_pos = clown_movement(player_pos, clown_pos, clown_speed)

    # Check for collisions
    if detect_collision(player_pos, clown_pos, clown_size):
        lives -= 1
        player_pos = [100, 100]  # Reset player position
        if lives == 0:
            running = False

    if detect_collision(player_pos, exit_pos, exit_size):
        draw_text("You Escaped!", BLUE, SCREEN_WIDTH // 2 - 100, SCREEN_HEIGHT // 2)
        pygame.display.update()
        pygame.time.delay(2000)
      
        running = False

    # Draw everything
    pygame.draw.rect(screen, BLUE, (exit_pos[0], exit_pos[1], exit_size, exit_size))
    pygame.draw.rect(screen, RED, (clown_pos[0], clown_pos[1], clown_size, clown_size))
    pygame.draw.rect(screen, WHITE, (player_pos[0], player_pos[1], player_size, player_size))

    # Display lives
    draw_text(f"Lives: {lives}", WHITE, 10, 10)

    pygame.display.flip()
    clock.tick(30)

pygame.quit()
sys.exit()

