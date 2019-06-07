---
title: "Oficina de bibliotecas Python: parte 2"
layout: post
excerpt: >-
    Nesta segunda aula, avançamos um pouco mais no matplotlib e começamos a
    usar o SciPy. Discutimos o ajuste de parâmetros de curvas com o método
    dos mínimos quadrados.
tags: [python, oficina]
---

## Um pouco de matplotlib (continuação)

Vamos continuar nossa incursão pelo matplotlib, focando em usar a interface
`pyplot` de forma efetiva e rápida. Como já mencionado, o `pyplot` permite
fazer gráficos de modo imperativo, *à la* MATLAB, sem conhecer programação
orientada a objetos.

Entre num shell interativo do Python. Vamos começar importando nossos
suspeitos usuais:

~~~ python
import numpy as np
import matplotlib.pyplot as plt
~~~

### Usando o pyplot interativamente

A maneira mais rápida de explorar seus dados, sejam de um experimento ou de
uma simulação, é fazer um gráfico interativo. Ativamos o modo interativo do
pyplot com a função `ion()`:

~~~ python
plt.ion()
~~~

(Analogamente, para desativar, usamos `ioff()`.)

Veja o que acontece agora quando fazemos um gráfico qualquer. Execute no
shell do Python

~~~ python
x = np.linspace(0, 2, 41)
plt.plot(x, x * x)
~~~

Sequer é preciso usar o comando `show()`. O matplotlib automaticamente abre
uma janela interativa com o gráfico. Mantenha-a aberta e execute

~~~ python
plt.plot(x, np.cos(x))
~~~

Você deve estar vendo um novo gráfico de linha desenhado automaticamente.
Na janela interativa, é possível ampliar e navegar pelo gráfico, além de
salvar uma cópia em PNG. A possibilidade de mudar parâmetros do seu gráfico
e ver as mudanças acontecerem *imediatamente* torna o modo interativo muito
prático para tarefas do dia-a-dia.

Mantenha o modo interativo ligado nas seções seguintes.

### Controlando o tamanho da figura

Quando executamos o comando `plot` acima, uma figura foi automaticamente
criada e exibida. Podemos criar uma figura sem graficar nada, como uma tela
em branco:

~~~ python
plt.figure()
~~~

Executando este comando no modo interativo, uma nova figura em branco deve
aparecer. Se você usar a função `plot` agora, é *nesta* figura que os
gráficos serão desenhados. O pyplot interpreta seus comandos *em contexto*:
um gráfico sempre vai para a última figura que foi selecionada. Para não se
confundir, é uma boa ideia usar apenas uma figura por vez no modo
interativo.

A primeira coisa que pode-se querer alterar numa figura é o tamanho. O
matplotlib armazena o tamanho de uma figura como um par `(largura, altura)`
em polegadas. Para alterá-lo, use o parâmetro `figsize`:

~~~ python
plt.figure(figsize=(6, 6))  # Figura quadrada de lado 6 polegadas
~~~

O segundo parâmetro importante é a densidade de pontos, que é o número de
pixels por polegada. Por exemplo, numa densidade de 100 PPP (pontos por
polegada, ou DPI, *dots per inch*), a figura acima tem resolução $$600
\times 600$$. Isto pode ser alterado com o parâmetro `dpi`:

~~~ python
plt.figure(figsize=(6, 6), dpi=120)  # Figura 720x720
~~~

Note que estes comandos criam *novas* figuras com os parâmetros
especificados. Se estivéssemos fazendo gráficos num script (um arquivo
`.py`), bastaria por a linha de código acima antes de todos os usos do
pyplot, o que garantria que a figura criada inicialmente já tivesse os
parâmetros desejados. No modo interativo, faz mais sentido alterar a *figura
atual*, que pode já conter gráficos etc. que não queremos jogar fora. Para
obter a figura atual, usamos a função `gcf()`:

~~~ python
fig = plt.gcf()
~~~

Daí, fazemos as alterações que queremos com os métodos `set_size_inches()` e
`set_dpi()`:

~~~ python
fig.set_size_inches(6, 6)
fig.set_dpi(120)
~~~

Você pode limpar a figura *atual* com o comando:

~~~ python
plt.clf()
~~~

Cuidado: isto elimina todos os gráficos na figura.

### Controlando as escalas de um par de eixos

Quando usamos a função `plot` acima, o matplotlib automaticamente criou uma
figura, que, como vimos, tem parâmetros que talvez queiramos alterar. A
segunda coisa que o matplotlib criou automaticamente foi um *par de eixos*,
que delimitam uma região da figura onde o nosso gráfico vive. Este também
pode ser criado independentemente do gráfico:

~~~ python
plt.axes()
~~~

Executando isto, você deve ver uma figura com um par de eixos em branco.
Qualquer `plot` executado em seguida vai desenhar gráficos nesse par de
eixos. Como no caso da figura, podemos controlar parâmetros do par de eixos
na sua criação. Por exemplo, mudar a projeção para polar, ou a escala em y
para logarítmica:

~~~ python
# Cria um par de eixos polar
plt.axes(projection='polar')
~~~

![Par de eixos em coordenadas polares.](../img/oficina-scipy-2019/polar-axes.png)

~~~ python
# Cria um par de eixos com escala logarítmica na vertical
ax = plt.axes(yscale='log')
ax.plot([1, 2, 3, 4], [1, 12, 31, 210])
~~~

![Par de eixos em semilog. Gráfico inserido para facilitar
visualização.](../img/oficina-scipy-2019/semilogy-example.png)

A função `axes()` retorna o par de eixos criado. No entanto, novamente, se
estamos no modo interativo podemos não ter feito nada disso inicialmente e
já ter um gráfico que não queremos perder. Neste caso queremos alterar o
par de eixos *atual*. Ele pode ser obtido com a função `gca()`:

~~~ python
ax = plt.gca()
~~~

Então alteramos as propriedades desejadas:

~~~ python
ax.set_xlim(0, 2)    # Equivalente a plt.xlim(0, 2)
ax.set_title('olá')  # Equivalente a plt.title('olá')
ax.set_xscale('log') # Equivalente a plt.xscale('log')
~~~

O par de eixos atual pode ser limpo com o comando:

~~~ python
plt.cla()
~~~

Em geral, as propriedades do par de eixos *atual* podem ser alteradas
diretamente por funções do `pyplot`, sem fazer nada do procedimento acima.
No entanto, é útil saber lidar com pares de eixos se quisermos fazer mais de
um gráfico na mesma figura, como na seção seguinte.

### Fazendo mais de um gráfico na mesma figura

Uma figura pode conter vários pares de eixos, isto é, vários gráficos
diferentes. Como exemplo, vamos criar uma grade igualmente espaçada de
gráficos usando a função `subplots()`:

~~~ python
fig, axarray = plt.subplots(2, 2) # duas linhas e duas colunas de gráficos
(ax1, ax2), (ax3, ax4) = axarray  # dá nomes individuais para os pares de
                                  # eixos

# gera dados aleatórios
x, y = np.random.random(30), np.random.random(30)

# faz gráficos diferentes em cada par de eixos
ax1.scatter(x, y, c=np.arange(30))
ax2.plot(x, y, 'o-')
ax3.loglog(x, y, 'o-')
ax4.text(.3, .45, 'Par de eixos 4')
~~~

![Grade 2x2 de gráficos.](../img/oficina-scipy-2019/subplots-2x2.png)

No código acima, o primeiro parâmetro passado para a função `subplots()` é o
número de linhas e o segundo é o número de colunas da grade.

Uma outra opção é a função `subplot()`, que cria apenas um par de eixos por
vez. Ela é chamada como

~~~ python
ax = plt.subplot(nlinhas, ncolunas, indice)
~~~

isto é, passamos o número de colunas, o de linhas, e o *índice* que
identifica o par de eixos que queremos: o índice é contado de `1` até
`ncolunas * nlinhas`, da esquerda para a direita, de cima para baixo (como
se lê). A função retorna o par de eixos `ax` e também o faz o par de eixos
*atual*, isto é, logo após a chamada acima, um `plt.plot()` faz um gráfico
no par de eixos `ax`, e é portanto equivalente a `ax.plot()`. A última forma
é preferível porque continua valendo depois de criarmos outros gráficos.
Um exemplo pode esclarescer melhor (execute linha por linha no modo
interativo):

~~~ python 
# Cria uma nova figura
fig = plt.figure(figsize=(8, 5))
# Esta é a figura *atual* no contexto do pyplot, como você pode verificar
# com
print(fig == plt.gcf())

# Vamos dividir a figura numa grade 2x3 e colocar um gráfico na primeira
# posição. No contexto, as duas linhas a seguir são equivalentes:
ax1 = fig.add_subplot(2, 3, 1)
ax1 = plt.subplot(2, 3, 1)

# Como acabou de ser criado, ax1 é o par de eixos *atual*, o que você
# pode verificar com
print(ax1 == plt.gca())

# Vamos fazer um gráfico simples em ax1. No contexto, as duas linhas a
# seguir são equivalentes:
ax1.plot([0, 1], [1, 2])
plt.plot([0, 1], [1, 2])

# Vamos criar outros eixos nas posições 2, 3, 4 e 5:
ax2 = fig.add_subplot(2, 3, 2)
ax3 = fig.add_subplot(2, 3, 3)
ax4 = fig.add_subplot(2, 3, 4)
ax5 = fig.add_subplot(2, 3, 5)

# Agora ax5 é o par de eixos atual. No entanto, como guardamos ax2 numa
# variável, ainda podemos fazer gráficos em ax2:
ax2.plot([0, 1], [3, 2])  # grafica em ax2
plt.plot([1, 3], [2, 2])  # grafica em ax5

# Finalmente, em vez de preencher a posição 6, vamos fazer um gráfico que
# ocupe toda a terceira coluna da figura. Ou seja, vamos dividir a figura
# numa grade 1x3 e criar um subplot ocupando a terceira posição:
ax3.remove()                         # remove ax3 da figura para abrir
axgrande = fig.add_subplot(1, 3, 3)  # espaço para axgrande

# Rearranja os gráficos para não haver interseção de eixos, legendas etc
plt.tight_layout()
~~~

O resultado final deve ser a figura:

![Grade de gráficos criados com o método
add_subplot().](../img/oficina-scipy-2019/subplots-tight-layout.png)

### Fazendo muitos gráficos semelhantes

Quando se explora dados, especialmente de simulações, é comum querer fazer
muitos gráficos semelhantes para comparação (alterando apenas alguns
parâmetros). Neste caso, vale a pena escrever uma *função* que crie seu
gráfico com os parâmetros desejados. Uma das utilidades da maquinaria que
exploramos acima (os pares de eixos, objetos do tipo `Axes`) é facilitar
este tipo de trabalho.

Se tomarmos o par de eixos em que o gráfico será criado como *parâmetro*,
livramos a nossa função da responsabilidade de posicionar este par de eixos
numa figura, ou de exibir/exportar a figura.  Isto nos dá liberdade para
usar a função *repetidas vezes* e, além disso, *modificar* o gráfico que ela
gerou antes de finalizar o trabalho. Esta flexibilidade é muito útil.

Por exemplo, adiantando um pouco de SciPy, vamos criar uma função que gere
gráficos das funções harmônicas esféricas, dependendo dos parâmetros
inteiros `m` e `l` e da resolução desejada:

~~~ python
from scipy.special import sph_harm

# Grafica o harmônico esférico de grau l e ordem m (|m| <= l)
def graf_harmonico(ax, m, l, resol=50, **params):
    # Gerando os dados
    phi = np.linspace(0, 2*np.pi, 2*resol)  # azimutal
    theta = np.linspace(0, np.pi, resol)    # polar
    phi, theta = np.meshgrid(phi, theta)
    z = sph_harm(m, l, phi, theta)

    # Graficando a função
    ax.contourf(phi, theta, z, **params)

    # Removendo marcadores dos eixos
    ax.set_xticks([])
    ax.set_yticks([])
~~~

Agora podemos graficar, por exemplo, todos os harmônicos esféricos até grau
3:

~~~ python
N = 3  # grau máximo dos harmônicos a graficar

plt.figure(figsize=(9, 5))

for l in range(N+1):
    for m in range(-l,l+1):
        # (São necessárias 2N+1 colunas e N+1 linhas)
        ax = plt.subplot(N+1, 2*N+1, N+m+1 + (2*N+1)*l)

        graf_harmonico(ax, m, l, cmap='magma') 

plt.tight_layout()
~~~

Neste caso, o parâmetro adicional `cmap` (que controla as cores dos
gráficos) foi repassado para a função `contourf()` através de `params`. O
resultado é:

![Harmônicos esféricos até grau
3.](../img/oficina-scipy-2019/harmonicos-esfericos.png)

### Mudando o estilo de um gráfico

É possível controlar cada mínimo detalhe estético de um gráfico --- largura
das linhas, opacidade dos pontos, cores de preenchimento, bordas, fontes,
tamanhos --- mas na prática é mais fácil usar um conjunto de configurações
pronto que funcione em harmonia. O matplotlib fornece vários *estilos* além
do padrão, que viemos usando até aqui. Você pode visualizar uma lista deles
com o comando:

~~~ python
print(plt.style.available)
~~~

A função `plt.style.use()` serve para escolher um (ou mais) tema(s). Depois
que ela é usada, todas as funções do pyplot se comportam de acordo com o
tema. O tema padrão é `'classic'`. Mudando, por exemplo, para o tema
`'ggplot'`:

~~~ python
x = np.linspace(0, 4, 50)
plt.style.use('ggplot')
plt.plot(x ** 2, x)
~~~

![Função sqrt(x) no estilo
'ggplot'.](../img/oficina-scipy-2019/sqrt-ggplot-style.png)

### Exportando uma figura

Por fim, podemos exportar figuras do matplotlib em formatos diferentes:
como imagem em PNG (compressão sem perdas) ou JPEG (com perdas), como
desenho vetorizado em SVG, ou como documentos prontos para impressão em PDF
ou PS. Em todo caso, usamos a função `savefig()`, cuja chamada é

~~~ python
plt.savefig(fname)
~~~

onde `fname` é o nome do arquivo, donde se infere seu formato pela extensão.
Há também parâmetros opcionais, dos quais destacamos
- `dpi`: é a densidade de pixels, em pontos por polegada. Se não for
  especificado, será a densidade da figura atual. Em geral, vale a pena
  salvar figuras numa densidade maior que a que se usa para visualização.
- `bbox_inches`: é a região da figura que será renderizada. Se for a string
  `'tight'`, o matplotlib procura ajustar os limites para que tudo fique
  dentro da imagem final (nenhum título/nome de eixo etc. é cortado).

Assim, comandos típicos de exportação são:

~~~ python
# O mesmo que salvar a partir da tela interativa
plt.savefig('figura.png')

# Um pouco melhor: não corta labels e títulos. Bom para slides
plt.savefig('figura.png', bbox_inches='tight')

# Salva um PNG de alta qualidade. Um pouco melhor para impressão
plt.savefig('figura.png', dpi=300, bbox_inches='tight')

# Salva a figura em PDF. O DPI é irrelevante: os elementos do gráfico são
# vetorizados, ficam com "resolução infinita". É o melhor para a maioria das
# impressões. Não é uma boa ideia se houver transparências na figura (PDF 
# não suporta transparências bem)
plt.savefig('figura.pdf', bbox_inches='tight') 
~~~

Se você estiver usando $$\LaTeX$$, a imagem em PDF pode ser carregada
exatamente da mesma forma que a imagem PNG (com o comando `includegraphics`
do pacote `graphicx`), com as vantagens de tornar o texto no gráfico
pesquisável e a resolução tão grande quanto se queira, além de diminuir o
tamanho do arquivo final. Esta opção é recomendável na maioria dos casos.

Isto encerra nossa rápida discussão sobre matplotlib.

### Links úteis de matplotlib

- [API da classe Axes](https://matplotlib.org/api/axes_api.html): descreve
  tudo que há para saber sobre os ''pares de eixos'' com que lidamos hoje.
- [Tutoriais oficiais do
  matplotlib](https://matplotlib.org/tutorials/index.html): o melhor recurso
  para aprender a biblioteca.

## Introdução ao SciPy

### O que é SciPy?

O projeto [SciPy](https://www.scipy.org/) é uma coleção de bibliotecas
Python open-source para matemática, ciência e engenharia, incluindo o NumPy
e o matplotlib. Por outro lado, parte do projeto é a
*biblioteca* `scipy`, que chamaremos apenas de SciPy. Ela contém como
submódulos a maioria das ferramentas que se espera de um software para
cientistas, incluindo funções especiais, integração, otimização,
interpolação, transformadas de Fourier, processamento de sinais, álgebra
linear, estatística e processamento de imagens. Veja [este
link](https://docs.scipy.org/doc/scipy/reference/).

### Ajustando parâmetros de curvas com `curve_fit`

`curve_fit()` é uma função do módulo `scipy.optimize`. Ela recebe uma função
e um conjunto de dados, e retorna os parâmetros da função que a ajustam
melhor aos dados segundo o critério dos mínimos quadrados. Isto é, dada uma
função $$f(x, \lambda)$$, na variável $$x$$ com parâmetro $$\lambda$$, e um
conjunto de dados experimentais $$(x_j, y_j)$$, o `curve_fit()` encontra o
parâmetro $$\lambda$$ tal que a soma

$$ \sum_j (y_j - f(x_j, \lambda))^2 $$

é mínima. Isto também é chamado *regressão*.

A chamada da função `curve_fit()` tem a forma típica:

~~~ python
curve_fit(func, dadosx, dadosy, p0, errosy)
~~~
`func()` é a função cujos parâmetros se deseja descobrir; ela recebe a
variável primeiro e os parâmetros depois: `func(x, a, b, ...)`. `dadosx` e
`dadosy` são sequências (arrays) de dados, onde x é a variável independente
e y é dependente. `p0` é um palpite inicial para os valores dos parâmetros,
e portanto é uma lista de tamanho igual ao número de parâmetros que a função
recebe (não contando a variável dependente `x`). `errosy` é uma sequência
*opcional* que indica os erros nas medidas da variável dependente. Supõe-se
que os erros em x são desprezíveis (se isto não for verdade, há métodos mais
complicados que podem lidar também com erros em x).

Vejamos um caso simples. Como fazer a regressão de uma reta com o
`curve_fit()`?

Primeiro definimos a função `func` que será ajustada. Ela tem dois
parâmetros: o coeficiente angular `a` e o coeficiente linear `b`:

~~~ python
def func(x, a, b):
    return a*x + b
~~~

Agora, os dados. Vamos usar a seguinte tabela (`dados.dat`):

~~~
8e-3    1.403
10e-3   1.378
12e-3   1.344
13e-3   1.338
17e-3   1.280
18e-3   1.282
22e-3   1.232
29e-3   1.142
~~~

Carregando como array do numpy:

~~~ python
x, y = np.loadtxt('dados.dat').T
~~~

Agora usamos a função `curve_fit`. Ela retorna uma lista de parâmetros e uma
*matriz de covariância*. Vamos chamar a primeira de `params` e a segunda de
`mcov`:

~~~ python
from scipy.optimize import curve_fit
params, mcov = curve_fit(func, x, y)
~~~

Os elementos da diagonal de `mcov` são as variâncias de `a` e `b`, ou seja,
o quadrado dos desvios-padrão, que identificamos como o erro associado aos
parâmetros `a` e `b`:

~~~ python
a, b = params
a_err, b_err = np.sqrt(np.diag(mcov))
~~~

Vamos graficar reta ajustada e os pontos da tabela para ver o que está
acontecendo:

~~~
t = np.array([0, 0.04])
plt.scatter(x, y)
plt.plot(t, func(t, a, b))
~~~

![Ajuste de uma reta.](../img/oficina-scipy-2019/line-fit.png)

Um excelente ajuste! Se quisermos os valores dos parâmetros:

~~~ python
print(a, a_err)
print(b, b_err)
~~~

O que fizemos acima é absolutamente genérico e se aplica a qualquer tipo de
função. Vamos a um exemplo mais complicado. A lei de Planck

$$ B_\nu(\nu, T) = \frac{2h\nu^3}{c^2} \frac{1}{e^{\frac{h\nu}{kT}} - 1} $$

dá o espectro de um corpo negro em função da frequência $$\nu$$ para uma
determinada temperatura $$T$$. Vamos supor que, das constantes que aparecem
na equação, apenas o valor de $$c$$ é conhecido, mas temos estimativas das
ordens de grandeza de $$h$$ e $$k$$. Também conhecemos a temperatura $$T$$
do experimento. Podemos determinar as constantes $$h$$ de Planck e $$k$$ de
Boltzmann com um único ajuste de curva. Por exemplo, suponha que temos os
seguintes dados experimentais salvos em `corponegro.dat`:

~~~
# T = 300 K
# frequência, radiança espectral (unidades do SI)
0.50e13   1.02e-12
0.82e13   2.57e-12
1.14e13   4.42e-12
1.46e13   4.69e-12
1.79e13   5.35e-12
2.11e13   4.94e-12
2.43e13   4.55e-12
2.75e13   3.15e-12
3.07e13   3.22e-12
3.39e13   2.56e-12
3.71e13   1.90e-12
4.04e13   1.63e-12
4.36e13   1.02e-12
4.68e13   1.46e-12
5.00e13   0.41e-12
~~~

Então:

~~~ python
# Define a função (lei de Planck)
T, c = 300, 299792458
def Bnu_corpo_negro(nu, h, k):
    return 2*h*(nu**3/c**2) * (1 / (np.exp(h*nu/(k*T)) - 1))

# Carrega os dados
nu_dados, Bnu_dados = np.loadtxt('corponegro.dat').T

# Faz o ajuste com as estimativas iniciais 1e-34 e 1e-23 para h e k
params, mcov = curve_fit(Bnu_corpo_negro, nu_dados, Bnu_dados,
                         [1e-34, 1e-23])
h, k = params
h_err, k_err = np.sqrt(np.diag(mcov))
~~~

Obtivemos os parâmetros $$h = (6.3 \pm 0.5) \times 10^{-34} J \cdot s$$ e
$$k = (1.33 \pm 0.08) \times 10^{-23} J/K$$. Compare com os valores precisos
$$6.63 \times 10^{-34}$$ e $$1.38 \times 10^{-23}$$ --- nada mau!.
Finalmente, vamos graficar os dados e a curva ajustada:

~~~ python
plt.plot(nu_dados, Bnu_dados, 'o', label='Dados')
nu = np.linspace(1, 5.5e13, 100)
plt.plot(nu, Bnu_corpo_negro(nu, h, k), label='Ajuste')
plt.legend()
~~~

![Radiação de corpo negro: curva ajustada e
dados.](../img/oficina-scipy-2019/corpo-negro.png)

O `curve_fit` funciona por aproximações sucessivas: em cada iteração, ele
modifica um pouco os parâmetros para se aproximar do mínimo. Note que,
tentando fazer o ajuste com estimativas iniciais ruins (por exemplo, 0 ou 1)
o `curve_fit` não converge e não conseguimos a solução. É sempre bom ter uma
estimativa inicial ao menos na ordem de grandeza correta.

O módulo `scipy.optimize` também tem funções para cálculo de mínimos locais
e globais e para encontrar raízes e soluções de sistemas de muitas
variáveis. Consulte a documentação para saber mais.

### Links úteis

- [Documentação completa do `scipy`](https://docs.scipy.org/doc/scipy/reference/)
- [Módulo
  `scipy.optimize`](https://docs.scipy.org/doc/scipy/reference/tutorial/optimize.html)
- [Documentação do
  `curve_fit`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.curve_fit.html#scipy.optimize.curve_fit)

## Licença

Este trabalho está licenciado sob a Licença
Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional (BY-NC-SA 4.0
internacional) Creative Commons. Para visualizar uma cópia desta licença,
visite <http://creativecommons.org/licenses/by-nc-sa/4.0/>.
