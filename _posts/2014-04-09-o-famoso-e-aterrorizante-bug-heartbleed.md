---
layout: default
title: O famoso e aterrorizante bug Heartbleed
date: 2014-04-09 17:39
author: brennords
comments: true
categories: [Hacking]
---
Muito tem se falado ultimamente sobre esse bug. Mas é realmente uma coisa tão séria assim? Sim, bastante.

Vi ontem (08/04/2014) links com palavras do tipo: "Falha grave", "OpenSSL comprometido", "Afetar toda a internet", "Dados sigilosos vulneráveis" e "Heartbleed" infestarem meu feed de notícias no facebook e twitter.

Extremamente curioso, abri umas 5 abas e fui ler sobre o tal bug sério.

Mas enfim, o que é esse bug e o que ele significa?

<a href="http://brenn0.files.wordpress.com/2014/04/heartbleed.png"><img class="aligncenter wp-image-747 size-full" src="http://brenn0.files.wordpress.com/2014/04/heartbleed.png" alt="heartbleed" width="341" height="413" /></a>

<!--more-->

Primeiro deixe-me explicar o que é o software afetado: o <strong>OpenSSL</strong>.

<blockquote>"<span style="color:#252525;">O </span>OpenSSL<span style="color:#252525;"> é uma implementação de </span>código aberto<span style="color:#252525;"> dos protocolos </span><strong>SSL e TLS</strong><span style="color:#252525;">. A biblioteca</span><span style="color:#252525;"> implementa as funções básicas de </span><strong>criptografia </strong><span style="color:#252525;">e disponibiliza várias funções utilitárias." (De acordo com nossos manos do Wikipédia: <a href="http://pt.wikipedia.org/wiki/OpenSSL" target="_blank">http://pt.wikipedia.org/wiki/OpenSSL</a>)</span></blockquote>

Sim, mas e aí?

Esse rapaz é largamente usado para criar conexões seguras usando os protocolos SSL e TLS. Traduzindo para alguns: ele é usado para conectar você e aquelas páginas (como a do seu banco) onde há um cadeadinho e "https" ao invés do "http" antes do endereço. Isso significa que você está (supostamente) usando uma conexão segura e os dados serão criptografados. Essa criptografia é usada para evitar que terceiros interceptem, leiam ou alterem os dados trocados durante a conexão (senhas, dados bancários, emails, qualquer coisa).

E o que "Heartbleed" tem haver com tudo isso?

Heartbleed é o nome de uma extensão do OpenSSL que permite manter a conexão segura entre os dois lados "viva" sem precisar renegociar a conexão, o que economiza recursos.

O bug foi batizando de "Heartbleed" (algo como "coração sangrando") porque o pedaço de código falho reside nessa extensão.

E por que está sendo levado tão a sério? O que está em jogo caso a falha seja explorada?

Porque as consequências são graves.

<ol style="text-align:justify;">
    <li>Esse bug está a solta há uns 2 anos;</li>
    <li>A popularidade do OpenSSL (cerca de 66% de todos os sites na internet o usam de acordo com a <a href="http://news.netcraft.com/archives/2014/04/02/april-2014-web-server-survey.html" target="_blank">Netcraft</a>). Até a data em que escrevo esse post, importantes sites como yahoo, flickr, steam e <strong>redtube </strong>usam versões vulneráveis;</li>
    <li>A exploração dessa falha não deixa pistas nos logs dos servidores, logo não é possível saber se o servidor foi comprometido.</li>
</ol>

Caso a falha seja explorada, é possível que, com um pouco de paciência, o cara que ataque o servidor obtenha, não só dados como senhas, mas as chaves de criptografia que protegem todo o tráfego e com isso em mãos já era. Com essas chaves, o atacante pode descriptografar qualquer dado já capturado e os FUTUROS dados também. Ou seja: fudeu, né?

E como a falha é explorada?

Se segure aí porque aqui vão um pouco de informações técnicas.

Basicamente, é tudo culpa dessa única linha de código:

```buffer = OPENSSL_malloc(1 + 2 + payload + padding); ```

Como se pode perceber, há memória alocada para o "payload + padding" e o padding é um valor controlado pelo usuário. Como não há uma checagem para essa alocação de dados, o atacante pode forçar o OpenSSL a ler partes da memória que não deveria.

Após enviar dados de forma maliciosa para a porta 443 do servidor, o atacante receberá de volta uma resposta com 64 kb de dados a mais do que deveria e que, provavelmente, não deveria ter acesso, como nomes de usuários e senhas.

Há esse limite de 64 kb porque é o limite de tamanho para a resposta que o Heartbeat dá. Mas isso não aparenta ser um problema, pois o atacante pode enviar outra requisição maliciosa, receber outros 64 kb, mandar mais uma e receber mais outros 64 kb... E repetir o processo até conseguir o que quer.

Caso se interesse, <a href="https://bitbucket.org/fb1h2s/cve-2014-0160/src/bba16b3eedef0e92bd91fea496b00c92eb515e29/Heartbeat_scanner.py?at=master" target="_blank">nesse link</a> há um script escrito na linguagem Python que verifica se um servidor é vulnerável.

E <a href="http://filippo.io/Heartbleed/" target="_blank">nesse site</a> também é possível testar se um servidor é vulnerável.

Não há uma solução?

Sim. Além de os servidores terem que atualizar suas versões do OpenSSL, é necessário que eles revoguem as chaves comprometidas e distribua novas.

Para os usuários comuns, na maioria das vezes o que resta é mudar as senhas de serviços que foram afetados. O <a href="http://staff.tumblr.com/post/82113034874/urgent-security-update" target="_blank">tumblr</a> e outros serviços já andam alertando seus usuários.

Alguns pensamentos

Deu pra concluir que a falha é realmente séria, certo? Além de dados como senhas, transações bancárias e outras coisas valiosas, um bug sério em uma forma popular de criptografia de dados também põe em risco a privacidade. Sabemos que na internet é difícil conseguir alguma, mas a criptografia é o que (supostamente) ainda nos permite a comunicação segura via internet.

Esse bug a solta há tanto tempo me fez lembrar dos slides da NSA e a GCHQ vazados pelo Edward Snowden que citam os esforços para se quebrar as formas de criptografia usadas na internet.

<a href="http://brenn0.files.wordpress.com/2014/04/nsa-bullrun-1-001.jpg"><img class="wp-image-749 size-full" src="http://brenn0.files.wordpress.com/2014/04/nsa-bullrun-1-001.jpg" alt="NSA Bullrun 1" width="460" height="388" /></a>

Mas informações sobre isso nesse <a href="http://www.theguardian.com/world/2013/sep/05/nsa-gchq-encryption-codes-security" target="_blank">link</a>.

A rapaziada do tor recomenda que, caso realmente precise de anonimato ou privacidade, fique fora da internet por uns dias até as coisas se acalmarem: <a href="https://blog.torproject.org/blog/openssl-bug-cve-2014-0160" target="_blank">https://blog.torproject.org/blog/openssl-bug-cve-2014-0160</a>

E aqui finalizo meu momento "teórico da conspiração", haha.

<strong>UPDATE 12 de abril de 2014</strong>

Sim, como eu e vários outros teorizavam, eles já sabiam dessa falha antes de nós: <a href="http://www.bloomberg.com/news/2014-04-11/nsa-said-to-have-used-heartbleed-bug-exposing-consumers.html" target="_blank">http://www.bloomberg.com/news/2014-04-11/nsa-said-to-have-used-heartbleed-bug-exposing-consumers.html</a>

Fontes consultadas e links com informações mais detalhadas (todos em inglês):

<a href="http://heartbleed.com/" target="_blank">http://heartbleed.com/</a>

<a href="http://www.garage4hackers.com/entry.php?b=2551" target="_blank">http://www.garage4hackers.com/entry.php?b=2551</a>

<a href="https://github.com/musalbas/heartbleed-masstest/blob/master/top1000.txt" target="_blank">https://github.com/musalbas/heartbleed-masstest/blob/master/top1000.txt</a>

<a href="http://techcrunch.com/2014/04/08/openssl-heartbleed-bug-leaves-much-of-the-internet-at-risk/" target="_blank">http://techcrunch.com/2014/04/08/openssl-heartbleed-bug-leaves-much-of-the-internet-at-risk/</a>

<a href="http://www.troyhunt.com/2014/04/everything-you-need-to-know-about.html" target="_blank">http://www.troyhunt.com/2014/04/everything-you-need-to-know-about.html</a>


