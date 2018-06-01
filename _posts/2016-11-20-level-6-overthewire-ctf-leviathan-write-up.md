---
layout: default
title: Level 6 Que tal interpretar um pouco de asm? [OverTheWire CTF – Leviathan] write-up
date: 2016-11-20 00:35
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
    <li><a href="https://brenn0.wordpress.com/2016/11/16/level-5-overthewire-ctf-leviathan-write-up/">Level 5</a></li>
</ul>

Último nível! (Apesar de que quando cheguei nele, achei que o 7 teria algo, mas não tem).

Sem papo-furado, deixe-me começar.

<script src="https://gist.github.com/nick-belane/9ee6aed79c36882f263095fe5b538995.js"></script>

Nada demais. Como sempre, um executável suid para ser explorado. Dessa vez, parecida com outras vezes, ele pede uma senha de 4 dígitos.

4 dígitos como argumento, ok.

```
leviathan6@melinda:~$ ./leviathan6 1234
$ cat /etc/leviathan_pass/leviathan7
ZW1pbmVt
```

E assim, acidentalmente, consegui a flag.

Zoeira. Claro que não foi tão fácil. O resultado da execução acima na realidade foi esse:

```
leviathan6@melinda:~$ ./leviathan6 1234
Wrong
```

Direto e reto.

Suponho que o conjunto de quatro dígitos corretos seja minha senha para a leitura do password do próximo nível. Mas como descobri-lo?
Usei o <em>ltrace</em> para tentar ver se o programa usava strcmp para comparar o que eu inseria com os digitos corretos. Mesmo sabendo que já tinha resolvido dois challs (ou mais, agora não lembro exatamente) com esse truque, não custava nada tentar.

```
leviathan6@melinda:~$ ltrace ./leviathan6 1234
__libc_start_main(0x804850d, 2, 0xffffd794, 0x8048590 unfinished ...&amp;gt;
atoi(0xffffd8c7, 0xffffd794, 0xffffd7a0, 0xf7e5619d) = 1234
puts(WrongWrong
)                               = 6
+++ exited (status 6) +++
```

É, não deu em nada. Ele provavelmente usa um simples <a href="http://www.inf.pucrs.br/flash/cbp/selecao_if.html"><em>if/else</em></a> e nesses casos ltrace não é tão útil.
O que fazer, o que fazer... Lembrei bem que na descrição do ctf dizia que não seriam precisos conhecimentos em programação para resolver os challs, então eu não deveria ter que abrir o gdb e tentar ler assembly. Mas foda-se.

<script src="https://gist.github.com/nick-belane/6c46caa5723e2464c17783873ccd4d12.js"></script>

O comando <em>disas main </em>(<em>disas</em> é a versão curta de <em>disassemble</em>) faz exatamente o que você vê. Mostra o código asm referente a função <em>main</em> (que é a função principal de um programa escrito em C e outras linguagem também, mas não vamos viajar agora falando sobre isso).

Longe (bem looongeee) de mim ser o rei do assembly, mas tentei e consegui entender o que o programa faz. Destaquei umas linhas e agora explicarei sobre as mesmas.

A linha 10 contém a instrução <strong>cmpl $0x2,0x8(%ebp). </strong><em>CMP</em> é uma instrução usada para se comparar dois operandos. Ela basicamente subtrai os dois e isso permite saber se eles são iguais ou não (dentre outras coisas também, mas o objetivo aqui não é dar uma super aula de asm) pois ela seta determinadas <a href="https://en.wikipedia.org/wiki/FLAGS_register">flags</a> da cpu de acordo com o resultado . Ah, e se você observou, a instrução no código é <em>cmpl</em> e não <em>cmp</em>. <a href="https://stackoverflow.com/questions/24118562/the-difference-between-cmpl-and-cmp">A diferença é que cmpl compara valores unsigned</a> (sem sinal, inteiros positivos, saca?).

Na linha 11, há a instrução <strong>je 0x8048545</strong>. Lembra que acabei de falar que <em>CMP</em> seta flags da CPU de acordo com o resultado? Só isso em si não é útil para muita coisa se não houver uma instrução que use o resultado variável dessas flags para tomar decisões. <em>JE</em> é uma dessas (<a href="https://www.tutorialspoint.com/assembly_programming/assembly_conditions.htm">há várias outras</a>). <em>JE jump if equals to zero</em> (se você sabe um pouco de english ajuda muito, porque as instruções são só siglas para palavras), isso significa que ele "salta se for igual a zero", e isso significa que: se a comparação resultar em 0, a flag ZERO vai ser setada para 1, <em>JE</em> verá isso como "ser igual a zero" e irá pular para <strong>main+56</strong>.

Se você achou que não foi uma boa explicação, não está sozinho. Deveria aproveitar <a href="https://savannah.nongnu.org/projects/pgubook/">este link</a> para um dos melhores livros de asm que já encontrei pelas webs. Sem falar que, quando as dúvidas surgem, o google é seu amigo (<a href="https://pt.wikipedia.org/wiki/PRISM_(programa_de_vigil%C3%A2ncia)">ou nem tanto</a>).

Agora voltando ao chall.

Logo que observei esse primeiro salto condicional no código, ficou óbvio que ele era o responsável por checar se a pessoa havia passado os devidos argumentos para o programa. Para sacar isso, nenhuma bruxaria foi necessária. É só perceber o que o programa faz quando não pula para main+56 (linha 19 do código postado). Entre a linha 12 e 18 fica fácil de ler as chamadas para <strong>printf</strong> e logo depois <strong>exit</strong>.

Beleza. Da linha 19 em diante temos o que o programa faz quando alguma coisa é passada como argumento. É a parte que interessa na busca dos 4 dígitos correto.

Destaquei a comparação na linha 24 para mostrar onde eu suspeitei (com um alto grau de certeza porque, porra, só tem mais essa comparação) que o valor passado para o programa seria comparado com o valor correto. Foi bastante óbvio não tanto pelo chamada para a função <a href="http://www.uniriotec.br/~morganna/guia/libc/fc_atoi.html">atoi</a> e as manipulações de dados antes da comparação, e sim pelo <em>jmp</em> condicional que acontece na linha seguinte, a 25.

Mas bora pelas partes do bagulho.

(Foi nesse dia que aprendi esse jeito doido da sintaxe AT&amp;T representar algo que seria mais bonitinho com a sintaxe intel: cmp ESP+0x1c é tão mais intuitivo que cmp 0x1c(%esp).)

A instrução <strong>cmp 0x1c(%esp),%eax</strong> compara o valor localizado no endereço de <strong>ESP+0x1c </strong>com o valor que esteja no <a href="https://pt.wikipedia.org/wiki/Registrador_(inform%C3%A1tica)">registrador</a> <strong>EAX</strong>. Interessante. Logo em seguida há <strong>jne 0x8048575</strong> que saltará para main+104 se a comparação dos valores anteriores não acender a flag ZERO (ou seja, se os valores não forem iguais). Mas o que chama a atenção são as instruções logo abaixo de <em>JNE</em>. As instruções que são executadas se os valores comparados anteriormente forem iguais. Destaquei a linha 29 do código para mostrar uma chamada para system. Será nossa shell querida? Parece melhor do que o que acontece na linha 32, um simples puts que, acredito eu, seja o que imprime o "Wrong".

Só há uma forma de descobrir: debuggar.

Coloquei um <a href="https://pt.wikipedia.org/wiki/Ponto_de_parada">breakpoint</a> na linha 24 do código para descobrir se a comparação era mesmo entre o valor que eu inseria e os dígitos corretos:

```
0x08048555 +72:	cmp    0x1c(%esp),%eax
(gdb) b *main+72
Breakpoint 1 at 0x8048555
```

Hora de rodar o programa.

```
(gdb) run 1234
Starting program: /home/leviathan6/leviathan6 1234

Breakpoint 1, 0x08048555 in main ()
```

Programa parado, hora de <a href="https://sourceware.org/gdb/onlinedocs/gdb/Registers.html">analisar</a> o que há em ESP+0x1c e EAX.

```
(gdb) print $eax
$1 = 1234
```

Exatamente como suspeitei. EAX está guardando o valor que passei. <a href="http://www.delorie.com/gnu/docs/gdb/gdb_56.html">Agora era só pegar</a> o outro valor da comparação e se saberia quais são os dígitos corretos.

```
(gdb) x/u $esp+0x1c
0xffffd59c: 7123
```

Será?

```
(gdb) quit
A debugging session is active.

Inferior 1 [process 15136] will be killed.

Quit anyway? (y or n) y
leviathan6@melinda:~$ ./leviathan6 7123
$ whomi
/bin/sh: 1: whomi: not found
$ whoami
leviathan7
$ cat /etv/leviathan_pass/leviathan7
cat: /etv/leviathan_pass/leviathan7: No such file or directory
$ cat /etc/leviathan_pass/leviathan7
YmxhYmxhYmxh
```

Assim que consegui o pass do leviathan7, corri para logar no que achava ser o último nível. Pós login, fui procurar um chall e encontrei isso aqui:

<script src="https://gist.github.com/nick-belane/1606cacaa62ab1d6211bc5a163447e95.js"></script>

Sorry about the writeups :( Tô tentando compartilhar um conhecimento com outros humanos aleatórios (ou outras possívels formas de vida, vai saber) que trombarem com o que escrevo pelas webs.

Então é isso aí. Realmente não foi um ctf díficil, mas aprendi pequenas coisas bem importantes em momentos que empaquei por apenas desconhecer um detalhe ou dois (como aquela absurdidade sobre a sintaxe AT&amp;T, bagulho básico e eu voando).

E como o arquivo CONGRATULATIONS indica, já pulei para algo mais sério e comecei a tentar resolver os challs do <a href="http://overthewire.org/wargames/narnia/">Narnia</a>. Write-ups vêm vindo por aí. Estou gostando muito dessa forma progressiva de ir subindo de nível. Quem sabe um dia não zero todos do Over The wire? :p

Caso você ache que eu tenha falado alguma merda lá em cima ou falta acrescentar algo, a seção de comentários aqui embaixo pode ser útil.
