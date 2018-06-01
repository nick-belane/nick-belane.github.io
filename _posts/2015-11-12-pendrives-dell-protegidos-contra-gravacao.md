---
layout: default
title: Pendrives Dell protegidos contra gravação
date: 2015-11-12 17:28
author: brennords
comments: true
categories: [Hacking]
---
Alguns computadores da Dell ultimamente estão vindo acompanhados com pendrives para a recuperação do sistema operacional. Como forma de evitar que o dono do computador use o pendrive para outro fim que não seja a recuperação do sistema, a Dell resolveu protege-lo contra gravação.

Qual o objetivo deles em fazer isso? Provavelmente para evitar que usuários alterassem acidentalmente os arquivos e não conseguissem recuperar o sistema, o que daria trabalho para o suporte da Dell. Ou então apenas não queriam presentear a galera com pendrives de 8gb.

Mas o objetivo deles não importa, o que importa é o nosso objetivo. E nosso objetivo é retirar essa proteção e usar o pendrive para armazenar nossos arquivos <del>pirateados</del>.

<img class="aligncenter size-full wp-image-1199" src="https://brenn0.files.wordpress.com/2015/11/dsc_4643a.jpg" alt="Pendrive Dell" width="676" height="427" />

<!--more-->

Após algumas pesquisas, trombei com <a href="http://www.techunboxed.com/2015/03/how-to-disable-write-protection-on-dell.html" target="_blank">este link</a> que explica o processo para desbloquear os pendrives que tenham esse adesivo azul na parte traseira e serão identificados pela ferramenta que indicarei como "<strong>TC58TEG6TCK (eD3.8K)</strong>". Algumas pessoas recebem/receberam outro tipo de modelo, mas já que não tive contato com o mesmo, não posso falar que o método aqui descrito irá funcionar com ele.

<strong>Siga as próximas instruções por sua conta em risco</strong>. Se você brickar seu pendrive, não me culpe. Mas provavelmente ele, protegido contra gravação, já está sendo uma peça inútil, então não fará muita diferença se der alguma merda.  <strong>Mesmo assim, se considere avisado.</strong>

Todos os créditos para o site <a href="http://www.techunboxed.com/2015/03/how-to-disable-write-protection-on-dell.html" target="_blank">Techunboxed</a> (inclusive, uso as imagens dele nesta postagem porque em casa não tenho um pc com Windows disponível para poder dar print nas telas do programa). Eu apenas traduzi as instruções que encontrei por lá.

Comece fazendo o download da ferramenta UPTool por <a href="https://mega.nz/#!RUsT3KhI!ItkcWEZRttdWqyTGmN1XA82t2rD7t0PQV62FdwwHPoc" target="_blank">este link</a>. Essa ferramenta faz algum tipo de formatação de "baixo-nível". Tentei encontrar alguma outra informação sobre esse software mas não encontrei. Usei por minha conta em risco e não espero que você venha me responsabilizar por algum eventual problema. Só posso dizer que deu tudo certo e o computador ainda não mostrou nenhuma janela cobrando uns bitcoins para descriptografar meus arquivos. Mas se você não quiser correr riscos, pode desistir (ou usar uma máquina virtual).

Extraia o arquivo zipado e execute o <strong>UPTool_Ver2092.exe</strong>. Você provavelmente irá se deparar com uma janela de aviso que dirá "<em>First time using this tool on this PC</em>", que significa que é a primeira vez que se roda esse software nesse (no seu) computador. Não me pergunte a razão disso.

Clique em OK e pluge seu pendrive no computador. É provável que ele o identifique com uma letra e o destaque em um tom azul como na imagem:

<img class="wp-image-1200 size-large" src="https://brenn0.files.wordpress.com/2015/11/2_dell.png?w=676" alt="2_dell" width="676" height="445" />Observe a identificação "TC58TEG6TCK (eD3.8K)

Na parte superior direita a opção "<em>By driver</em>" deve estar marcada por padrão. O tutorial que segui, instrui para se trocar essa opção por qualquer outra que não seja "<em>By driver</em>". Não sou muito fã de seguir cegamente uma coisa sem me perguntar as razões, mas queria desbloquear logo essa porra. Selecionei a opção "<em>By 8-port hub</em>" suspeitando que essas opções seriam apenas a forma do programa classificar e organizar os dispositivos reconhecidos.

Assim como nas instruções originais, vi o pendrive ser movido para a parte inferior. Observe a imagem:

<img class="wp-image-1201 size-large" src="https://brenn0.files.wordpress.com/2015/11/3_dell.png?w=676" alt="3_dell" width="676" height="445" /> O que quis dizer com "movido para a parte inferior".

Agora é hora de clicar em "<em>Load Settings</em>", selecionar, dentre os arquivos extraídos do zip que mandei baixar inicialmente, o arquivo <strong>DELL.ini</strong> e clicar em "<em>Open</em>". Pode dar uma lida no arquivo .ini se ficou curioso também.

Então clique em "<em>ReFresh</em>" para ter certeza de que o arquivo .ini foi lido e está sendo usado. O drive pode ser movido para uma diferente posição.

Para finalizar, clique no grande botão que contém os dizeres "<em>Start</em>". O que ele faz é um pouco óbvio e dispensa explicação.

O Pendrive deixará de ficar azul e irá ficar amarelo (isso na janela do software, claro), com um nível de porcentagem e a cor roxa (alguns diriam azul) o preenchendo de acordo com o avanço do processo. Observe isso na imagem:

<img class="aligncenter size-large wp-image-1202" src="https://brenn0.files.wordpress.com/2015/11/5_dell.png?w=676" alt="5_dell" width="676" height="445" />

O processo é demorado e pode levantar de 30 a 60 minutos. No fim, ele ficará verde e provavelmente liberado para gravação.

Um dos pendrives ao qual submeti esse processo, retornou um erro quando estava em mais ou menos uns 70% e começou a ser reconhecido como se não tivesse capacidade de armazenamento. Comecei a achar que tinha dado merda e o pendrive havia sido perdido, mas só para garantir, voltei ao programa, selecionei "<em>By 16-port Hub</em>" (mesmo achando que não iria influenciar em nada), carreguei o arquivo dell.ini, dei refresh e cliquei em start. O processo demorou bem menos (cerca de 15 minutos) e no fim não deu erro. O pendrive havia sido desbloqueado contra gravação com sucesso.

<img class="wp-image-1203 size-large" src="https://brenn0.files.wordpress.com/2015/11/7_dell.png?w=676" alt="" width="676" height="445" /> É isso que você verá em caso de erro.

Algumas outras pessoas comentaram que mesmo dando erro, o pendrive era desbloqueado. Não posso comentar sobre esses casos porque não vi isso acontecer nas vezes que fiz.

Agora já pode me agradecer por ter economizado 20 reais ao não ter pago por um pendrive de 8gb.


