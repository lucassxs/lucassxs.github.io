+++
author = "Lucas Stefano"
title = "Classe de busca em vetores genéricos em C++"
date = "2019-03-05"
description = "Texto"
tags = [
    "c", "vetores",
]
+++

O algoritmo a seguir fornece uma classe capaz de realizar uma busca num vetor qualquer.

Através de templates, um objeto da classe Busca pode trabalhar com qualquer tipo de dado que um determinado vetor estiver armazenando através de um único método. Não tendo, portanto, a necessidade de se criar métodos específicos.

Como exemplo, o programa cria um vetor **v[]** de caracteres e um outro **v2[]** de números inteiros e realiza a busca, com o objeto Buscador, tanto recursivamente quanto iterativamente.

```c
/* Escrito por Lucas Stefano 
 */
 
#include <iostream>
using namespace std;
 
class Busca {
public:
    template <class tipo> int iterativa (tipo x, int n, tipo *v);
    template <class tipo> int recursiva (tipo x, int n, tipo *v);
};
 
/* As duas funções abaixo recebem x, n e v[0...n-1].
 * Devolvem um índice k tal que v[k] = x. Mas, se tal k
 * não existir, devolvem -1. */
 
template <class tipo>
int Busca::iterativa (tipo x, int n, tipo *v) {
    int k = n - 1;
    for (k; k >= 0 && v[k] != x; k--);
    return k;
}
 
template <class tipo>
int Busca::recursiva (tipo x, int n, tipo *v) {
    return (n == 0 || v[n-1] == x) ? n-1 : recursiva (x, n-1, v);
}
 
int main () {
    char v[] = {'a', 'b', 'v', 'j', 'h', 'w', 'x'};
    int v2[] = {3, 4, 2, 8, 19, 34, 77, 88};
    Busca Buscador;
 
    cout << "Busca iterativa: índice do char 'v' em v[]: " << Buscador.iterativa ('v', 7, v);
    cout << "\n";
    cout << "Busca iterativa: índice do int 19 em v2[]: " << Buscador.iterativa (19, 8, v2);
    cout << "\n\n";
    cout << "Busca recursiva: índice do char 'v' em v[]: " << Buscador.recursiva ('v', 7, v);
    cout << "\n";
    cout << "Busca recursiva: índice do int 19 em v2[]: " << Buscador.recursiva (19, 8, v2);
    cout << "\n";
 
    return 0;
}
```