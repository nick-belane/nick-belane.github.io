---
layout: default
title: Level 2 - Explorando um stack overflow como se fosse anos 90 [OverTheWire CTF – Narnia] write-up
date: 2017-01-03 17:00
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/12/13/level-1-overthewire-ctf-narnia-write-up/">Level 1</a></li>
</ul>

Continuando a viagem por narnia, chegou a hora de passar do level2.

Logado no ssh do ctf e dentro de <strong>/narnia</strong>, executei o chall pela primeira vez:

```
narnia2@melinda:/narnia$ ./narnia2
Usage: ./narnia2 argument
```

Executando com o argumento:

```
narnia2@melinda:/narnia$ ./narnia2 argumento
argumentonarnia2@melinda:/narnia$
```

Aparentemente ele só imprime o argumento passado e nada mais. Pode-se confirmar o comportamento checando o código fonte:

<script src="https://gist.github.com/anonymous/9eb7a87061a1436de489ac2136ca9ce5.js"></script>

Simples.

Na primeira linha da função <strong>main</strong>, temos um <a href="http://linguagemc.com.br/string-em-c-vetor-de-caracteres/">vetor de chars</a> chamado <strong>buf </strong>que tem o tamanho necessário para guardar 128 caracteres. Logo depois tem um if simples que verifica se foi ou não passado um argumento para o programa, mas essa parte é dispensável. A linha mais importante é a <strong>strcpy(buf,argv[1]);</strong>

<a href="http://www.cprogressivo.net/2013/03/strcpy-Como-copiar-uma-String-em-C.html">strcpy</a> é uma função famosa por seu potencial perigoso. O que ela faz é copiar o segundo argumento para o primeiro. Mas ao contrário da sua parente <a href="http://linguagemc.com.br/a-biblioteca-string-h/">strncpy</a>, a função não te dá a opção de avisar o quanto se pode copiar para a variável de destino. No casso do chall, strcpy apenas supõe que buf é grande o suficiente para receber o valor que vier de argv[1].

Apesar de não haver nada de errado em usar strcpy (sei lá que motivo alguém teria), não dá pra usar a mesma quando se está tratando de dados que podem variar de tamanho e muito menos para entradas de usuários pela razão óbvia: a variavel de destino pode ter um tamanho menor do que a entrada de dados, o que causaria um bom e velho <a href="https://pt.wikipedia.org/wiki/Transbordamento_de_dados">buffer overflow</a>.

Hora de ver um buffer se estourando na prática:

```
narnia2@melinda:/narnia$ ./narnia2 `python -c 'print A*150'`
Segmentation fault
```

Apesar do erro <strong><a href="https://pt.wikipedia.org/wiki/Falha_de_segmenta%C3%A7%C3%A3o">Segmentation fault</a></strong> não significar exatamente que o que aconteceu foi mesmo um problema de buffer overflow, já vamos confirmar que o erro foi causado por terem sido passados 150 A's como argumento.

```
narnia2@melinda:/narnia$ gdb -q narnia2
Reading symbols from narnia2...(no debugging symbols found)...done.
(gdb) source /usr/local/peda/peda.py
gdb-peda$ checksec
CANARY : disabled
FORTIFY : disabled
NX : disabled
PIE : disabled
RELRO : disabled
gdb-peda$
```

O comando checksec do <a href="https://github.com/longld/peda">peda</a> serve para verificar se o binário tem determinadas proteções. Nem vou entrar no mérito de explica-las porque ainda estou engatinhando por essas águas (engatinhar na água?), mas foi bom saber que não há nenhuma proteção ativa e que o programa pode ser explorado como se <a href="http://insecure.org/stf/smashstack.html">estivessemos nos anos 90</a>.

No <a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">write-up do level0</a> falei de forma básica pra caaralho de como o stack se parecia e funcionava. Irei supôr que saibas o que tentei ensinar e agilizar este post pulando as explicações que fiz lá.

Agora quer saber a razão do segmentation fault?

<script src="https://gist.github.com/anonymous/25d4109f19931355b815b4eb1b9f28f9.js"></script>

É bastante informação nova se você não conhece sobre <a href="http://www.numaboa.com.br/informatica/queisso/521-registradores">registradores</a>. Por um momento pensei em detalhar tudo usando minhas próprias palavras, mas por que reescrever o que já foi escrito e publicado por aí? Recomendo <a href="http://www.crimesciberneticos.com/2011/03/exploiting-buffer-overflow.html">este</a> bom link que dá uma aula sobre os registradores, o stack e tudo mais que você precisaria para entender o resto da exploração neste write-up (lá chega até a ter informação suficiente para que se possa resolver o chall sem precisar de write-up).

Agora voltando...

O segmentation fault acontece quando o programa tenta acessar um endereço de memória proibido para ele, seja por questões de permissões ou por questão de existência. No caso acima, pode-se comprovar que é o segundo caso.

Quando insiro 150 B's e o programa só tem reservado no stack espaço para 128 desses caracteres, acabo sobreescrevendo seja lá o que estiver no próprio stack depois desses 128 bytes. Se você seguiu meu conselho e deu uma lida <a href="http://www.crimesciberneticos.com/2011/03/exploiting-buffer-overflow.html">aqui</a> antes de continuar, sabe bem o que é um stack frame. Sabe também que, quando uma função é chamada, o programa "pula" para o determinado endereço que as instruções da função estão e que, no fim dessa função, o programa precisa saber para onde voltar. Para voltar, o software usa um endereço de retorno, que é sempre salvo no stack assim que se chama alguma função. Esse endereço fica bem abaixo do stack frame.

Logo, assim que 128 B's lotam o espaço reservado pela variavel buf, os outros 22 B's sobreescrevem o que vem em seguida e entre esses dados sobreescritos está o endereço de retorno.

<img class="size-full wp-image-1366" src="https://brenn0.files.wordpress.com/2016/12/stack_frame.jpg" alt="Créditos pela imagem: http://www.crimesciberneticos.com/2011/03/exploiting-buffer-overflow.html" width="400" height="241" />

É importante que entendas sobre o funcionamento do stack, então, reforço que não ignores <a href="http://www.crimesciberneticos.com/2011/03/exploiting-buffer-overflow.html">este</a> link e pesquise mais pela internet se houver dúvidas.

Assim que a função termina seu trabalho, o programa vai buscar no stack o endereço de retorno salvo anteriormente (estou abstraindo todas <a href="http://www.cin.ufpe.br/~arfs/Assembly/apostilas/Tutorial%20Assembly%20-%20Gavin/Default.htm">instruções em asm</a> responsáveis por pôr isso em prática). Mas o que acontece se os B's extras sobreescreveram e corromperam esse endereço? O programa não tem como saber e vai tentar retornar sua execução de toda a forma. E é por isso que no dump acima <strong>EIP</strong> (o registrador responsável por guardar o endereço da próxima instrução a ser executada) tem o valor <strong>0x42424242</strong>: o endereço de retorno apontava para 4 B's hexadecimais. E 0x42424242 não é um endereço válido, logo, causa para um segmentation fault.

Se controlamos o endereço de retorno, controlamos o processo. E se controlamos o processo, podemos obriga-lo a fazer nossas vontades. E a vontade aqui é ganhar uma shell com privilegios narnia3.

Ok, mas como fazer isso?

Basicamente o que precisa ser feito é: inserir instruções no processo e fazer com que o endereço de retorno aponte para onde estiver essas instruções. Vamos substituir o endereço de retorno original pelo endereço de nosso <a href="https://brenn0.wordpress.com/2016/12/13/level-1-overthewire-ctf-narnia-write-up/" target="_blank" rel="noopener">shellcode</a>.

 Mas antes de usar qualquer shellcode, precisa-se saber onde o mesmo estará na memória. Há varias formas de se descobrir isso. Usei o gdb e o peda para pôr um breakpoint no momento que a função main chama strcpy, afinal, tanto o endereço de origem, quanto o endereço de destino da nossa string envenenada serão passados como argumento para a função.

<script src="https://gist.github.com/anonymous/6a126497f672ea88e5f3760f4897062b.js"></script>

Logo ali na linha 32 pode-se ver os "Guessed arguments".

```
arg[0]: 0xffffd4c0 --0x2c (',')
arg[1]: 0xffffd741 ('B' repeats 150 times)
```

Como a <a href="http://www.cplusplus.com/reference/cstring/strcpy/">documentação de strcpy</a> nos conta, temos aí o primeiro argumento <strong>0xffffd4c0</strong> como sendo o destino da cópia da string e <strong>0xffffd741</strong> a origem.

<script src="https://gist.github.com/anonymous/caf593f9ec433eafa6fbde9ab756a452.js"></script>

Usei o comando 'n' de next para executar e parar na instrução seguinte sem entrar na função (use 's' de step e veja a diferença). E com o comando x/s [endereço] vejo que os B's estão bem guardados nos dois endereços que peguei de strcpy.

Agora é só pôr nesse endereço o shellcode e sobreescrever o endereço de retorno.

Para mudar o endereço de retorno, preciso saber qual parte dos 150 B's sobreescreveu o mesmo. Também há várias técnicas para descobrir. Muitas vezes consigo calcular o tamanho do stack frame analisando o asm da função, dessa forma, adiciono + 4 ao tamanho do frame e sobreescrevo exatamente o endereço de retorno.

<script src="https://gist.github.com/anonymous/fb9335ee41a863cc8bab2aa94b205e90.js"></script>

As instruções responsáveis pela formação do stack frame de main são as 4 primeiras (de main+0 a main+6). A primeira, <strong>push ebp</strong>, salva o valor que aponta para a base do stack (ebp) no próprio stack. A segunda, <strong>mov ebp,esp</strong>, envia o valor da base do stack (ebp) para esp, conhecido como stack pointer e responsável por apontar para o topo do stack. A terceira linha, faz <a href="https://stackoverflow.com/questions/24588858/and-esp-0xfffffff0">isso aqui</a> (começando a ter preguiça de contar tudo). E a quarta, <strong>sub esp,0x90</strong>, subtrai do stack pointer atual o valor 90h que em decimal é 140.

Isso me parece bem mais fácil de entender com a ajuda de desenhos e tal. Vou deixar <a href="http://duartes.org/gustavo/blog/post/journey-to-the-stack/">este link</a> que dá uma aula melhor que a minha.

O que mais importante se tira da interpretação dessas instruções é: o tamanho que a função main reservou para os dados é de 140.

É possível provar facilmente se esse valor está correto. Enviarei 140 B's + 4 R's como argumento para o programa e ver o que acontece.

<script src="https://gist.github.com/anonymous/564696a8bb80e15d470f2d789feaa5f5.js"></script>

Agora temos a faca e o queijo na mão.

Usei o mesmo shellcode usado no <a href="https://brenn0.wordpress.com/2016/12/13/level-1-overthewire-ctf-narnia-write-up/">chall anterior</a> e comecei a montar o exploit. Subtrai os 48 bytes do shellcode e preferi, ao invés de B's, usar um <a href="https://en.wikipedia.org/wiki/NOP_slide">nopsled</a> para preencher o espaço restante do buffer porque é coisa de leet. O exploit ficou: <strong>\x90 * 92 + shellcode + endereço de retorno.</strong>

Ainda somei uns bytes ao endereço que peguei sendo argumento de destino para o buffer só para ver o nopsled em ação e ao invés de usar <strong>0xffffd4c0</strong>, usei <strong>0xffffd510 </strong>(faça suas modificações e analise com o gdb se souber).

<script src="https://gist.github.com/nick-belane/4b353b3fe9075043af5701d3f0436e7b.js"></script>
