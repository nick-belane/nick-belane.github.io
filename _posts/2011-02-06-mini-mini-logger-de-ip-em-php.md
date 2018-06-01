---
layout: default
title: Mini-Mini logger de IP em PHP
date: 2011-02-06 01:32
author: brennords
comments: true
categories: [Hacking]
---
Primeira vez que eu posto algum código escrito em PHP aqui, não é nada super especial, eu criei ele pra incorporar a outro código, como não vi nenhum código prontinho e comentado em nenhum lugar, resolvi trazer pra cá.

Como o título do post já diz, é um mini-mini (põe mini nisso) logger de IP, ou seja, ele pega o IP do bandido que visita a página, e salva dentro de um arquivo de texto. Eu o usei num problema onde não se tinha acesso ao banco de dados, e queria registrar uns endereços para os meus propósitos, que não convem contar aqui (: .

Vou deixar ele bem comentadinho, afinal são só 4 linhas de código:

```
?php
$ip = getenv(REMOTE_ADDR);    // passa para a variável $ip o ip do bandido
$handle = fopen('ips.txt', 'a') ;   // Abre o arquivo ips.txt para escrita 'a'
fwrite($handle, \n . $ip . \n) ;  // Escreve o conteúdo da variável $ip no arquivo
fclose($handle) ;   // Fecha o arquivo
?
```

Reparem que o código não sabe o que é controle de erros, mas esse código é mais um rascunho do que tudo. Escrevi só pra alguns testes e resolvi postar pra tentar manter minha promessa de posts semanais.

Minha estratégia agora é pegar o mais interessante de cada semana e postar aqui. Bom, eu confesso que esse código não foi o que mais me intrigou durante a semana, mas eu só pensei nessa estratégia no fim dessa que já acabou...

Enfim, é isso (:

Sem falar que amanhã volta as aulas, novatas ai vou eu o/
