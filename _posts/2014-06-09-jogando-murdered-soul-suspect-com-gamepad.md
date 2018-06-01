---
layout: default
title: Jogando Murdered: Soul Suspect com gamepad
date: 2014-06-09 14:51
author: brennords
comments: true
categories: [Emulador Gamepad, Hacking, Murdered: Soul Suspect com Gamepad, Murdered: Soul Suspect Controle, Problema Murderd: Soul Suspect, Tutorial Murdered: Soul Suspect, x360ce Murdered: Soul Suspect]
---
Essa postagem vai para meus manos pobres que, assim como eu, não puderam pagar <a href="http://www.walmart.com.br/produto/Games/Acessorios-Xbox-360/Microsoft/252729-Controle-sem-Fio-XBox-NSF-00023-Preto-Microsoft" target="_blank">189 reais em um controle original no estilo do xb0x 360 da querida Microsoft.</a>

Alguns jogos tem o chato problema (ou implicância) com controles USB "não originais" e comprados em uma <a href="http://www.kabum.com.br/cgi-local/site/produtos/descricao.cgi?codigo=37652&amp;origem=52&amp;utm_source=GOOGLE-SHOPPING&amp;utm_medium=COMPARADOR&amp;gclid=CNa--7uM7b4CFavm7AodrSIAFg" target="_blank">lojinha qualquer por 20 reais</a>. <a href="http://murdered.com/" target="_blank"><strong>Murdered: Soul Suspect</strong></a> é um deles. Mesmo com o controle conectado e funcionando, o jogo o ignora. Preconceito com as origens humildes do mesmo, eu suponho.

Maaas, como a necessidade é uma das percursoras da criatividade e, após pagar <a href="http://store.steampowered.com/app/233290/" target="_blank">85 reais pelo jogo</a> na Steam, seria uma merda não poder jogar largado na cama usando meu controle de sempre. Então fui na busca de alguma solução.

<a href="https://brenn0.files.wordpress.com/2014/06/murdered_soul_suspect_artwork_logo.jpg"><img class="aligncenter size-large wp-image-955" src="http://brenn0.files.wordpress.com/2014/06/murdered_soul_suspect_artwork_logo.jpg?w=676" alt="Murdered_Soul_Suspect_Artwork_Logo" width="676" height="710" /></a>

<!--more-->

O jogo é bastante novo: foi lançado no dia 5 de junho do ano atual (2014) e no dia 7 do mesmo mês eu já havia adquirido minha cópia por ter curtido muito a ideia por trás dele. Isso, claro, fez com que minhas buscas no Google não me trouxessem nenhum resultado satisfatório. Apenas encontrei mais uma dúzia de neguinhos com o mesmo problema que eu.

Quando o assunto envolve computação ou tecnologia em geral, <strong>se dar por vencido não é uma opção</strong> (copiei essa frase da parede de um bar) e, como já havia solucionado um problema parecido para jogar GTA IV, imaginei que a solução era possível. Só precisava descobrir como.

Obviamente, meu primeiro passo foi usar o <a href="http://www.4shared.com/rar/aiIxpnVG/XInput_Test__Emulador_de_Contr.html?locale=pt-BR" target="_blank">mesmo método que usei com o GTA  IV</a>. Entrar em mais detalhes seria inútil, pois o resultado foi: porra nenhuma.

<a href="https://brenn0.files.wordpress.com/2014/06/large-2149509.jpg"><img class="size-large wp-image-956" src="http://brenn0.files.wordpress.com/2014/06/large-2149509.jpg?w=676" alt="Hora de acender um cigarro e pensar..." width="676" height="380" /></a>

Eu já sabia de que precisava de algum programa que pegasse as entradas do meu controle e fizesse o sistema acreditar que elas vêm de um controle Microsoft. Coçando a cabeça, ainda sem entender direito o motivo do meu programa antigo não ter funcionado e pesquisando no Google variadas formas de fazer isso, encontrei o <a href="https://code.google.com/p/x360ce/" target="_blank"><strong>x360ce</strong>. </a>

Ele basicamente promete fazer o mesmo que o antigo programa que uso para jogar GTA IV, mas há um porém essencial: bibliotecas tanto de 32 bits, quanto <strong>64</strong>.

Claro que eu joguei no lixo uma enorme quantidade de tempo apanhando para fazer funcionar, né? Mas a razão disso foi apenas falta de atenção.  <strong>Eu havia deixado passar o fato de Murderd: Soul Suspect ser um jogo feito para processadores 64 bits</strong>. Era aí que morava a razão pela qual o emulador antigo não funcionava: eu estava sendo estúpido e usando uma versão de 32 bits.

Esse foi o momento eureka.

Segue agora um curto tutorial do que fiz:

<ol style="text-align:justify;">
    <li>Baixe o executável do <a href="https://x360ce.googlecode.com/files/x360ce.App-2.1.2.191.zip" target="_blank">x360ce</a> e a <a href="https://x360ce.googlecode.com/files/x360ce_lib64_r848_VS2010.zip" target="_blank">biblioteca de 64-bits</a> e extraia tudo na mesma pasta, claro.</li>
    <li>Na primeira vez que o abrir (<strong>com seu controle USB conectado</strong>), você verá uma caixa de aviso em inglês dizendo que o arquivo de configuração "x360ce.ini" não foi encontrado. O programa dará as opções de criar automaticamente esse arquivo ou não. Claro que você deve clicar em "<strong>yes</strong>" e seguir para o próximo passo.</li>
    <li>Provavelmente o programa irá detectar seu controle e exibirá uma nova janela. Escolha a primeira opção, marque a caixinha e clique em Next. Isso irá fazer com o que o programa busque as melhores configurações para o seu controle.</li>
    <li>Caso você seja sortudo como eu, o programa não irá encontrar nenhum arquivo de configuração disponível. Clique em Finish de qualquer forma e prossiga.</li>
    <li>Agora essa parte pode ser complicada. Se seu controle não foi reconhecido e configurado automaticamente, você terá que fazer tudo na mão. Mas não se assuste com todas as abas de configurações e etc.</li>
    <li>Após umas pesquisas pelo Google, descobri que, caso você não esteja usando algo esdrúxulo como uma guitarra para usar de controle, pode usar como modelo de configuração algum dos disponíveis no programa. Isso realmente facilitou minha vida, porque eu já estava meio perdido na aba "Advanced". No meu exemplo, usei o "<strong>Logithec Cordless Rumblepad 2</strong>". Você pode fazer o mesmo clicando na caixa de listagem na parte de baixo do x360ce, selecionando essa opção e clicando em "<strong>Load</strong>". A única coisa que precisei mudar foi a ordem dos botões Y, X, B, e A que estavam trocados.</li>
    <li>Após trocar a ordem dos botões, cliquei em "<strong>Save</strong>" lá na parte de baixo para não ter que fazer isso sempre. Recomendo que você também salve suas configurações.</li>
    <li>E agora é a hora da verdade. Selecione todos os arquivos do x360ce e copie para dentro da pasta do executável do Murderd: Soul Suspect (...<strong>Murdered - Soul Suspect\Binaries\Win64</strong>). O execute e depois execute o jogo. Se tudo, como minha vó diria, estiver na paz de deus, seu controle irá funcionar como sempre.</li>
</ol>

Agora é só morrer, resolver crimes, exorcizar demônios e <a href="http://www.jeuxcapt.com/upload/module_images/1393496090_murdered-soul-suspect-jeuxcapt-2.jpg" target="_blank">assustar criancinhas</a>.
