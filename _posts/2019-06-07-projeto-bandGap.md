---
layout: post
title: Cálculo de Band-gap com Python
tags: [python, projeto]
---

## Introdução

Com o objetivo de demonstrar alguns dos usos do Python com suas bibliotecas
[Numpy](https://www.numpy.org/),[Scipy](https://www.scipy.org/scipylib) e
[Matplotlib](https://matplotlib.org/) no meio científico, o GEPAC desenvolverá
um pequeno projeto de um programa para ler e tratar automáticamente alguns
dados de espectroscopia UV-Vis.

O programa irá ler arquivos de texto gerados pelo espectrômetro, contendo o
comprimento de onda emitido e a absorbância do material e, usando um método bem
conhecido na física e engenhaira de materiais, fazer o plot de
[Tauc](https://en.wikipedia.org/wiki/Tauc_plot) e obter automáticamente a
energia de _Band-gap_ (ou _banda proibida_, em português) do material através
dele.

O foco do projeto será na aplicação das bibliotecas e não no desenvolvimeto de
um programa completo, de modo que a parte física será apenas usada como
contextualização e os únicos pré requisitos para acompanhar são conhecimentos
em Python e nas citadas biliotecas (confira nosso material sobre elas
[aqui](https://gepac.github.io/2019-05-17-oficina-scipy-aula1/)).

A oficina será realizada no *dia 7 de Junho* (Sexta-Feira), das *12:51h até
as 13:57h*.

Todo o material referente a este e aos nossos outros projetos podem ser
encontrados no [site do GEPAC](https://gepac.github.io/).

As ferramentas usadas para o projeto serão:

- Leitura de documentos de texto com `numpy.loadtxt()`
- Operações com `ndarray`
- Visualização de dados com `matplotlib.pyplot`
- Suavização dos dados usando `scipy.signal.savgol_filter()`
- Aproximação de um gráfico de derivada com `numpy.diff()`
- Fitting linear usando `scipy.stats.linregress()`


## Observações

O seguinte projeto fará uso das bibliotecas Numpy, SciPy e Matplotlib do
Python 3. Para ter uma maior familiaridade com elas, acesse nosso material:

- [Oficina de bibliotecas Python: parte 1](https://gepac.github.io/2019-05-17-oficina-scipy-aula1/)
- [Oficina de bibliotecas Python: parte 2](https://gepac.github.io/2019-05-24-oficina-scipy-aula2/)
- Oficina de bibliotecas Python: parte 3 - EM CONSTRUÇÃO

*Estão disponíveis
[aqui](https://github.com/gepac/gepac.github.io/tree/master/img/projeto-bandgap-2019/data) alguns
dados de espectroscopia para serem usados no projeto.*

## Teoria

O estudo dos materiais semicondurores atrai cada vez mais os físicos e
engenheiros.  Dos transistores dentro de seu computador aos LEDs e células
solares, as propriedades dos semicondutores vêm sendo amplamente aproveitadas e
se tornaram cruciais no desenvolvimento da ciência e tecnologia.

![foto](/img/projeto-bandgap-2019/forno.jpg)

- Imagem: fornalhas para tratamento térmico de materiais Fonte: http://www.ifsc.usp.br/naca/facilities/

Aqui mesmo no [IFSC](https://www.ifsc.usp.br/) temos vários grupos de pesquisa
trabalhando com eles:

* [LFF - Grupo de Filmes Finos](https://www2.ifsc.usp.br/portal-ifsc/grupo-de-filmes-finos/?rowid_grupo=1043)
* [NaCA - Nanomateriais e Cerâmicas Avançadas](https://www2.ifsc.usp.br/portal-ifsc/grupo-de-crescimento-de-cristais-e-materiais-ceramicos/?rowid_grupo=23)
* [Grupo de Espectroscopia de Sólidos](https://www2.ifsc.usp.br/portal-ifsc/grupo-de-espectroscopia-de-solidos/?rowid_grupo=29)
* [Grupo de Semicondutores](https://www2.ifsc.usp.br/portal-ifsc/grupo-de-semicondutores/?rowid_grupo=19)
* [Grupo de Polímeros “Prof. Bernhard Gross”](https://www2.ifsc.usp.br/portal-ifsc/grupo-de-polimeros-prof-bernhard-gross/?rowid_grupo=20)

E estes são só alguns exemplos. *Mas o que exatamente são esses materiais?*

Diferente dos metais, os semicondutores não possuem elétrons livres para
circular. Mas com uma excitação suficientemente grande, esses elétrons podem
"saltar" de suas posições estáveis na *banda de valência* para a *banda de
condução*, onde agora sim podem ser transportados.

Para que isso ocorra, primeiro eles precisam "pular" por uma zona entre as duas
bandas, chamada de *band-gap*, que representa a diferença de energia entre a
*valência* e a *condução*:

![Bandgap](/img/projeto-bandgap-2019/bandgap.png)

Essa energia pode ser adquirida de diversas formas. Uma delas é através dos *fótons*.
Sabendo que a energia $$ E $$ de um *fóton* é igual à:

$$E = h\nu$$

Onde $$h$$ é a *constante de Plank* e $$\nu$$ a frequência do *fóton*, que por
sua vez é:

$$\nu = c / \lambda$$

Com $$\lambda$$, o *comprimento de onda* e $$c ≃ 2.997 * 10^8 m/s$$, a
velocidade da luz.

Os semicondutores *absorvem* ou *refletem* a luz de determinado cumprimento de
onda de acordo com uma relação entre a energia associada a esse comprimento e o
*bandgap* do material.

Esse é o princício da *Espectroscopia UV-Visível* (ou simplismente *UV-Vís*): o
*espectofotômetro* emite luz num comprimento de onda variável e ao mesmo tempo
mede a *absorbância* ($$A$$) do material.

Finalmente, essas duas medidas são relacionadas e podemos obter o valor da
energia de excitação do material desejado.

![foto](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/DU640_spectrophotometer.jpg/1200px-DU640_spectrophotometer.jpg)

- Imagem: espectofotômetro UV-Vís. Fonte:
  [Wikimedia](https://en.wikipedia.org/wiki/Ultraviolet%E2%80%93visible_spectroscopy);
  autor da foto:
  [TimVickers](https://commons.wikimedia.org/wiki/User:TimVickers)

O [gráfico de Tauc](https://en.wikipedia.org/wiki/Tauc_plot) tem justamente
esse propósito. Usando a chamada *relação de Tauc e Davis-Mott*, manipulamos os
dados obtidos na espectroscopia e conseguimos uma relação entre *comprimento de
onda*, *absorbância* e *energia de bandgap*:

$$(\alpha h \nu)^n = \beta (h \nu - E_g)$$

Parece uma fórmula bem complicada, mas repare:

O parâmetro $$\alpha$$ vem da [Lei de
Beer-Lambert](http://www.ufrgs.br/leo/site_espec/conceito.html), e com um pouco
de manipulação chegamos à:

  $$\alpha = 2.303 A / L$$

Onde $$A$$ é nossa conhecida *absorbância* e $$L$$ vem do *tamanho da amostra*.
Quando vamos fazer a espectroscopia de um material, ele é feito em pó e
misturado com água dentro de um [recipiente
padrão](https://en.wikipedia.org/wiki/Cuvette), que tem um tamanho bem definido
$$L = 1 cm$$:

  ![couvette](https://upload.wikimedia.org/wikipedia/commons/0/00/2_sizes_of_cuvette.jpg)

  - Imagem: recipiente padrão "couvette" de quartzo Fonte: https://en.wikipedia.org/wiki/Cuvette#/media/File:2_sizes_of_cuvette.jpg

Assim ficamos com:

$$\alpha = 2.303 A (cm^{-1})$$

O $$n$$ está relacionado com a [transição](https://www.youtube.com/watch?v=x3LOTaEVLak) do elétron entre as bandas. Ele só pode assumir o valor de $$2$$ ou $$1/2$$ e é dado dependendo do material.

Em relação à $$h \nu$$, vimos anteriormente que corresponde à energia do
fóton, sendo $$h ≃ 6.63*10^{-34} J.s$$, podemos escrevê-la como:

$$E ≃ 1240/\lambda (eVnm)$$

$$\beta$$ é simplesmente uma constante, não se preocupe com ela.

Repare que quando $$h\nu = E_g$$, então $$(\alpha h\nu)^n = 0$$.

No *gráfico de Tauc*, definimos a energia ($$h \nu$$) como *eixo x* do nosso
gráfico, enquanto $$(\alpha h\nu)^n$$ é o *eixo y*.
Teremos um pico bem definido próximo de $$\alpha = 0$$. Este pico possui uma
região razoávelmente linear, a qual ao extrapolarmos uma reta, interceptará
$$y = 0$$ exatamente onde $$x = E_g$$!


## Método

Assim que realizamos a espectroscopia *UV-Vis* de um material, normalmente
obtemos um arquivo de texto (`.txt`, `.dat`, `.csv`, etc). Este arquivo possui
duas colunas: a primeira correspondendo aos valores do comprimento de onda
$$\lambda$$ e a segunda a absorbância $$A$$ correspondente para este
comprimento. Com os resultados em mãos, agora vamos:

1. Ler o arquivo, obtendo assim dois `arrays`: um contendo os valores de
   $$\lambda$$ e o outro os de $$A$$

2. Obter os *eixos x* e *y* do nosso gráfico de Tauc fazendo: $$x =
   1240 / \lambda$$. Este primeiro corresponde à $$h \ nu$$ (energia); $$y =
   (2.303xA)^n$$ E este à $$(\alpha h \nu)^n$$. Para os próximos materiais,
   vamos trabalhar com $$n = 2$$ (transição direta).

3. Suavizar os dados, diminuindo o ruído.

4. Computar o gráfico da primeira derivada e encontrar seu ponto de máximo
   global.

5. Usar o ponto correspondente ao máximo do gráfico da primeira derivada no
   gráfico original. Selecionar os pontos próximos a ele e fazer o ajuste de
   uma reta.

5. Usar os coeficientes da reta encontrada para calcular onde ela intercepta o
   eixo x.


## Programando!

Começamos com um único arquivo de texto, contendo duas colunas. Este arquivo,
por exemplo, corresponde aos dados obtidos com uma amostra de dióxido de
titânio ($$TiO_2$$).

![documento](/img/projeto-bandgap-2019/tio2.png)

A primeira coluna corresponde ao comprimento de onda emitida pelo
espectofotômetro. A segunda, à absorbância medida.

Agora começaremos a trabalhar com esses dados. Primeiro vamos importar algumas
bibliotecas do Python:

```python3
import numpy as np
from matplotlib import pyplot as plt
```

Já podemos ler os dados do arquivo e calcular nossos eixos, como descrito
anteriormente:

```python3
lamb, A = np.loadtxt("tio2_abs.txt", delimiter='\t', unpack=True)

x = 1240 / lamb
y = (2.303 * x * A)**2
```

**Desafio:**
Com o *pyplot*, grafique x contra y dos seus dados. Neste caso, obtemos:

![grafico 1](/img/projeto-bandgap-2019/Figure_3.png)

Não se preocupe se seu gráfico estiver um pouco mais feio, veremos como lidar
com isso a seguir.

Levando em conta que os dados não costumam vir nada suaves, antes de mais nada
precisaremos filtrá-los.

> OBS: Se quiséssemos, até poderíamos já interpolar uma função e calcular sua
> derivada. Mas repare que mesmo em um conjunto de dados bom, teríamos alguns
> problemas:

> ![ruido](/img/projeto-bandgap-2019/Figure_2.png)

> Isso porque os pequenos "picos" devido ao ruído costumam ser muito abrubtos,
> e se não suavizarmos os dados brutos, até mesmo a variação entre dois pontos
> pode ser o bastante para causar esse tipo de coisa.

Vamos usar o filtro de
[Savitzky-Golay](https://en.wikipedia.org/wiki/Savitzky%E2%80%93Golay_filter)
para fazer essa suavização. Ele funciona repartindo o gráfico original
em vários pedaços, os quais individualmente fazemos o ajuste de um polinômio
de grau definido. Segue uma animação ilustrando seu funcionamento:

![Savitzky-Golay](https://upload.wikimedia.org/wikipedia/commons/thumb/8/89/Lissage_sg3_anim.gif/400px-Lissage_sg3_anim.gif)
- Fonte: https://en.wikipedia.org/wiki/File:Lissage_sg3_anim.gif ; feito por [Cdang](https://commons.wikimedia.org/wiki/User:Cdang)

Felizmente, o *scipy* possui uma função para isso!
Importamos assim o que precisaremos para dar continuidade ao trabalho:
```python3
from scipy.signal import savgol_filter
```

### [scipy.signal.savgol_filter](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.signal.savgol_filter.html)

A função para o filtro de Savitzky–Golay do *SciPy* recebe um *array* de
números e retorna outro de mesma dimensão, que corresponde ao *array* filtrado.
Entre os argumentos que ele recebe, os que nos interessam são a *janela* (isto
é, o número de elementos que ele considera para cada um dos citados *pedaços*
usados para o ajuste) e a *ordem do polinômio* que será usado.

É importante notar que, quanto maior a janela, embora mais suave o gráfico resultante, mais nos distanciamos do gráfico original, perdendo assim precisão.

Desta forma, agora precisamos decidir quais os parâmetros que usaremos. Isso
pode ser um tanto subjetivo, uma vez que não podemos dizer quais os
*melhores* valores (até porque eles mudariam para cada gráfico). Nesse caso,
usaremos a *janela* = 51 e a *ordem* = 3.

Encorajo que experimente com diferentes valores e compare o resultado final
obtido para cada um deles.

Escrevemos então:

```python3
y_smooth = savgol_filter(y, 51, 3)
```

Comparando o gráfico original com o novo gráfico resultante temos:
![comparação](/img/projeto-bandgap-2019/savgolexp.png)

Podemos ver que o novo gráfico está bem menos ruidoso.

Agora, usamos a função
[numpy.diff()](https://docs.scipy.org/doc/numpy-1.13.0/reference/generated/numpy.diff.html)
para obtermos uma aproximação do gráfico da primeira derivada de nossos dados.
Vale ressaltar: a função `numpy.diff()` funciona calculando a diferença entre dois
números consecutivos no *array* fornecido. Isso implica algumas coisas:

- Estamos calculando uma *aproximação* da derivada, embora possamos obter uma
  boa aproximação do que o gráfico dela seria desta forma.
- Por estarmos trabalhando com arrays apenas, nenhuma função precisa ser interpolada.
- O array retornado terá 1 (um) elemento a menos que o fornecido.

Fazemos então da seguinte forma:

```python3
dy = np.diff(y_smooth, 1)
dx = np.diff(x, 1)
```

Vamos visualizar o que temos até agora:

```python
from matplotlib import pyplot as plt
import numpy as np
from scipy.signal import savgol_filter

lamb, A = np.loadtxt('tio2_abs.txt', delimiter='\t', unpack=True)

x = 1240 / lamb
y = (2.303 * x * A)**2

y_smooth = savgol_filter(y, 51, 3)

dy = np.diff(y_smooth, 1)
dx = np.diff(x, 1)

plt.plot(x[:-1], dy/dx, label="dy/dx", color='red')
plt.plot(x, y_smooth, label="WTF")
plt.xlabel("Energia")
plt.ylabel("$(\alpha h \nu)^2$")
plt.title("Tauc plot")
plt.legend()
plt.grid()
plt.show()
```

Este programa, ao ser executado, deve retornar um gráfico parecido com este:

![grafico](/img/projeto-bandgap-2019/Figure_4.png)

Já está bem melhor do que aquela primeira imagem! Mas também podemos suavizar
o gráfico de $$dy/dx$$:

```python3
dydx_smooth = savgol_filter(dy/dx, 101, 3)

plt.plot(x[:-1], dydx_smooth, label = "dy/dx smooth", color = 'red')
```

Podemos usar uma *janela* maior para a suavização do gráfico da primeira
derivada, uma vez que queremos apenas seu ponto de máximo. A distorção dos
dados originais por conta do filtro não causará grande impacto.
Agora temos:

![smooth dy/dx](/img/projeto-bandgap-2019/Figure_5.png)

Agora basta que selecionemos o ponto de máximo global no gráfico de $$dy/dx$$:

```python3
maxindex = np.argmax(dydx_smooth)

plt.scatter(x[maxindex], y_smooth[maxindex], marker='x', color='k', label="x = " + str(x[maxindex]))
```

Temos:

![xmax](/img/projeto-bandgap-2019/Figure_6.png)

Agora já temos quase tudo pronto! O último passo é selecionar o conjunto dos
pontos mais próximos ao $$x$$ encontrado e fazermos um ajuste linear com eles.
No caso, seleciono 10 pontos para trás e 10 para frente, dessa forma:

```python3
x_linear = x[maxindex - 10 : maxindex + 10]
y_linear = y_smooth[maxindex - 10 : maxindex + 10]
```

Agora, usaremos a função
[scipy.stats.linregress()](https://docs.scipy.org/doc/scipy-0.14.0/reference/generated/scipy.stats.linregress.html)
para fazermos o ajuste da reta e obtermos os coeficientes angular e linear
dela. O valor do Bandgap (?), como descrito, é $$-b/a$$.

```python
a, b, r_value, p_value, stderr = linregress(x_linear, y_linear)
E_bandgap = -b/a
```

Tudo pronto! Daqui para frente só trabalharemos na visualização dos resultados.
O resultado final do programa é:

```python
from matplotlib import pyplot as plt
import numpy as np
from scipy.signal import savgol_filter
from scipy.stats import linregress

def f(x, a, b):
    return a*x + b

lamb, A = np.loadtxt('tio2_abs.txt', delimiter='\t', unpack=True)

x = 1240 / lamb
y = (2.303 * x * A)**2

y_smooth = savgol_filter(y, 51, 3)

dy = np.diff(y_smooth, 1)
dx = np.diff(x, 1)
dydx_smooth = savgol_filter(dy / dx, 101, 3)
maxindex = np.argmax(dydx_smooth)

x_linear = x[maxindex - 10: maxindex + 10]
y_linear = y_smooth[maxindex - 10: maxindex + 10]
a, b, r_value, p_value, stderr = linregress(x_linear, y_linear)
E_bandgap = round(-b/a, 2)

visualization_x = np.linspace(E_bandgap, x[maxindex-60], 2)

plt.scatter(E_bandgap, 0, marker='x', color='k', label="Bandgap = " + str(E_bandgap) + "eV")
plt.plot(x, y_smooth)
plt.plot(visualization_x, f(visualization_x, a, b), color='red')
plt.xlabel("Energia")
plt.ylabel("$(alpha h nu)^2$")
plt.title("Tauc plot")
plt.legend()
plt.grid()
plt.show()
```

Esse código nos dá:

![Resultado](/img/projeto-bandgap-2019/Result.png)

## Licença

Este trabalho está licenciado sob a Licença
Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional (BY-NC-SA 4.0
internacional) Creative Commons. Para visualizar uma cópia desta licença,
visite http://creativecommons.org/licenses/by-nc-sa/4.0/.
