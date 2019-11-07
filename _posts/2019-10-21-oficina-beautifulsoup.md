---
title: "Introdução a Web Scraping com BeautifulSoup"
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

## Navegação e Pesquisa

~~~ python
soup.div
soup.find('tag') 
soup.find('tag', id='id') 
soup.findAll('tag') # Retorna uma lista com todos os elementos com a tag 'tag'
soup.findAll('tag', limit=5) # Retorna uma lista com 5 elementos com a tag 'tag'
~~~

### Outros tipos de navegação

~~~ python
# Busca os elemento posteriores e anteriores
soup.findNext()
soup.findPrevious()
soup.findAllNext()


# Busca de baixo para cima
soup.find_parent()
soup.find_parents()

# Encontra outros elementos no mesmo nível que o elemento atual
soup.findNextSibling()
soup.findPreviousSibling()

soup.findNextSiblings()
soup.findPreviousSiblings()

~~~

## Acessando atributos do elemento

~~~ python
tag.getText()
tag.get_text()
tag.attrs.keys() # Retorna lista de atributos da tag

# Acessando o conteúdo do atributo
tag['class'] 
tag.get('class')
~~~


## Exemplo de utilização

~~~ python
from bs4 import BeautifulSoup
import requests

# Fazendo request da página
url = 'http://www.iftm.edu.br/patrocinio/cursos/tecnico-integrado-presencial/eletronica/corpo-docente/'

html = requests.get(url)
soup = BeautifulSoup(html.content, 'html.parser')

# Buscando e Armazenando dados da página

dados = []
titulo = soup.findAll('h2')[3] # Buscando elemento h2 cujo conteúdo é Corpo Docente

for e in titulo.findNextSiblings(): # Encontra irmãos do título
  dados.append(e.get_text()) # Coleta o conteúdo dos elementos irmãos

for i in range(len(dados)):
  dados[i] = dados[i].replace('\n', '') 

~~~



### Links úteis

- [Documentação completa da biblioteca `BeautifulSoup`](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)


## Licença

Este trabalho está licenciado sob a Licença
Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional (BY-NC-SA 4.0
internacional) Creative Commons. Para visualizar uma cópia desta licença,
visite <http://creativecommons.org/licenses/by-nc-sa/4.0/>.
