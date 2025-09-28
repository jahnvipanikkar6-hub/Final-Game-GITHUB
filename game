import pygame
import random
import sys

pygame.init()

# Screen settings
WIDTH, HEIGHT = 800, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Catch the Coins Challenge")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED   = (200, 0, 0)
GOLD  = (255, 215, 0)
BROWN = (139, 69, 19)
PURPLE = (128, 0, 128)  # Monster color

# Clock
clock = pygame.time.Clock()
FPS = 60

# Player
player = pygame.Rect(50, HEIGHT - 60, 40, 40)
player_vel_y = 0
gravity = 1
on_ground = True
coins = 5  # start with some coins so player doesn't die immediately

# Monster
monster = pygame.Rect(-100, HEIGHT - 60, 40, 40)  # start behind
monster_speed = 1
monster_min_distance = 60
monster_boost_timer = 0

# Obstacles
obstacles = []

# Coins
coin_list = []

# Fonts
font = pygame.font.SysFont(None, 36)
small_font = pygame.font.SysFont(None, 28)
title_font = pygame.font.SysFont(None, 44)

# Spawn timers
obstacle_timer = 0
coin_timer = 0

# Game timer
TOTAL_TIME = 50  # seconds

# Instruction screen
show_instructions = True
obstacle_hits = 0  # track how many obstacles hit

while True:
    screen.fill(WHITE)

    if show_instructions:
        title = title_font.render("Catch the Coins Challenge", True, BLACK)
        screen.blit(title, (WIDTH//2 - 200, 50))

        instructions = [
            "Rules:",
            "1. Get away from the monster as far as possible.",
            "2. Don't hit obstacles, or monster will get closer!",
            "3. Don't let your coins drop to 0; you'll die instantly.",
            "4. Collect 20 coins to win before time runs out.",
            "Press SPACE to start."
        ]
        for i, line in enumerate(instructions):
            text = small_font.render(line, True, BLACK)
            screen.blit(text, (50, 150 + i*30))

        pygame.display.flip()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                show_instructions = False
                start_ticks = pygame.time.get_ticks()
        continue

    dt = clock.tick(FPS)

    # Timer
    seconds_passed = (pygame.time.get_ticks() - start_ticks) / 1000
    time_left = max(0, TOTAL_TIME - seconds_passed)

    # Lose if timer runs out before collecting 20 coins
    if time_left <= 0 and coins < 20:
        lose_text = font.render("Time ran out! 😱", True, RED)
        screen.blit(lose_text, (WIDTH//2 - 150, HEIGHT//2))
        pygame.display.update()
        pygame.time.wait(3000)
        break

    # Events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and on_ground:
                player_vel_y = -15
                on_ground = False

    # Controls: SHIFT to power-up (cost 2 coins)
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LSHIFT] and coins >= 2:
        player.x += 15
        coins -= 2

    # Gravity
    player.y += player_vel_y
    player_vel_y += gravity
    if player.y >= HEIGHT - 60:
        player.y = HEIGHT - 60
        player_vel_y = 0
        on_ground = True

    player.x = max(0, min(WIDTH - player.width, player.x))

    # Monster speedup logic
    monster_boost_timer += 1
    if monster_boost_timer > random.randint(120, 300):
        monster_speed = min(5, monster_speed + 0.5)
        monster_boost_timer = 0
    else:
        monster_speed = max(1, monster_speed)

    # Monster distance adjusts with obstacle hits
    effective_distance = monster_min_distance - obstacle_hits * 5
    effective_distance = max(20, effective_distance)  # never get too close instantly
    if player.x - monster.x > effective_distance:
        monster.x += monster_speed

    # Spawn obstacles
    obstacle_timer += 1
    if obstacle_timer > 90:
        obstacle_timer = 0
        x = WIDTH
        y = HEIGHT - 40
        obstacles.append(pygame.Rect(x, y, 30, 30))

    # Spawn coins
    coin_timer += 1
    if coin_timer > 60:
        coin_timer = 0
        x = WIDTH
        y = HEIGHT - random.choice([100, 140, 180])
        coin_list.append((x, y, 10))

    # Move obstacles left
    for obstacle in obstacles[:]:
        obstacle.x -= 5
        if obstacle.right < 0:
            obstacles.remove(obstacle)

    # Move coins left
    new_coins = []
    for coin in coin_list:
        cx, cy, r = coin
        cx -= 5
        if cx + r > 0:
            new_coins.append((cx, cy, r))
    coin_list = new_coins

    # Check collisions with coins
    for coin in coin_list[:]:
        cx, cy, r = coin
        coin_rect = pygame.Rect(cx - r, cy - r, r*2, r*2)
        if player.colliderect(coin_rect):
            coins += 1
            coin_list.remove(coin)

    # Collisions with obstacles
    for obstacle in obstacles[:]:
        if player.colliderect(obstacle):
            if coins > 0:
                coins -= 1
            obstacle_hits += 1  # monster gets closer with hits
            obstacles.remove(obstacle)

    # Kill player immediately if coins reach 0
    if coins <= 0:
        lose_text = font.render("You ran out of coins! 😱", True, RED)
        screen.blit(lose_text, (WIDTH//2 - 150, HEIGHT//2))
        pygame.display.update()
        pygame.time.wait(3000)
        break

    # Win condition: 20 coins collected
    if coins >= 20:
        win_text = font.render("You collected 20 coins! 🎉", True, (0, 150, 0))
        screen.blit(win_text, (WIDTH//2 - 180, HEIGHT//2))
        pygame.display.update()
        pygame.time.wait(3000)
        break

    # Monster catches player if coins < 2 after 20s
    if monster.x + monster.width >= player.x and seconds_passed > 20 and coins < 2:
        lose_text = font.render("Caught by monster! 😱", True, RED)
        screen.blit(lose_text, (WIDTH//2 - 150, HEIGHT//2))
        pygame.display.update()
        pygame.time.wait(3000)
        break

    # Draw player and monster
    pygame.draw.rect(screen, RED, player)
    pygame.draw.rect(screen, PURPLE, monster)

    # Draw obstacles
    for obstacle in obstacles:
        pygame.draw.rect(screen, BROWN, obstacle)

    # Draw coins
    for coin in coin_list:
        pygame.draw.circle(screen, GOLD, (coin[0], coin[1]), coin[2])

    # HUD
    text = font.render(f"Coins: {coins}", True, BLACK)
    screen.blit(text, (10, 10))
    timer_text = font.render(f"Time: {int(time_left)}s", True, BLACK)
    screen.blit(timer_text, (WIDTH - 150, 10))

    # Instructions
    instruct1 = small_font.render("Press SPACE to Jump", True, BLACK)
    instruct2 = small_font.render("Press SHIFT to Power Up (costs 2 coins)", True, BLACK)
    screen.blit(instruct1, (WIDTH//2 - 100, 10))
    screen.blit(instruct2, (WIDTH//2 - 180, 40))

    pygame.display.flip()
