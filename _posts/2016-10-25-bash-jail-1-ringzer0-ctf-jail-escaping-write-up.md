---
layout: default
title: Bash Jail 1 [RingZer0 CTF - Jail Escaping] write-up
date: 2016-10-25 20:43
author: brennords
comments: true
categories: [CTFs]
---
Link: https://ringzer0team.com/challenges/218 (necessário login).

Esse chall é bem simples, dá pra se resolver com um conhecimento basicão sobre bash.

Depois de acessar o ssh (ssh ringzer0team.com -p 1016 -l level1) pela primeira vez, fui recebido pela seguinte tela:

```
BASH Jail Level 1:
Current user is uid=1000(level1) gid=1000(level1) groups=1000(level1)

Flag is located at /home/level1/flag.txt

Challenge bash code:
-----------------------------

while :
do
        echo Your input:
        read input
        output=`$input`
done

-----------------------------
Your input:
```

O que se sabe a partir daí?

Sabemos que tempos permissões de level1 (irrelevante nesse caso), que a amada flag está em /home/level1/flag.txt e, o mais importante, estamos limitados e sendo desafiados pelo código em bash acima.

Lendo o código, deu para perceber que a primeira linha do loop fica responsável por escrever na tela "Your input:". A segunda lê o que digitamos e a terceira executa o que foi digitado como um `comando` do linux e salva a saída do comando na variável "output".

Se bateu a curiosidade, procure material sobre bash script. Pode ser útil e é fácil de aprender (falo isso até como um lembrete para mim mesmo).

Se tentarmos inserir qualquer comando linux, tipo um <em>cat /home/level1/flag.txt</em>, não receberemos o resultado do comando na nossa tela porque, como você já deve imaginar, o resultado é guardado dentro da variável output e de lá não sai para nenhum outro lugar.

Então como ler o arquivo flag.txt?

Após uma reflexão e a inserção de alguns comandos, percebi que o script perde o controle e deixa o sistema mostrar a mensagem de erro apropriada caso eu inserisse algo que o sistema não reconhecesse:

```
Your input:
leia flag.txt
/home/level1/prompt.sh: line 24: leia: command not found
```

Pensando agora sobre a resolução que acabei chegando, foi meio viajado da minha parte, mas na hora imaginei algo assim: "como fazer o sistema ler e me dizer o conteúdo do arquivo flag.txt sem guardar isso em output?"

Instantaneamente, mas logo sem esperança, já que não faria sentido um arquivo .txt ter permissão de execução, tentei usar <em>./flag.txt</em>.

```
Your input:
./flag.txt
/home/level1/prompt.sh: line 24: ./flag.txt: Permission denied
```

Foi uma ideia bem burra, eu sei. Mas graças a ela foi que lembrei da existência do comando <em>source</em>. Esse comando lê um arquivo como entrada e tenta executar cada linha do arquivo como se fosse um comando. A ideia dessa vez era fazer com que source lesse o arquivo flag.txt, encontrasse e tentasse executar os caracteres da flag como um comando, e então ver o sistema retornar um erro do tipo "FLAG-SOUAFLAG": command not found.

E foi isso mesmo que aconteceu:

```
Your input:
source /home/level1/flag.txt
/home/level1/flag.txt: line 1: FLAG-U2VtIGZsYWdzIGdyYXR1aXRhcw==: command not found
```

Depois de ler write-ups da galera, vi que esse possivelmente é o jeito mais merda de resolver esse chall.Principalmente depois de encontrar um paper (acho que foi na phrack, mas nesse momento procurei em vários lugares e não conseguir achar um link e nem o arquivo no meu pc :( ) falando sobre como quebrar várias jails for fun and profit. Eu poderia ter executado facilmente uma shell no sistema e lido a flag (até consegui executa-la, mas, por noobice, não conseguir imprimir na tela o conteúdo do arquivo). Vamos apenas fazer de conta que pensei fora da caixa e tive uma sacada genial.

Acabei avançando ainda mais nesses bash jail e aprendendo bash ao mesmo tempo, qualquer hora organizo as anotações e jogo outro write up aqui.
