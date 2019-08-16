---
layout: post
title: >-
    Oscilações membranas: Solução numérica
excerpt: >-
    Com esse projeto temos o objetivo de mostrar o funcionamento da discretização finita para uma EDP parabólica e homogênea
tags: [python, projeto]
---

# Solução numérica para ondas estacionárias em membranas

# Introdução

Nem sempre é necessário ou possível obter a solução exata de um determinado sistema, para esses problemas usamos modelagem e simulações para obter soluções numéricas.

A ideia com essa modelagem é mostrar que apesar do problema exato ser complexo, a solução numérica é simples e têm diversas aplicações.

Equação de onda:

$$\nabla^2 u = \frac{1}{v^2}\frac{\partial^2 u}{\partial t^2}$$

$$\rho\frac{\partial^2 u}{\partial t^2} - \sigma\nabla^2 u = 0$$

Para a forma matricial usaremos $K = - \sigma\nabla^2$ para a modelagem:

$$MU''+KU = 0$$

$$U(t) = \Phi\sin{(\omega t)}$$

$$U''(t) = -\Phi\omega^2\sin{(\omega t)}$$

$$K\Phi - \omega^{2}M\Phi = 0$$

$$K\Phi = \omega^{2}M\Phi$$

$$\rho\frac{d^{2}u_{ij}}{dt^{2}} - \sigma\frac{u_{i+1,j}+u_{i-1,j}+u_{i,j+1}+u_{i,j-1}-4u_{ij}}{\delta^{2}} = 0$$

![Malha de discretizando da membranda](/img/projeto-membranas-2019/malha.png)

#### Bibliotecas usadas
~~~python
import numpy as np
import scipy.linalg as la
import matplotlib.pyplot as plt
from mpl_toolkits import mplot3d
~~~

#### Cria o índice global
~~~python
def ij2n(i,j,Nx):
    return(i + j*Nx)
~~~

#### Construção das matrizes K e M
~~~python
def buildKM(Nx, Ny, rho, sigma, delta, mascara):
    NxNy = Nx*Ny
    K = np.zeros( (NxNy,NxNy) )
    M = np.zeros( (NxNy,NxNy) )

    for i in range(1, Nx-1):
        for j in range(1, Ny-1):
            Ic = ij2n(i,   j,   Nx)
            K[Ic,Ic] = -4
            
            Ie = ij2n(i+1, j,   Nx)
            K[Ic,Ie] = 1
            
            Iw = ij2n(i-1, j,   Nx)
            K[Ic,Iw] = 1
            
            In = ij2n(i,   j+1, Nx)
            K[Ic,In] = 1
            
            Is = ij2n(i,   j-1, Nx)
            K[Ic,Is] = 1

    K *= -sigma/(delta**2)

    for i in range(Nx):
        for j in range(Ny):
            Ic=ij2n(i,j,Nx)

            if masc[i, j]:
                K[Ic,:]=0.
                K[:,Ic]=0.
                K[Ic,Ic]=1e9

            M[Ic,Ic] = rho

    return(K, M)
~~~
#### Montagem da máscara da membrana
~~~python
def mascara(Nx, Ny):
    Mk = np.zeros( (Nx,Ny), np.int )
    
    # Define um raio que tem 40% do menor lado da malha
    r = np.floor(0.4*min(Nx, Ny))

    for i in range(Nx):
        x = i - Nx//2
        for j in range(Ny):
            y = j - Ny//2
            
            # Pode colocar a função que desejar no if
            if x**2 + y**2 > r**2:
                # Define com 1 os pontos da membrana que estão travados
                Mk[i,j] = 1
    
    # Trava as bordas
    Mk[ 0, :] = 1
    Mk[-1, :] = 1
    Mk[ :, 0] = 1
    Mk[ :,-1] = 1

    return Mk
~~~
#### Atribuindo valores
~~~python
Nx = int(input("Nx: "))
Ny = int(input("Ny: "))
Lx = 1.
delta = Lx/(Nx-1)
sigma = float(input("sigma: "))
rho = float(input("rho: "))

K, M = MatMembrane(Nx, Ny, rho, sigma, delta, mascara(Nx, Ny))
~~~
#### Resolvendo o sistema e obtendo os Autovalores e Autovetores

~~~python
[D, V] = scipy.linalg.eig(A, B)
A*V = D*B*V
~~~
- Funcionamento da função de eig do scipy

A = matriz (NxNy x NxNy)
B = matriz (NxNy x NxNy)
D = autovalores (NxNy)
V = autovetores (NxNy x NxNy)

$$AV = DBV$$
$$K\Phi = \omega^{2}M\Phi$$

~~~python
def resolveSistema(K, M):
    omega2, phi = la.eig(K, M)
    
    # Ordena os resultados
    idx = np.argsort(omega2)
    omega2 = omega2[idx]
    phi = phi[:,idx]
    omega = np.sqrt(omega2)
    
    return(phi, omega)
~~~
#### Visualização dos dados
~~~python
opcao = int(input("Escolher modo (1) ou todos os modos (0): "))

phi, omega = resolveSistema(K, M)

if opcao:
    while True:
        k = int(input("Digite um valor inteiro: "))
        Z = phi[:, k].reshape(Nx, Ny)
        

        zlim = np.max(np.abs(Z))*1.02

        x = range(Nx)
        y = range(Ny)

        X, Y = np.meshgrid(x, y)

        fig = plt.figure(dpi=200)
        ax = plt.axes(projection='3d')

        ax.set_xlabel('x')
        ax.set_ylabel('y')
        ax.set_zlabel('z')
        ax.set_xlim(0, Nx-1)
        ax.set_ylim(0, Ny-1)
        ax.set_zlim(-zlim, zlim)
        plt.tight_layout()
        plt.ion()

        for i in np.linspace(0, 1, 1000):
            ax.clear()
            ax.set_zlim(-zlim, zlim)
            ax.plot_surface(X, Y, Z*np.sin(omega[k]*i), cmap="binary")
            plt.show()
            plt.pause(0.00001)
else:
    k = 0
    Z = phi[:, k].reshape(Nx, Ny)
    

    zlim = np.max(np.abs(Z))*1.02

    x = range(Nx)
    y = range(Ny)

    X, Y = np.meshgrid(x, y)

    fig = plt.figure(dpi=200)
    ax = plt.axes(projection='3d')

    ax.set_xlabel('x')
    ax.set_ylabel('y')
    ax.set_zlabel('z')
    ax.set_xlim(0, Nx-1)
    ax.set_ylim(0, Ny-1)
    ax.set_zlim(-zlim, zlim)
    plt.tight_layout()
    plt.ion()

    for k in range(Nx*Ny):
        Z = phi[:, k].reshape(Nx, Ny)
        ax.clear()
        ax.set_zlim(-zlim, zlim)
        ax.plot_surface(X, Y, Z, cmap="binary")
        plt.show()
        plt.pause(0.00001)
~~~
