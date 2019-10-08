---
layout: post
title: "Quem é Julia?"
excerpt: Apresentar a linguagem Julia, que vem crescendo rapidamente no meio científico.
tags: [julia, curso]
---

[![Linguagem Julia](https://github.com/JuliaLang/julia-logo-graphics/blob/master/images/julia-logo-color.png?raw=true)](https://julialang.org)

## O que é Julia?
- Linguagem de alto nível e de tipagem dinâmica;
- Tem como proposito de atender a computação de alto desempenho numérico e científico;
- Usa a estrutura de compilação LLVM/JIT (just-in-time);
- Começou a ser desenvolvida em 2009 no MIT;
- Tem inspirações em linguagens como LISP, Scheme, Matlab, python, C++ entre outras;

## Por que usar Julia?

- Bom desempenho comparável a C, C++ e Fortran;
- Despacho múltiplo;
- Projetada para computação paralela e distribuída;
- Não é necessário vetorizar o código para melhorar o desempenho;
- A biblioteca padrão é escrita majoritariamente em julia;
- Ecossistema mantido pela comunidade, que faz julia crescer ainda mais.

## Como usar?
[Julia Download](https://julialang.org/downloads/)

[JuliaBox](https://www.juliabox.com)

[Documentação](https://docs.julialang.org/en/v1/)

[CheatSheets Quantecon](https://cheatsheets.quantecon.org/)

## Desempenho
[Julia Micro-Benchmarks](https://julialang.org/benchmarks/)
![benchmark](https://julialang.org/images/benchmarks.svg)

## Tipagem
### Hierarquia do tipo Number

- Tipos abstratos e primitivos

![Hierarquia number](https://upload.wikimedia.org/wikipedia/commons/4/40/Type-hierarchy-for-julia-numbers.png)

### Tipagem dinâmica
```julia
# \pi + TAB = π
A = [1, .1, 5.2, π]
```
```julia
B = [1, 2, 3, 4]
```
```julia
C = []
push!(C, "Hello World");
push!(C, "a");
push!(C, 6);
push!(C, 3.5);
push!(C, 3+5im);

# \euler + TAB = ℯ

push!(C, ℯ);

for i in C
    println(typeof(i))
end
```
### Tipagem estática
```julia
D = Array{Integer, 1}([])
push!(D, .2)
push!(D, 2)
push!(D, true)
```
### Tipos arbitrários BigInt e BigFloat
```julia
factorial(50)
factorial(BigInt(50))

BigFloat(π, 50000)
```
## Arrays

### Índices em arrays
Os índices em julia começa com 1, porém, é muito fácil de mudar

```julia
using Pkg;
Pkg.add("OffsetArrays");

using OffsetArrays

coeficiente = rand(5);
coeficiente = OffsetVector(coeficiente, -1:3);
coeficiente[-1]
```
* Exemplo
```julia
using OffsetArrays

coeficiente = OffsetVector([6, 5, -2, 3, 1], -1:3)
polinomio(x, coeficiente) = sum(coeficiente[n]*x^n for n in eachindex(coeficiente))

polinomio(2.0, coeficiente)
```

### Operador `dot`
```julia
x = rand(5);

sin(x)

function seno(x)
    y = []
    for i in x
        push!(y, sin(i))
    end
    return y
end

x = rand(5);

@time seno(x);
@time sin.(x);
@time @. sin(x);
```

## Multiple Dispatch (Despacho múltiplo)

```julia
struct Asteroide
    # Tipo Asteroide
end

struct Espaconave
    # Tipo Espaconave
end

function colide_com(x::Asteroide, y::Asteroide)
    # trata colisão Asteroide-Asteroide
end
function colide_com(x::Asteroide, y::Espaconave)
   # trata colisão Asteroide-Espaconave
end
function colide_com(x::Espaconave, y::Asteroide)
   # trata colisão Espaconave-Asteroide
end
function colide_com(x::Espaconave, y::Espaconave)
   # trata colisão Espaconave-Espaconave
end
```

## Metaprogramação: Expressões, Símbolos e Macros
[Metaprogramação JuliaDocs](https://docs.julialang.org/en/v1/manual/metaprogramming/#Metaprogramming-1)

- Expressões e Símbolos
```julia
# Criando uma expressão
eq = :(a*x^2 + b*x + c)

dump(eq)

eq.head

eq.args

a=1;b=2;c=-3;x=5
eval(eq)
```
- Macros
```julia
macro time2(ex)
    return quote
        local t0 = time()
        local val = $ex
        t = Float16(time()-t0)
        println("  ", t, " seconds, por time2")
        val
    end
end

a = rand(5);

@time sin.(a);
@time2 sin.(a);
```

## Julia Ecossistema
[Ecossistema](https://julialang.org/ecosystems/)
- [JuliaPhysics](https://juliaphysics.github.io/latest/index.html)
    - [Simulação de dinâmica](https://nbviewer.jupyter.org/github/JuliaDynamics/JuliaDynamics/blob/master/tutorials/Billiards%20Example/billiards_example.ipynb)

## Finalizando
Exemplo de como definir álgebra geométrica em Julia
```julia
# importando pacotes
import LinearAlgebra
import Base: +, -

# Criando o tipo multivetor
struct MultiVector{T<:Number}
    vetor::Array{T,1}
    escalar::T
end

# Criando o operador de produto geométrico
∘(x::Array{T,1}, y::Array{T,1}) where {T<:Number} = MultiVector(LinearAlgebra.cross(x,y)
                                                                , LinearAlgebra.dot(x,y))
# Sobrecarga dos operadores + e -
+(x::MultiVector, y::MultiVector) = MultiVector(x.vetor+y.vetor, x.escalar+y.escalar)
-(x::MultiVector, y::MultiVector) = MultiVector(x.vetor-y.vetor, x.escalar-y.escalar)

# Testando as operações
a = rand(3)
b = rand(3)

c = ∘(a,b)
println(c)
c = a∘b
println(c)
println(c+c)
println(c-c)
```
