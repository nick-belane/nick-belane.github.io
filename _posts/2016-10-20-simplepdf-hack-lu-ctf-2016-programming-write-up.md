---
layout: default
title: SimplePDF - 10.000 PDF Unpackings [hack.lu CTF 2016 - Programming] Write-up
date: 2016-10-20 17:24
author: brennords
comments: true
categories: [CTFs]
---
Descrição:

This one is realy simple ... Just get the Flag out of this <a href="https://brenn0.files.wordpress.com/2016/10/simplepdf_f8004a3ad0acde31c40267b9856e63fc1.pdf">pdf</a>.

E foi bem simples mesmo, demorei mais do que devia só porque me enrolei em fatores que nada tinham haver com o chall.

O arquivo PDF tem uns 30 mb e só contém uma página com os dizeres "Test123". Obviamente tinha mais coisa nesse arquivo do que aparentava.

Após uns poucos palpites, me liguei que havia um outro pdf anexado ao pdf original. Então usei o <em>pdfdetach</em> para extrai-lo.

```pdfdetach -saveall simplepdf_f8004a3ad0acde31c40267b9856e63fc.pdf```

O arquivo extraído tinha o nome de "pdf10000.pdf" e, em conteúdo, era idêntico ao simplepdfblablabla.pdf. A diferença era que anexado a esse arquivo havia um outro chamado "pdf9999.pdf".

Tanto pelo nome do arquivo quanto pela categoria do desafio (programming), não foi dificil concluir o que precisava ser feito: ou extrair 10.000 arquivos pdf na mão, ou criar alguma coisa para automatizar o processo.

De primeira pensei em criar um loop em python chamando o pdfdetach, mas a elegância de uma solução vale pontos para o ego. Por que usar o python como intermediador para chamar um programa quando se pode usar o bash para chama-lo diretamente?

```

for ((i=10000; i =0; i--))
do
pdfdetach -saveall pdf$i.pdf
echo $i
done

```

Então rodei essa porra, inocentemente, em uma máquina virtual que tinha um minusculo espaço de armazenamento. Acabei achando que tinha encontrado o último PDF quando lá pelo pdf7445 meu script começou a jogar erros na tela reclamando por não ter encontrado o pdf7444. Eu, estupidamente, não percebi que quase 2000 arquivos pdfs de mais ou menos 30mb cada tinham entupido a máquina virtual. Perdi um tempo tentando entender se o pdf7445 guardava a flag sem me ligar que ele estava corrompido não por fazer parte do desafio, mas porque não foi completamente extraido.

Aí para não acabar com o pouco espaço que havia, introduzi uma gambiarra no código: ele agora só ia manter 10 arquivos e ir excluindo os outros.

```

#!/bin/bash
for ((i=10000; i =10; i--))
do
pdfdetach -saveall pdf$i.pdf
echo $i
rm pdf$((i+10)).pdf
done

```

Depois de um tempo (20-30 minutos, não lembro bem), o script finalizou e fiquei com os arquivos pdf do 10 ao 0. Então extrai manualmente o que havia no 0 e encontrei o arquivo start.pdf. Quando extrai o que havia no start.pdf depois de ver que nele ainda não tinha flag, acabei ficando com um novo arquivo também chamado de start.pdf mas que agora estava vazio. O filha da putinha tinha um anexo chamado start.pdf que acabava sobreescrevendo o próprio start.pdf e o resultado disso acabava sendo uma merda de um arquivo vazio. Então troquei o nome do start.pdf para start2.pdf e usei pela última vez o pdfdetach, aí sim pude ver um outro arquivo start.pdf que era onde a flag morava.

```flag{pdf_packing_is_fun}```
