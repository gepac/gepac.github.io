---
title: "Introdução a Web Scraping com BeutifulSoup"
layout: post
excerpt: >-
    Nesta oficina será apresentado o conceito de WebScraping com o uso da biblioteca BeautifulSoup.
tags: [python, biblioteca, html, oficina]
---

## O que é Web Scraping?

Web Scraping é uma técnica de mineração de dados utilizada para coletar dados de sites da internet. Para exemplificar, imagine que você quer observar a flutuação dos preços dos ventiladores perto da Black Friday. Para isso, você precisa coletar os preços dos ventiladores que são disponibilizados nos sites de lojas de eletrodomésticos em um certo período de tempo. Através dessa técnica, você pode coletar esses dados e fazer sua análise. 

## HTML

As páginas dos sites são arquivos escritos com HTML, Hypertext Markup Language, que são interpretados pelo navegador e organizadas visualmente para você. A estilização normalmente é feita a partir de CSS, Cascading Style Sheets, mas não será o foco nessa oficina. Para o entendimento de como mineirar dados de uma página Web, é necessário que você saiba o básico de HTML.  

HTML, como seu próprio nome diz, é uma línguagem de marcação de hipertexto. Isso quer dizer que, de um modo geral, HTML é um modo de escrita que é sintaticamente distiguível. Cada elemento do HTML é definido por uma tag, e entre ela há o conteúdo do elemento.

~~~ html
<html>
    <body>
        <h1>GEPAC</h1>
        <h2>Grupo de Estudos de Programação Aplicada à Ciência</h2> 
        <table id='tabela-unica'>
            <tr>
              <th>Nome</th>
              <th>Número aleatório, aleatoriamente atribuído</th>
            </tr>
            <tr>
              <td>CJ</td>
              <td>8</td>
            </tr>
            <tr>
              <td>Michael</td>
              <td>96</td>
            </tr>
          </table>
    </body>
</html>
~~~

No código acima, há o elemento com identificador 'tabela-unica' cujo conteúdo está entre a tag 'table'.


## Fazendo requisições de Páginas Web

Para começar a coletar os dados de uma página, a priori, necessita-se de fazer o download da mesma. Temos uma biblioteca para isso em python.

~~~ python
import requests
url = 'http://www.iftm.edu.br/patrocinio/cursos/tecnico-integrado-presencial/eletronica/' #URL da pagína do IFTM - Campus Patrocínio que contém dados dos professores do curso de eletrônica
html = requests.get(url)
print(html)
~~~

Se a saída deste código for '<Response [200]>' quer dizer que a página foi baixada com sucesso. Dependendo da página que deseja baixar, haverá alguns impasses, por isso, aconselho a estudar um pouco mais da biblioteca requests para tratar eventuais erros e diferentes tipos de requests.

~~~ python
print(html.content) #Para visualizar o conteúdo da página baixada
~~~

## Introdução a BeautifulSoup

BeautifulSoup, sopa bonita, é uma biblioteca python para extração de dados de arquivos HTML e XML. Com ela você consegue facilmente navegar e pesquisar elementos e seus conteúdos. 

~~~ python
from bs4 import BeautifulSoup # Importa a biblioteca
soup = BeautifulSoup(html.content, 'html.parser') # Cria objeto BeautifulSoup 
print(soup.prettify()) # Mostra o conteúdo da página baixada com sua identação
~~~

Depois que você criou o objeto BeautifulSoup, você consegue navegar no arquivo facilmente. 


~~~ python


~~~


































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
