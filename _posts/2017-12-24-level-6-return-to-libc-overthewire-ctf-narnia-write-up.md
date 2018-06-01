---
layout: default
title: Level 6 - Return-to-libc [OverTheWire CTF - Narnia] write-up
date: 2017-12-24 15:57
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/12/13/level-1-overthewire-ctf-narnia-write-up/">Level 1</a></li>
    <li><a href="https://brenn0.wordpress.com/2017/01/03/level-2-overthewire-ctf-narnia-write-up/">Level 2</a></li>
    <li><a href="https://brenn0.wordpress.com/2017/01/07/level-3-overthewire-ctf-narnia-write-up/">Level 3</a></li>
    <li><a href="https://brenn0.wordpress.com/2017/01/15/level-4-overthewire-ctf-narnia-write-up/">Level 4</a></li>
    <li><a href="https://brenn0.wordpress.com/2017/05/31/level-5-overthewire-ctf-narnia-write-up/" target="_blank" rel="noopener">Level 5</a></li>
</ul>

Rodar <strong>narnia6</strong> não oferece muitas informações:

https://gist.github.com/anonymous/71ff88b0e36503caacaacbe55c63f751

Aparentemente ele exige dois argumentos e, se os dois forem passados, o primeiro é impresso.

No código fonte encontramos respostas sobre o comportamento do programa e como ele é explorável.

https://gist.github.com/anonymous/a4758c9c68a8e510afcec2d41cda3ed4

São recebidos dois argumentos, <strong>b1</strong> e <strong>b2</strong> e, de forma não muito normal, imprime b1 usando <strong>fp</strong>, que no começo do código é declarado e setado como um ponteiro para a função <a href="https://www.tutorialspoint.com/c_standard_library/c_function_puts.htm" target="_blank" rel="noopener">puts</a>.

O que chama a atenção é a forma com que os argumentos são salvos nos vetores de char b1 e b2: usa-se a função <strong>strcpy</strong>. Já falei outras vezes sobre como a mesma é insegura, pois faz cópias sem verificar o espaço disponível no destino, o que pode causar buffer overflows.

É exatamente o que acontece nesse caso. Qualquer vetor de char maior que 8 que for passado para o programa como qualquer um dos dois argumentos vai ultrapassar o buffer e causar problemas.

Observe o que aconte se forem passados dois argumentos de 20 caracteres:

https://gist.github.com/anonymous/09967a3e2c2c26a251260cb3f71fc98a

Muito bom. Se controlamos EIP, controlamos o programa.

Mas, desde o começo, acreditei que esse level não seria mais um simples stack overflow como os anteriores. Costumo usar o comado <strong>checksec</strong> no peda assim que carrego o binário e já havia descoberto o que o diferencia dos binários anteriores:

https://gist.github.com/anonymous/761c781f895ba2d752c484e8895e7b82

O bit <strong>NX</strong> está setado! Ou seja, pôr shellcode na stack e jogar a execução do programa pra lá não vai funcionar, porque quando esta forma de proteção está habilitada, as regiões da memória são separadas entre as que contém código executável e as que servem apenas para o armazenamento de dados. Como a stack foi criada apenas para guardar valores, faz sentido que nada que esteja nela seja executável. Você pode aprender mais sobre NX <a href="https://pt.wikipedia.org/wiki/Bit_NX" target="_blank" rel="noopener">aqui</a> e usando o google.

Depois de observar esse fator, lembrei de uma das técnicas utilizadas para burlar esse mecanismo de segurança: a <a href="https://pt.wikipedia.org/wiki/Return-to-libc_attack" target="_blank" rel="noopener">return-to-libc</a>.

Já que não é possível inserir e executar nosso próprio código, que tal usar o código que já está no executável?

Essa é uma proposição bem ampla e vai depender das limitações impostas pelo programa e da criatividade e habilidade do cara que estiver explorando. E noob do jeito que sou, usarei a forma mais básica e comum. Mas deixe-me tentar explicar essa porra melhor (tô meio como sono mas tentarei).

No cabeçalho do código pode-se ver os includes:

```

#include stdio.h
#include stdlib.h
#include string.h

```

Quando um programador dá um <a href="http://www.tiexpert.net/programacao/c/include.php" target="_blank" rel="noopener">#include</a> nessas porra, insere no seu programa funções já prontas de outros arquivos. Tipo a primeira, <a href="https://pt.wikipedia.org/wiki/Stdio.h" target="_blank" rel="noopener">stdio.h</a>, que é uma das bibliotecas padrão do C e seu nome é uma sigla para "standard input and output", traz consigo a função <a href="https://stackoverflow.com/questions/4867229/code-for-printf-function-in-c" target="_blank" rel="noopener">printf</a> que faz o trabalho sujo por trás da impressão de algum dado na tela. As bibliotecas são uma forma de não ter que reescrever o mesmo código sempre, sabe? Por isso existem essas já conhecidas e padronizadas.

Ou seja, além do código fonte vísivel, tem mais um bocado de funções disponíveis para execução.

Consegue ver onde podemos chegar?

O segundo #include do código fonte do desafio é o da <a href="https://pt.wikipedia.org/wiki/Stdlib.h" target="_blank" rel="noopener">stdlib.h</a>. Ela é mais uma das bibliotecas padrão e é a que interessa para nós porque nela está a função <a href="https://www.tutorialspoint.com/c_standard_library/c_function_system.htm" target="_blank" rel="noopener">system</a>, meu pirraia. E caso não saibas, essa função tem a capacidade de executar comandos no sistema.

Consegue ver melhor?

Voltando ao código do chall, ao fazer uma melhor análise enquanto crashava o bagui, acabei sacando que quando se estouram os buffers, estourando de maneira calculada, eu conseguia sobreescrever o ponteiro fp. É, aquele puto criado naquela linha meio complicada, que me fez viajar um pouco para entende-lo, começou a ter algum sentido para existir. Suponho que seja a forma que o criador do desafio tenha encontrado para facilitar a execução de um ataque tipo return2libc.

Não conseguiu ver o porquê?

https://gist.github.com/anonymous/1a0360d36ed1d23081b6ffee39cca9d4

Observe a linha 10. fp é chamado usando b1 como argumento. Essa porra tá praticamente dada. Pense bem, se é possível corromper o ponteiro fp para apontar para qualquer lugar/função que quisermos (leia-se: system), ela já vai estar pronta para receber b1 como argumento (leia-se: /bin/sh). E, ao executar system("bin/sh"), teremos uma shell.

Para começar, era preciso saber o endereço de system. O que não é dificil de descobrir.

https://gist.github.com/anonymous/350fc5626ba753a2f20a02d551e6c25d

Após setar um breakpoint em main e rodar o programa só para vê-lo parar, é só dar um "p system" para descobrir o endereço.

Com o endereço para onde queria levar a execução do programa, comecei os testes para descobrir os exatos valores do overflow. Apanhei um pouco porque noob é noob e de vez em quando ainda me enrolo brincando na stack.

Após algumas tentativas, encontrei uma forma meio... Talvez estranha de explorar a falha:

https://gist.github.com/anonymous/649a8dbf88ae4b06ca68156a977b836b

Para o primeiro argumento, b1, enviei oito A's, o que já ultrapassa o limite reservado na memória para o mesmo. Após os A's, mando o endereço de system, que vai sobreescrever o ponteiro fp.

Mas e quando fp for chamar b1? Vai ter A's e um endereço ao invés do argumento que preciso.

Foi aí que me enrolei um pouco por não conseguir entender bem o que havia acontecido quando alinhei o segundo argumento e peguei uma shell.

Depois de um tempo no gdb, percebi que, ao estourar o buffer de b2 com oito C's, e enviar a string "/bin/sh" logo depois, aparentemente estava sobreescrevendo b1 com o valor "/bin/sh".

Ou seja, parecia uma gambiarra massa. Mas de fato funcionava e o objetivo foi alcançado.

https://gist.github.com/anonymous/c0816aaf0c4849543880573c39598640

A shell com privilégios de narnia7 foi obtida com sucesso.
