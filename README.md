# Snake-Game
import pygame
import random
import faulthandler
import time
import os

pygame.mixer.init()

pygame.init()

#colors
white = (255,255,255)
red = (255,0,0)
black = (0,0,0)
blue = (0,0,255)
orange = (7,198,0)
yellow = (255,255,0)

#creating window
screen_width = 900
screen_height = 600
gamewindow = pygame.display.set_mode((screen_width,screen_height))

#bg image
bgimg = pygame.image.load("Untitled.png")
bgimg = pygame.transform.scale(bgimg,(screen_width,screen_height)).convert_alpha()

#game title
pygame.display.set_caption("SnakesWithAnik")
pygame.display.update()

clock = pygame.time.Clock()
font = pygame.font.SysFont(None,55)


def text_screen(text, color, x, y):
    scree_text = font.render(text, True,color)
    gamewindow.blit(scree_text,[x,y])

def plot_snake(gamewindow, color, snk_list, snake_size):
    for x,y in snk_list:
        pygame.draw.rect(gamewindow, black, [x, y, snake_size, snake_size])

#welcome
def welcome():
    exit_game = False
    while not exit_game:
        gamewindow.fill(yellow)
        text_screen("Welcome to", blue, 330, 240)
        text_screen("Snake Game",red, 320,290)
        text_screen("Press 'Space' to play", black, 250, 340)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                exit_game = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    game_loop()
        pygame.display.update()
        clock.tick(60)

#game loop
def game_loop():
    pygame.mixer.music.load("Sakura-Girl-Daisy-chosic.com_.mp3")
    pygame.mixer.music.play()
    # game specific variables
    exit_game = False
    game_over = False
    snake_x = 45
    snake_y = 55
    food_x = random.randint(20, 600)
    food_y = random.randint(100, 550)
    snake_size = 30
    fps = 60
    velocity_x = 0
    velocity_y = 0
    score = 0
    init_velocity = 4

    snk_list = []
    snk_length = 1

    #check if high score file exists
    if (not os.path.exists("highscore.txt")):
        with open("highscore.txt", "w") as f:
            f.write("0")

    try:
        with open("highscore.txt", "r") as f:
            highscore = f.read().strip()
            if highscore == "":
                highscore = 0
            else:
                highscore = int(highscore)
    except FileNotFoundError:
        highscore = 0

    while not exit_game:
        if game_over:
            # Save the high score
            with open("highscore.txt", "w") as f:
                f.write(str(highscore))
            gamewindow.fill(white)
            text_screen("Game Over!", red, 300, 200)
            text_screen(f"Highest Score : {highscore}", black, 120, 250)
            text_screen("Press 'Enter' to continue", blue, 120, 300)
            text_screen("Press 'Esc' to quit", black, 120, 350)

            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    exit_game = True
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_RETURN:
                        game_loop()
                    if event.key == pygame.K_ESCAPE:
                        with open("highscore.txt", "w") as f:
                            f.write(str(0))
                        pygame.quit()
                        quit()

        else:
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_ESCAPE:
                        with open("highscore.txt", "w") as f:
                            f.write(str(0))
                        pygame.quit()
                        quit()
                    if event.key == pygame.K_RIGHT:
                        velocity_x = init_velocity
                        velocity_y = 0
                    if event.key == pygame.K_LEFT:
                        velocity_x = -init_velocity
                        velocity_y = 0
                    if event.key == pygame.K_UP:
                        velocity_y = -init_velocity
                        velocity_x = 0
                    if event.key == pygame.K_DOWN:
                        velocity_y = init_velocity
                        velocity_x = 0


            snake_x += velocity_x
            snake_y += velocity_y

            if abs(snake_x - food_x) < 20 and abs(snake_y - food_y) < 20:
                score += 10
                food_x = random.randint(20, screen_width // 2)
                food_y = random.randint(20, screen_height // 2)
                snk_length += 5
                if score > int(highscore):
                    highscore = score


            gamewindow.fill(white)
            gamewindow.blit(bgimg,(0,0))
            text_screen("Score : " + str(score), blue, 5, 5)
            text_screen("Highest Score : " + str(highscore), red, 250, 5)
            pygame.draw.rect(gamewindow, red, [food_x, food_y, snake_size, snake_size])

            head = []
            head.append(snake_x)
            head.append(snake_y)
            snk_list.append(head)

            if len(snk_list) > snk_length:
                del snk_list[0]
            if head in snk_list[:-1]:
                game_over = True
                pygame.mixer.music.load("gameOvertuba.mp3")
                pygame.mixer.music.play()

            if snake_x < 0 or snake_x > screen_width or snake_y < 0 or snake_y > screen_height:
                game_over = True
                pygame.mixer.music.load("gameOvertuba.mp3")
                pygame.mixer.music.play()

            plot_snake(gamewindow, black, snk_list, snake_size)
        pygame.display.update()
        clock.tick(fps)
    pygame.quit()
    quit()

welcome()
