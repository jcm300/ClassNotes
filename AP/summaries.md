# Algoritmos Paralelos

Algoritmos que envolvem apenas o __Paradigma de Memória Distribuída__.

Os melhores algoritmos para paralelizar são os que apresentam complexidade: O($`N^3`$), O($`N!$`), ou seja, com grande complexidade (problemas NP-completos).

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
(x,y)
0 \leq x \leq 1
\sqrt{x^2 + y^2} \leq 1 então:
                            1 - está dentro do quarto de círculo
                            2 - senão está fora
```
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