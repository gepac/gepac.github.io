---
css:
  - "/css/frescuras.css"
layout: page
title: Sobre
subtitle: Quem? Por quê? Para quê?
---

O GEPAC (Grupo de Estudos de Programação Aplicada à Ciência) busca, de maneira
dinâmica e colaborativa, construir os conhecimentos necessários para aproximar
estudantes da computação científica.

Através de palestras, oficinas e projetos guiados, o GEPAC implementa soluções
didáticas para problemas reais. Ao aproximar os alunos de projetos
computacionais, ajudamos a capacitar futuros pesquisadores e profissionais do
mercado.

---

Fundado em fevereiro de 2019, por alunos de cursos de bacharelado e mestrado do
IFSC, com supervisão do
[Prof. Dr. Fernando Paiva](https://www2.ifsc.usp.br/portal-ifsc/pagina-pessoal-docente/?codigo=7228).

Atualmente, o grupo é administrado pelos seguintes membros:

{%- assign metade = site.pessoas.size | divided_by: 2 %}
{{ metade }}

<div class="row">
<div class="col-md-6 col-sm-6">
{% for manolo in site.pessoas limit:metade %}
	{%- if manolo.link -%}
		{%- assign manolink = manolo.link -%}
	{%- else -%}
		{%- assign manolink = "#" -%}
	{%- endif -%}

	{%- if manolo.photo != "" -%}
		{%- assign manophoto = manolo.photo -%}
	{%- else -%}
		{%- assign manophoto = "ninguem.jpg" -%}
	{%- endif -%}

	<div class="media frescurinhas">
		<div class="pull-right ">
			<a href="{{ manolink }}">
				<img class="media-object img-circle" src="/pessoas/{{ manophoto }}" alt="{{ manolo.name | append: " é supimpa :)" }}">
			</a>
		</div>
		<div class="media-body">
			<h4 class="media-heading">{{ manolo.name }}</h4>
			<div style="margin-top: -1.5em">
				{{ manolo.content | markdownify }}
			</div>
		</div>
	</div>
{% endfor %}
</div>

<div class="col-md-6 col-sm-6">
{%- for manolo in site.pessoas offset:metade -%}
	{%- if manolo.link -%}
		{%- assign manolink = manolo.link -%}
	{%- else -%}
		{%- assign manolink = "#" -%}
	{%- endif -%}

	{%- if manolo.photo != "" -%}
		{%- assign manophoto = manolo.photo -%}
	{%- else -%}
		{%- assign manophoto = "ninguem.jpg" -%}
	{%- endif -%}

	<div class="media frescurinhas">
		<div class="pull-left">
			<a href="{{ manolink }}">
				<img class="media-object img-circle" src="/pessoas/{{ manophoto }}" alt="{{ manolo.name | append: " é supimpa :)" }}">
			</a>
		</div>
		<div class="media-body">
			<h4 class="media-heading">{{ manolo.name }}</h4>
			<div style="margin-top: -1.5em">
				{{ manolo.content | markdownify }}
			</div>
		</div>
	</div>
{%- endfor -%}
</div>
</div>
