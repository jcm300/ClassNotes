# Scripting no Processamento da Linguagem Natural

## Python

### Misc
```python
# input do utilizador
editor = input("What is your favorite text editor? ")

# lista
a = [3, 6, 7, 2, 5, 9, 4, 0, 10]

# seleciona os elementos com indice de 0 a 5
a[0:5]
# seleciona os elementos com indice de 0 a 3
a[:3]
# seleciona os elementos com indice de 2 em 2
a[::2]
# faz reverse da lista
a[::-1]
# faz reverse da lista até ao elemento de indice 5
a[5::-1]
# seleciona os elementos com indice 4 até ao indice len(a)-1
a[4:-1]
# seleciona os elementos com indice 4 até ao indice len(a)-2
a[4:-2]

for i in a:
    if i < 5:
        print(str(i) + " is lower than 5")
    #else if
    elif i > 5:
        print(str(i) + " is higher than 5")
    else:
        print("FIVE!")

#cria uma lista com valores de 0 a 10
range(10)
#cria uma lista com valores de 1 a 10
range(1,10)

# fazer import de uma biblioteca
import datetime

# Assim pode-se referir Counter sem collections antes
from collections import Counter

# função
def timestamp():
    print(datetime.datetime.now())

# função
def repeat(str, n):
    return str * n

words = ['quem', 'como', 'quais']
# lista por compreensão
repetitions = [repeat(word, 2) for word in words]

# readlines lê linha a linha um ficheiro
for line in open('file.txt').readlines():
    #codigo

# lê o ficheiro e devolve uma string
file = open('file.txt').read()

```

### Listas
```python
teams = ['slb', 'scp', 'fcp']

# adiciona o elemento ao fim da lista
teams.append('scb')

# adiciona os elementos da lista passada como parâmetro ao fim da lista
teams.extend(['bfc', 'mfcf'])
# inserir numa dada posição
teams.insert(3, 'vfc')

# remove primeira ocurrência do valor
teams.remove('slb')
# remove e retorna o valor no indice
teams.pop(3)
# remove elemento do indice
del teams[2]
# atualiza o valor do indice
teams[1] = 'lsc'

# retorna o primeiro index do valor
teams.index('fcp')
# retorna o número de ocurrências do valor
teams.count('scp')
```

### Dicionários
```python
colors = {'fcp': 'blue', 'slb': 'red', 'scp': 'green'}

# tamanho do dicionário
len(colors)
# verifica se chave existe no dict
'slb' in colors
#obter valor com chave 'aca'
colors['aca'] # KeyError: 'aca'
#obter valor com chave 'aca', caso não exista devolve a string 'Não Existe'
colors.get('aca', 'Não existe')

# devolve um set com as chaves
colors.keys()
# devolve a lista com os valores
colors.values()
# devolve uma lista com os items
colors.items()
```

## Filtros Unix

### Filosofia
Escrever programas que fazem uma coisa e fazem-no bem.

Escrever programas para trabalharem em conjunto.

Escrever programas pra tratar de streams de texto, porque é uma interface universal.

### O que é?
Programa que pega no __input__ e produz um __output__.

Pode ser usado numa operação stream.

Pode ser misturado e combinado para criar operações complexas.

### Filtros mais usados

- __head__: primeiras linhas
- __tail__: últimas linhas
- __cut__: remove colunas
- __paste__: merge de linhas de ficheiros
- __nl__: número de linhas
- __wc__: conta linhas, palavras, caracteres
- __grep__: filtro segundo um padrão
- __cat__: (concatena e) imprime
- __tr__: traduz ou apaga caracteres
- __sed__: editor de streams para filtrar e transformar o texto
- __awk__: linguagem de procura e processamento de padrões
- __perl__: Perl (pode ser usado tal como o awk)
- __sort__: ordena linhas de ficheiros de texto
- __uniq__: reporta ou omite linhas repetidas
- __tee__: lê do standard input e escreve para o standard output e para ficheiros

### Exemplos

```bash
find | tail #input from STDIN
tail my_large_file.txt #input from file(s) (arguments)

tail my_large_file | nl #output para STDOUT
tail my_large_file > last_lines.txt #output redirecionado para ficheiro

tail -n 20 my_large_file # show 20 lines (uso de argumentos para modificar o comportamento dos filtros)

history | awk '{a[$4]++}END{for(i in a){print a[i] "\t" i}}' | sort -rn | head # Calcula os comandos bash mais usados

sort | uniq -c | sort -nr # ordena(desc) linhas pelo número de ocurrências

cat /dev/urandom | tr -dc "0-9a-zA-Z!@#$%^&*_+-" | head -c 100 # gera 100 caracteres aleatórios
```

### Python System calls
```python
import subprocess

exit_code = subprocess.call(["ls", "-l"])

# retorna output como byte string
output = subprocess.check_output('ifconfig')

# uso da função decode() para converter byte string para string
print(returned_output.decode("utf-8"))
```

### Criar um filtro Unix em Python
```python
import getopt
import sys

# definir as opções que o comando pode receber
options, remainder = getopt.getopt(sys.argv[1:], 'abc:', ['output=', 'verbose', 'version='])
dict_opts = dict(options)

# input do STDIN ou de um ficheiro
if remainder:
    input = open(remainder[0])
else:
    input = sys.stdin

# output para o STDOUT ou para ficheiro
out = dict_opts.get('--output', None)
if out:
    output = open(out, 'w+')
else:
    output = sys.stdout

output.write("".join(input.readlines()))
```

#### Versão simplificada
```python
import fileinput, getopt, sys, re
opts, args = getopt.getopt(sys.argv[1:], "d")

for line in fileinput.input(args):
    line = line.strip()
    # ...
```

## Expressões Regulares

### O que é?
RegEx é uma linguagem para expecificar strings de procura de texto.

É usado para especificar padrões para ser equiparadas com strings.

Normalmente são usados em processadores de texto e em editores de texto, motores de busca e ferramentas para processamento de texto.

Em python, importar a biblioteca __re__.

Podem expressar linguagens regulares (liguagens aceites por autómatos finitos determinísticos).

Múltiplos standards, o mais comum é o da POSIX.

### Exemplo
```python
import re

string = 'This is my string'

re.findall(r'my', string) # ['my']
re.sub(r'my', 'your', string) # 'This is your string'

# Básico
re.findall(r'amanhã', 'O António chega amanhã.') #amanhã
re.findall(r'sac.', 'O João leva os livros numa saca.') #saca

# Quantificação
re.findall(r'golo', 'Ronaldo chuta e... golo!!!') #golo
re.findall(r'goloo?', 'Ronaldo chuta e... golo, goloo!!!') #golo e goloo
re.findall(r'golo+', 'Ronaldo chuta e... golooooooo!!!') #golooooooo
re.findall(r'goloo*', 'Ronaldo chuta e... golo!!!') #golo
re.findall(r'golo{2,5}', 'Ronaldo chuta e... golooo!!!') #golooo

# Grouping
re.findall(r'alde(ão|ãe|õe)s', 'Os aldeões fizeram uma festa na aldeia.')

# Disjunção e Intervalos
    #[AEIOU]
    #[0123456789]
    #alun[oa]
    #[A-Z]
    #[0-9]
    #[0-9A-F]
    #[AEIOU]
    #[0123456789]
    #alun[oa]
    #[A-Z]
    #[0-9]
    #[0-9A-F]
    #[^aeiou]

# Classes de caracteres
    #\d (digito)
    #\D (não \d)
    #\w (letra, digito ou underscore)
    #\W (não \w )
    #\s (espaço)
    #\S (não espaço)

# Âncoras
    #^ (início da linha)
    #$ (fim da linha)
    #\b (limite/fronteira da palavra)

# Grupos de Captura
re.findall(r'O ([A-Z][a-z]+) tem ([\w ]+)', 'O Carlos tem uma mota.') # [('Carlos', 'uma mota')]
re.findall(r'O ([A-Z][a-z]+) tem (?:[\w ]+)', 'O Carlos tem uma mota.') # ['Carlos'], não captura o segundo grupo

# Funções
    #re.search, Procura pela string por matches com o padrão, retornando um objeto Match ou None se não for encontrado nenhum match.

    #re.findall, Retorna uma lista de todos os matches sem sobreposição da string. Se um ou mais grupos de captura estão presentes no padrão, retorna uma lista de grupos, será uma lista de tuplos se o padrão tem mais do que um grupo. Resultados vazios são incluídos no resultado.

    #re.split, Separa a string origem pelas as ocurrências do padrão, returnando uma lista contendo o resultado das substrings. Se os parênteses de captura são usados no padrão, então o texto de todos os grupos no padrão são também returnados como parte do resultado da lista. Se maxsplit for diferente de zero, no máximo, maxsplits splits ocorrerão, e o restante da string será retornado como o elemento final da lista. 

    #re.sub, Retorna a string obtida por substituir as ocurrências mais à esquerda sem sobreposição do padrão na string pelo replacement repl. O repl pode ser uma string ou uma função, se é uma string backslash escapes são processados. Se é uma função, é passado o objeto Match e deve retornar o replacement em string a ser usado.

# Match não greedy
pars = '<p>Isto é texto.</p><p>E isto também.</p>'
re.findall(r'<p>.*?</p>', pars)

# Padrão multi linha
comm = '/* Isto é\num comentário multi-linha */'
re.findall(r'/\*.*?\*/', comm, re.DOTALL)
# ou
re.findall(r'/\*(?:.|\n)*?\*/', comm)

# Lookahead/lookbehind
re.findall(r'(?<=abc)def', 'abcdef') # def
# pode ser positivo ou negativo e aplicado antes ou após o match
#(?=…) : lookahead positivo	
    #Ex: (?=\d{10})\d{5} => 01234 in 0123456789
#(?<=…) : lookbehind positivo
    #Ex: (?<=\d)cat => cat in 1cat
#(?!…) : lookahead negativo 
    #Ex: (?!theatre)the\w+ => theme matches but theatre not
#(?<!…) : lookbehind negativo 
    #Ex: \w{3}(?<!mon)ster : Munster matches but monster not

# Função de replacement
pt_en = {'palavra': 'word', 'ola': 'hello'}
text = 'Isto é uma palavra'
re.sub(r'\w+', lambda x: pt_en.get(x[0], x[0]), text)

# Padrão util 
for x in re.findall(pattern, string):
    # do stuff
```
