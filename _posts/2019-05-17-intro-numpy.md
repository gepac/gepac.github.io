---
layout: post
title: >-
    Introdução ao NumPy
excerpt: >-
    Este post aborda criação e manipulação de arrays multidimensionais
    com o NumPy.
tags: [python, oficina]
---

### O que é NumPy?

[NumPy](https://www.numpy.org) é um pacote Python que permite fazer cálculos
numéricos com *arrays* de forma simples e eficiente. Enquanto os elementos
de uma sequência são identificados por um índice (por exemplo, $$x_n$$), e
os elementos de uma matriz, por dois índices (por exemplo, $$A=(a_{ij})$$),
os elementos de um array se identificam por uma quantidade arbitrária de
índices. Por exemplo, se as matrizes $$A_1$$, $$A_2$$ e $$A_3$$ têm mesma
dimensão, podemos combiná-las num único array $$B$$, tal que o elemento
$$B_{ijk}$$ seja o número na j-ésima linha e k-ésima coluna da matriz
$$A_i$$.

Uma forma simples de implementar um array é criar listas de listas:

~~~ python
vetor = [1, 2, 3]       # vetor de dimensão 3

matriz = [[1, 2, 3],    # matriz de dimensão 2x3, ou sequência 
          [4, 5, 6]]    # de 2 vetores de dimensão 3 cada

tensor = [[[1, 0, 0],   # tensor de dimensão 2x2x3, ou 
           [0, 2, 1]],  # sequência de duas matrizes de 
          [[0, 0, 0],   # dimensão 2x3 cada
           [1, 1, 0]]]
~~~

Para acessar por exemplo, o elemento da 1ª linha e da 3ª coluna da matriz,
basta escrever `matriz[0][2]` (lembre-se que a contagem de índices em Python
começa pelo 0).

No entanto, se quisermos, por exemplo, somar ou multiplicar duas matrizes,
ou se quisermos aplicar uma mesma função a todos os elementos do tensor,
fazer cálculos mais complicados como de autovalores e autovetores,
precisamos escrever vários laços aninhados, o que seria tedioso.

Vejamos o que o `numpy` pode fazer por nós. Vamos importá-lo com o nome
`np`, como é tradicional:

~~~ python
import numpy as np
~~~

A função `np.array` permite transformar listas de listas como as nossas em
arrays do numpy:

~~~ python
# Cria duas matrizes
A = [[1, 2], [3, 4]]
B = [[2, 0], [0, 1]]
# Transforma em arrays do numpy
A, B = np.array(A), np.array(B)
~~~

Agora podemos fazer diversas operações, como soma, produto
elemento-por-elemento (operador `*`), produto usual de matrizes (operador
`@`), e produto por escalar:

~~~ python
soma = A + B
produto_elem = A*B
produto_matr = A@B
combinacao = 5*A - B
~~~

O NumPy lida automaticamente com as operações, nos poupando de escrevê-las.
Mais do que isso, o NumPy faz as operações *mais rápido* do que códigos com
laços em listas de listas, porque é implementado em linguagens de baixo
nível mais rápidas que o Python. Isto faz diferença quando lidamos com um
volume muito grande de operações ou de dados.

### O array N-dimensional 

Um array do NumPy (ou `ndarray`, ''N-dimensional array'') é formado de
elementos de um mesmo tipo --- por exemplo, todos inteiros, ou todos
*floats* de precisão dupla. Podemos fazer a consulta pelo atributo `dtype`:

~~~ python
a = np.array([1, 2, 3])
b = np.array([1.0, 2.2, 1])
c = a + b
print(a.dtype, b.dtype, c.dtype) # Mostra "int64 float64 float64"
~~~

Um array criado a partir de inteiros será inteiro por padrão. Se arrays de
inteiros e floats forem combinados, o NumPy converte tudo em floats, como no
exemplo `c` acima.

Outro atributo importante de um array é seu *formato*, que é uma tupla de
números inteiros indicando a quantidade de índices (*dimensão* do array) e a
extensão em cada índice. Por exemplo, um array de formato `(5,)` é uma
sequência de cinco números, um array de formato `(5,3)` é uma matriz $$5
\times 3$$, e um de formato `(3,3,3)` é um ''cubo'' de 27 números (que pode
ser pensado como uma coleção de 3 matrizes $$3 \times 3$$). O formato de um
array `a` pode ser acessado pelo atributo `shape`:

~~~ python
print(a.shape) # mostra "(3,)"
d = np.ones((3, 3, 3, 3)) # array de elementos iguais a
                          # 1, com formato especificado
print(d.shape) # mostra "(3, 3, 3, 3)"
~~~

Duas maneiras comuns de criar arrays unidimensionais são as funções `arange`
e `linspace`. A função `arange` funciona como o `range` do python: vai do
valor inicial (incluso) até o final (não incluso) a um passo especificado
(que pode ser não inteiro):

~~~ python
arr1 = np.arange(10)          # 0, 1, 2, ..., 9
arr2 = np.arange(10, 20)      # 10, 11, ..., 19
arr3 = np.arange(10, 20, 2)   # 10, 12, ..., 18 (20 não incluso)
arr4 = np.arange(10, 20, 1.4) # 10, 11.4, 12.8, ...,19.8
~~~

Se em vez de especificar o *passo* quisermos especificar o *número de
elementos* da sequência, usamos a função `linspace`:

~~~ python
arr5 = np.linspace(10, 20, 11)    # 10, 11, ..., 20 (11 elementos)
arr6 = np.linspace(10, 20, 101)   # 10, 10.1, ..., 20 (101 elementos)
arr7 = np.linspace(0, np.pi, 100) # de 0 até pi, 100 elementos
~~~

Note que a função `linspace`, ao contrário de `arange`, inclui o valor
final.

Vamos usar o método `reshape` para criar um array de formato `(2, 4, 4)`.
Como isto dá um total de $$2 \times 4 \times 4$$ = 32 elementos, podemos
partir de uma sequência de 32 números:

~~~ python
seq = np.arange(32)
arr = seq.reshape(2, 4, 4)
~~~

O array `arr` pode ser pensado como uma coleção de 2 matrizes $$4 \times
4$$.  Podemos acessar a primeira ou a segunda matriz com `arr[0]` e
`arr[1]`, respectivamente. A segunda linha da primeira matriz é `arr[0][1]`
e seu quarto elemento é `arr[0][1][3]`. O NumPy oferece uma sintaxe mais
simples para estes acessos: `arr[0, 1]` e `arr[0, 1, 3]`.

Assim como uma lista em Python pode ser *fatiada* (por exemplo,
`lista[0:10:3]` vai do primeiro ao nono elemento, de três em três), um array
N-dimensional pode ser fatiado *em cada índice individualmente*. Vamos
explorar o que isso significa.

Se quisermos a primeira e a segunda linhas da primeira matriz, na linguagem
dos arrays, o que queremos é *fixar o primeiro índice* em `0` (o que denota
a primeira matriz) enquanto o segundo índice varia de `0` até `1` (primeiras
duas linhas) e o terceiro índice varia de `0` até `3` (todos os quatro
elementos de cada linha). Ou seja, escrevemos `arr[0, 0:2, :]`, onde o
símbolo `:` sozinho denota que o terceiro índice assume *todos* os quatro
valores possíveis.

Se quisermos, agora, a primeira e a segunda *colunas* da mesma matriz, basta
acessar `arr[0, :, 0:2]`. A primeira coluna de cada matriz: `arr[:, :, 0]`.
A primeira e a terceira linhas de cada matriz: `arr[:, 0:3:2, :]`. Teste
todas as combinações de *slices* (fatias) até entender.

(**Cuidado**: a atribuição `mat = arr[0]` faz de `mat` a primeira matriz em
`arr`, *não* uma cópia. Se depois disso fizermos `arr[0, 0, 0] = -1`, por
exemplo, o valor será alterado *tanto em* `arr` *como em* `mat`. Por outro
lado, quando se altera `mat`, o NumPy a transforma automaticamente em uma
cópia verdadeira do conteúdo de `arr`, e assim, fazer  `mat[0, 0] = -2` não
altera o valor correspondente em `arr`. Se quiser que `mat` seja uma cópia
independente desde o começo, defina `mat = arr[0].copy()`.)

Os slices só nos permitem acessar elementos em progressão aritmética. Para
acessar de uma única vez vários elementos arbitrários de um array, há outros
métodos. No caso de um array unidimensional, podemos informar uma *lista*
(ou um array) com os índices que queremos em vez de um slice:

~~~ python
arr2[[0, 4, 7]] # primeiro, quinto e oitavo elementos
~~~

Outra coisa que podemos fazer, mas agora também no caso N-dimensional, é
passar um array de booleanos *com mesmo formato* que o array que queremos
acessar, com valor `True` nas posições que interessam e `False` nas que não
devem ser retornadas. Por exemplo, o array `arr2 % 3 == 0` tem valores
`True` onde os valores de `arr2` são divisíveis por 3:

~~~ python
masc2 = arr2 % 3 == 0
arr2[masc2] # 12, 15, 18
~~~

Podemos fazer o mesmo para o array `arr`:

~~~ python
masc = arr % 3 == 0
arr[masc]   # 0, 3, 6, ..., 30
~~~

Veja que isto funciona mesmo para `arr`, que tem 3 dimensões, mas o
resultado é sempre unidimensional. A ordem do resultado respeita a ordem dos
índices do array original (elementos da primeira matriz vêm antes dos da
segunda, elementos da primeira linha vêm antes dos da terceira etc). O array
de booleanos se chama *máscara* e este tipo de acesso é chamado *masking*.

### Broadcasting

A operação que acabamos de realizar, `arr % 3 == 0`, pode parecer estranha,
mas tem uma interpretação muito clara: estamos perguntando se `arr` é
divisível por 3, *elemento por elemento*. Diversas operações podem ser
realizadas elemento por elemento de um array: operações aritméticas (soma,
produto etc), aplicação de funções (módulo, cosseno, exponencial, etc) e
operações lógicas (igual, maior que, menor ou igual a, etc):

~~~ python
A > B       # array de booleanos
B > 0       # array de booleanos
np.cos(A)   # cossenos dos elementos
np.abs(np.cos(B))   # valores absolutos
                    # dos cossenos
A + B       # soma arrays
A + 1       # soma 1 em cada elemento de A
A * B       # produto elemento por elemento
A * (-1)    # produto de cada elemento por -1
~~~

Observe que as operações `A > B` e `B > 0` são de naturezas diferentes:
enquanto, na primeira, comparamos cada elemento de `A` com um elemento
correspondente em `B`, na segunda, comparamos cada elemento de `B` com um
único número, 0.  Há a mesma distinção entre `A + B` e `A + 1` ou entre `A *
B` e `A * (-1)`. Isto não é difícil de entender. Agora tente fazer o
seguinte:

~~~ python
A > np.array([1, 5])
~~~

O NumPy compara uma matriz $$2 \times 2$$ com um vetor de duas entradas sem
reclamar! O que está acontecendo? A resposta é que, tanto quando comparamos
`A` com um único número (i.e. array de formato `(1,)`) como quando
comparamos com um vetor (array de formato `(2,)`), o NumPy aplica as
chamadas *regras de broadcasting* para criar, do número e do vetor, arrays
de *mesmo formato* que `A` e então aplicar a comparação elemento por
elemento. O mesmo vale para as operações aritméticas (pode tentar).

As regras são simples na prática, mas um pouco demoradas de se explicar.
Veja [este
link](https://docs.scipy.org/doc/numpy-1.15.0/user/basics.broadcasting.html)
para detalhes. No nosso exemplo, os elementos da *primeira coluna* da matriz
A são comparados com 1 enquanto os elementos da segunda coluna são
comparados com 5.

A outra coisa que fizemos acima foi aplicar funções matemáticas a arrays,
que funcionam elemento por elemento. Funções que recebem arrays do NumPy são
chamadas *funções universais*. O próprio NumPy vem equipado com [várias
delas](https://docs.scipy.org/doc/numpy/reference/ufuncs.html#available-ufuncs).
Uma função python que só realiza operações aritméticas funciona
perfeitamente bem com arrays:

~~~ python
def f(a, b, c):
    return b*b - 4*a*c

f(A, B, A@B) # funciona!
~~~

No entanto, uma função como a seguir não funciona com um array:

~~~ python
def g(a, b, c):
    if a < b: return c
    else: return a + b

g(A, B, A@B)  # erro!
~~~

A razão é que, se `a` e `b` são arrays, `a < b` não é um booleano, mas um
array de booleanos, que não tem um valor-verdade bem definido. O que
queremos é que a operação `g` seja realizada sobre *cada elemento
correspondente* dos arrays `a`, `b` e `c`, com o broadcasting sendo
realizado automaticamente onde for necessário. Isto se chama *vetorizar* a
função `g` e é feito pela função `vectorize`:

~~~ python
g = np.vectorize(g)
g(A, B, A@B)   # funciona! também faz broadcasting:
g(A, B, 1)
~~~

Nota: outra forma de obter uma função universal a partir de uma função
Python qualquer é usar o `frompyfunc`, bastante similar ao `vectorize`. Você
pode precisar disso se a performance for importante. Consulte a documentação
do NumPy.

### Usando tabelas de dados externas

Podemos carregar dados numéricos (por exemplo, de um experimento de Física)
diretamente na forma de um array do NumPy. As diversas formas de carregar e
salvar arquivos com arrays estão listadas
[aqui](https://docs.scipy.org/doc/numpy/reference/routines.io.html). Vamos
lidar apenas com tabelas em arquivos de texto, o jeito mais simples de
registrar dados. Suponha que o arquivo `C10.dat` contenha o seguinte:

~~~
# Curva de carga, capacitor de 10 µF
# Tempo (s), tensão (V)
0.59    0.45
0.92    1.62
1.29    2.45
1.73    3.03
2.13    3.45
2.43    3.74
3.53    4.21
5.13    4.38
5.39    4.41
6.29    4.45
7.13    4.47
~~~

As linhas que começam em `#` são comentários, ignorados por padrão. As
outras formam uma tabela numérica, que podemos transformar em um array do
NumPy com a função `loadtxt`:

~~~ python
dados = np.loadtxt('C10.dat')
~~~

Isto produz um array de formato `(11, 2)`, isto é, uma matriz de onze linhas
e duas colunas, como esperado. Consulte [a
documentação](https://docs.scipy.org/doc/numpy/reference/generated/numpy.loadtxt.html#numpy.loadtxt)
do `loadtxt` para mais detalhes sobre leitura de arquivos.

Indo na outra direção, podemos gravar arrays de uma ou duas dimensões como
tabelas de texto usando a função `savetxt`:

~~~ python
x = np.linspace(-np.pi, np.pi, 100)
cossenos = np.cos(x)
tangentes = np.tan(x)
dados = np.column_stack((x, cossenos, tangentes))
np.savetxt('dados.dat', dados)
~~~

Novamente, veja [a
documentação](https://docs.scipy.org/doc/numpy/reference/generated/numpy.savetxt.html#numpy.savetxt)
do `savetxt` para detalhes.

#### Alguns links úteis

- [Referência do
  NumPy](https://docs.scipy.org/doc/numpy/reference/index.html): está tudo
  contido aqui. É grande.
- [O tutorial oficial do
  NumPy](https://docs.scipy.org/doc/numpy/user/quickstart.html) (mais ou
  menos o que nós cobrimos até agora)
- [As muitas maneiras de *criar*
  arrays](https://docs.scipy.org/doc/numpy/reference/routines.array-creation.html)
- [As muitas maneiras de *manipular*
  arrays](https://docs.scipy.org/doc/numpy/reference/routines.array-manipulation.html)
- [Lista de funções
  universais](https://docs.scipy.org/doc/numpy/reference/ufuncs.html#available-ufuncs)


## Exercícios

1. *Unidade imaginária*. Verifique que a matriz $$ J = \begin{bmatrix} 0 & 1
   \\ -1 & 0 \end{bmatrix} $$ satisfaz $$J^2 = -I$$ usando o NumPy.
   (Lembre-se de usar o operador `@`, e não `*`.)

1. Calcule $$\exp(\cos(n))$$ para todos os inteiros `n` de -10 até 10.
   (Dica: use a função `arange`).

1. Defina `x = np.linspace(0, 2*np.pi, 100)` e use uma máscara para
   selecionar todos os valores de `x` tais que $$\sin x < 0$$.

1. Use a função `savetxt()` para escrever uma tabela com 100 valores de
   $$x$$ e $$e^x$$, com `x` variando entre 0 e 1.

1. Escreva uma tabela numérica num arquivo de texto e carregue-a com a
   função `loadtxt()`. Usando *slicing*, faça um array contendo apenas a
   primeira coluna da tabela.

## Licença

Este trabalho está licenciado sob a Licença
Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional (BY-NC-SA 4.0
internacional) Creative Commons. Para visualizar uma cópia desta licença,
visite <http://creativecommons.org/licenses/by-nc-sa/4.0/>.
