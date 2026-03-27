
import pygame
import random
import sys

LARGURA, ALTURA = 640, 480
TAMANHO_BLOCO = 20
FPS = 12
COR_FUNDO = (15, 15, 15)
COR_COBRA = (150, 200, 255)
COR_CABECA = (150, 200, 255)
COR_COMIDA = (220, 50, 50)
COR_TEXTO = (220, 50, 50)
TEMPO_MENSAGEM_MORTE_MS = 2000

def posicao_aleatoria(excluidas):

    cols = LARGURA // TAMANHO_BLOCO

    rows = ALTURA // TAMANHO_BLOCO

    while True:

        x = random.randrange(cols) * TAMANHO_BLOCO

        y = random.randrange(rows) * TAMANHO_BLOCO

        if (x, y) not in excluidas:

            return (x, y)

def resetar_jogo():

    cx = (LARGURA // 2) // TAMANHO_BLOCO * TAMANHO_BLOCO

    cy = (ALTURA // 2) // TAMANHO_BLOCO * TAMANHO_BLOCO

    cobra = [(cx, cy), (cx - TAMANHO_BLOCO, cy), (cx - 2 * TAMANHO_BLOCO, cy)]

    direcao = (TAMANHO_BLOCO, 0)

    comida = posicao_aleatoria(set(cobra))

    score = 0

    return cobra, direcao, comida, score

def desenhar_tudo(tela, cobra, comida, score, fonte):

    tela.fill(COR_FUNDO)

    pygame.draw.rect(tela, COR_COMIDA, (comida[0], comida[1], TAMANHO_BLOCO, TAMANHO_BLOCO))

    for i, (x, y) in enumerate(cobra):

        cor = COR_CABECA if i == 0 else COR_COBRA

        pygame.draw.rect(tela, cor, (x, y, TAMANHO_BLOCO, TAMANHO_BLOCO))

    texto_score = fonte.render(f"Pontos: {score}", True, COR_TEXTO)

    tela.blit(texto_score, (10, 10))

    pygame.display.flip()

def mostrar_mensagem_central(tela, texto, fonte, ms):

    tela.fill(COR_FUNDO)

    render = fonte.render(texto, True, COR_TEXTO)

    rect = render.get_rect(center=(LARGURA // 2, ALTURA // 2))

    tela.blit(render, rect)

    pygame.display.flip()

    pygame.time.delay(ms)


def main():

    pygame.init()

    tela = pygame.display.set_mode((LARGURA, ALTURA))

    pygame.display.set_caption("Jogo da Cobrinha ")

    relogio = pygame.time.Clock()

    fonte = pygame.font.SysFont(None, 32)

    fonte_morte = pygame.font.SysFont(None, 64)

    cobra, direcao, comida, score = resetar_jogo()

    while True:

        relogio.tick(FPS)

        for event in pygame.event.get():

            if event.type == pygame.QUIT:

                pygame.quit()

                sys.exit()

            if event.type == pygame.KEYDOWN:

                dx, dy = direcao

                if event.key == pygame.K_UP and dy == 0:

                    direcao = (0, -TAMANHO_BLOCO)

                elif event.key == pygame.K_DOWN and dy == 0:

                    direcao = (0, TAMANHO_BLOCO)

                elif event.key == pygame.K_LEFT and dx == 0:

                    direcao = (-TAMANHO_BLOCO, 0)

                elif event.key == pygame.K_RIGHT and dx == 0:

                    direcao = (TAMANHO_BLOCO, 0)

        head_x, head_y = cobra[0]

        dx, dy = direcao

        nova_cabeca = (head_x + dx, head_y + dy)

        if (

            nova_cabeca[0] < 0 or nova_cabeca[0] >= LARGURA or

            nova_cabeca[1] < 0 or nova_cabeca[1] >= ALTURA

        ):

            mostrar_mensagem_central(tela, "Morreu", fonte_morte, TEMPO_MENSAGEM_MORTE_MS)

            cobra, direcao, comida, score = resetar_jogo()

            continue

        if nova_cabeca in cobra:

            mostrar_mensagem_central(tela, "Morreu", fonte_morte, TEMPO_MENSAGEM_MORTE_MS)

            cobra, direcao, comida, score = resetar_jogo()

            continue

        cobra.insert(0, nova_cabeca)

        if nova_cabeca == comida:

            score += 1

            comida = posicao_aleatoria(set(cobra))

        else:

            cobra.pop()

        desenhar_tudo(tela, cobra, comida, score, fonte)

if __name__ == "__main__":

    main()
