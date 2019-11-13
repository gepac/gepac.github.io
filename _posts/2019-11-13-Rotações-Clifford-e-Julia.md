---
title: "Rotações Clifford e Julia"
layout: post
excerpt: >-
    Palestra do dia 13/11/2019 sobre rotações do ponto de vista das Álgebras de Clifford com implementação em Julia.
tags: [julia,clifford,algebras,gepac,rotor]
---

# Introdução

Os números complexos e os quaternions são ferramentas bastante convenientes quando se trata de rotações. Suas construções, no entanto, envolvem objetos misteriosos que surgem, aparentemente, do nada. Hoje, vamos mostrar como as rotações funcionam nas Álgebras de Clifford, além de uma implementação em Julia, usando o pacote **Grassmann.jl**.

## Álgebras de Clifford

Um espaço vetorial tem operações de soma e multiplicação por escalar em sua definição, mas o produto entre vetores não é trivial. Em qualquer curso de álgebra linear é abordado o **produto interno** $$a\cdot b$$, uma forma bilinear, simétrica e positiva que leva dois vetores em um escalar. Também conhecemos da geometrica analítica o **produto vetorial** $$a\times b$$, que leva dois vetores em outro vetor, normal ao plano que contém os vetores de entrada e é anti-simétrico. Esse produto, porém, é um caso muito específico do $$\mathbb{R}^3$$, e por isso vamos adotar o **produto externo** $$a\wedge b$$, também anti-simétrico, porém que leva dois vetores em um **bivetor**, um segmento orientado de plano que possui a área do paralelogramo definido pelos vetores de entrada. 

### Produto Geométrico

Além desses produtos, podemos pensar em algo mais geral. Denote o produto entre $$a$$ e $$b$$ como $$ab$$. Podemos decompor esse produto como $$ab = \frac{1}{2}[(ab + ba) +(ab-ba)]$$, isto é, uma parte **simétrica** e uma **anti-simétrica**. Ou seja, podemos definir um novo produto, o **produto geométrico** $$ab=a\cdot b+a\wedge b$$.

Com isso podemos introduzir a Álgebra de Clifford do $$\mathbb{R^n}$$.

### A álgebra $\mathbb{Cl}_2$

Seja uma base ortonormal b = $$\{e_1,e_2\}$$ do $$\mathbb{R}^2$$. Aplicando o produto geométrico nessa base, podemos gerar o bivetor $$e_1e_2=-e_2e_1$$. Vamos calcular o quadrado desse bivetor:

$$
(e_1e_2)^2 = e_1e_2e_1e_2\\
(e_1e_2)^2 = -e_1e_1e_2e_2
$$

Como os vetores são ortonormais, podemos inverter a ordem dos vetores, desde que troquemos o sinal. Além disso:

$$
e_1e_1 = e_1\wedge e_1 = 1
$$

Isto é,

$$
(e_1e_2)^2=-1
$$

Com isso, temos uma estrutura análoga ao $$\mathbb{C}$$. 

### Rotações em 2D

Uma rotação em 2D pode ser computada como $$x\mapsto ux{u}^{-1}$$, onde $$u$$ é um número complexo. Uma rotação de um ângulo $$\theta$$ é associada ao número complexo $$cos(\theta/2)+e_1e_2sin(\theta/2)=exp(e_1e_2\frac{\theta}{2})$$. Ou seja, podemos definir a rotação num espaço 2D como:
$$
x \mapsto e^{(e_1e_2\frac{\theta}{2})}xe^{(-e_1e_2\frac{\theta}{2})}
$$
Vamos implementar isso em Julia:

## Grassmann.jl

Em Julia, podemos instalar um pacote da seguinte maneira:

Abra o terminal e entre no terminal e digite "Julia". Em seguida, execute:

```julia
using Pkg
Pkg.add("nome do pacote")
ou 
Pkg.add(PackageSpec(path="https://github.com/chakravala/Grassmann.jl.git"))#no caso do Grassmann.jl
```



O pacote Grassmann.jl implementa, dentre outras coisas, os produtos citados anteriormente. Com isso, podemos implementar a rotação anterior :

```julia
using Grassmann

basis"++" #gera os elementos do Cl(2)

function roda(x,theta) #define uma função que rotaciona x por um ângulo theta
    return round(exp(v12*theta/2)*x*exp(-v12*theta/2))
end

vetor = 1*v1#retorna um multivetor    
vetor_rot = roda(vetor,pi/2)
println(vetor)
println(vetor_rot)
println(vetor_rot[1])#podemos acessar os elementos como um array
```

## Generalizando o resultado

Refletindo um vetor duas vezes por dois vetores, estamos também rotacionando este vetor pelo dobro do ângulo entre os vetores usados nas reflexões. Isto é, se queremos rotacionar um vetor $$x$$ pelo ângulo entre os vetores $$m$$ e $$n$$, fazemos $$x \mapsto -nxn$$, pegamos o resutado e fazemos $$x \mapsto mnxnm$$. Denotando $$mn$$ por $$R$$ e $$nm$$ por $$\tilde{R}$$, o inverso de $$R$$, temos a seguinte transformação:   
$$
x \mapsto \tilde{R}xR\\
\text{onde }R = e^{B\frac{\theta}{2}}\text{e }\tilde{R}=e^{-B\frac{\theta}{2}}
$$
Caso $$m$$ e $$n$$ sejam unitários, chamamos $$R$$ de **rotor**. Basicamente, estamos substituindo o bivetor $$e_1e_2$$ por um um bivetor unitário quaquer, possibilitando rotacionar qualquer vetor em qualquer plano de rotação no $$\mathbb{R}^3$$. 

```julia
using Grassmann

basis"+++" #gera os elementos do Cl(3)

function roda3d(x,B,theta)
    return round(exp(B*theta/2)*x*exp(-B*theta/2))
end

B = v13 #define o plano de rotação
vetor = v1
vetor_rot3d = roda3d(vetor,B,pi/2)
println(vetor)
println(vetor_rot3d)
println(vetor_rot3d[1])
```

## Links

[site do buda](https://hirobuda.github.io/)

[Applications of Clifford’s Geometric Algebra](https://arxiv.org/abs/1305.5663)

[Geometric Algebra](https://arxiv.org/abs/1205.5935)

[Grassmann.jl](https://github.com/chakravala/Grassmann.jl)

