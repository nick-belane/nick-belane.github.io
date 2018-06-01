---
layout: default
title: Dual Boot Windows Vista e Slax
date: 2010-02-01 00:23
author: brennords
comments: true
categories: [Hacking]
---
Primeiro post de 2010 demorou mais chegou, era pra eu ter postado mais cedo mais a <span style="text-decoration:line-through;">preguiça</span> falta de tempo não deixou, então deixa eu parar de falar besteira e começar a falar logo sobre o que é de interesse.

Eu já usei um monte de distros, passei um bom tempo usando o Ubuntu, mais ai cansei dele e resolvi procurar alguma outra distro, sei lá simplesmente enjoei dele, pensei em tentar alguma distro bem leve, e acho que eu encontrei o que eu procurava.

O <a href="http://www.slax.org/" target="_blank"><strong>Slax</strong></a> é baseado no Slackware, ele é mais usado como Live CD, e é muito leve.

O que eu achei mais legal foi a opção de antes de baixa-lo, poder adicionar ou retirar aplicações, montando o Slax ao meu gosto, e não precisa de registro nem nada. Monte o seu lá no site: <a href="http://www.slax.org/build.php" target="_blank">http://www.slax.org/build.php</a>

Lá se foram 4 paragráfos, e eu ainda não começei a explicar como fazer o dual boot, calma juro que vou começar agora ;) .

<!--more-->

Primeiro, o Windows Vista tem umas frescurinhas (umas milhares), o primeiro passo seria criar uma partição pro Slax (pra deixar as coisas mais organizadas ^^), o Vista tem um gerenciador de partições (para encontra-lo va ao menu iniciar, depois clique com o botão direito encima da opção computador, e escolha a opção gerenciar, logo depois vá  em Gerenciamento de Disco), mas esse gerenciador simplesmente não deixava eu criar a partição, o que eu fiz então? Procurei um gerenciador melhor, e que funcionasse no Vista. Depois de um tempo de procura eu encontrei esse ótimo programa: <a href="http://www.baixaki.com.br/download/easeus-partition-master-home-edition.htm" target="_blank">EASEUS Partition Master 4.1.1 Home Edition</a>

É gratuito e muito simples de usar, crie uma nova partição (cuidado para não ter problemas com os arquivos do Vista, e depois não conseguir mais iniciar o computador), escolha o tamanho de sua preferência (formate como FAT32), depois disso o programa vai pedir pra reiniciar o PC e tals, ele vai reiniciar, redimensionar e formatar a partição, ela agora está pronta para receber os arquivos do Slax.

Depois de criar um cantinho no HD pro Slax, eu montei e fiz o download do Slax, extrai a imagem ISO para dentro da nova partição, e agora chegou a hora de configurar o Bootloader.

Eu não tinha muita idéia de como configurar, então tive alguns problemas, o Windows Vista não usa o mesmo esquema do WinXP, sofri um pouco (foi mais por falta de atenção) mas consegui.

O que eu fiz foi: primeiro baixar o <a href="http://neosmart.net/dl.php?id=1" target="_blank">EasyBCD</a>, que serve para configurar o Bootloader, adicionei uma entrada para o Grub (vá em Add/Remove Entries, vá na aba NeoGrub e clique em Install NeoGrub).

Depois disso é hora de configurar o Grub, clique em Configure, apague tudo do arquivo que se abriu e cole isso lá:

<pre><span style="color:#000000;">default 0
timeout 5

title Slax Graphics mode (KDE)
root (<span style="color:#ff0000;">hdx,y</span>)
kernel /boot/vmlinuz initrd=/boot/initrd.gz ramdisk_size=6666 root=/dev/ram0 rw autoexec=xconf;telinit~4 changes=/slax/
initrd /boot/initrd.gz

title Slax Always Fresh
root (<span style="color:#ff0000;">hdx,y</span>)
kernel /boot/vmlinuz initrd=/boot/initrd.gz ramdisk_size=6666 root=/dev/ram0 rw autoexec=xconf;telinit~4
initrd /boot/initrd.gz

title Slax Copy To RAM
root (<span style="color:#ff0000;">hdx,y</span>)
kernel /boot/vmlinuz initrd=/boot/initrd.gz ramdisk_size=6666 root=/dev/ram0 rw copy2ram autoexec=xconf;telinit~4
initrd /boot/initrd.gz

title Slax Text Mode
root (<span style="color:#ff0000;">hdx,y</span>)
kernel /boot/vmlinuz initrd=/boot/initrd.gz ramdisk_size=6666 root=/dev/ram0 rw changes=/slax/
initrd /boot/initrd.gz
</span></pre>

Não esqueça de substituir o "<span style="color:#ff0000;">HDX</span>" para o hdd que o Slax está (geralmente hd0) e o "<span style="color:#ff0000;">y</span>" pela partição que o Slax está (no meu caso foi 4, passei um tempo pra descobrir isso, editei a configuração do Grub várias vezes pra acertar).

Depois de tudo isso, meus parabéns :D, você deve estar com o Slax instalado no seu computador, o KDE é bem mais bonitinho do que o GNOME ^^ (pelo menos eu achei), já fiz muitas besteiras no meu sistema aqui, e tive que reinstalar ele 2 vezes, mas tudo isso serve como experiência (: .

Depois de fuçar um pouco, meu Desktop Slax ficou assim (nem me importo se é o KDE antigo, o que me importa é que é funcional :P):

<a href="http://brenn0.files.wordpress.com/2010/02/desktop.png"><img class="size-medium wp-image-207 " title="Desktop_Slax" src="http://brenn0.files.wordpress.com/2010/02/desktop.png?w=300" alt="Desktop_Slax" width="300" height="187" /></a>

Acho que esse é um dos <span style="text-decoration:line-through;">poucos</span> posts úteis do blog, então enjoy (: .

Esse post foi quase uma tradução (seria mais como uma imitação :P) desse texto do fórum do Slax: <a href="http://www.slax.org/forum.php?action=view&amp;parentID=25778" target="_blank">http://www.slax.org/forum.php?action=view&amp;parentID=25778</a> foi esse texto que me ajudou a instalar e configurar o sistema, resolvi escrever esse aqui por que em português não encontrei nada.

Ao som de <a href="http://www.youtube.com/watch?v=k6EQAOmJrbw" target="_blank">My Chemical Romance Teenagers</a> termino esse post (Lika me chamou de <span style="text-decoration:underline;"><strong>EMO</strong></span> por causa disso [aaaaaa], emo já é demais, EU NÃO SOU EMO só por que ouço músicas depressivas e sombrias (:, pra ser emo eu teria que ser branco, dentre outras caracteristicas das quais eu não possuo ^^ )

Bjx :* e té o meu próximo post ^^.
