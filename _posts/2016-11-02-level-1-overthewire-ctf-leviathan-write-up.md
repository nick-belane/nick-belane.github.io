---
layout: default
title: Level 1 - Debuggando com o ltrace [OverTheWire CTF – Leviathan] write-up
date: 2016-11-02 13:55
author: brennords
comments: true
categories: [CTFs]
---
Depois de passar rapidamente pelo <a href="https://brenn0.wordpress.com/2016/10/31/level-0-e-1-overthewire-ctf-leviathan-write-ups/">Level 0</a>, pulei no Level 1. Nele, logo após o login, encontrei o seguinte arquivo:

```
leviathan1@melinda:~$ ls -alh
-r-sr-x--- 1 leviathan2 leviathan1 7.4K Nov 14 2014 check
leviathan1@melinda:~$ file check
check: setuid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.24, BuildID[sha1]=0d17ae20f672ebc7d440bb4562277561cc60f2d0, not stripped
```

Nada surpreendente. <strong>check</strong> é um executável que roda com privilégios de leviathan2, que é o usuário que queremos ser. Agora é só descobrir o que ele faz e tentar descobrir como se aproveitar.

```
leviathan1@melinda:~$ ./check
password: fucku
Wrong password, Good Bye ...
```

Ok, apenas executar o arquivo não nos diz muita coisa, principalmente se não se sabe a senha correta. É preciso saber o que esse binário faz por debaixo dos panos. Aí entra na jogada o <em>ltrace.</em>

Confesso. Eu não conhecia o <em>ltrace</em>. Só o irmão mais velho dele, o <em>strace</em>. Mas é exatamente por isso que se mete as caras para resolver ctfs: aprender coisas novas o/

Enquanto o strace monitora as chamadas de sistema que um executável faz, o ltrace monitora as chamadas para bibliotecas. Se você clicar <a href="https://sergioprado.org/analisando-aplicacoes-linux-com-strace-e-ltrace/">aqui</a>, vai ganhar uma explicação mais completa.

Então, executando o check com o ltrace de olho no que ele faz:

```
leviathan1@melinda:~$ ltrace ./check
__libc_start_main(0x804852d, 1, 0xffffd684, 0x80485f0 unfinished ...
printf(password: ) = 10
getchar(0x8048680, 47, 0x804a000, 0x8048642password: fucku
) = 102
getchar(0x8048680, 47, 0x804a000, 0x8048642) = 117
getchar(0x8048680, 47, 0x804a000, 0x8048642) = 99
strcmp(fuc, sex) = -1
puts(Wrong password, Good Bye ...Wrong password, Good Bye ...
) = 29
+++ exited (status 0) +++
```

Mas olhe só. Ele pega a string que inserimos e compara os três primeiros caracteres com a string "sex". A mágica do ltrace é que pode se ver ele chamando printf, getchar, strcmp, puts... Todas as funções que fazem parte da festa e seus respectivos parametros e valores de retorno.

Agora é executar o check para ver o que ele fará se inserirmos a senha correta.

```
leviathan1@melinda:~$ ltrace ./check
__libc_start_main(0x804852d, 1, 0xffffd684, 0x80485f0 unfinished ...
printf(password: ) = 10
getchar(0x8048680, 47, 0x804a000, 0x8048642password: sex
) = 115
getchar(0x8048680, 47, 0x804a000, 0x8048642) = 101
getchar(0x8048680, 47, 0x804a000, 0x8048642) = 120
strcmp(sex, sex) = 0
system(/bin/sh$
```

Nada mal, system executa uma shell se strcmp se alegrar.

Hora de abandonar o ltrace e pegar a senha do próximo nível.

```
leviathan1@melinda:~$ ./check
password: sex
$ whoami
leviathan2
$ cat /etc/leviathan_pass/leviathan2
ZmxhZyA6KQ==
```

Dia dos finados é dia de matar flag ;)
