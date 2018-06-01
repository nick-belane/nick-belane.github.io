---
layout: default
title: Rodando COSI no Linux via Wine
date: 2011-07-24 22:28
author: brennords
comments: true
categories: [Hacking]
---
Eu pretendia escrever um post falando sobre a minha demora pra pôr atualizações aqui mas não tô muito afim pra falar a verdade, então resolvi postar uma coisa rapidinha aqui: como fazer o <strong>COSI</strong> rodar via wine.

<p style="text-align:center;"><img src="http://brenn0.files.wordpress.com/2011/07/255245.jpg" alt="" width="247" height="344" />

COSI é uma ferramenta de compressão e descompressão de que tipo de arquivos especificamente eu não sei, mas só sei que ela é muito usada pra descomprimir jogos de Playstation 1 ripados, e como eu gosto de jogar aqueles jogos velhos e lembrar da época em que eu era apenas uma criança <del>sociopata</del> inocente que ficava feliz em ter novos cds, uso essa ferramenta sempre. O grande problema agora que aparentemente eu migrei pra o Ubuntu de vez, é que eu não encontrei ferramenta similar que rodasse no meu linux. Mas antes de desistir e procurar por algum pornô barato na net resolvi persistir. Primeiro eu tentei fazer o que me parecia mais lógico: rodar essa porra via wine claro, mas ele não abria de maneira alguma, parecia que o pornô seria minha única opção da noite...

<!--more-->

Mas como desistir é pra bichas resolvi batalhar mais, mandei o comando "wine COSI" no meu terminal e a mensagem de erro que ele me retornou foi essa aqui:



```

err:module:import_dll Library MFC42.DLL (which is needed by L&amp;quot;Z:/home/fuck/Área de Trabalho/Nova Pasta/Epsxe 1.7/bios/Die_Hard_Trilogy_2__SLUS-01015___U_/cosi.exe&amp;quot;) not found
err:module:LdrInitializeThunk Main exe initialization for L&amp;quot;Z:/home/fuck/Área de Trabalho/Nova Pasta/Epsxe 1.7/bios/Die_Hard_Trilogy_2__SLUS-01015___U_/cosi.exe&amp;quot; failed, status c0000135

```

O wine praticamente jogou isso na minha bela face: "A dll meu irmão, eu preciso da dll pra rodar essa porra aqui, se eu não tiver a MFC42 aqui do meu lado não vai rolar caralho nenhum".

Ignorante todo o filho da puta...

Depois de descobrir isso, só foi pôr no google MFC42.dll e fazer o download, mas como eu sou uma boa pessoa deixo o link aqui de mão beijada: <a href="http://www.dll-files.com/dllindex/dll-files.shtml?mfc42" target="_blank">http://www.dll-files.com/dllindex/dll-files.shtml?mfc42</a>

Coloquei a bendita na pasta do COSI e consegui descomprimir meu Die Hard 2.

Só faltava uma cerveja aqui pra comemorar, mas agente vai no que tem mermo: uma panela de macarrão e excesso de catchup, parece bem apetitoso (:

Tá bom, já gastei tempo de mais aqui. Posto mais quando estiver afim e sem ressaca.

Mostre respeito e comente logo aí embaixo antes de começar a jogar.

:*
