---
layout: default
title: Level 0 - Usando grep [OverTheWire CTF – Leviathan] write-up
date: 2016-10-31 18:18
author: brennords
comments: true
categories: [CTFs]
---
Depois de finalizar o <a href="http://overthewire.org/wargames/bandit/">Bandit</a> do notório CTF online <a href="http://overthewire.org/about">OverTheWire</a>, me achei preparado para encarar os outros desafios. Pretendo, talvez, fazer write-up para os challs do Bandit, mas eles são tão simples que caberiam em uma postagem de uma única linha (a maioria deles). Logo achei melhor qualquer dia desses fazer um único grande post com a história de como passei de cada nível.

Se quiser uma descrição completa sobre os challs do Leviathan, dê uma checada nesse <a href="http://overthewire.org/wargames/leviathan/">link</a>. Mas, de forma resumida, o que disseram é que: para resolver os desafios não será necessário conhecimento em programação, apenas bom senso e conhecimento sobre alguns comandos básicos *nix.

Era hora de abrir um terminal e começar.

Depois de logar com o user <strong>leviathan0</strong> no servidor ssh <strong>leviathan.labs.overthewire.org, </strong>comecei listando o que havia no meu diretório home:

```
leviathan0@melinda:~$ ls -a
. .. .backup .bash_logout .bashrc .profile
```

Se você usasse apenas <em>ls</em> sem o parametro -a,<em> </em>passaria batido pelos arquivos .ocultos e não veria a pasta <strong>.backup, </strong>a única da lista fora da normalidade.

Já dentro de .backup temos:

```
leviathan0@melinda:~/.backup$ ls -alh
total 140K
drwxr-x--- 2 leviathan1 leviathan0 4.0K Oct 14 14:27 .
drwxr-xr-x 3 root root 4.0K Nov 14 2014 ..
-rw-r----- 1 leviathan1 leviathan0 131K Nov 14 2014 bookmarks.html
```

O arquivo <strong>bookmarks.html </strong>é um arquivo html (duh!) cheio de umas porcarias que nem li. Como ele era a única coisa que existia, a probabilidade do password para o próximo nível estar nele era grande. Daí em diante há várias opções, como por exemplo ler o arquivo inteiro na captura de algo. Mas foi mais fácil fazer assim:

```
leviathan0@melinda:~/.backup$ cat bookmarks.html | grep password
DTA HREF=http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is T3BzLCBuYWRhIGRlIGZsYWdzIGdyYXR1aXRhcw== ADD_DATE=1155384634 LAST_CHARSET=ISO-8859-1 ID=rdf:#$2wIU71password to leviathan1/A
```

Esse começou bem fácil ;)

<strong>Fato irrelevante</strong>: é bem satisfatório jogar e realmente resolver os ctfs do OverTheWire porque há alguns anos atrás (talvez 2011, tô com preguiça de usar a way back machine) eu costumava logar e só apanhar tentanto resolver os challs e colocar meu nome nas páginas html que ficavam liberadas para você escrever a cada desafio resolvido.

<strong>Fato um pouco relevante</strong>: resolvi de última hora que não postaria write-up do level1 junto do level0 (mesmo o 0 sendo bem simples) só por questão de <del>putaria</del> organização ou preguiça de escrever. Escolha a opção que mais agradar.
