---
layout: default
title: Level 5 Abusando links simbólicos [OverTheWire CTF – Leviathan] write-up
date: 2016-11-16 19:29
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/10/31/level-0-e-1-overthewire-ctf-leviathan-write-ups/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/02/level-1-overthewire-ctf-leviathan-write-up/">Level 1</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/06/level-2-overthewire-ctf-leviathan-write-up/">Level 2</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/12/level-3-overthewire-ctf-leviathan-write-up/">Level 3</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/13/level-4-overthewire-ctf-leviathan-write-up/">Level 4</a></li>
</ul>

<blockquote>Com o evoluir dos write-ups, vou começando a ficar mais breve. Como, por exemplo, a essa altura nem precisa falar mais que estou logado como leviathan5 no servidor ssh, certo?

Se chegou aqui de para-quedas e voar em alguma coisa, volte ao level0 ;)</blockquote>

```leviathan5@melinda:~$ ls -alh
total 28K
drwxr-xr-x   2 root       root       4.0K Nov 14  2014 .
drwxr-xr-x 172 root       root       4.0K Jul 10 14:12 ..
-rw-r--r--   1 root       root        220 Apr  9  2014 .bash_logout
-rw-r--r--   1 root       root       3.6K Apr  9  2014 .bashrc
-rw-r--r--   1 root       root        675 Apr  9  2014 .profile
-r-sr-x---   1 leviathan6 leviathan5 7.5K Nov 14  2014 leviathan5```

<strong>leviathan5</strong> será o explorado da vez.

```leviathan5@melinda:~$ ./leviathan5
Cannot find /tmp/file.log```

Ok, se for para agradar, eu crio o arquivo <strong>file.log</strong> em <strong>/tmp</strong>.

```leviathan5@melinda:~$ echo fodase /tmp/file.log
leviathan5@melinda:~$ ./leviathan5
fodase```

Então leviathan5 lê o arquivo /tmp/file.log e me mostra o conteúdo?
Só ver o que ele faz por fora impossibilita a criação de teorias especificas. Hora de usar o <em>ltrace</em> (amigo de todas as horas e challs do leviathan) e descobrir o que o programa faz por baixo dos panos.

```leviathan5@melinda:~$ ltrace ./leviathan5
__libc_start_main(0x80485ed, 1, 0xffffd794, 0x8048690 unfinished ...&amp;gt;
fopen(/tmp/file.log, r)                      = 0
puts(Cannot find /tmp/file.logCannot find /tmp/file.log
)                = 26
exit(-1 no return ...&amp;gt;
+++ exited (status 255) +++
```

Mas eu não tinha acabado de criar essa porra?
Hora de tentar de novo.

```leviathan5@melinda:~$ echo fodase /tmp/file.log
leviathan5@melinda:~$ ltrace ./leviathan5                                       __libc_start_main(0x80485ed, 1, 0xffffd794, 0x8048690 unfinished ...&amp;gt;
fopen(/tmp/file.log, r)                      = 0x804b008
fgetc(0x804b008)                                 = 'f'
feof(0x804b008)                                  = 0
putchar(102, 0x8048720, 0xffffd79c, 0xf7e5619d)  = 102
fgetc(0x804b008)                                 = 'o'
feof(0x804b008)                                  = 0
putchar(111, 0x8048720, 0xffffd79c, 0xf7e5619d)  = 111
fgetc(0x804b008)                                 = 'd'
feof(0x804b008)                                  = 0
putchar(100, 0x8048720, 0xffffd79c, 0xf7e5619d)  = 100
fgetc(0x804b008)                                 = 'a'
feof(0x804b008)                                  = 0
putchar(97, 0x8048720, 0xffffd79c, 0xf7e5619d)   = 97
fgetc(0x804b008)                                 = 's'
feof(0x804b008)                                  = 0
putchar(115, 0x8048720, 0xffffd79c, 0xf7e5619d)  = 115
fgetc(0x804b008)                                 = 'e'
feof(0x804b008)                                  = 0
putchar(101, 0x8048720, 0xffffd79c, 0xf7e5619d)  = 101
fgetc(0x804b008)                                 = '\n'
feof(0x804b008)                                  = 0
putchar(10, 0x8048720, 0xffffd79c, 0xf7e5619dfodase
)   = 10
fgetc(0x804b008)                                 = '\377'
feof(0x804b008)                                  = 1
fclose(0x804b008)                                = 0
getuid()                                         = 12005
setuid(12005)                                    = 0
unlink(/tmp/file.log)                          = 0
+++ exited (status 0) +++
```

Tudo certo, tudo ok. A única coisa que só acabei percebendo nesse momento foi que, após ler e imprimir o conteúdo de file.log, ele o exclui. Mas ele não chama <a href="http://www.uniriotec.br/~morganna/guia/libc/cs_unlink.html">unlink </a>sem antes <a href="http://man7.org/linux/man-pages/man2/getuid.2.html">pegar meu uid real</a> (o de leviathan5). Essa última parte fez minha criatividade trabalhar. E se file.log for um <a href="https://www.vivaolinux.com.br/dica/Link-simbolico-e-hardlink">link simbólico</a> para <strong>/etc/leviathan_pass/leviathan6</strong>? Esta seria uma boa razão para pegar meu uid antes de executar unlink, porque assim o arquivo seria lido mas não excluído, afinal, antes de chamar <a href="http://manpages.ubuntu.com/manpages/trusty/pt/man2/setuid.2.html">setuid</a>, eu ainda tenho permissões de leviathan6.

Só para constar, não tenho total certeza sobre o arquivo ser excluído no fim se o programa não pegasse meu uid real (não sei como os caras do over the wire lidam com as possibilidades de alguém tentar fazer merda no ctf deles), mas esta porra faz sentido.

Hora de testar o link simbólico e ver se essa flag vem.

```leviathan5@melinda:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
leviathan5@melinda:~$ ./leviathan5
ZW1pbmVt
```


