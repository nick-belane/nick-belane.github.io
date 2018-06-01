---
layout: default
title: Por que minha rede Wi-Fi é aberta?
date: 2014-07-03 13:37
author: brennords
comments: true
categories: [Rant]
---
Nessa última semana, não lembro exatamente como, mas esbarrei com o site deste projeto: <a href="https://openwireless.org/" target="_blank">https://openwireless.org/</a>

O que ele propõe é bem contrário ao que vemos hoje.<strong> Ele sugere que você compartilhe um pedacinho da sua internet criando uma rede WiFi aberta</strong>. Isso mesmo, aberta para qualquer um por perto usar.

<a href="https://brenn0.files.wordpress.com/2014/07/open-wireless-router-firmware-download-security.jpg"><img class="size-large wp-image-1022" src="http://brenn0.files.wordpress.com/2014/07/open-wireless-router-firmware-download-security.jpg?w=676" alt="Compartilha sua internet com o mundo." width="676" height="417" /></a>

Caso você saia procurando, dificilmente encontrará redes abertas (até mesmo as de estabelecimentos como restaurantes usam uma senha para apenas compartilha-la com os consumidores).

Será que você nunca passou por uma situação de emergência em que era preciso usar a internet apenas para uma consulta rápida e isso não foi possível porque não havia nenhum bom samaritano com uma rede aberta por perto?

<!--more-->

Hoje em dia não é fácil encontrar uma rede WiFi aberta como era há alguns anos. O que muitos podem argumentar é que não deixam sua rede aberta por questões de segurança (medo de ter seu computador invadido ou que alguém use a conexão para cometer crimes). Ou alguma outra parcela também pode argumentar que prefere não dividir sua conexão com ninguém porque são elas que pagam (todos tem o direito de ser egoístas). Ou então argumentam as duas coisas.

Claro que não tiro as razões dessas pessoas, principalmente as que estão preocupadas com as questões de segurança (explico depois como é possível manter sua rede doméstica segura e também mante-la disponível para todos). Mas tenho que confessar que comprei o ideal do projeto.

Na minha opinião, não vejo nenhum mau em ter uma rede WiFi aberta. É legal poder ajudar as pessoas que passem por perto de onde moro e precisem acessar a internet. Ou então aquele novo vizinho que não ganha tanto dinheiro assim e comprou um smartphone baratinho mas não pode pagar por acesso a internet. Mesmo que provavelmente a maioria das pessoas apenas a usem para checar o Facebook e suas mensagens no Whatsapp, nunca se sabe quando alguém vai usá-la para ler um e-mail urgente ou fazer o download de algum livro.

Maaaas nem só de ideais vive o homem. Mesmo sendo algo bonito a se fazer, essa atitude também pode ser perigosa. Farei uma pequena lista dos riscos que se corre, como mitigar esses riscos, e também como evitar que sua conexão fique lenta<del> (impossibilitando a visita ao Redtube)</del> porque alguém resolveu baixar dezenas de torrents usando sua boa vontade.

1. ARP Poisoning e ataques Man-in-the-middle

Ao permitir que terceiros convivam na mesma rede que você, corre-se o risco de que algum espírito de porco tente se aproveitar. Caso esse ser consiga ter sucesso em um ataque do tipo <a href="http://www.vivaolinux.com.br/artigo/ARP-Poisoning-compreenda-os-principios-e-defendase" target="_blank">ARP Poisoning</a>, é possível que ele <strong>intercepte e até modifique as conexões de todos que estão ligados a rede</strong> (configurando o ataque denominado <a href="https://pt.wikipedia.org/wiki/Ataque_man-in-the-middle" target="_blank">Man-in-the-middle)</a>. Pode-se obter senhas, suas conversas no Facebook... Tudo que você e as outras pessoas conectadas estejam enviando e recebendo. Sem falar que também é possível a falsificação de dados, como fazer que você seja redirecionado a uma página falsa ao entrar no site do seu banco, por exemplo. Enfim, a imaginação é o limite e esse é o mais perigoso dos riscos.

<a href="https://brenn0.files.wordpress.com/2014/07/attaque_man_in_the_middle.jpg"><img class="wp-image-1017 size-full" src="http://brenn0.files.wordpress.com/2014/07/attaque_man_in_the_middle.jpg" alt="Um ataque Man-in-the-middle ilustrado. (De forma passiva, o atacante apenas escuta todas as conexões. De forma ativa, ele escuta e ainda altera as mesmas." width="495" height="314" /></a>

Obviamente não queremos que ninguém conectado a nossa rede possa fazer isso, certo?

Para evitar ataques desse tipo, o que se tem a fazer é <strong>"ensinar" ao seu roteador (ou switch) quem é quem dentro da rede</strong>. Evitarei entrar em grandes detalhes sobre o que é o protocolo ARP e como ele funciona, porque a postagem ficaria bem longa (mais do que está ficando) e fugiria um pouco do foco, mas recomendo <a href="http://www.vivaolinux.com.br/artigo/ARP-Poisoning-compreenda-os-principios-e-defendase" target="_blank">esse link aqui</a> para quem precisar saber mais.

O procedimento pode variar de roteador para roteador (ou de switch, mas daqui para frente irei falar apenas roteador, foda-se), mas o segredo é você configurar a associação ARP do rapaz.

Sinto que irei resumir bastante, mas isso aqui não é um tutorial passo-a-passo e sim algo para dar uma luz do que fazer. No caso de alguém ficar sem entender algum termo, deixarei os que achar necessário linkados para sites externos com explicações mais profundas. E ainda me faço disponível para ajudar quem queira colaborar com essa ideia de WiFi aberto e não tenha ideia de como configurar sua rede. Basta usar o formulário de contato que respondo sem demora: <a href="http://brenn0.wordpress.com/entre-em-contato/" target="_blank">http://brenn0.wordpress.com/entre-em-contato/</a>

Voltando...

Provavelmente aí na sua rede doméstica seu roteador esteja configurado com um servidor <a href="http://www.tecmundo.com.br/2079-o-que-e-dhcp-.htm" target="_blank">DHCP</a> distribuindo IPs automáticos para todos, não é? Bom, precisaremos mudar um pouco isso.

Você terá que reservar <a href="http://tecnologia.hsw.uol.com.br/questao549.htm" target="_blank">endereços IP</a>s fixos para cada dispositivo pessoal (seu notebook, desktop, celular... A porra toda). Não é algo complicado de se fazer. Ao ir nas configurações do pequeno servidor DHCP do roteador, é necessário que se conheça o <a href="http://www.tecmundo.com.br/5483-o-que-e-um-endereco-mac-e-como-fazer-para-descobri-lo-no-seu-computador-ou-smartphone.htm" target="_blank">endereço MAC</a> do dispositivo que queira dar um IP e pronto.

<a href="https://brenn0.files.wordpress.com/2014/07/1.png"><img class="wp-image-1018 size-full" src="http://brenn0.files.wordpress.com/2014/07/1.png" alt="1" width="578" height="223" /></a>

Talvez seja preciso verificar o manual do seu roteador ou, infelizmente, caso você não sabia da metade do que falei, <a href="https://duckduckgo.com/" target="_blank">pedir ajuda a alguém</a>.

Feito isso, agora é só ir nas configurações de associação ARP e dizer ao seu roteador que determinado endereço IP pertence a tal endereço MAC. Pronto, agora ele nunca mais será enganado por pessoas maldosas.

<a href="https://brenn0.files.wordpress.com/2014/07/2.png"><img class="aligncenter size-large wp-image-1019" src="http://brenn0.files.wordpress.com/2014/07/2.png?w=676" alt="2" width="676" height="255" /></a>

Ha, antes que eu esqueça, isso irá proteger apenas os dispositivos que estiverem nesta lista (os seus, no caso). As outras pessoas que virão usar seu WiFi irão ficar vulneráveis, a menos que você faça esse processo para elas também, o que é inviável caso sejam estranhos. O que podemos fazer é apenas torcer para que elas sejam inteligentes e usem conexões criptografadas como <a href="https://pt.wikipedia.org/wiki/HTTPS" target="_blank">SSL/TSL</a> e <a href="https://pt.wikipedia.org/wiki/SSH" target="_blank">SSH</a> (<strong>quem em sã consciência não faz isso em uma rede WiFi pública??</strong>).

Acredito que esse processo serve para todos roteadores baratinhos que existam por aí (como o meu). Há roteadores com mecanismos mais legais de defesa contra esse ataque. Mas, se você for o dono de um desses roteadores legais, provavelmente não vai estar lendo isso porque já deve saber o que deve e como fazer.

2. Aproveitadores baixando dezenas de torrents <del>pornográficos</del> e sugando a conexão

Isso pode ser um pé no ovo esquerdo. Seria uma maravilha se quem estivesse usando a conexão disponibilizada por você tivesse noção e pensasse "não farei isso, pode atrapalhar as outras pessoas", mas <strong>sabemos muito bem que o ser humano não é conhecido pelo seu altruísmo</strong>, então devemos tomar medidas para que alguém não estupre sua conexão.

A configuração também pode depender de roteador para roteador (cheque o manual, peça ajuda, saia fuçando em tudo). No meu caso, a forma que ele permite de controlar o uso da banda é usando uma determinada faixa de IPs. Acredito que muitos roteadores por aí também funcionem assim.

Suponho que você já tenha configurado endereços de IPs fixos para seus dispositivos, deixando o resto disponível para o uso geral. Após isso, o processo é simples: definir toda a faixa de ip restante e limitar o quanto da sua banda eles poderão usar. Depois disso não precisa se preocupar, pois sua conexão nunca irá ficar lenta por causa do uso e abuso de alguém.

<a href="https://brenn0.files.wordpress.com/2014/07/4.png"><img class="wp-image-1021 size-large" src="http://brenn0.files.wordpress.com/2014/07/4.png?w=676" alt="4" width="676" height="196" /></a> 10% da minha conexão para o povão.



3. Seus computadores e dispositivos expostos

Alguns ainda podem argumentar que, ao permitir que estranhos participem da sua rede local, uma certa camada de segurança, que é adicionada pelo roteador (pode funcionar como um firewall), é ultrapassada.

<strong>E estão certos.</strong>

Mas não é por esse motivo que seu computador irá ser invadido. Para falar a verdade, caso você tome as devidas medidas de segurança - como usar um bom anti-vírus e mantê-lo atualizado, ter um firewall, manter o sistema operacional e aplicativos atualizados, <del>não usar windows</del>... - seu computador (ou dispositivo) <strong>estará relativamente seguro</strong>.

<strong>Computadores e dispositivos pessoais não são facilmente invadidos como servidores pelo simples fato de que não precisam "se expôr"</strong>. Ao contrário de um servidor que precisa ter portas abertas, que é criado para que as pessoas se conectem a ele, um computador pessoal não é assim. É possível ter quase todas as portas fechadas e, para as que forem necessárias, manter configurações seguras. Isso fará que seja praticamente impossível que um atacante consiga acessa-lo.

Então podemos concluir que para mitigar esse risco, basta apenas tomar as medidas básicas de segurança que já citei ali em cima, tomar uma cerveja e ficar tranquilo por que ninguém irá invadir seus computadores (pera, tem a <strong>NSA</strong>) só por dividirem a mesma rede.

4. Questões legais

Nessa parte as coisas podem ficar complicadas. Não há como impedir que alguém com fins maliciosos usem sua rede para cometer crimes na internet, certo?

<strong>Mas perca o medo de ser preso no lugar de alguém.</strong>

É claro que será o SEU endereço IP que ficará registrado e, caso aconteça uma investigação, eles irão rastrear o endereço até sua casa, irão sim.

<strong>Mas provar que foi você o responsável pelo crime é uma história completamente diferente</strong>. Mesmo que seja necessário vascular seus computadores e a porra toda, é óbvio que não serão encontradas provas (se você for um bandido a história é outra).

<strong>Sem falar que ter uma rede WiFi aberta irá ser um álibi</strong> (o que pode funcionar tanto para pessoas de bem quanto para criminosos, mas não entrarei nisso). Irá ampliar a gama de suspeitos e encontra-lo será trabalho para a polícia (há maneiras de fazer isso, eles sabem). E, não esqueça, não é crime nenhum você ter sua rede aberta para quem quiser usar.

Se as coisas funcionassem dessa forma, bem que seria possível que a polícia acusasse e processasse seu provedor de internet só por terem fornecido o acesso a internet para alguém que cometeu um crime.

Para concluir, <strong>TALVEZ</strong> seja uma quebra de contrato com seu provedor de internet o fato de compartilhar sua conexão com outras pessoas. Mas é claro que somos nós que pagamos pelo serviço e deveríamos poder fazer o que quisermos. Então, meu conselho para essa parte é mandar um gostoso <a href="https://brenn0.files.wordpress.com/2014/07/foda-se.gif" target="_blank"><strong>foda-se</strong></a> para provedores que te proíbem disso.

Ps: só sei dessas coisas por causa da namorada, advogada e consultora <a href="https://twitter.com/RaizaFeitoza" target="_blank">Raíza Rodrigues</a>.

5. Tomar uma cerveja

Ok, isso não é um risco, mas eu precisava concluir com alguns comentários e queria tomar uma cerveja, então relaxe aí e vá pegar a sua também.

Percebeu como é possível compartilhar sua conexão usando uma conexão WiFi aberta e não ter nenhum problema?

Eu mantive meu <a href="http://www.palpitedigital.com/o-que-e-ssid-wireless/" target="_blank">SSID</a> separado e protegido por senha, e criei um outro chamado "Breno Bonitao - openwireless.org" só por questão de organização. Até recomendo isso também.

Agora irei acabar minha cerveja. Recomendo que você faça isso também.
