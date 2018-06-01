---
layout: default
title: Escondendo strings e dificultando a análise de um binário [Parte 1]
date: 2017-06-20 18:17
author: brennords
comments: true
categories: [Hacking]
---
Em um belo e aleatório dia, resolvi que iria criar desafios para um CTF inexistente. Sem grandes pretensões. Tinha apenas a intenção de aprender como resolver os desafios criados por outros seres, já que tentar recriar os mecanismos de segurança que se quer burlar pode ser uma ótima forma de aprender a alcançar tal feito.

<img class="wp-image-1477 size-full" src="https://brenn0.files.wordpress.com/2017/06/arcane_bullshit.png" alt="" width="517" height="245" />

Meu objetivo inicial foi criar um leve binário no linux que recebesse uma senha como entrada e, se a senha estivesse correta, imprimisse a flag.

O primeiro código que escrevi foi esse:

<script src="https://gist.github.com/anonymous/57a15666d500b7185809063c27bdf075.js"></script>

Bem simples, não?

Ao compilar e rodar, tudo funcionou como deveria:

```
brenno@budweiser:~/Docs/ctf/brenn0$ gcc chall_versao0.c -o chall_versao0
brenno@budweiser:~/Docs/ctf/brenn0$ ./chall_versao0
Uso: ./chall_versao0 [senha]
brenno@budweiser:~/Docs/ctf/brenn0$ ./chall_versao0 123
Sai daqui, porra!
brenno@budweiser:~/Docs/ctf/brenn0$ ./chall_versao0 bbb
Sai daqui, porra!
brenno@budweiser:~/Docs/ctf/brenn0$ ./chall_versao0 666
Tome sua flag: CTF{UAL}
```

A intenção aqui é fazer com que o ser faça a engenharia reversa do binário, abra o gdb, sete uns breakpoints, descubra o valor da senha ou até simplesmente faça um jmp safado e imprima a flag na tela.

Mas sabemos como esses hackudos são. Imaginei que a primeira coisa que fariam seria usar o comando <a href="http://www.linfo.org/strings.html">strings</a> em cima do meu binário na busca de uma flag fácil.

```
brenno@budweiser:~/Docs/ctf/brenn0$ strings chall_versao0
/lib64/ld-linux-x86-64.so.2
libc.so.6
puts
__stack_chk_fail
printf
atoi
__libc_start_main
__gmon_start__
GLIBC_2.4
GLIBC_2.2.5
UH-P
CTF{UAL}H
AWAVA
AUATL
[]A\A]A^A_
Uso: %s [senha]
Tome sua flag: %s
Sai daqui, porra!
;*3$
GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609
crtstuff.c
__JCR_LIST__
deregister_tm_clones
__do_global_dtors_aux
[...]
```

E a flag está mesmo disponível de forma fácil. Os que são ainda mais hackers poderiam, caso conhecessem o formato da flag, fazer apenas:

```
brenno@budweiser:~/Docs/ctf/brenn0$ strings chall_versao0 | grep CTF
CTF{UAL}H
```

Não escrevi 24 linhas de código para ver meu chall resolvido em 2 segundos. Isso fez com que eu fosse em busca de formas para esconder melhor a string. Queria algo simples e facilmente quebrável. Logo, ignorei as sugestões de usar criptografia e outros métodos mais 1337 que eu.

Após algumas pesquisas, descobri que o comando string, usado cru, sem opcionais, busca no binário apenas strings maiores que 3. Ou seja, modificando meu código atual para partir a flag em pedaços poderia esconde-la melhor.

<script src="https://gist.github.com/anonymous/5e0a3681c139d41da8bb925da98a5b42.js"></script>

Agora ao compilar e passar o strings o resultado era esse:

```
brenno@budweiser:~/Docs/ctf/brenn0$ gcc chall_versao1.c -o chall_versao1
brenno@budweiser:~/Docs/ctf/brenn0$ strings chall_versao1
[...]
UH-P
AWAVA
AUATL
[]A\A]A^A_
Uso: %s [senha]
Tome sua flag: %s%s%s%s
Sai daqui, porra!
;*3$
GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609
crtstuff.c
__JCR_LIST__
deregister_tm_clones
[...]
```

Sem flag via strings!

Mas depois de ter estudado o strings, me liguei que o hackudo mais hackudão dominador do comando poderia tentar usar sua funcionalidade de forma estendida. Era só passar como argumento <em>-n 2</em> que o comando iria retornar strings maiores que 1.

```
brenno@budweiser:~/Docs/ctf/brenn0$ strings -n2 chall_versao1
ELF
td
[lixo pra caralho]
UH
UH
UH
dH
%(
CT
F{
UA
L}
H
TH
;E
u'H
dH3
%(
f.
AWAVA
AUATL
UH
SI
L)
t 1
H9
[]A\A]A^A_
f.
Uso: %s [senha]
Tome sua flag: %s%s%s%s
Sai daqui, porra!
;4
zR
[mais lixo pra caralho]
completed.7585
```

Tem bem mais porcaria no resultado. Mesmo assim, como nunca deve se duvidar de um hackudo motivado, a flag, mesmo repartida, está visível nas linhas destacadas.

E agora?

Claro que poderia partir a flag caracter por caracter, mas decidi inserir outros caracteres em meio a flag real para confundir (até disfarça a flag no disas de main, no próximo post falo mais sobre) e tentar obrigar o ser a resolver o desafio do jeito que pretendi.

O código ficou assim:

<script src="https://gist.github.com/anonymous/1bb687ad83ff39a7449ffb854753ae10.js"></script>

Ao compilar e tentar pescar algo com strings -n2 teremos:

```
brenno@budweiser:~/Docs/ctf/brenn0$ gcc chall_versao2.c -o chall_versao2
brenno@budweiser:~/Docs/ctf/brenn0$ strings -n2 chall_versao2
ELF
[lixo lixo lixo]
UH
dH
%(
CT
TC
F{
{F
UA
AU
L}
}L
#H
`H
u'H
dH3
%(
AWAVA
AUATL
UH
SI
L)
t 1
H9
[]A\A]A^A_
f.
Uso: %s [senha]
Tome sua flag: %s%s%s%s
Sai daqui, porra!
;4
zR
zR
;*3$
@r
[lixo lixo lixo]
```

Ela continua surgindo, mas é bem mais difícil de reconhece-la. E agora acredito que seja menos custoso tentar traduzi-la nesse meio ao invés de abrir o debugger e começar a hackinagem de verdade.

Esta postagem está se tornando grande demais e, com base nos padrões atuais de leitura e engajamento do usuário médio da world wide web, preciso finaliza-la para evitar problemas de monetização. Deixo para contar em uma eventual segunda parte as técnicas que usei para criar uma versão melhorada desse desafio. Usei umas noobices legais anti-debugging e expandi o aprendizado. <a href="http://twitter.com/brennords">Siga-me</a> e tenha a garantia que não irá perder o próximo post.

E usando essa desculpa cheia de palavras bonitas <del>para justificar a preguiça, </del>finalizo esta linda e glamorosa postagem de técnicas 1337.

Antes que esqueça, aqui vão uns links que me ajudaram a aprender:

<ul>
    <li><a href="https://www.exploit-db.com/papers/13188/">https://www.exploit-db.com/papers/13188/</a></li>
    <li><a href="http://www.ouah.org/linux-anti-debugging.txt">http://www.ouah.org/linux-anti-debugging.txt</a></li>
    <li><a href="http://www.linfo.org/strings.html">http://www.linfo.org/strings.html</a></li>
</ul>
