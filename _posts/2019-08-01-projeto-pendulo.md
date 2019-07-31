---
layout: post
title: >-
    Projeto: Pêndulo, do simples ao caótico
excerpt: >-
    Neste projeto, utilizaremos conhecimentos de Scipy para resolver um problema bem conhecido na física, o pêndulo.
tags: [python, projeto]
---

# Parte 1: Pêndulo simples (regime linear)

## Equação de movimento do pêndulo:

$$ m \ell \frac{d^2\theta}{dt^2} = - m g \sin \theta,$$

resultando na conhecida equação diferencial de segunda ordem:

$$ \frac{d^2\theta}{dt^2} = -\frac{g}{\ell}\sin\theta.$$

Em princípio, como dispomos de equação diferencial que descreve o nosso modelo, seríamos capazes de, dadas as condicões inciais, calcular o comportamento futuro do pêndulo. Infelizmente, a não-linearidade introduzida pelo $\sin\theta$ do lado direito da equação impede que sejam usados os métodos conhecidos de solução de equações diferenciais.

## Linearização

A saída tradicional para esse dilema é usar um método perturbativo e nos preocuparmos apenas com variações pequenas em torno de um ponto conhecido. No caso expandimos em torno do ponto de equilíbrio $\theta=0$ e consideramos que o ângulo $\theta$ é pequeno, portanto podemos aproximar

$$\sin\theta \approx \theta,$$

o que pode ser entendido lembrando a expansão de Taylor do seno em torno do ângulo 0:

$$\sin\theta = \theta - \frac{\theta^3}{3!} +  \frac{\theta^5}{5!} + \cdots,$$

onde vemos que para $\theta$ pequeno (próximo de zero) os termos não lineares podem ser desprezados.

Outra forma de ver isto é através de um gráfico.

Para isso, importaremos as bibliotecas Numpy e Matplotlib e iremos variar $\theta$ de 0 a 20 graus a cada $0.2$ graus, transformando em radianos no final. 

Com este dado, criamos uma função linear de $\theta$ no eixo x e de seu valor em radianos no eixo y. Agora, temos que comparar com seu valor de seno e para isso utilizamos uma segunda função ainda com $\theta$ no eixo x e de $\sin\theta$ no eixo y.

~~~ python
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

θ = np.linspace(0, 20, 100+1)
θ_rad =  np.deg2rad(θ)
pl_linear, = plt.plot(θ, θ_rad, label=r'$\theta$') 

pl_sin, = plt.plot(θ, np.sin(θ_rad), label=r'$\sin\theta$')

plt.xlabel(r'$\theta$')
plt.legend(handles=[pl_linear, pl_sin])
plt.show()
~~~

Podemos ver que para ângulos pequenos (menores que 10°) há pouca diferença entre $\sin\theta$ e $\theta$.

A equação aproximada fica então

$$ \frac{d^2\theta}{dt^2} = -\frac{g}{\ell}\theta$$

que é uma equação linear e pode ser resolvida por métodos tradicionais, resultando em

$$\theta(t) = \theta_0\cos(\omega_0 t - \phi),$$

onde $\theta_0$ é o ângulo inicial, $\omega_0 = \sqrt{g/\ell}$ é a denominada frequência angular natural do pêndulo e $\phi$ depende da velocidade inicial do pêndulo (pode ser encontrado de $v_0 = \theta_0\sqrt{g\ell}\sin\phi$).

Fixando alguns parâmetros do pêndulo

A partir de agora vamos fixar um pêndulo com $\ell=9.8$ usando $g = 9.8m/s^2$ (de modo que $\omega_0=1$), sempre considerando a massa sendo largada do repouso (que resulta em $\phi=0$).
 
~~~ python
ℓ = 9.8 # ℓ é o comprimento
g = 9.8 # g a aceleração da gravidade
ω0 = np.sqrt(g/ℓ)
T0 = 2 * np.pi / ω0 # Período da oscilação natural
~~~

## Discretização do tempo

Ao realizar cálculos numéricos ou visualização no computador, precisamos discretizar todas as funções contínuas. Ao discretizar no tempo precisamos fixar a definição temporal (intervalo entre dois instantes de tempo computados), o tempo total e o número total de instantes de tempo avaliados. Vamos definir uma função para ajudar a calcular fatores de temporização. Como estamos lidando com funções oscilantes, nos baseamos no tempo de um ciclo de oscilação. Então a função recebe o número de ciclos desejados, o tempo um ciclo e, opcionalmente, o número de intervalos de tempo em um ciclo; caso este último argumento não seja fornecido, vamos assumir o valor 100. Com esses valores a função calcula e retorna (na ordem) o tempo total de simulação, o número total de pontos simulados e o intervalo de tempo entre dois pontos.

~~~ python
def timing(ncycles, T, n_per_cycle = 100):
    return (ncycles * T, ncycles * n_per_cycle, T / n_per_cycle)
~~~

# Parte 2: Pêndulo simples (regime não-linear)

A questão agora é a seguinte: O que acontece se o ângulo $\theta$ não é necessariamente pequeno? Neste caso, não temos uma solução exata, mas podemos usar o mesmo método para a solução numérica. Voltando à esquação original,

$$\frac{d^2\theta}{dt^2} = - \frac{g}{\ell}\sin\theta,$$

fazemos a transformação

~~~ python
\begin{eqnarray}
  x & = & \theta \\
  y & = & \frac{d\theta}{dt}
\end{eqnarray}
~~~

Chegando a:

~~~ python
\begin{eqnarray}
  \frac{dx}{dt} & = & y \\
  \frac{dy}{dt} & = & - \frac{g}{\ell} \sin x
\end{eqnarray}
~~~

Com o qual podemos montar a solução usando SciPy.

~~~ python
def simple_nonlinear_pendulum(xy, t, g, ℓ):
    x, y = xy
    return y, -g/ℓ * np.sin(x)

def simulate_simple_nonlinear_pendulum(θ0, tmax, nintervals, g, ℓ):
    θ0_rad = np.deg2rad(θ0)
    t = np.linspace(0, tmax, nintervals + 1)
    xy0 = [θ0_rad, 0.]
    xy = odeint(simple_nonlinear_pendulum, xy0, t, args=(g, ℓ))
    return t, np.rad2deg(xy)
~~~

Uma função para fazer comparação da solução linear com a não-linear, dados os parâmetros.

~~~ python
def compare_nonlinearity(θ0, tmax, nintervals, g, ℓ):
    t, xy = simulate_simple_nonlinear_pendulum(θ0, tmax, nintervals, g, ℓ)
    linear = θ0*np.cos(np.sqrt(g/ℓ) * t)
    pn, = plt.plot(t, xy[:,0], label='Não-linear')
    pl, = plt.plot(t, linear, label='Linear')
    plt.xlabel(r'$t$')
    plt.ylabel(r'$\theta$')
    plt.legend(handles=[pn, pl])
    plt.show()
~~~

Fixando o intervalo de tempo e número de intervalos:

~~~ python
ncycles = 5
tmax, nintervals, _ = timing(ncycles, T0)

compare_nonlinearity(5.0, tmax, nintervals, g, ℓ)
~~~

Note como para $\theta_0$ pequeno não se vê diferença entre o sistema não-linear e o linear. Vejamos agora com um ângulo inicial maior.

~~~ python
compare_nonlinearity(25.0, tmax, nintervals, g, ℓ)
~~~

Aqui já começamos a ver que o sistema não linearizado apresenta uma frequência menor de operação. Vejamos para um ângulo inicial ainda maior.

~~~ python
compare_nonlinearity(45., tmax, nintervals, g, ℓ)
~~~

Mantendo a tendência de diminuir a frequência com o aumento de $\theta_0$. Mais dois ângulos maiores:

~~~ python
compare_nonlinearity(90., tmax, nintervals, g, ℓ)

compare_nonlinearity(160., tmax, nintervals, g, ℓ)
~~~

Note como neste último caso fica também claro que o formato da onda, antes aparentemente senoidal, é dependente do ângulo inicial.

Vemos que o modelo linear, como esperado, é completamente inadequado para descrever o sistema real, que apresenta não-linearidade, quando fora do regime perturbativo.

# Parte 4: Força externa

Vamos agora acrescentar um novo elemento: uma força externa atuando sobre o sistema. Para termos um comportamento mais interessante, essa força externa terá um formato senoidal com frequência angular $\omega_F$ e amplitude $f$ dadas, isto é, terá a forma $ f\sin(\omega_F t)$.

Nosso sistema fica então:

~~~ python
\begin{eqnarray}
  \frac{dx}{dt} & = & y \\
  \frac{dy}{dt} & = & -\frac{g}{\ell}\sin x - q y + f \sin \omega_F t.
\end{eqnarray}
~~~

Para simular este sistema seguimos o mesmo procedimento anterior, mas iremos tomar um cuidado adicional: Como existe uma força externa além da gravidade, é possível que o pêndulo passe acima de seu ponto de origem, podendo inclusive "rodar" para o outro lado. Para manter os ângulos dentro de uma faixa específica (para facilitar os gráficos) vamos então continuamente manter os ângulos na faixa $[-180º, 180º)$.

~~~ python
def normalize_angle(angle):
    return (angle + 180.) % 360. - 180.

def forced_dissipative_nonlinear_pendulum(xy, t, g, ℓ, q, f, ωf):
    x, y = xy
    return y, -g/ℓ*np.sin(x)-q*y+f*np.sin(ωf*t)

def simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf):
    θ0_rad = np.deg2rad(θ0)
    t = np.linspace(0, tmax, nintervals + 1)
    xy0 = [θ0_rad, 0.]
    xy = odeint(forced_dissipative_nonlinear_pendulum, xy0, t, args=(g, ℓ, q, f, ωf))
    return t, np.rad2deg(xy)
~~~

Vamos fixar $q=0.5$ e $\omega_F = 2/3$, e usar $f$ como parâmetro e ajustar o intervalo de $t$. Também, agora calculamos a temporização com base na frequência forçante:

~~~ python
q = 0.5
ωf = 2./3.
Tf = 2 * np.pi / ωf
ncycles = 10
tmax, nintervals, _ = timing(ncycles, Tf)

def forced_dissipative_nonlinear_pendulum(xy, t, g, ℓ, q, f, ωf):

f = 0.01
t, xy = simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf)
q01 = normalize_angle(xy[:,0])
p01, = plt.plot(t, xy[:, 0])
plt.xlabel(r'$t$')
plt.ylabel(r'$\theta$')
plt.show()
~~~

Note como o sistema começa oscilando de acordo com a sua frequência natural, mas com o amortecimento que desgasta a energia inicialmente fornecida, passa a ser dominado pela frequência da força externa.


## Parte 5: Sensibilidade a condições iniciais

Vamos agora avaliar agora um outro aspecto do sistema: Vejamos o que acontece quando o sistema se desenvolve a partir de condições iniciais ligeiramente diferentes (continuamos com velocidade inicial zero, mas variamos ligeiramente o ângulo inicial). Avaliaremos então a diferença (absoluta) entre as duas trajetórias com a passagem do tempo.

Primeiramente, para $f=0.1$.

~~~ python
θ0 = 12. # Ângulo inicial
Δθ0 = 0.2
θ0prime = θ0 + Δθ0 # Ângulo inicial ligeiramente modificado

ncycles = 10
tmax, nintervals, _ = timing(ncycles, Tf)

f = 0.1

t, xy_0 = simulate_forced(θ0,      tmax, nintervals, g, ℓ, q, f, ωf)
t, xy_1 = simulate_forced(θ0prime, tmax, nintervals, g, ℓ, q, f, ωf)

plt.plot(t, np.abs(normalize_angle(xy_0[:, 0] - xy_1[:, 0])))
plt.yscale('log')
plt.xlabel(r'$t$')
plt.ylabel(r'Diferença')
plt.title(r'$f=' + str(f) + r'$')
plt.show()
~~~

Note como a diferença entre as trajetórias decai rapidamente. Na verdade, usamos a escala logaritmica no eixo das diferenças para mostrar que a queda tem tendência exponencial. Isto é,

$$ | \Delta\theta | \approx e^{\lambda t}, $$

onde $\lambda$ é negativo neste caso.

Agora vejamos o que acontece para $f=1.2$. (Para mostrar melhor o comportamento, começamos com um valor bem menor de diferença nos ângulos iniciais.)

~~~ python
θ0 = 12.
Δθ0 = 0.00000001
θ0prime = θ0 + Δθ0

ncycles = 30
tmax, nintervals, _ = timing(ncycles, Tf)

f = 1.2

t, xy_0 = simulate_forced(θ0,      tmax, nintervals, g, ℓ, q, f, ωf)
t, xy_1 = simulate_forced(θ0prime, tmax, nintervals, g, ℓ, q, f, ωf)

plt.plot(t, np.abs(normalize_angle(xy_0[:, 0] - xy_1[:, 0])))
plt.yscale('log')
plt.xlabel(r'$t$')
plt.ylabel(r'Diferença')
plt.title(r'$f=' + str(f) + r'$')
plt.show()
~~~

Vemos que neste caso o comportamento é oposto: a diferença entre as trajetórias continua seguindo uma tendência da forma

$$ |\Delta\theta| \approx e^{\lambda t}, $$

mas agora o coeficiente $\lambda$ é **positivo**! Isto significa que **qualquer pequena diferença nas condições inciais será amplificada rapidamente**, resultando em que o comportamento do sistema é na prática impossível de prever, visto que não é possível uma precisão perfeita na determinação das condições iniciais. Esta é uma característica dos sistemas denominados **caóticos**.

## Parte 6: Seção de Poincaré

Uma técnica útil na análise de sistemas dinâmicos é a denominada *seção de Poincaré*, ou *mapa de Poincaré*, que é essencialmente a interseção de uma trajetória no espaço de fase com um espaço de menor dimensão (um subespaço) específico.

O subespaço a escolher depende do sistema e da análise desejada. No nosso caso, vamos plotar apenas os pontos que correspondem a instantes que satisfazem a relação $\omega_F t = 2n\pi$ onde $n$ é um inteiro. Isto é, estamos plotando apenas os pontos onde a trajetória está em fase com a força externa aplicada. Devido às aproximações numéricas, devemos na verdade plotar um ponto se ele satisfizer $|t - 2n\pi/\omega_F| < \Delta t/2$ para algum valor de $\Delta t$ apropriado. No nosso caso, usaremos para $\Delta t$ o valor do intervalo entre amostras no sistema simulado, visto que não há precisão temporal maior do que essa em nossos resultados.

Vamos fazer uma função que dado um array de valores de tempo, o tempo de ciclo e a precisão de tempo $\Delta t$ seleciona todos os índices (retornando um vetor de booleanos) que estão sincronizados com o ciclo de acordo com a expressão acima:

~~~ python
def select_synchronized(t, Tf, Δt):
    return np.abs(np.mod(t+Tf/2, Tf) - Tf/2) < Δt/2

q = 0.5
ncycles = 200
tmax, nintervals, Δt = timing(ncycles, Tf)

f = 0.1

t, xy = simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf)
traj = normalize_angle(xy[:, 0])

selected = select_synchronized(t, Tf, Δt)

plt.plot(traj[selected], xy[selected, 1], 'o', markersize=3, alpha=0.3) # Plota apenas esses pontos
plt.show()
~~~

Note como, com exceção de alguns pontos devidos ao transiente inicial, os pontos agora se acumulam em um mesmo local. A razão é simples: depois do transiente inicial, o sistema assume uma trajetória periódica de acordo com a frequência da força externa, e portanto, se olhamos a intervalos determinados pelo período dessa oscilação, encontraremos o pêndulo sempre no mesmo ponto.

Vejamos agora o que acontece no caso caótico.

~~~ python
f = 1.2

t, xy = simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf)
traj = normalize_angle(xy[:, 0])

selected = select_synchronized(t, Tf, Δt)

plt.plot(traj[selected], xy[selected, 1], '.', markersize=3, alpha=0.3)
plt.show()
O que acontece se deixamos o sistema executar por mais tempo?

ncycles = 2000
tmax, nintervals, Δt = timing(ncycles, Tf)

t, xy = simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf)

traj = normalize_angle(xy[:, 0])
selected = select_synchronized(t, Tf, Δt)

plt.plot(traj[selected], xy[selected, 1], '.', markersize=3, alpha=0.3)
plt.show()
~~~

Note como o formato ficou o mesmo. Quer dizer, apesar de toda a característica caótica do sistema, ele é atraído a uma região específica do espaço de estado, denominado um **atrator**. A estrutura dos atratores no regime caótico é bastante complexa, tendo dimensionalidades fracionárias (o número de dimensões do espaço do atrator não é um número inteiro), e portanto são denominados **fractais**; os atratores fractais são denominados de **atratores estranhos**.

## Parte 7: Duplicação de período

Vamos analisar o regime estacionário para diferentes parâmetros de amplitude da força externa ($f$). Para isso, simulamos o sistema para um grande número de ciclos e descartamos todos os resultados com exceção dos últimos ciclos, que mostrarão o funcionamento do sistema após o término do transiente.

~~~ python
ncycles = 212 # Número total de ciclos a simular
samples_per_cycle = 100 # Número de pontos amostrados por ciclo
tmax, nintervals, _ = timing(ncycles, Tf, samples_per_cycle)
ncycles_discard = 200 # Número de ciclos a descartar (evitando o transiente)
ndiscard = ncycles_discard * samples_per_cycle # Número de amostras descartadas

plt.figure(figsize=(9,9))
fs = [1.065, 1.075, 1.082, 1.0826] # Alguns valores de f
for i, f in enumerate(fs):
    t, xy = simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf)
    traj = normalize_angle(xy[:, 0])
    
     # Seleciona os valores não descartados
    to_keep = np.arange(nintervals+1) > ndiscard # Calcula os indices a manter
    traj_steady = traj[to_keep]
    t_steady = t[to_keep]
    
    # plota os gráficos
    plt.subplot(len(fs), 1, i+1)
    plt.plot(t_steady, traj_steady)
    plt.ylabel(r'$\theta$')
    plt.title(r'$f='+str(f)+r'$')
plt.xlabel(r'$t$')
plt.tight_layout()
~~~

Note que para $f=1.065$, no regime estacionário a oscilação tem um período igual ao da força externa (no caso dos parâmetros usados, $T_F = 3\pi$).

Já para $f=1.075$, os picos oscilam alternadamente de altura, o que significa que o período de repetição é dobrado. Para $f=1.082$ temos 4 picos de valores diferentes antes da repetição, o que implica um período quadruplicado em relação ao original. Para $f=1.0826$ temos 8 picos distintos, apesar de isso ser muito difícil de enxergar na figura. Esse processo de duplicação se repete indefinidamente, com intevalos entre os valores de $f$ para a duplicação cada vez menores, até que chegamos no regime caótico. Essa **duplicação de período** é típica em vários sistemas caóticos.
### Diagrama de bifurcação

Uma forma tradicional de analisar o processo de duplicação de período na transição para o caos é através da construção de um **diagrama de bifurcação** para analisarmos os pontos onde a duplicação de período ocorre.

Para o nosso sistema usamos a mesma técnica da seção de Poincaré para extrair os pontos em fase com força externa e plotamos, para cada valor de $f$, todos os valores encontrados nesses pontos dentro do regime estacionário.

*OBS:* A execução do código abaixo é demorada, pois realiza um grande número de simulações.

~~~ python
# Faixa de valores de f a simular
fs = np.linspace(1.065,  1.087, 200+1)

# Parâmetros da simulação
ncycles = 250
ncycles_discard = 200
samples_per_cycle = 100
tmax, nintervals, Δt = timing(ncycles, Tf, samples_per_cycle)
ndiscard = ncycles_discard * samples_per_cycle

plt.figure(figsize=(9,9))
for f in fs:
    t, xy = simulate_forced(θ0, tmax, nintervals, g, ℓ, q, f, ωf)
    traj = normalize_angle(xy[:, 0])
    selected = select_synchronized(t, Tf, Δt) # Selecionados pela seção de Poincaré
    traj_steady = traj[selected & (np.arange(nintervals+1) > ndiscard)] # Pega apenas os não-descartados
    # Plota todos os pontos no mesmo valor de f
    plt.plot(f * np.ones_like(traj_steady), traj_steady, 'k.', markersize=1)
plt.minorticks_on()
plt.grid(which='both', color='r', alpha=0.1)
plt.show()
~~~

Note como até um pouco acima de $f=1.066$ a trajetória atinge sempre o mesmo ponto (para um dado $f$). Um pouco acima disso já são atingidos dois pontos distintos. Isso é uma marca de que o período de oscilação é o dobro do período de amostragem. Por volta de $f=1.079$ há uma nova duplicação (cada um dos pontos anteriores é dividido em dois). Próximo a $f=1.082$ há outra duplicação. Esse processo em princípio se repete indefinidamente, com o intervalo entre um ponto de duplicação e outro diminuindo exponencialmente, até que se atinge o ponto de caos.

Vale a pena você fazer mudanças na faixa de $f$ usada para entender melhor o sistema.
