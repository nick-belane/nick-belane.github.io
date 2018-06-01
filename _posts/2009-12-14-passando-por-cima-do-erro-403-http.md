---
layout: default
title: Passando por cima do erro 403 (HTTP)
date: 2009-12-14 04:02
author: brennords
comments: true
categories: [Hacking]
---
Algumas vezes, tentamos acessar algum arquivo em um determinado site, e nos deparamos com uma página com os dizeres: <strong>403 Forbidden</strong>

Pra quem não sabe o que é o Wikipedia explica: <strong><a href="http://en.wikipedia.org/wiki/HTTP_403" target="_blank">http://en.wikipedia.org/wiki/HTTP_403</a></strong>

O que fazer então? O servidor está nos dizendo que aquilo é proibido, ele está negando o acesso, mas quem é o servidor pra nos proibir de fazer algo?

Com um truque besta (meio antigo e não muito funcional), se pode em alguns casos, passar por cima dessa proibição.

<!--more-->

Acho que alguns sabem que "<strong>/../</strong>" faz com que se volte um diretório, e que "<strong>/./</strong>" não faz nada a não ser continuar no diretório atual.

Bom, alguns servidores restringem o acesso a uma URL especifica, ou seja, ele restringe <strong>www.exemplo.com.br/secret/passwords.txt, </strong>mas não restringe o acesso a URL <strong>www.exemplo.com.br/./secret/passwords.txt</strong>, por que a segunda URL é diferente, mas vai apontar para o mesmo lugar, dando assim acesso ao arquivo, bastou usar o "<strong>/./</strong>" e o servidor foi enganado.

Lembre-se que nem todos servidores usam a restrição por URL.

Agora quando o servidor dizer que você não pode, você pode olhar para a cara dele e dizer: "<strong>Sim eu posso</strong>".

Mesmo com o mês de novembro em branco (passei a maioria do tempo estudando), e eu só postar quase na metade de dezembro, ainda recebo visitas diárias :D, não é nada que se compare as visitas que o site da Google recebe por dia, mas isso aqui também não anda às moscas (: .

Enquanto escrevo o post tô ouvindo <a href="http://www.youtube.com/watch?v=SmJxtgmsqAE" target="_blank"><strong>Green Day</strong></a>, mas sei que ela não mereçe.
