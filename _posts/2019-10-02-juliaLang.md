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
- Usa a estrutura do compilador LLVM para a JIT (just-in-time);
- Começou a ser desenvolvida em 2009;
- Tem inspirações em linguagens como LISP, Scheme, Matlab, python entre outras;

## Por que usar Julia?

- Bom desempenho comparável a C e FORTRAN;
- Despacho múltiplo;
- Projetado para computação paralela e distribuida;
- Não é necessário vetorizar o código para melhorar o desempenho;
- A biblioteca padrão é escrita em julia;
- Ecosistema 

## Como usar?
[Julia Download](https://julialang.org/downloads/)

[JuliaBox](https://www.juliabox.com)

[Documentação](https://docs.julialang.org/en/v1/)

## Desempenho
[Julia Micro-Benchmarks](https://julialang.org/benchmarks/)
![benchmark](https://julialang.org/images/benchmarks.svg)

## Tipagem
### Hierarquia tipo Number
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
## Vetorização

### Índices em arrays
Os índices em julia começa com 1, porém é muito fácil de mudar

```julia
using Pkg;
Pkg.add("OffsetArrays");

using OffsetArrays

coeficiente = rand(5);
coeficiente = OffsetVector(coeficiente, -1:end);
coeficiente[-1]
```

### O `for` pode comer solto, com bom senso
```julia
function seno(x)
    y = []
    for i in x
        push!(y, sin(i))
    end
    return y
end

a = rand(5)

@time seno(a);
@time sin.(a);
@assert seno(a)==sin.(a)
```


## Multiple Dispatch (Despacho múltiplo)

```julia
function colide_com( x :: Asteroide, y :: Asteroide )
    # trata colisão Asteroide-Asteroide
end
function colide_com( x :: Asteroide, y :: Espaconave )
   # trata colisão Asteroide-Espaconave
end
function colide_com( x :: Espaconave, y :: Asteroide )
   # trata colisão Espaconave-Asteroide
end
function colide_com( x :: Espaconave, y :: Espaconave )
   # trata colisão Espaconave-Espaconavee
end
```

## Metaprogramação e Macros
- Símbolos
```julia
gepac = "Hello World!"
:gepac
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
