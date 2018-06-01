---
layout: default
title: Level 1 - Execução de código de variáveis de ambiente - [OverTheWire CTF – Narnia] write-up
date: 2016-12-13 17:32
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">Level 0</a></li>
</ul>

Levei mais tempo do que deveria para resolver esse chall e isso apenas prova o que é ter problemas em níveis fundamentais do estudo de exploitation. Fiquei feliz por ter demorando tempo suficiente para aprender novos conceitos e ter estudado uma boa quantidade de assuntos.

Depois da putaria de se justificar para ninguém, hora de write-up o/

Feita a conexão ssh com o servidor <a href="http://overthewire.org/wargames/narnia/">narnia do Over The Wire</a> usando o usuário narnia1 e o pass encontrado como a <a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">flag do último nível</a>, fui em <strong>/narnia</strong> e executei o chall da vez:

```
narnia1@melinda:/narnia$ ./narnia1
Give me something to execute at the env-variable EGG
```

Hum, ele deseja que se coloque algo na <a href="https://www.todoespacoonline.com/w/2015/07/variaveis-de-ambiente-no-linux/">varíavel de ambiente</a> <strong>EGG</strong> para que possa executar. Vamos tentar e ver no que vai dar.

```
narnia1@melinda:/narnia$ EGG=BBBB
narnia1@melinda:/narnia$ export EGG
narnia1@melinda:/narnia$ ./narnia1
Trying to execute EGG!
Segmentation fault
```

Ops, quebramos algo.

Sem perder tempo, hora de um <em>cat narnia1.c</em> e descobrir o que ele quer fazer com EGG.

<script src="https://gist.github.com/anonymous/bfde74bdbf17ec578a47ff5cfeb8be08.js"></script>

É bastante simples. No ínicio da função <strong>main</strong> (linha 4) é declarado um ponteiro de função<sup>(<a href="http://www.dca.fee.unicamp.br/cursos/EA876/apostila/HTML/node144.html">1</a>, <a href="http://www.cprogramming.com/tutorial/function-pointers.html">2</a>)</sup> chamado <strong>ret</strong>. A sacada aqui é observar como, na linha 12, ret recebe o valor de EGG graças a função <a href="http://man7.org/linux/man-pages/man3/getenv.3.html">getenv</a>, mas não o valor literal de EGG, e sim o endereço que aponta para o ínicio do valor guardado por EGG.

Tudo isso só quer dizer que na linha 13 de main quando ret é chamada, ela irá pular para o ínicio das possíveis instruções que estarão na variável de ambiente. Por isso que o processo vai cuspir um <a href="https://en.wikipedia.org/wiki/Segmentation_fault">segmentation fault</a> se encontrar quatro B's.

Me confundi com esse simples comportamento e levei um tempo escrevendo, debuggando e testando código. Vou abrir um parentêses gigante para falar de como entendi:

O código final que me ajudou a entender sobre o chall foi este:

<script src="https://gist.github.com/anonymous/f1f7ffe8a22b12effaac19b591595362.js"></script>

Dentro de uma pasta no próprio /tmp do servidor, compilei meu código (passando argumento para o gcc compilar uma versão de 32 bits porque o pc em que o ctf está rodando tem 64) e criei e exportei a variável de ambiente.

```
narnia1@melinda:/tmp/1212$ gcc -m32 teste.c -o teste
narnia1@melinda:/narnia$ EGG=BLABLA
narnia1@melinda:/narnia$ export EGG
```

Depois disso, foi hora de ir no gdb e analisar.

<script src="https://gist.github.com/anonymous/7ffa379a0ab62461ec2ed533f94fdd0d.js"></script>

Resolvi omitir o disas da função main (linha 4) para não ocupar espaço no post de forma desnecessária. É relevante só dizer que na linha 5 coloquei um breakpoint na última linha da função (main+201).

Logo executei meu programa e vi ele imprimindo os valores que mandei.

Na primeira linha da execução, ele imprime o valor armazenado em EGG no formato de string ("BLABLA"). Na segunda, o ponteiro para EGG (0xffffd8a0). Na terceira, no formato inteiro (-10080). Na quarta linha da execução, imprimi o ponteiro não inicializado de ret e em seguida imprimi o mesmo depois de ele ter ganho o valor de getenv("EGG").

Depois de todo o trabalho pesado do programa, o breakpoint entra em ação. Meu objetivo era poder checar e ter certeza do valor que ret receberia e para onde ele apontava.

Usando x/s + o endereço que ret ganhou, pude ver que o BLABLA estava lá.

Após entender o código do chall, ficou fácil ter ideia do que fazer em seguida: enriquecer EGG com instruções válidas, de preferência instruções que dessem uma shell! Ou seja, um <a href="http://www.mentebinaria.com.br/zine/edicoes/1/ConstruindoShellcodes.txt">shellcode</a>!

Assim como no <a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">chall anterior</a>, usarei o python para passar as instruções em um formato que o processo entenda e execute.

Claro que poderia copiar algum <a href="https://www.google.com.br/search?q=a+%2Fbin%2Fsh+shellcode&amp;oq=a+%2Fbin%2Fsh+shellcode&amp;aqs=chrome..69i57j69i58j69i60j69i64l3.7092j0j9&amp;sourceid=chrome&amp;es_sm=122&amp;ie=UTF-8">shellcode pronto</a> e tentar fazer funcionar com o chall, mas preferi ir atrás de mais conhecimento e acabei encontrando um ótimo paper ensinando a construir shellcodes: <a href="https://www.exploit-db.com/papers/13224/">https://www.exploit-db.com/papers/13224/</a> (<a href="http://www.mentebinaria.com.br/zine/edicoes/1/ConstruindoShellcodes.txt">aqui</a> tem um outro que está em pt-br, só dei uma lida por cima mas parece ser bem equivalente ao outro).

Com a ajuda do paper, criei shellcodes que não fazem nada, só chamam exit com o código de retorno 0:

```
narnia1@melinda:/narnia$ EGG=`python -c 'print \x31\xc0\xb0\x01\x31\xdb\xcd\x80'`
narnia1@melinda:/narnia$ export EGG
narnia1@melinda:/narnia$ ./narnia1
Trying to execute EGG!
narnia1@melinda:/narnia$ echo $?
0
```

Você pode ver que dessa vez não houve segmentation fault.

As crases, no ínicio e no fim do comando, servem para avisar que não quero a string "python -c..." e sim o resultado desse comando armazenado em EGG.

Fiquei brincando um tempo e criei ainda mais shellcodes inúteis:

<script src="https://gist.github.com/nick-belane/95b9827bace5dfc39bc5b66dd78489bd.js"></script>

Ainda pensei em escrever sobre a criação desses belos códigos, mas preferi não reescrever o que já está bem documentado pela internet por gente que sabe mais do que eu. Me refiro aos links que deixei lá em cima. Não deixe de passar por eles. Seria muito kiddie da vossa parte passar o resto da vida só no copy e paste de shellcodes dos outros.

Mas agora é hora de copiar e colar um shellcode pronto que chama /bin/sh e resolver esse chall!

O código assembly referente ao shellcode que usei está no fim do <a href="https://www.exploit-db.com/papers/13224/">paper</a> que me ensinou a escrever os meus próprios.

<script src="https://gist.github.com/nick-belane/37c8c62b1f2b469336b988c422dce776.js"></script>

Uhul.
