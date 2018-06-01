---
layout: default
title: Derrubando a PSN e a Xbox Live
date: 2015-01-11 00:22
author: brennords
comments: true
categories: [Hacking]
---
Como você talvez deva ter ouvido falar, no natal do ano passado, o grupo <a href="http://www.gamevicio.com/i/noticias/203/203560-grupo-lizard-squad-ameaca-desligar-o-xbox-live-no-natal-para-sempre/" target="_blank">Lizard Squad</a> deixou vários jogadores de video-game frustrados ao deixar off-line a <a href="http://br.playstation.com/psn/" target="_blank">Playstation Network</a> e a <a href="http://www.xbox.com/pt-BR/Live/Home" target="_blank">Xbox Live</a>.

Maior falta de consideração com a rapaziada, certo?

<a href="https://brenn0.files.wordpress.com/2015/01/leadls.jpg"><img class="  wp-image-1068 aligncenter" src="https://brenn0.files.wordpress.com/2015/01/leadls.jpg?w=300" alt="LeadLS" width="567" height="305" /></a>


Era de se supôr que eles deviam ter uma enorme e poderosa <a href="http://www.tecmundo.com.br/spyware/2330-o-que-sao-bots-e-botnets-.htm" target="_blank">botnet</a> para conseguir tal feito. O que foi confirmado pelos mesmos ao confessar que toda a ação era uma <a href="http://meiobit.com/305919/lizard-squad-psn-xbox-live-ferramenta-ataques-ddos-paga/" target="_blank">jogada de marketing</a> para demonstrar o poder da botnet que eles tinham.

Mas a parte interessante é que essa botnet não é formada por computadores pessoais como de costume. Ela foi formada por roteadores.

<!--more-->

Claro que essa não é uma técnica nova, super ultra mega fodona, mas ao <a href="http://krebsonsecurity.com/2015/01/lizard-stresser-runs-on-hacked-home-routers/" target="_blank">ler de forma um pouco mais detalhada</a> sobre como a botnet do grupo agia, percebi as vantagens e facilidades de se montar uma botnet que tenha como alvo atacar e usar roteadores.

O código malicioso que transforma roteadores que rodam Linux em zumbis é uma variação do malware conhecido como "Linux.BackDoor.Fgt.1".

Após infectar e ter o roteador sob controle, é possível usa-lo para ataques de Ddos do tipo <a href="http://www.seginfo.com.br/ataques-de-amplificacao-de-dns-e-recomendacoes-cert-br-via-nelsonmurilo-e-certbr/" target="_blank">amplificação de DNS</a>, <a href="http://www.vivaolinux.com.br/artigo/BSD-Sockets-em-linguagem-C?pagina=11" target="_blank">UDP Flood</a> e <a href="https://pt.wikipedia.org/wiki/SYN_Flood" target="_blank">SYN Flood</a>. É possível também usá-lo para escanear faixas de endereços IPs à procura de mais dispositivos e, caso encontre e consiga estabelecer uma conexão, ele tenta se conectar via telnet e usar uma lista de senhas (normalmente as padrão usadas pelos fabricantes: admin/admin) para se logar. Caso tenha sucesso nesse ataque de adivinhação de senha, o malware instrui o dispositivo invadido a fazer download e executar um script que fará o download do código malicioso apropriado para aquele sistema.

E foi dessa forma que o grupo dos lagartos conseguiu montar uma botnet poderosa o suficiente para foder com a PSN e a Live ao mesmo tempo.

Ha, e apesar de roteadores serem um alvo mais fácil, nada impede que se infecte desktops ou outro tipo de dispostivo (camêras de segurança que rodem linux, por exemplo).

Para evitar de ter sua banda roubada para um grande ataque de negação de serviço que me impeça de jogar Call Of Dutty, simplesmente evite usar senhas padrão. Se for usuário doméstico, evite ter ativo o acesso remoto para o seu dispositivo. Essas são medidas básicas de segurança equivalentes a trancar a porta do carro.

Deixo aqui uma analíse bem mais técnica do malware (em russo, usei o google mesmo): <a href="http://vms.drweb.com/virus/?i=4242198" target="_blank">http://vms.drweb.com/virus/?i=4242198</a>

E a fonte master desse post com mais algumas informações (em inglês): <a href="http://krebsonsecurity.com/2015/01/lizard-stresser-runs-on-hacked-home-routers/" target="_blank">http://krebsonsecurity.com/2015/01/lizard-stresser-runs-on-hacked-home-routers/</a>
