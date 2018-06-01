---
layout: default
title: GHOST, a vulnerabilidade que de fantasma não tem nada
date: 2015-01-30 18:11
author: brennords
comments: true
categories: [Hacking]
---
Durante a semana, no dia 28 de janeiro para ser mais exato, vi minha linha do tempo no twitter ser infestada por gente falando sobre um tal de <strong>GHOST</strong>.

Não demorou muito tempo para que eu percebesse que não se falava sobre uma <a href="https://pt.wikipedia.org/wiki/Ghost_%28banda%29" target="_blank">banda sueca de heavy rock</a> ou do <a href="https://pt.wikipedia.org/wiki/Ghost" target="_blank">filme preferido da minha vó,</a> e sim de mais uma nova <a href="https://community.qualys.com/blogs/laws-of-vulnerabilities/2015/01/27/the-ghost-vulnerability" target="_blank">séria vulnerabilidade</a> que dessa vez afeta praticamente todos os sistemas Linux.

Então, após dar um tempo para que mais informações detalhadas surgissem sobre a tal, resolvi escrever sobre (assim como fiz com o bug <a href="https://brenn0.wordpress.com/2014/04/09/o-famoso-e-aterrorizante-bug-heartbleed/" target="_blank">Hearthbleed</a>).

<a href="https://brenn0.files.wordpress.com/2015/01/ghost-vulnerability-620x350.jpg"><img class="wp-image-1109 size-full" src="https://brenn0.files.wordpress.com/2015/01/ghost-vulnerability-620x350.jpg" alt="ghost-vulnerability-620x350" width="620" height="350" /></a>

 <!--more-->

GHOST? Que porra é essa?

<strong>GHOST</strong> foi o nome dado para uma crítica vulnerabilidade encontrada na biblioteca <a href="https://www.gnu.org/software/libc/" target="_blank">glibc</a> por pesquisadores da empresa de segurança <a href="https://www.qualys.com/" target="_blank">Qualys</a>. A mesma ganhou esse nome bonitinho por ser desencadeada pelas funções <em><strong>G</strong>et<strong>HOST</strong>byname*( ). </em>

E por que tanto drama?

A biblioteca glibc é parte do coração dos sistemas linux, logo, a quantidade de sistemas usando versões vulneráveis é enorme. E ainda pior, pois a falha pode ser usada para a execução local e <strong>remota</strong> de código. Teoricamente, qualquer aplicativo, que use uma das funções <em>gethostbyname</em> para fazer a conversão de um nome de host para um endereço ip, é um alvo em potencial. A vulnerabilidade consiste em um belo <a href="https://pt.wikipedia.org/wiki/Heap_overflow" target="_blank"><em>heap overflow</em></a> na espera de criatividade para ser explorado.

Essa vulnerabilidade existe desde da versão 2.2 da glibc que foi lançada em 2000 (!) e até foi corrigida em 2013, mas como não foi reconhecida como ameaça de segurança, deixou com que muitos sistemas continuassem a usar versões esculhambadas.

Uma explicação mais técnica

A vulnerabilidade GHOST é explorada por meio das funções <em>gethostbyname*( ). </em>O código vulnerável é chamado quando uma aplicação que faz uso da biblioteca glibc quer fazer uma resolução de DNS (algo bem comum).

Na prática o ataque ocorreria desta forma: o atacante envia um argumento malicioso como hostname, o aplicativo passa o argumento para a função vulnerável da glibc processar e é nessa hora que acontece um buffer overflow que pode finalizar o aplicativo ou dar ao atacante a possibilidade de executar código arbitrário.

Mas há algumas limitações para que a falha seja explorada. Por exemplo, como o argumento explorado é o hostname, o primeiro caractere precisa ser um número e o último não pode ser um ponto (.) e o hostname inteiro só pode ser feito de números e pontos, o que vai minar bastante a liberdade do atacante.

Para uma analise melhor e mais completa, deixo o link da própria Qualys (em english) para os amantes de bugs e exploração de software: <a href="https://www.qualys.com/research/security-advisories/GHOST-CVE-2015-0235.txt" target="_blank">https://www.qualys.com/research/security-advisories/GHOST-CVE-2015-0235.txt</a>

Testando seu sistema

Fazer isso é simples. Compile e rode o código encontrado abaixo que roubei da analíse técnica da <a href="https://www.qualys.com/research/security-advisories/GHOST-CVE-2015-0235.txt" target="_blank">Qualys</a>:

```#include netdb.h&amp;gt;
#include stdio.h&amp;gt;
#include stdlib.h&amp;gt;
#include string.h&amp;gt;
#include errno.h&amp;gt;

#define CANARY &amp;quot;in_the_coal_mine&amp;quot;

struct {
  char buffer[1024];
  char canary[sizeof(CANARY)];
} temp = { &amp;quot;buffer&amp;quot;, CANARY };

int main(void) {
  struct hostent resbuf;
  struct hostent *result;
  int herrno;
  int retval;

  /*** strlen (name) = size_needed - sizeof (*host_addr) - sizeof (*h_addr_ptrs) - 1; ***/
  size_t len = sizeof(temp.buffer) - 16*sizeof(unsigned char) - 2*sizeof(char *) - 1;
  char name[sizeof(temp.buffer)];
  memset(name, '0', len);
  name[len] = '&#092;&#048;';

  retval = gethostbyname_r(name, &amp;amp;resbuf, temp.buffer, sizeof(temp.buffer), &amp;amp;result, &amp;amp;herrno);

  if (strcmp(temp.canary, CANARY) != 0) {
    puts(&amp;quot;vulnerable&amp;quot;);
    exit(EXIT_SUCCESS);
  }
  if (retval == ERANGE) {
    puts(&amp;quot;not vulnerable&amp;quot;);
    exit(EXIT_SUCCESS);
  }
  puts(&amp;quot;should not happen&amp;quot;);
  exit(EXIT_FAILURE);
}
```

O real perigo

A Qualys tem seu <a href="https://pt.wikipedia.org/wiki/Prova_de_conceito" target="_blank">PoC</a> que explora o Exim Mail e prometeu um módulo para o Metasploit em breve e tentativas de explorar o bug <em>in the wild</em> estão sendo detectadas. Mas depois de todo burburinho sobre o bug, muitos estão concluindo que esta falha não é tão perigosa quanto parece e não é uma nova HeartBleed.

Nem todos aplicativos aceitam esse tipo de dado (o hostname) como uma entrada controlada pelo usuário e as limitações que citei acima também são outro empecilho. Alguns aplicativos também checam o argumento antes de passa-lo para a função vulnerável, o que também impede a exploração. Ainda é sabido que as funções vulneráveis estão obsoletas porque não são usadas para fazer a tradução de nomes de domínio para endereços IPv6 (novos aplicativos usam a função <em>getaddrinfo( )</em>, que dá suporte ao IPv6). E, claro, o patch para a correção da falha já existe e a solução é atualizar o sistema.

Então, caso já não esteja, atualize seu sistema, o reinicie (ou os aplicativos continuarão a usar a biblioteca antiga) e seja feliz.

Referências para esta postagem:

<ul>
    <li style="text-align:justify;"><a href="https://community.qualys.com/blogs/laws-of-vulnerabilities/2015/01/27/the-ghost-vulnerability" target="_blank">https://community.qualys.com/blogs/laws-of-vulnerabilities/2015/01/27/the-ghost-vulnerability</a></li>
    <li style="text-align:justify;"><a href="http://threatpost.com/of-ghost-glibc-vulnerability-patching-and-exploits/110719" target="_blank">http://threatpost.com/of-ghost-glibc-vulnerability-patching-and-exploits/110719</a></li>
    <li style="text-align:justify;"><a href="https://nakedsecurity.sophos.com/2015/01/29/the-ghost-vulnerability-what-you-need-to-know/?utm_source=feedburner&amp;utm_medium=feed&amp;utm_campaign=Feed%3A+nakedsecurity+%28Naked+Security+-+Sophos%29" target="_blank">https://nakedsecurity.sophos.com/2015/01/29/the-ghost-vulnerability-what-you-need-to-know/?utm_source=feedburner&amp;utm_medium=feed&amp;utm_campaign=Feed%3A+nakedsecurity+%28Naked+Security+-+Sophos%29</a></li>
    <li style="text-align:justify;"><a href="http://blog.trendmicro.com/trendlabs-security-intelligence/not-so-spooky-linux-ghost-vulnerability/" target="_blank">http://blog.trendmicro.com/trendlabs-security-intelligence/not-so-spooky-linux-ghost-vulnerability/</a></li>
    <li style="text-align:justify;"><a href="https://www.centosblog.com/critical-glibc-remote-vulnerability-exploit-ghost-patch-glibc-now/" target="_blank">https://www.centosblog.com/critical-glibc-remote-vulnerability-exploit-ghost-patch-glibc-now/</a></li>
    <li style="text-align:justify;"><a href="http://chargen.matasano.com/chargen/2015/1/27/vulnerability-overview-ghost-cve-2015-0235.html" target="_blank">http://chargen.matasano.com/chargen/2015/1/27/vulnerability-overview-ghost-cve-2015-0235.html</a></li>
    <li style="text-align:justify;"><a href="http://www.openwall.com/lists/oss-security/2015/01/27/9" target="_blank">http://www.openwall.com/lists/oss-security/2015/01/27/9</a></li>
    <li style="text-align:justify;"><a href="http://blog.mythic-beasts.com/2015/01/28/glibc-0-day-exploit-ghost-how-were-handling-it/" target="_blank">http://blog.mythic-beasts.com/2015/01/28/glibc-0-day-exploit-ghost-how-were-handling-it/</a></li>
</ul>
