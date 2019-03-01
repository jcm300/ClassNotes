# Algoritmos Paralelos

Algoritmos que envolvem apenas o __Paradigma de Memória Distribuída__.

Os melhores algoritmos para paralelizar são os que apresentam complexidade: O($`N^3`$), O($`N!`$), ou seja, com grande complexidade (problemas NP-completos).

A resolução de problemas por métodos diretos tem complexidade O($`N`$) e os métodos iterativos tem complexidade O($`N^3`$).

| Complexidade | Nº de operações por elemento |
| :----------: | :--------------------------: |
| ...          | ...                          |
| O($`N!`$)    | $`(N-1)!`$                   |
| O($`N^3`$)   | $`N^2`$                      |
| O($`N^2`$)   | $`N`$                        |
| O($`NlogN`$) | $`logN`$                     |
| O($`N`$)     | $`1`$                        |
| ...          | ...                          |

$`Nº\ de\ operaões\ por\ elemento = \frac{complexidade}{nº\ de\ elementos}`$

São mais fáceis de paralelizar e de obter ganhos os algoritmos que são maus em termos sequenciais.

É muito mais dificil paralelizar algoritmos com estruturas irregulares do que com estruturas regulares. Convém passar estruturas irregulares para regulares. Considera-se uma estrutura irregular seja pelo uso de apontadores, seja pelos acessos irregulares aos dados.

## Algoritmo de aproximação ao valor de $`\pi`$
![aproxPI](images/aproxPi.png)
```math
(x,y) \\
0 \leq x \leq 1 \\
\sqrt{x^2 + y^2} \leq 1 \\
``` 
então:
1. está dentro do quarto de círculo
2. senão está fora

Quadrado é circunscrito ao círculo.

Quantos mais "tiros" tendencionalmente melhor será a aproximação ao $`\pi`$

Código em MATLAB/Octave:
```matlab
% this script uses random numbers to compute pi

n=input('Número de pontos > ');

in=0;       % number of points inside circle
fplot('sqrt(1-x^2)',[0,1]), hold on
for i=1:n
    x=rand; y=rand;
    plot(x,y,'k*'), hold on
    if (x^2+y^2 <= 1)
        in=in+1;
    end
end
(in/n)*4
hold off
```

## Caixeiro Viajante (TSP)

Algoritmo "no greedy" (Método de Monte Carlo)

Temos os pontos $`P_i(x_i,x_j),`$ $`i,j=1,...,n`$ e conhecemos as distâncias $`d(P_i,P_j)=\sqrt{(x_j-x_i)^2+(y_j-y_i)^2}`$

$`D(i,j) = d(P_i,P_j)`$ [a matriz D é simétrica]

$`D(i,j)`$: distância entre as "cidades" i e j

### Método de Monte Carlo:

começa com:

$`\sum{d(i,j)}`$

rota inicial aletória (O($`N!`$))

depois:

São feitas melhorias locais aleatoriamente até não se conseguir obter ganhos ao fim de x ciclos:

![MonteCarloMethod](images/monteCarlometh.png)

## Fenómeno de Marsaglia
```matlab
% MATLAB (OCTAVE) script marsaglia: ilustra o fenómeno de Marsaglia

% a partir de uma semente racional x(1) (nota: todos os números 
% representados no computador são racionais), este código produz uma
% sequência x(1),...,x(20), de números entre 0 e 1, tal que os pontos 
% (x(k),x(k+1)), k=1,...19, estão todos sobre duas rectas paralelas. 
% Isto mostra que existe uma forte correlação entre eles, pelo que este
% não é um bom gerador de números pseudo-aleatórios


n=20;                         % número de pontos
x(1)=rand;                    % semente (entre 0 e 1)

for i=1:n
    x(i+1)=rem(2*x(i),1);     % gerador
end
x'
% Usando plot(x) obtemos um gráfico de pontos (k,x(k)) e pode parecer que
% não há correlação entre os números x(k)...
subplot(2,1,1), plot(x,'o')

% Porém, o gráfico de pontos (x(k),x(k+1)) mostra que há uma forte
% correlação já que estes pontos estão sobre duas rectas paralelas...

a=x(1:n-1); b=x(2:n); 
subplot(2,1,2), plot(a,b,'+')               

% NOTA: para executar este código deve guardá-lo num ficheiro com a 
% extensão .m (por exemplo marsaglia.m) e, na linha de comandos, invocar o 
% nome sem extensão ( > marsaglia)
```

## Mersenne Twister
Gerador de números pseudo-aleatórios (PRNG) e é bastante usado. 

O seu nome deriva do facto de que o tamanho do seu período é escolhido para ser um primo de Mersenne.

## Simulated Annealing (SA)
Técnica probabilistica para aproximar o ótimo global de uma determinada função. Mais especificamente é uma meta heuristica para aproximar o ótimo global num grande espaço de procura para um problema de otimização. É uma técnica iterativa pelo que ao fim de cada iteração "o espaço por onde procura uma melhor solução que a atual" diminui.

Algoritmo:

Em cada iteração do algoritmo, um novo ponto é gerado aleatoriamente. A distância do novo ponto a partir do ponto atual, ou a extensão da pesquisa, é baseada em uma distribuição de probabilidade com uma escala proporcional à "temperatura". O algoritmo aceita todos os novos pontos que baixam o objetivo, mas também, com certa probabilidade, pontos que aumentam o objetivo. Ao aceitar pontos que aumentam o objetivo, o algoritmo evita ficar preso em mínimos locais nas primeiras iterações e é capaz de explorar globalmente as melhores soluções.

Caso do caixeiro viajante usando Monte Carlo com Simulated Annealing:

```matlab
function [Tdist,town]=traveling(D)
% function [Tdist,town]=traveling(x,y,D)
% uses simulated annealing to solve the Traveling Salesman Problem: given n
% towns and the distances between any two of them, finds the shortest route 
% that starts at one of the towns, goes once through everyone of the others 
% and returns to the first one

n=length(D);
% FIRST tentative route 
town=randperm(n);           % a random permutation of the first n integers 
Tdist=D(town(n),town(1));
for i=1:n-1                               % length of 
    Tdist=Tdist+D(town(i),town(i+1));     % initial route
end

T=1;     % initial temperature
i=0;
while i < 100           % stop if no changes for 100 iterations
    c=randi(n);         % randomly chooses a town (at position c in route)
    if c==1             % and swaps its position with the next one
        previous=n; next1=2; next2=3;
    elseif c==n-1
        previous=n-2; next1=n; next2=1;
    elseif c==n
        previous=n-1; next1=1; next2=2;
    else
        previous=c-1; next1=c+1; next2=c+2;
    end
    % delta=increment in length of route
    delta=D(town(previous),town(next1))+D(town(c),town(next2))-D(town(previous),town(c))-D(town(next1),town(next2)); 
    % accept or discard change to route 
    if delta<0 | (exp(-delta/T)>= rand) %if delta<0 | (T>0.001 & (exp(-delta/T)>= rand))?
        temp=town(c); town(c)=town(next1); town(next1)=temp; % swap order of town(c) and town(c+1) in route                 
        Tdist=Tdist+delta; 
        if delta~=0
            i=0;
        end
    else i=i+1;
    end
    T=0.999*T;
end
```

## BLAS (Basic Linear Algebra Subprograms)

Níveis:
- BLAS 1: (vetor x vetor)
  - dados : O($`N`$)
  - FLOPS : O($`N`$)
  - Ex: $`\sum_{i=1}^{n} x_i * y_i`$ ($`n`$ produtos + $`n-1`$ adições)
- BLAS 2: (matriz x vetor)
  - dados : O($`N^2`$)
  - FLOPS : O($`N^2`$) 
  - Ex: $`A . x`$ ou seja, para $`i=1:n`$, $`\sum_{j=1}^{n} A(i,j) * x_j`$ ($`n^2`$ produtos + $`n(n-1)`$ adições)
- BLAS 3: (matriz x matriz)
  - dados : O($`N^2`$)
  - FLOPS : O($`N^3`$)
  - Ex: $`A.B`$ ($`n^3`$ produtos + $`n^2(n-1)`$ adições) 

Os kernels em cada nível são divididos em (começam por):
- S : precisão simples
- D : precisão dupla
- C : precisão simples de números complexos
- Z : precisão dupla de números complexos

LAPACK (Linear Algebra PACKage) = LINPACK + EISPACK

### Kernel saxpy

Fórmula: $`y \leftarrow y + a.x`$

Ex:

$`
\begin{bmatrix}
    a_{11} & a_{12} & a_{13} \\[0.3em]
    a_{21} & a_{22} & a_{23} \\[0.3em]
    a_{31} & a_{32} & a_{33}
\end{bmatrix}
.
\begin{bmatrix}
    x_1 \\[0.3em]
    x_2 \\[0.3em]
    x_3
\end{bmatrix}
= \begin{bmatrix}
    a_{11}.x_1 + a_{12}.x_2 + a_{13}.x_3 \\[0.3em]
    a_{21}.x_1 + a_{22}.x_2 + a_{23}.x_3 \\[0.3em]
    a_{31}.x_1 + a_{32}.x_2 + a_{33}.x_3
\end{bmatrix}
\Rightarrow
x_1.
\begin{bmatrix}
    a_{11} \\[0.3em]
    a_{21} \\[0.3em]
    a_{31}
\end{bmatrix}
+ 
x_2 . 
\begin{bmatrix}
    a_{12} \\[0.3em]
    a_{22} \\[0.3em]
    a_{32}
\end{bmatrix}
+
x_3 .
\begin{bmatrix}
    a_{13} \\[0.3em]
    a_{23} \\[0.3em]
    a_{33}
\end{bmatrix}
`$

### Uso de blocos

$`
\left[
\begin{array}{cc|cc}
    a_{11} & a_{12} & a_{13} & a_{14} \\[0.3em]
    a_{21} & a_{22} & a_{23} & a_{24} \\[0.3em]
    \hline
    a_{31} & a_{32} & a_{33} & a_{34} \\[0.3em]
    a_{41} & a_{42} & a_{43} & a_{44}
\end{array}
\right]
.
\left[
\begin{array}{cc|cc}
    b_{11} & b_{12} & b_{13} & b_{14} \\[0.3em]
    b_{21} & b_{22} & b_{23} & b_{24} \\[0.3em]
    \hline
    b_{31} & b_{32} & b_{33} & b_{34} \\[0.3em]
    b_{41} & b_{42} & b_{43} & b_{44}
\end{array}
\right]
= \begin{bmatrix}
    C_{11} & C_{12} \\[0.3em]
    C_{21} & C_{22}
\end{bmatrix}
`$

$`
\begin{bmatrix}
    A_{11} & A_{12} \\[0.3em]
    A_{21} & A_{22} 
\end{bmatrix}
.
\begin{bmatrix}
    B_{11} & B_{12} \\[0.3em]
    B_{21} & B_{22} 
\end{bmatrix}
= \begin{bmatrix}
    C_{11} & C_{12} \\[0.3em]
    C_{21} & C_{22}
\end{bmatrix}
`$

$`
C_{11} = A_{11} . B_{11} + A_{12} . B_{21} \\
... \\
(NxN)\ com\ N = \frac{n}{p}\ em\ que\ n=ordem\ da\ matrix
`$

## Google Matrix

A Google matrix é uma matriz estocástica particular que é usada pelo algoritmo PageRank da Google. A matrix representa um grafo com arestas a representar os links entre páginas (websites). Esta matriz é uma matriz de adjacência esparsa. 

### PageRank

PageRank é um algoritmo usado pelo Google Search para rankear as páginas web nos resultados obtidos pelo motor de busca da Google.

O PageRank de forma a calcular uma estimativa do rank de (quão importante é) uma página, conta o número e a qualidade dos links para a página. O pressuposto subjacente é que sites mais importantes provavelmente receberão mais links de outros sites.

## Algoritmos "Matrix-free"

É um algoritmo para resolver um sistema linear de equações ou um problema de eigenvalue, que não armazena a matriz de coeficientes explicitamente, mas acede a matriz avaliando produtos vetoriais de matriz. Tais algoritmos podem ser preferíveis quando a matriz é tão grande que armazená-la e manipulá-la custaria muito tempo de memória e de computador, mesmo com o uso de métodos para matrizes esparsas.

Muitos algoritmos iterativos permitem uma implementação matrix-free entre eles:
 - Método de potência
 - Algoritmo Lanczos 
 - LOBPCG (Locally Optimal Block Preconditioned Conjugate Gradient Method)
 - Algoritmo de recorrência coordenada de Wiedemann 
 - Método do gradiente conjugado 

Soluções distribuídas têm sido também exploradas usando sistemas de software paralelos coarse-grain de modo a alcançar soluções homogéneas de sistemas lineares.

## Matriz Banda

$`a_{ij} = 0\ se\ |i-j|>k\ (k<<n)`$

Um caso particular é a matriz tridiagonal (k=1).

### Matriz Tridiagonal

Uma matriz tridiagonal é uma matriz banda que apenas não tem zeros na diagonal principal, na primeira diagonal abaixo e na primeiro diagonal acima da diagonal principal.

Ex:

$`
\begin{pmatrix}
    a_{11} & a_{12} & \cdots    & 0 \\
    a_{21} & a_{22} & \ddots    & \vdots \\
    \vdots & \ddots & \ddots    & a_{n-1,n}\\
    0      & \cdots & a_{n,n-1} & a_{nn} 
\end{pmatrix}
`$

## Algoritmo de Strassen/Volker Strassen

É um algoritmos para multiplicação de matrizes. É mais rápido que o algoritmo padrão para a multiplicação de matrizes e é util na prática para matrizes grandes, mas seria mais lento que os algoritmos mais rápidos conhecidos para matrizes extremamente grandes.

Complexidade assintótica: O($`N^{2.8074}`$)