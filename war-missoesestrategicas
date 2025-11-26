/* war_missoes.c
   WAR com missoes estrategicas (nivel final)
   - usa malloc/calloc/free, ponteiros, srand/rand
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define MAX_NAME 30
#define MAX_COLOR 10
#define MAX_MISSION_LEN 128

/* Estrutura que representa um territorio */
typedef struct {
    char nome[MAX_NAME];
    char cor[MAX_COLOR];
    int tropas;
} Territorio;

/* ---------------------------
   Prototipos das funcoes
   --------------------------- */
Territorio* criarMapaDinamico(int n);
void cadastrarTerritorios(Territorio* mapa, int n);
void exibirMapa(const Territorio* mapa, int n); /* passagem por valor (ponteiro por valor) para exibir */
void atacar(Territorio* atacante, Territorio* defensor);
void liberarMapa(Territorio* mapa);
void atribuirMissao(char** destino, char* missoes[], int totalMissoes); /* passagem por referencia para alocar e atribuir */
void exibirMissao(const char* missao); /* passagem por valor (ponteiro por valor) */
int verificarMissao(const char* missao, Territorio* mapa, int tamanho, const char* playerColor);
void liberarMissoes(char** missoesPlayers, int numPlayers);

/* ---------------------------
   Implementacao das funcoes
   --------------------------- */

/* Aloca dinamicamente um vetor de territorios (calloc inicializa com zeros) */
Territorio* criarMapaDinamico(int n) {
    Territorio* mapa = (Territorio*) calloc(n, sizeof(Territorio));
    return mapa;
}

/* Leitura dos territorios via terminal */
void cadastrarTerritorios(Territorio* mapa, int n) {
    for (int i = 0; i < n; i++) {
        printf("\nCadastro do territorio %d\n", i);
        printf("Nome (max %d chars): ", MAX_NAME-1);
        fgets(mapa[i].nome, MAX_NAME, stdin);
        mapa[i].nome[strcspn(mapa[i].nome, "\n")] = '\0';

        printf("Cor do exercito (ex: red, blue): ");
        fgets(mapa[i].cor, MAX_COLOR, stdin);
        mapa[i].cor[strcspn(mapa[i].cor, "\n")] = '\0';

        do {
            printf("Quantidade de tropas (>=0): ");
            if (scanf("%d", &mapa[i].tropas) != 1) {
                printf("Entrada invalida. Digite um numero inteiro.\n");
                while (getchar() != '\n');
                mapa[i].tropas = -1;
            }
        } while (mapa[i].tropas < 0);
        while (getchar() != '\n'); /* limpar buffer */
    }
}

/* Exibe o mapa atual - passagem por valor de ponteiro (na pratica, nao altera dados) */
void exibirMapa(const Territorio* mapa, int n) {
    printf("\n=== MAPA ATUAL (%d territorios) ===\n", n);
    for (int i = 0; i < n; i++) {
        printf("[%d] Nome: %s | Cor: %s | Tropas: %d\n",
               i, mapa[i].nome, mapa[i].cor, mapa[i].tropas);
    }
}

/* Simula um ataque entre dois territorios usando dados (1..6)
   Regras:
   - Se dado atacante > dado defensor => atacante vence:
       defensor muda de cor (copia cor do atacante)
       defensor.tropas = atacante.tropas / 2 (metade das tropas do atacante)
   - Senao: ataque falha e atacante perde 1 tropa (se possuia >1)
*/
void atacar(Territorio* atacante, Territorio* defensor) {
    if (atacante == NULL || defensor == NULL) return;

    int dadoAtacante = (rand() % 6) + 1;
    int dadoDefensor  = (rand() % 6) + 1;

    printf("\nRolagem: atacante %d x defensor %d\n", dadoAtacante, dadoDefensor);

    if (dadoAtacante > dadoDefensor) {
        printf("Atacante venceu! %s assume %s\n", atacante->nome, defensor->nome);
        /* transfere cor e metade das tropas */
        strncpy(defensor->cor, atacante->cor, MAX_COLOR-1);
        defensor->cor[MAX_COLOR-1] = '\0';
        defensor->tropas = atacante->tropas / 2;
        if (defensor->tropas < 1) defensor->tropas = 1;
    } else {
        printf("Defensor resistiu! Atacante perde 1 tropa.\n");
        if (atacante->tropas > 1) atacante->tropas -= 1;
    }
}

/* Libera memoria alocada para o mapa */
void liberarMapa(Territorio* mapa) {
    free(mapa);
}

/* Sorteia uma missao e copia para destino (destino apontado por char**).
   A funcao aloca memoria para a missao do jogador usando malloc.
   Uso: atribuirMissao(&missaoJogador, missoesDisponiveis, total);
*/
void atribuirMissao(char** destino, char* missoes[], int totalMissoes) {
    if (destino == NULL || missoes == NULL || totalMissoes <= 0) return;
    int idx = rand() % totalMissoes;
    size_t len = strlen(missoes[idx]) + 1;
    *destino = (char*) malloc(len * sizeof(char));
    if (*destino == NULL) {
        printf("Erro ao alocar memoria para missao.\n");
        return;
    }
    strcpy(*destino, missoes[idx]);
}

/* Exibe a missao - passagem por valor (ponteiro passado por valor) */
void exibirMissao(const char* missao) {
    if (missao == NULL) return;
    printf("\nSua missao: %s\n", missao);
}

/* Verifica se a missao foi cumprida.
   Para simplicidade, reconhecemos 3 tipos de missoes:
   - "Conquistar X territorios": verifica se jogador (cor) possui >= X territorios
   - "Eliminar todas as tropas da cor Y": verifica se nao existe territorio com cor Y e tropas>0
   - "Controlar todos os territorios de cor X": verifica se jogador tem todos territorios cujo nome indicaria continente (neste exemplo nao usamos)
   A funcao recebe a missao, o mapa, o tamanho e a cor do jogador (playerColor).
   Retorna 1 se cumprida, 0 caso contrario.
*/
int verificarMissao(const char* missao, Territorio* mapa, int tamanho, const char* playerColor) {
    if (missao == NULL || mapa == NULL || tamanho <= 0 || playerColor == NULL) return 0;

    /* Caso 1: "Conquistar X territorios" */
    if (strncmp(missao, "Conquistar ", 11) == 0) {
        int x = atoi(missao + 11); /* extrai numero apos "Conquistar " */
        int count = 0;
        for (int i = 0; i < tamanho; i++) {
            if (strcmp(mapa[i].cor, playerColor) == 0) count++;
        }
        if (count >= x) return 1;
        return 0;
    }

    /* Caso 2: "Eliminar todas tropas da cor Y" */
    if (strncmp(missao, "Eliminar todas tropas da cor ", 29) == 0) {
        const char* alvoCor = missao + 29;
        for (int i = 0; i < tamanho; i++) {
            if (strcmp(mapa[i].cor, alvoCor) == 0 && mapa[i].tropas > 0) return 0;
        }
        return 1; /* nenhuma tropa encontrada da cor alvo */
    }

    /* Caso 3: "Controlar todos territorios de cor Z" */
    if (strncmp(missao, "Controlar todos territorios de cor ", 34) == 0) {
        const char* alvoCor = missao + 34;
        for (int i = 0; i < tamanho; i++) {
            if (strcmp(mapa[i].cor, alvoCor) == 0) {
                /* se existe territorio da cor alvo e nao pertence ao playerColor, falha */
                if (strcmp(mapa[i].cor, playerColor) != 0) return 0;
            }
        }
        /* se nao encontrou nenhum em desacordo, considerar cumprida (simplificacao) */
        return 1;
    }

    /* Missao padrao: nao implementada -> retornar 0 */
    return 0;
}

/* Libera vetor de missoes dos jogadores */
void liberarMissoes(char** missoesPlayers, int numPlayers) {
    if (missoesPlayers == NULL) return;
    for (int i = 0; i < numPlayers; i++) {
        if (missoesPlayers[i] != NULL) free(missoesPlayers[i]);
    }
    free(missoesPlayers);
}

/* ---------------------------
   Funcao principal
   --------------------------- */
int main() {
    srand((unsigned) time(NULL));

    int nTerritorios;
    printf("Quantos territorios no mapa? ");
    if (scanf("%d", &nTerritorios) != 1 || nTerritorios <= 0) {
        printf("Entrada invalida.\n");
        return 1;
    }
    while (getchar() != '\n');

    Territorio* mapa = criarMapaDinamico(nTerritorios);
    if (mapa == NULL) {
        printf("Erro de alocacao para mapa.\n");
        return 1;
    }

    cadastrarTerritorios(mapa, nTerritorios);

    int numPlayers;
    printf("\nQuantos jogadores? ");
    if (scanf("%d", &numPlayers) != 1 || numPlayers <= 0) {
        printf("Entrada invalida.\n");
        liberarMapa(mapa);
        return 1;
    }
    while (getchar() != '\n');

    /* Vetor de missoes possiveis (pelo menos 5) */
    char* missoesDisponiveis[] = {
        "Conquistar 3 territorios",
        "Conquistar 5 territorios",
        "Eliminar todas tropas da cor red",
        "Eliminar todas tropas da cor blue",
        "Controlar todos territorios de cor green",
        /* voce pode adicionar mais missoes aqui */
    };
    int totalMissoes = sizeof(missoesDisponiveis) / sizeof(missoesDisponiveis[0]);

    /* Array dinamico para armazenar a missao de cada jogador (cada item eh char*) */
    char** missoesPlayers = (char**) malloc(numPlayers * sizeof(char*));
    if (missoesPlayers == NULL) {
        printf("Erro ao alocar missoesPlayers.\n");
        liberarMapa(mapa);
        return 1;
    }
    for (int i = 0; i < numPlayers; i++) missoesPlayers[i] = NULL;

    /* Array com cor de cada jogador (para simplificar pedimos a cor do jogador) */
    char** coresPlayers = (char**) malloc(numPlayers * sizeof(char*));
    if (coresPlayers == NULL) {
        printf("Erro ao alocar coresPlayers.\n");
        liberarMissoes(missoesPlayers, numPlayers);
        liberarMapa(mapa);
        return 1;
    }

    for (int i = 0; i < numPlayers; i++) {
        coresPlayers[i] = (char*) malloc(MAX_COLOR * sizeof(char));
        if (coresPlayers[i] == NULL) {
            printf("Erro de memoria para cor do jogador %d\n", i);
            /* liberar ja alocado */
            for (int j = 0; j < i; j++) free(coresPlayers[j]);
            free(coresPlayers);
            liberarMissoes(missoesPlayers, numPlayers);
            liberarMapa(mapa);
            return 1;
        }
        printf("\nJogador %d, informe a cor do seu exercito (ex: red, blue, green): ", i);
        fgets(coresPlayers[i], MAX_COLOR, stdin);
        coresPlayers[i][strcspn(coresPlayers[i], "\n")] = '\0';
    }

    /* Sorteia e atribui missao para cada jogador (aloca dinamicamente) */
    for (int i = 0; i < numPlayers; i++) {
        atribuirMissao(&missoesPlayers[i], missoesDisponiveis, totalMissoes);
        printf("\nJogador %d, sua missao foi sorteada. Ela sera exibida agora (apenas uma vez):\n", i);
        exibirMissao(missoesPlayers[i]); /* passagem por valor (ponteiro por valor) */
    }

    /* Loop de jogo simples: jogadores atacam por turno ate alguem cumprir missao ou usuarios encerrarem */
    int vencedor = -1;
    int continuar = 1;
    while (continuar) {
        for (int p = 0; p < numPlayers; p++) {
            printf("\n----- Turno do Jogador %d (cor %s) -----\n", p, coresPlayers[p]);
            exibirMapa(mapa, nTerritorios);

            int atkIdx, defIdx;
            printf("Escolha indice do territorio atacante (ou -1 para sair): ");
            if (scanf("%d", &atkIdx) != 1) { while(getchar()!='\n'); atkIdx = -1; }
            if (atkIdx == -1) { continuar = 0; break; }
            printf("Escolha indice do territorio defensor: ");
            if (scanf("%d", &defIdx) != 1) { while(getchar()!='\n'); defIdx = -1; }
            while (getchar() != '\n');

            /* validacoes basicas */
            if (atkIdx < 0 || atkIdx >= nTerritorios || defIdx < 0 || defIdx >= nTerritorios) {
                printf("Indices invalidos. Pular turno.\n");
                continue;
            }
            if (strcmp(mapa[atkIdx].cor, coresPlayers[p]) != 0) {
                printf("Voce deve escolher um territorio que pertenca a sua cor (%s).\n", coresPlayers[p]);
                continue;
            }
            if (strcmp(mapa[atkIdx].cor, mapa[defIdx].cor) == 0) {
                printf("Nao pode atacar um territorio da mesma cor.\n");
                continue;
            }
            if (mapa[atkIdx].tropas < 2) {
                printf("Territorio atacante precisa ter pelo menos 2 tropas para atacar.\n");
                continue;
            }

            /* realizar ataque */
            atacar(&mapa[atkIdx], &mapa[defIdx]);

            /* apos ataque, garantir que coresPlayers estejam atualizados se jogador conquistou um territorio */
            /* Se o jogador conquistou o defensor, atualizamos o dono logico (coresPlayers nao mudam) */
            /* Atualizacao de dono: se mapa[defIdx].cor == coresPlayers[p] agora, o jogador controla mais territorios */

            /* verificar missoes silenciosamente */
            if (verificarMissao(missoesPlayers[p], mapa, nTerritorios, coresPlayers[p])) {
                vencedor = p;
                break;
            }
        }
        if (vencedor != -1) {
            printf("\n*** Jogador %d cumpriu a sua missao e venceu o jogo! ***\n", vencedor);
            break;
        }

        if (!continuar) break;

        /* opcao para continuar ou encerrar */
        printf("\nDeseja continuar jogando? (1 = sim, 0 = nao): ");
        if (scanf("%d", &continuar) != 1) {
            continuar = 0;
        }
        while (getchar() != '\n');
    }

    if (vencedor == -1) {
        printf("\nJogo encerrado sem vencedor por missao.\n");
    }

    /* liberar memoria: mapa, missoes dos jogadores, coresPlayers */
    liberarMapa(mapa);
    liberarMissoes(missoesPlayers, numPlayers);
    for (int i = 0; i < numPlayers; i++) free(coresPlayers[i]);
    free(coresPlayers);

    printf("\nMemoria liberada. Fim do programa.\n");
    return 0;
}
