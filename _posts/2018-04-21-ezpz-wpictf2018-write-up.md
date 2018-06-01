---
layout: post
title: EZPZ [WPICTF2018] - Write-up
date: 2018-04-21 16:58
author: brennords
comments: true
categories: [ctf, CTFs, exploit, Hacking, Reverse/Xpl/Bin, xpl]
---
<p style="text-align:justify;">O <a href="https://wpictf.xyz/" target="_blank" rel="noopener">WPICTF</a> rolou no final de semana passado, do dia 13/04 ao dia 15/04. Não tive tempo suficiente para tentar resolver todos os challs, mas, nos poucos minutos que passei, consegui solucionar o mistério de dois binários. Um deles foi o <strong>ezpz</strong>.</p>

<p style="text-align:justify;">Nenhuma descrição foi oferecida além do link para o programa e o endereço no qual o mesmo rodava.</p>

<blockquote>nc ezpz.wpictf.xyz 31337

redundant servers on 31338 and 31339

made by awg

file from <a href="https://github.com/brerodrigues/CTFs/blob/master/WPICTF2018/ezpz/ezpz?raw=true">https://drive.google.com/open?id=1RpTW-5_-pv6ShZEDBTAoIB_AiPkb8db9</a></blockquote>

<p style="text-align:justify;">Ao rodar o negócio, na primeira linha se vê algo sobre "debugging" e aparentemente três endereços de memória. Na segunda, se requisita que seja inserida a senha correta para pegarmos a flag, mas ao inserir qualquer valor, temos um erro de segmentation fault.</p>

<img class=" size-full wp-image-1595 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/1ezpz.png" alt="1ezpz" width="498" height="112" />

<p style="text-align:justify;">Agora temos que debuggar esse bagulho e descobrir o que está havendo. Começo usando o <em>ltrace</em> e observando as chamadas de função, dá para ver que, após a senha ser inserida, é chamada "<a href="https://www.tutorialspoint.com/c_standard_library/c_function_getenv.htm" target="_blank" rel="noopener"><em>getenv</em></a>", que é uma função responsável por pegar valores das <a href="https://www.todoespacoonline.com/w/2015/07/variaveis-de-ambiente-no-linux/" target="_blank" rel="noopener">variáveis de ambiente</a>. É bem provável que a razão desse segmentation fault seja o fato de que no meu linux não há variável de ambiente chamada "<strong>EZPZPASSWORD</strong>".</p>

<p style="text-align:justify;"><img class=" size-full wp-image-1596 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/2ezpz.png" alt="2ezpz" width="714" height="250" /></p>

<p style="text-align:justify;">Então criei e setei um valor para a variável EZPZPASSWORD e rodei o ezpz mais uma vez.</p>

<p style="text-align:justify;"><img class=" size-full wp-image-1597 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/3ezpz.png" alt="3ezpz" width="721" height="342" /></p>

<p style="text-align:justify;">Olha só, ele até tentou imprimir a flag. O problema é que no meu sistema também não existe uma variável chamada <strong>EZPZFLAG</strong>. Mas criando e setando uma pode-se ver o resultado:</p>

<img class=" size-full wp-image-1598 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/4ezpz.png" alt="4ezpz" width="720" height="310" />

<p style="text-align:justify;">Já foi possível entender, por cima, o funcionamento do programa. Com certeza essas variáveis estão setadas no servidor que o chall está rodando. Mas saber disso não ajuda tanto. E por ser um desafio de XPL e não REV, provavelmente haveria uma falha a ser explorada e eu ainda não havia percebido onde ela estava.</p>

<p style="text-align:justify;">Quando rodei o programa com o ltrace, saquei que se usava <a href="https://www.tutorialspoint.com/c_standard_library/c_function_fgets.htm" target="_blank" rel="noopener">fgets</a> para receber a senha inserida por nós. Então tive a impressão, errada, de que seria inútil buscar um overflow nesse input porque fgets costuma ser segura. Mas ainda é possível usar fgets e estourar o buffer de destino.</p>

<p style="text-align:justify;">Pode ser culpa de um erro de cálculo ou uma falta de atenção, mas pode acontecer. Não é porque fgets recebe como argumento o tamanho do buffer de destino que o tamanho esteja correto. Um erro totalmente humano.</p>

<p style="text-align:justify;">Percebi que era o caso do desafio quando inseri como senha uma longa string de 200"A's" usando, dentro do gdb, comando: run &lt;&lt;&lt; $(python -c 'print "A"*200')</p>

<p style="text-align:justify;"><img class=" size-full wp-image-1599 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/5ezpz.png" alt="5ezpz" width="587" height="376" /></p>

<p style="text-align:justify;">Sobreescrevi totalmente o endereço de retorno. Quando a instrução ret é executada, vai buscar na stack esse endereço, que é onde todos meus A's estão morando.</p>

<p style="text-align:justify;">Agora, com todo esse poder em mãos, o que fazer?</p>

<p style="text-align:justify;">Após umas tentativas preguiçosas de calcular exatamente quantos bytes eram necessários para chegar exatamente no endereço de retorno (diminui de 200 para 100 A's, depois para 150... E assim chegar a exatos 136), dei uma olhada com quais esquemas de segurança o binário contava.</p>

<p style="text-align:justify;"><img class=" size-full wp-image-1600 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/6ezpz.png" alt="6ezpz" width="187" height="112" /></p>

<p style="text-align:justify;"><strong><a href="https://pt.wikipedia.org/wiki/Bit_NX" target="_blank" rel="noopener">Nx</a></strong> não nos deixa executar instruções inseridas na stack, <strong><a href="https://access.redhat.com/blogs/766093/posts/1975793">PIE</a></strong> faz com que, falando de forma bruta, cada vez que o programa for rodado, o mesmo seja carregando em posições diferentes da memória. E o <strong><a href="http://tk-blog.blogspot.com.br/2009/02/relro-not-so-well-known-memory.html">partial RELRO </a></strong>protege alguns setores do binário de serem sobreescritos, tipo a <a href="https://www.youtube.com/watch?v=t1LH9D5cuK4">Global Offset Table</a> (eu acho).</p>

<p style="text-align:justify;">Certo, isso diminui as opções. Mandei um <em>info functions</em> porque já havia feito um monte de coisas e faltava o básico: análisar o código.</p>

<p style="text-align:justify;"><img class=" size-full wp-image-1601 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/7ezpz.png" alt="7ezpz" width="284" height="202" /></p>

<p style="text-align:justify;">Meu gdb tá com esse problema de encontrar, mesmo que não exista, em alguns binários em particular, o arquivo do source, ainda não consertei essa porra mas é isso aí.</p>

<p style="text-align:justify;">Além de main, há duas funções interessantes: <strong>correct_pw</strong> e <strong>wrong_pw</strong>.</p>

<p style="text-align:justify;">Os nomes já são sugestivos, mas fazendo o disassembly para confirmar, dá para ver que correct_pw é a função que vai buscar o valor na variável de ambiente EZPZFLAG e imprimi-lo.</p>

<img class=" size-full wp-image-1602 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/8ezpz.png" alt="8ezpz" width="525" height="357" />

<p style="text-align:justify;"></p>

<p style="text-align:justify;">O objetivo agora ficou claro: sobreescrever o endereço de retorno com o endereço de correct_pw.</p>

<p style="text-align:justify;">Seria um clássico desafio de stack overflow igual a vários que já fiz: estourar o buffer e sobreescrever o endereço de retorno com a função que imprime a flag, a não ser pelo pequeno detalhe de que, toda vez que o programa é executado, os endereços são diferentes graças a proteção do <a href="https://exploit.courses/files/bfh2017/day5/0x53_ExploitMitigations_PIE.pdf" target="_blank" rel="noopener">PIE</a>.</p>

<p style="text-align:justify;">Mas o binário facilita a vida: essa primeira linha de "debugging" e os três endereços exibidos são, respectivamente, os endereços de correct_pw, wrong_pw e main. Dá para se comprovar isso observando e setando breakpoints em main+21,28 e 35, que é quando são enviados os valores para RCX, RDX e RSI antes de chamar o printf.</p>

<img class=" size-full wp-image-1603 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/9ezpz.png" alt="9ezpz" width="954" height="528" />

<p style="text-align:justify;">Era óbvio o que tinha que se fazer: conectar ao servidor que ezpz rodava, pegar a primeira linha com os endereços, extrair o primeiro endereço, mandar 136 bytes + esse endereço e assim executar correct_pw e ver a flag ser impressa.</p>

<p style="text-align:justify;">Na correria, peguei o código de um exploit antigo e adaptei para o ezpz. Não é bonito mas funciona. Fica óbvio que ainda preciso aprender a usar a <a href="https://docs.pwntools.com/en/stable/" target="_blank" rel="noopener">pwntools</a> e, após o meu exploit, postarei outro (que não me pertence) com um código bem menor graças as facilidades da pwntools.</p>

https://gist.github.com/brerodrigues/5a46cddb56a087c40e8176afc9902c9b

<p style="text-align:justify;">E aqui uma versão bem mais bonita: <a href="https://github.com/soolidsnake/Write-ups/blob/master/WPICTF/ezpz/exploit.py" target="_blank" rel="noopener">https://github.com/soolidsnake/Write-ups/blob/master/WPICTF/ezpz/exploit.py</a></p>

&nbsp;
