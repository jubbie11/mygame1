import pygame
import random

# Initialize Pygame
pygame.init()

# Set up display
screen = pygame.display.set_mode((800, 600))  # Change screen size
pygame.display.set_caption("Balloon Shooter")

# Colors
WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)  # Change bullet color
GREEN = (0, 255, 0)  # Change balloon color
YELLOW = (255, 255, 0)

# Set up clock for controlling frame rate
clock = pygame.time.Clock()

# Balloon properties
balloon_x = random.randint(50, 750)  # Change X coordinate range
balloon_y = 600
balloon_speed = 5

# Bullet properties
bullet_x = 400
bullet_y = 300
bullet_speed = -10
bullet_state = "ready"  # "ready" means you can't see the bullet on the screen

# Score
score = 0

# Font
font = pygame.font.Font(None, 36)

def show_score(x, y):
    score_display = font.render(f"Score: {score}", True, YELLOW)
    screen.blit(score_display, (x, y))

def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    pygame.draw.rect(screen, BLUE, [x + 16, y + 10, 4, 10])

def is_collision(balloon_x, balloon_y, bullet_x, bullet_y):
    distance = ((balloon_x - bullet_x) ** 2 + (balloon_y - bullet_y) ** 2) ** 0.5
    if distance < 27:
        return True
    else:
        return False

# Main game loop
running = True
while running:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Check for key press
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                if bullet_state == "ready":
                    bullet_x = balloon_x  # Change bullet's starting X position
                    fire_bullet(bullet_x, bullet_y)

    # Move the balloon
    balloon_y -= balloon_speed

    # Balloon reaches the top
    if balloon_y < 0:
        balloon_y = 600
        balloon_x = random.randint(50, 750)

    # Move the bullet
    if bullet_state == "fire":
        fire_bullet(bullet_x, bullet_y)
        bullet_y += bullet_speed

    # Check for collision
    collision = is_collision(balloon_x, balloon_y, bullet_x, bullet_y)
    if collision:
        bullet_y = 300  # Reset bullet position
        bullet_state = "ready"
        score += 1
        balloon_y = 600
        balloon_x = random.randint(50, 750)  # New balloon position

    # Reset bullet if it goes out of the screen
    if bullet_y <= 0:
        bullet_y = 300  # Change bullet reset position
        bullet_state = "ready"

    # Draw the balloon
    pygame.draw.circle(screen, GREEN, (balloon_x, balloon_y), 20)

    # Display the score
    show_score(10, 10)

    # Update the screen
    pygame.display.update()

    # Control the frame rate
    clock.tick(60)

# Quit Pygame
pygame.quit()
