---
layout: post
title: >-
    Oficina: NumPy, SciPy e matplotlib
---

O GEPAC realizará, nos dias 17, 24 e 31 (sextas-feiras) de maio, uma oficina
sobre NumPy, SciPy e matplotlib, gratuita e aberta a toda a comunidade IFSC.

SciPy e NumPy são bibliotecas Python que reúnem ferramentas para cientistas,
tanto de simulação como tratamento de dados, baseadas em estruturas de dados
de alto desempenho. A biblioteca matplotlib se encarrega de visualização de
dados, em forma de gráficos, histogramas e _heatmaps_.

O objetivo da oficina é capacitar os usuários em tarefas básicas com estas
três ferramentas --- por exemplo, álgebra linear, integração numérica,
transformadas de Fourier e ajuste de parâmetros a dados experimentais --- e
dar-lhes autonomia para usá-las em tarefas mais complexas.

A oficina será dada em três aulas, de 12h51 a 13h57, no laboratório 206 do
LEF. Para participar, é preciso [manifestar interesse pelo link](https://forms.gle/3x3aC3JxmKKG8bAk8)
até o dia 15/05. As vagas são limitadas.

A oficina será estruturada como a seguir:

## NumPy

1. O que é NumPy?
2. N-dimensional array (`ndarray`)
    - Estrutura
    - Tipos de dados
    - Slicing
    - Masking
3. Broadcasting
    - Operações com arrays
    - `vectorize` e `frompyfunc`
    - Funções universais
4. Usando tabelas de dados externas: `loadtxt` e `savetxt`

## matplotlib

1. O que é `matplotlib`?
2. O submódulo `pyplot`
    - Gráficos em escala linear, semilog e log-log
    - Histogramas
    - Heatmaps
3. Manipulando figuras
    - Tamanho personalizado
    - Subplots
    - Exportação

## SciPy

1. O que é SciPy?
2. Ajustando parâmetros a dados experimentais com `curve_fit`
3. Noções de `scipy.linalg`
     - Encontrar inversas, determinantes, normas (`inv`,`det`, `norm`)
     - Resolver sistemas lineares (`solve`)
     - Calcular autovalores e autovetores (`eig`)
4. Noções de `scipy.fftpack`
     - Funções `fft`, `ifft`, `rfft`, `irfft`
     - Exemplo: processar um sinal de áudio
5. Noções de `scipy.integrate`
     - Funções de quadratura `quad`, `dbldquad`, etc.
     - Solução de sistemas de EDOs com `odeint`
     - Exemplo: simular lançamento oblíquo com resistência do ar
