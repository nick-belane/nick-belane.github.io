---
layout: default
title: Level 4 01101100101 [OverTheWire CTF – Leviathan] write-up
date: 2016-11-13 23:34
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/10/31/level-0-e-1-overthewire-ctf-leviathan-write-ups/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/02/level-1-overthewire-ctf-leviathan-write-up/">Level 1</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/06/level-2-overthewire-ctf-leviathan-write-up/">Level 2</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/12/level-3-overthewire-ctf-leviathan-write-up/">Level 3</a></li>
</ul>

Depois do level3 e seu nível de desafio não muito impressionante, cheguei no 4 esperando algo pior. Mas o <strong>leviathan4</strong> conseguiu ser ainda mais simples.

Primeira coisa a se fazer, após ter logado no ssh, foi listar os arquivos do meu diretório home:

```leviathan4@melinda:~$ ls -alh
total 24K
drwxr-xr-x   3 root root       4.0K Nov 14  2014 .
drwxr-xr-x 172 root root       4.0K Jul 10 14:12 ..
-rw-r--r--   1 root root        220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root root       3.6K Apr  9  2014 .bashrc
-rw-r--r--   1 root root        675 Apr  9  2014 .profile
dr-xr-x---   2 root leviathan4 4.0K Nov 14  2014 .trash
```

E na pasta .trash se encontra o executável alvo chamado <strong>bin</strong>.

```leviathan4@melinda:~$ ls -alh .trash/
total 16K
dr-xr-x--- 2 root       leviathan4 4.0K Nov 14  2014 .
drwxr-xr-x 3 root       root       4.0K Nov 14  2014 ..
-r-sr-x--- 1 leviathan5 leviathan4 7.3K Nov 14  2014 bin
```

Tanto o nome quanto a execução do arquivo bin são bem indicativos do que ele faz.

```leviathan4@melinda:~$ ./.trash/bin
01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010
```

Seria só isso? Ele me dá a flag em binário? Assim de graça?
Só por pura desconfiança, o executei com o <em>ltrace</em> para sacar o que rolava.

```leviathan4@melinda:~$ ltrace ./.trash/bin
__libc_start_main(0x80484cd, 1, 0xffffd784, 0x80485c0 unfinished ...&amp;gt;
fopen(/etc/leviathan_pass/leviathan5, r)     = 0
+++ exited (status 255) +++```

Mais claro e simples do que isso não dá pra ficar.
Agora foi só usar esse <a href="http://string-functions.com/binary-string.aspx">site</a>, converter os bin para str e ganhar a senha do próximo nível.
