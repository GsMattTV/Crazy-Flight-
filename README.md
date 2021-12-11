# Crazy-Flight! 
from pygame import K_LEFT, K_RIGHT, K_UP, K_DOWN
import pygame
from random import choice, randint

d = {    #   x   y
    K_LEFT: (-5, 0),
    K_RIGHT: (5, 0),
    K_UP: (0, -5),
    K_DOWN: (0, 5)
}


def make_obj(n, filename):
    objs = []
    for i in range(n):
        img = pygame.image.load("clouds.png").convert()
        img = pygame.transform.scale(img, (75, 90))
        obj = {'image': img,
              'x': randint(0, 500),
              'y': randint(0, 500)
        }
        objs.append(obj)
    return objs


def update(lst, speed):
    new_lst = []
    for elem in lst:
        screen.blit(elem['image'], (elem['x'], elem['y']))
        elem['y'] += speed
        if elem['y'] < 500:
            new_lst.append(elem)
        else:
            elem['y'] = 0
            elem['x'] = randint(0, 450)
            new_lst.append(elem)
    return new_lst
    
    

WIDTH = 500
HEIGHT = 500
FPS = 50
WHITE = (255,255,255)
BLUE = (131, 213,250)
# Init
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
clock = pygame.time.Clock()
font = pygame.font.SysFont('Comic Sans MS', 30)
text = font.render('GAME OVER', True, (255, 0, 0))
# Plane ship
plane = pygame.image.load("goodplane.png").convert()
plane = pygame.transform.scale(plane, (110, 100))
plane_rect = plane.get_rect()
hsw = plane_rect.width // 2
hsh = plane_rect.height // 2
# Bullet
bullet = pygame.image.load("bullet.png").convert()
bullet = pygame.transform.scale(bullet, (17, 17))
bullet = pygame.transform.rotate(bullet, 90)
# Enemy
enemy = pygame.image.load('badplane.png').convert()
enemy = pygame.transform.scale(enemy, (110, 100))
enemy = pygame.transform.rotate(enemy, 180)
#Cloud
clouds = make_obj(5, 'clouds.png')



rect = enemy.get_rect()
we = rect.width
he = rect.height-70
pos = [0, 150, 250]
xe = choice(pos)
ye = 0
dx, dy = 0, 0
x, y = 220, 400
dby = 0
xb, yb = -1, -1
shoot = False
game_over = False
count = 0
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not shoot:
                shoot = True
                xb, yb = x+hsw-7, y+hsh-5
            if event.key in d:
                dx, dy = d[event.key]
        if event.type == pygame.KEYUP:
            dx, dy = 0, 0
        
    if not game_over:
        x += dx
        y += dy
        ye += 3
    
    # Collision between bullet and a plane
    if ye > HEIGHT or ((ye < yb < ye + he)  and (xe < xb < xe + we)):
        ye = randint(-170, 0)
        xe = choice(pos)
        xb, yb = -1, -1
        count += 1
    
    screen.fill(BLUE)
    # Collistion of two planes
    if (x < xe + we) and (y < ye+he) and (xe < x + hsw*2) and  (ye < y + hsh*2):
        screen.blit(text, (150, 100))
        game_over = True
    
    screen.blit(plane, (x, y))
    screen.blit(enemy, (xe, ye))
    score = font.render('Kills '+str(count), True, (255, 0, 0))
    screen.blit(score, (10, 10))
    clouds = update(clouds, 3)
    if shoot:
        yb -= 3
        screen.blit(bullet, (xb, yb))
        if yb < 0:
            shoot = False
    pygame.display.update()
    clock.tick(FPS)
