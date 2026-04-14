"Пинг-Понг". Это мультииплеерная игра...

Игра реализована на языке Python c 
помощью библиотеки Pygame

Игрок 1 управляет левой ракеткой с 
помощью клавиш... Игрок 2 управляет 
правой ракеткой с помощью клавиш...





import pygame
import sys


pygame.init()

WIDTH, HEIGHT = 800, 600
FPS = 60
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
YELLOW = (255, 255, 0)

PADDLE_WIDTH, PADDLE_HEIGHT = 15, 100
BALL_SIZE = 15
PADDLE_SPEED = 7
BALL_SPEED_X, BALL_SPEED_Y = 5, 5


font = pygame.font.Font(None, 36)

class GameSprite(pygame.sprite.Sprite):
   
  def __init__(self, x, y, width, height, color):
        super().__init__()
        self.image = pygame.Surface((width, height))
        self.image.fill(color)
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
        self.color = color

  def draw(self, screen):
        screen.blit(self.image, self.rect)

class Player(GameSprite):
    
  def __init__(self, x, y, width, height, color, up_key, down_key, name):
        super().__init__(x, y, width, height, color)
        self.up_key = up_key
        self.down_key = down_key
        self.name = name
        self.score = 0

  def update(self, keys):
        
  if keys[self.up_key] and self.rect.y > 0:
      self.rect.y -= PADDLE_SPEED
        
  if keys[self.down_key] and self.rect.y < HEIGHT - PADDLE_HEIGHT:
      self.rect.y += PADDLE_SPEED

class Ball(GameSprite):

  def __init__(self, x, y, size, color):
      super().__init__(x, y, size, size, color)
      self.speed_x = BALL_SPEED_X
      self.speed_y = BALL_SPEED_Y

  def update(self):
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y


  if self.rect.y <= 0 or self.rect.y >= HEIGHT - BALL_SIZE:
            self.speed_y = -self.speed_y

  def reset(self, direction=None):
      
  self.rect.x = WIDTH // 2 - BALL_SIZE // 2
  self.rect.y = HEIGHT // 2 - BALL_SIZE // 2
  if direction == 'left':
      self.speed_x = -BALL_SPEED_X
  elif direction == 'right':
      self.speed_x = BALL_SPEED_X
  else:
            self.speed_x = BALL_SPEED_X if self.speed_x > 0 else -BALL_SPEED_X
        self.speed_y = BALL_SPEED_Y if self.speed_y > 0 else -BALL_SPEED_Y

def draw_text(screen, text, x, y, color=WHITE):
    
  text_surface = font.render(text, True, color)
  screen.blit(text_surface, (x, y))

def main():
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Ping-Pong")
    clock = pygame.time.Clock()

  
  running = True
  game_active = True
  show_start_screen = True

   
  player_left = Player(
        20, HEIGHT//2 - PADDLE_HEIGHT//2,
        PADDLE_WIDTH, PADDLE_HEIGHT, BLUE,
        pygame.K_w, pygame.K_s, "Left Player"
    )

  player_right = Player(
        WIDTH - 20 - PADDLE_WIDTH, HEIGHT//2 - PADDLE_HEIGHT//2,
        PADDLE_WIDTH, PADDLE_HEIGHT, RED,
        pygame.K_UP, pygame.K_DOWN, "Right Player"
    )

  ball = Ball(WIDTH//2 - BALL_SIZE//2, HEIGHT//2 - BALL_SIZE//2, BALL_SIZE, YELLOW)

  
  all_sprites = pygame.sprite.Group()
    all_sprites.add(player_left, player_right, ball)

  
  ball.reset('right')

  
  while running:
      clock.tick(FPS)

      
  for event in pygame.event.get():
      if event.type == pygame.QUIT:
          running = False
      if event.type == pygame.KEYDOWN:
              
  if event.key == pygame.K_ESCAPE:
        running = False
               
  if event.key == pygame.K_SPACE and not game_active:
                    game_active = True
                    player_left.score = 0
                    player_right.score = 0
                    ball.reset('right')
              
k  if event.key == pygame.K_RETURN and show_start_screen:
              show_start_screen = False

        
  if show_start_screen:
      screen.fill(BLACK)
      draw_text(screen, "PING-PONG", WIDTH//2 - 80, HEIGHT//2 - 100, YELLOW)
      draw_text(screen, "Press ENTER to start", WIDTH//2 - 120, HEIGHT//2, WHITE)
      draw_text(screen, "Left: W/S | Right: UP/DOWN", WIDTH//2 - 160, HEIGHT//2 + 50, WHITE)
      draw_text(screen, "Press ESC to quit", WIDTH//2 - 100, HEIGHT//2 + 100, WHITE)
      pygame.display.flip()
      continue

  if game_active
     keys = pygame.key.get_pressed()

           
  player_left.update(keys)
  player_right.update(keys)
  ball.update()

           
  if ball.rect.colliderect(player_left.rect):
      ball.speed_x = abs(ball.speed_x)
      offset = (ball.rect.centery - player_left.rect.centery) / (PADDLE_HEIGHT // 2)
      ball.speed_y += offset * 2
      ball.speed_y = max(-8, min(8, ball.speed_y))

  if ball.rect.colliderect(player_right.rect):
      ball.speed_x = -abs(ball.speed_x)
      offset = (ball.rect.centery - player_right.rect.centery) / (PADDLE_HEIGHT // 2)
      ball.speed_y += offset * 2
      ball.speed_y = max(-8, min(8, ball.speed_y))

          
  if ball.rect.x <= 0:
      player_right.score += 1
      ball.reset('right')
      pygame.time.wait(500)
  elif ball.rect.x >= WIDTH - BALL_SIZE:
      player_left.score += 1
      ball.reset('left')
      pygame.time.wait(500)

             
  if player_left.score >= 5 or player_right.score >= 5:
     game_active = False

        
  screen.fill(BLACK)

        
  for i in range(0, HEIGHT, 40):
      pygame.draw.rect(screen, WHITE, (WIDTH//2 - 5, i, 10, 20))

      
  for sprite in all_sprites:
     sprite.draw(screen)

        
  draw_text(screen, f"{player_left.score}", WIDTH//2 - 60, 20, BLUE)
  draw_text(screen, f"{player_right.score}", WIDTH//2 + 40, 20, RED)

     
  if not game_active and not show_start_screen:
      winner = "Left Player wins!" if player_left.score >= 5 else "Right Player wins!"
      draw_text(screen, "GAME OVER", WIDTH//2 - 80, HEIGHT//2 - 80, YELLOW)
      draw_text(screen, winner, WIDTH//2 - 100, HEIGHT//2 - 30, WHITE)
      draw_text(screen, "Press SPACE to play again", WIDTH//2 - 150, HEIGHT//2 + 30, WHITE)
      draw_text(screen, "Press ESC to quit", WIDTH//2 - 100, HEIGHT//2 + 80, WHITE)

  pygame.display.flip()

pygame.quit()
    sys.exit()

if __name__ == "__main__":
    main()
