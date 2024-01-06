+++
author = "Lucas Stefano"
title = "Jogo da Velha em C"
date = "2019-03-05"
description = "Texto"
tags = [
    "c", "jogo",
]
+++


Eu tinha feito há algum tempo atrás o mesmo jogo em Java, mas não tinha gostado muito do meu código-fonte. Era ineficiente e longo demais. Daí que resolvi refazê-lo, agora em C. Demorei cerca de três horas para terminar.

A inteligência da CPU é bem simples. Ela primeiro testa se existe a possibilidade de ganhar o jogo no ato, e, se houver, ela joga para ganhar. Se não tiver como vencer, ela tenta se defender, testando se existe alguma forma de o jogador ganhar na próxima jogada dele. Se existir, ela se defende.

Pra jogar em uma posição vazia (representada por 0) é só digitar a linha e a coluna, que variam de 1 a 3, separadas por um espaço.

```c
/* ***********************************
   * Autor: Lucas Stefano 
   * Data: 20/12/2023
   ***********************************
*/
 
#include <stdio.h>
#include <stdlib.h>
 
#define LINHAS  3
#define COLUNAS 3
#define CASAS   9
 
void init_tabuleiro(void);
void mostra_tabuleiro(void);
void jogada_player(void);
void jogada_cpu(void);
int update_tabuleiro(int x, int y); // retorna 0 se a operação for bem sucedida
int testa_vencedor(void); // 2 - vitória cpu, 1 - vitória player, 0 - nada aconteceu
int velha(void); // conta o número de casas vazias no tabuleiro
 
enum vez { PLAYER, CPU } jogador;
 
int tabuleiro[LINHAS][COLUNAS];
 
void main() {
    int escolha, status;
    jogador = PLAYER; // seta o primeiro jogador para player
    init_tabuleiro(); // inicia o tabuleiro com 0s
 
    while(1) {
        if (jogador == PLAYER) {
            if (!velha()) break;
            mostra_tabuleiro();
            jogada_player();
            status = testa_vencedor();
            if (status) break;
        }
        if (jogador == CPU) {
            if (!velha()) break;
            jogada_cpu();
            status = testa_vencedor();
            if (status) break;
        }
    }
 
    mostra_tabuleiro();
 
    if (status == 1) printf("\nParabéns, você venceu!\n");
    else if (status == 2) printf("\nVocê perdeu! :(\n");
    else printf("\nDeu velha!\n");
 
    while(1) {
        printf("\nJogar novamente? (1 = sim / 2 = não) : ");
        scanf("%d", &escolha);
        if (escolha == 1) main();
        else if (escolha == 2) exit(0);
        else printf("\nEscolha invalida!");
    }
}
 
void init_tabuleiro() {
    register int i;
    int *p;
    p = (int *) tabuleiro;
 
    for (i = 0; i < CASAS; i++ ) *(p+i) = 0;
}
 
int velha() {
    register int i;
    int *p, zeros = 0;
    p = (int *) tabuleiro;
 
    for (i = 0; i < CASAS; i++ ) if (*(p+i) == 0) zeros++;
 
    if (!zeros) return 0;
    else return 1;
}
 
void mostra_tabuleiro() {
    register int i, j;
    printf("\n");
 
    for (i = 0; i < 3; i++) {
        for (j = 0; j < 3; j++) {
            printf("%d", tabuleiro[i][j]);
        }
        printf("\n");
    }
}
 
int update_tabuleiro(int x, int y) {
    if (jogador == PLAYER) {
            tabuleiro[x][y] = 1;
            jogador = CPU;
            return 0;
        }
    if (jogador == CPU) {
        if (tabuleiro[x][y] != 0) return 1;
        else {
            tabuleiro[x][y] = 2;
            jogador = PLAYER;
            return 0;
        }
    }
}
 
void jogada_player() {
    int x, y;
    printf("\nDigite a linha e a coluna em que deseja jogar: ");
    scanf("%d %d", &x, &y);
 
    x--, y--;
 
    if (tabuleiro[x][y] != 0 || x < 0 || x > 2 || y < 0 || y > 2) {
        printf("\nJogada inválida.\n");
        jogada_player();
    }
    else update_tabuleiro(x, y);
}
 
void jogada_cpu() {
    register int i, j, x, y;
    int ad; // ataque e defesa. Quando 'ad' vale 2, a cpu testa a possibilidade de ataque.
            // se valer 1, ela testa por defesa.
 
    // série de testes que verificará se há possibilidade de o jogador ou a CPU vencer
    // o jogo em alguma linha, alguma coluna ou alguma diagonal.
 
    for (ad = 2; ad >= 1; ad--) {
        for (i = 0; i < 3; i++) { // para todas as linhas e colunas
            if (tabuleiro[i][1] == ad && tabuleiro[i][2] == ad) 
                if (!update_tabuleiro(i, 0)) return;
            if (tabuleiro[i][0] == ad && tabuleiro[i][2] == ad) 
                if (!update_tabuleiro(i, 1)) return;
            if (tabuleiro[i][0] == ad && tabuleiro[i][1] == ad) 
                if (!update_tabuleiro(i, 2)) return;
            if (tabuleiro[1][i] == ad && tabuleiro[2][i] == ad) 
                if (!update_tabuleiro(0, i)) return;
            if (tabuleiro[0][i] == ad && tabuleiro[2][i] == ad) 
                if (!update_tabuleiro(1, i)) return;
            if (tabuleiro[0][i] == ad && tabuleiro[1][i] == ad) 
                if (!update_tabuleiro(2, i)) return;
        }
 
        // para as diagonais
        if (tabuleiro[0][0] == ad && tabuleiro[2][2] == ad) 
            if (!update_tabuleiro(1, 1)) return;
        if (tabuleiro[0][2] == ad && tabuleiro[2][0] == ad) 
            if (!update_tabuleiro(1, 1)) return;
        if (tabuleiro[0][2] == ad && tabuleiro[1][1] == ad) 
            if (!update_tabuleiro(2, 0)) return;
        if (tabuleiro[2][2] == ad && tabuleiro[1][1] == ad) 
            if (!update_tabuleiro(0, 0)) return;
        if (tabuleiro[2][0] == ad && tabuleiro[1][1] == ad) 
            if (!update_tabuleiro(0, 2)) return;
        if (tabuleiro[0][0] == ad && tabuleiro[1][1] == ad) 
            if (!update_tabuleiro(2, 2)) return;
    }
 
    // caso nenhum dos testes acima sejam satisfeitos, a cpu joga aleatoriamente
 
    srand(time(NULL));
 
    while(1) {
        x = rand() % 3;
        y = rand() % 3;
        if (!update_tabuleiro(x,y)) return;
    }
}
 
int testa_vencedor() {
    register int i;
    int a = (jogador == CPU) ? 1 : 2; // 'a' varia de acordo com quem o chama
 
    for (i = 0; i < 3; i++)
        if (tabuleiro[i][0] == a && tabuleiro[i][1] == a && tabuleiro[i][2] == a) return a;
    for (i = 0; i < 3; i++)
        if (tabuleiro[0][i] == a && tabuleiro[1][i] == a && tabuleiro[2][i] == a) return a;
 
    if (tabuleiro[2][0] == a && tabuleiro[1][1] == a && tabuleiro[0][2] == a) return a;
    if (tabuleiro[0][0] == a && tabuleiro[1][1] == a && tabuleiro[2][2] == a) return a;
 
    return 0;
}
```

