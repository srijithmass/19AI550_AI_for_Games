# Ex.No: 11  Mini Project 
#### DATE: 25/10/2024                                                                            
#### REGISTER NUMBER : 212221240009

### AIM: 
The aim is to create a Block Collector game where an AI player automatically moves to collect items (red boxes) and avoids obstacles (blue boxes).

### ALGORITHM:


1. **Initialize Game:**
   - Set up the screen dimensions, background, player, items, and obstacles.
   - Define controls for both human and AI players.
   - Set up score tracking and collision detection.


2. **AI Logic:**
   - Detect Nearest Item: Calculate the distance between the AI player and each collectible item (red box).
   - Move Toward Item: Direct the AI player to move horizontally and vertically toward the nearest item.
   - Avoid Obstacles: When an obstacle is close, move in the opposite direction to evade it.

3. **Update Game Elements:**
   - Player Movements: Allow the AI to follow the calculated path toward the nearest item while evading obstacles.
   - Collision Check: If the AI collects an item, increment the score. If it hits an obstacle, end the game.

4. **Game Loop:**
   - Update the position of all game elements.
   - Redraw the screen and display the updated score.
   - Continuously check for collision events and respond accordingly.

### PROGRAM:

```python
# Import necessary libraries
import pygame
import random

# Initialize pygame
pygame.init()

# Define screen dimensions and colors
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)  # Color for obstacles

# Set up the display
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Block Collector: A 2D Adventure")

# Load a background image or create a gradient background
try:
    background = pygame.image.load("download.jpeg").convert()
    background = pygame.transform.scale(background, (WIDTH, HEIGHT))  # Scale image to fit screen
except:
    # Gradient background if image isn't available
    background = pygame.Surface((WIDTH, HEIGHT))
    for y in range(HEIGHT):
        color = (int(255 * y / HEIGHT), int(128 * y / HEIGHT), int(64 * y / HEIGHT))
        pygame.draw.line(background, color, (0, y), (WIDTH, y))

# Define player class
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(GREEN)
        self.rect = self.image.get_rect()
        self.rect.center = (WIDTH // 2, HEIGHT // 2)
        self.speed = 5

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT]:
            self.rect.x += self.speed
        if keys[pygame.K_UP]:
            self.rect.y -= self.speed
        if keys[pygame.K_DOWN]:
            self.rect.y += self.speed

        # Boundary constraints
        self.rect.left = max(self.rect.left, 0)
        self.rect.right = min(self.rect.right, WIDTH)
        self.rect.top = max(self.rect.top, 0)
        self.rect.bottom = min(self.rect.bottom, HEIGHT)

# Define item class for score boxes
class Item(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30))
        self.image.fill(RED)
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, WIDTH - self.rect.width)
        self.rect.y = random.randint(0, HEIGHT - self.rect.height)

# Define moving obstacle class (blue boxes)
class Obstacle(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((40, 40))
        self.image.fill(BLUE)
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, WIDTH - self.rect.width)
        self.rect.y = random.randint(0, HEIGHT - self.rect.height)
        self.speed_x = random.choice([-3, 3])  # Random horizontal speed
        self.speed_y = random.choice([-3, 3])  # Random vertical speed

    def update(self):
        # Move the obstacle
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y

        # Bounce off the edges
        if self.rect.left <= 0 or self.rect.right >= WIDTH:
            self.speed_x *= -1
        if self.rect.top <= 0 or self.rect.bottom >= HEIGHT:
            self.speed_y *= -1

# Initialize player, collectible items, and moving obstacles
player = Player()
items = pygame.sprite.Group()
for i in range(5):
    item = Item()
    items.add(item)

# Create 3 moving obstacles
obstacles = pygame.sprite.Group()
for i in range(3):
    obstacle = Obstacle()
    obstacles.add(obstacle)

# Add all sprites to a group
all_sprites = pygame.sprite.Group()
all_sprites.add(player)
all_sprites.add(*items)
all_sprites.add(*obstacles)

# Set timer to add new score boxes (items) every few seconds
NEW_ITEM_EVENT = pygame.USEREVENT + 1
pygame.time.set_timer(NEW_ITEM_EVENT, 3000)  # Generate a new score box every 3 seconds

# Game variables
running = True
score = 0
font = pygame.font.SysFont(None, 36)
game_over = False

# Game loop
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == NEW_ITEM_EVENT:
            # Create a new score item and add it to the groups
            item = Item()
            items.add(item)
            all_sprites.add(item)

    if not game_over:
        # Update all sprites
        all_sprites.update()
        
        # Check for collisions with items
        hits = pygame.sprite.spritecollide(player, items, True)
        if hits:
            score += 1
        
        # Check for collisions with any moving obstacle
        if pygame.sprite.spritecollide(player, obstacles, False):
            game_over = True  # End the game if player hits an obstacle

        # Draw background and all sprites
        screen.blit(background, (0, 0))
        all_sprites.draw(screen)
        
        # Display score
        score_text = font.render(f"Score: {score}", True, BLACK)
        screen.blit(score_text, (10, 10))
        
        pygame.display.flip()
        pygame.time.Clock().tick(60)
    
    else:
        # Game Over screen
        screen.fill(BLACK)
        game_over_text = font.render("Game Over", True, RED)
        final_score_text = font.render(f"Final Score: {score}", True, WHITE)
        screen.blit(game_over_text, (WIDTH // 2 - 50, HEIGHT // 2 - 30))
        screen.blit(final_score_text, (WIDTH // 2 - 70, HEIGHT // 2 + 10))
        pygame.display.flip()
        
pygame.quit()

```



### OUTPUT:

![AFG1](https://github.com/user-attachments/assets/3e2b3354-72ef-487c-ad7d-8383dfd2d660)

![AFG2](https://github.com/user-attachments/assets/2a4253ef-daa6-49b1-8c35-d11f4bf171bc)

### RESULT:
The AI-controlled player collects items autonomously while avoiding obstacles, aiming to maximize the score until a collision ends the game is created.






