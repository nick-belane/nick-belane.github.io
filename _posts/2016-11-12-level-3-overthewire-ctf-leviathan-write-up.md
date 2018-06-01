---
layout: default
title: Level 3 - Reutilizando o velho ltrace [OverTheWire CTF – Leviathan] write-up
date: 2016-11-12 18:55
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/11/02/level-1-overthewire-ctf-leviathan-write-up/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/02/level-1-overthewire-ctf-leviathan-write-up/">Level 1</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/06/level-2-overthewire-ctf-leviathan-write-up/">Level 2</a></li>
</ul>

Dando seguimento a série de write-ups do <a href="http://overthewire.org/wargames/leviathan/">Leviathan</a>, é hora de ownar o Level3.

Esse nível foi bem mais fácil do que eu esperava, até mais fácil que o nível anterior.

Logado como leviathan3 no ssh, comecei a trabalhar.

```leviathan3@melinda:~$ ls -l
total 12
-r-sr-x--- 1 leviathan4 leviathan3 9962 Mar 21 2015 level3
leviathan3@melinda:~$ ./level3
Enter the passwordbla
bzzzzzzzzap. WRONG```

Como pode-se ver, temos mais um executável suid que nos pergunta por uma senha e bzzzzzzap, verifica se ela está correta.

Ínicio a análise usando o velho <em>ltrace</em>.

```leviathan3@melinda:~$ ltrace ./level3
__libc_start_main(0x80485fe, 1, 0xffffd7a4, 0x80486d0 unfinished ...
strcmp(h0no33, kakaka) = -1
printf(Enter the password) = 20
fgets(Enter the passwordbla
bla\n, 256, 0xf7fcac20) = 0xffffd59c
strcmp(bla\n, snlprintf\n) = -1
puts(bzzzzzzzzap. WRONGbzzzzzzzzap. WRONG
) = 19
+++ exited (status 0) +++```

Parece que a senha correta é <strong>snlprintf</strong>. Mas que comparação é aquela de "hono33" com "kakaka"?

Anyway, executei o programa e dessa vez inseri a senha correta para ver o que acontecia.

```leviathan3@melinda:~$ ./level3
Enter the passwordsnlprintf
[You've got shell]!
$ whoami
leviathan4
$ cat /etc/leviathan_pass/leviathan4
dGhlbWF0cml4aGFzeW91
$```

Aí está a senha do leviathan4. Ainda achei estranho pelo nível poder ser resolvido de forma idêntica ao level1, mas sei lá, né? Não consegui me aprofundar e saber o que era o primeiro strcmp, mas acredito que seja apenas porra nenhuma.


