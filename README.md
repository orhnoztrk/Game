import pygame
import random

# Pygame'i başlat
pygame.init()

# Ekran boyutları
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Renkler
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)

# Ekranı oluştur
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption('2D Arcade Araba Yarışı')

# Arabalar ve engeller için ayarlar
car_width = 50
car_height = 100
player_x = SCREEN_WIDTH // 2 - car_width // 2
player_y = SCREEN_HEIGHT - car_height - 10
player_speed = 5

obstacle_width = 50
obstacle_height = 100
obstacle_speed = 5
obstacles = []

# Skor
score = 0
font = pygame.font.SysFont("Arial", 30)

# Engelleri oluşturma
def create_obstacle():
    obstacle_x = random.randint(0, SCREEN_WIDTH - obstacle_width)
    obstacle_y = -obstacle_height
    obstacles.append([obstacle_x, obstacle_y])

# Engelleri hareket ettirme
def move_obstacles():
    global score
    for obstacle in obstacles:
        obstacle[1] += obstacle_speed
        if obstacle[1] > SCREEN_HEIGHT:
            obstacles.remove(obstacle)
            score += 1

# Çarpışma kontrolü
def check_collision(player_x, player_y, obstacles):
    for obstacle in obstacles:
        if (player_x < obstacle[0] + obstacle_width and
            player_x + car_width > obstacle[0] and
            player_y < obstacle[1] + obstacle_height and
            player_y + car_height > obstacle[1]):
            return True
    return False

# Oyun döngüsü
running = True
clock = pygame.time.Clock()

while running:
    screen.fill(WHITE)

    # Oyuncu hareketi
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 0:
        player_x -= player_speed
    if keys[pygame.K_RIGHT] and player_x < SCREEN_WIDTH - car_width:
        player_x += player_speed

    # Engelleri oluştur ve hareket ettir
    if random.randint(1, 20) == 1:
        create_obstacle()
    move_obstacles()

    # Engelleri çiz
    for obstacle in obstacles:
        pygame.draw.rect(screen, RED, (obstacle[0], obstacle[1], obstacle_width, obstacle_height))

    # Oyuncu arabasını çiz
    pygame.draw.rect(screen, BLUE, (player_x, player_y, car_width, car_height))

    # Çarpışma kontrolü
    if check_collision(player_x, player_y, obstacles):
        running = False

    # Skoru göster
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (10, 10))

    # Ekranı güncelle
    pygame.display.update()

    # FPS ayarı
    clock.tick(30)

    # Olayları kontrol et
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

# Oyunu kapat
pygame.quit()
