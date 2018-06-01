---
layout: default
title: DHCP Starvation - Ataque de negação de serviço em rede local
date: 2015-01-24 21:47
author: brennords
comments: true
categories: [Hacking]
---
Esse post ensina a basicamente cometer um ato de <a href="http://desciclopedia.org/wiki/Vandalismo" target="_blank">vandalismo</a>. Então seja responsável e, caso faça isso em alguma rede que não lhe pertença, esqueça que foi esta postagem que te ensinou. E, claro que preciso dizer, esse conhecimento que passo agora deve ser usado para fins pacíficos e tem a intenção de mostrar como se proteger ao exibir a forma que ocorre o ataque. Blá, blá, bla.

Então vamos lá.

<a href="https://brenn0.files.wordpress.com/2015/01/keep-calm-its-just-a-ddos-3.png"><img class="  wp-image-1090 aligncenter" src="https://brenn0.files.wordpress.com/2015/01/keep-calm-its-just-a-ddos-3.png?w=257" alt="keep-calm-its-just-a-ddos-3" width="437" height="507" /></a>

Antes de tudo, o que é o <strong>DHCP</strong>?

<!--more-->

Agindo na <a href="http://www.youtube.com/watch?v=oMZHndVSF3s" target="_blank">camada de aplicação do TCP/IP</a>, o <strong>Protocolo de Configuração Dinâmica de Endereços de Rede</strong> (em inglês <em>Dynamic Host Configuration Protocol</em>, formando a sigla DHCP) é responsável pela distribuição automática e gerência de <a href="https://pt.wikipedia.org/wiki/Endere%C3%A7o_IP" target="_blank">endereços ip</a> em uma rede.

De uma forma simplificada, pode-se dizer que o seu funcionamento consiste nessas etapas:

<strong>1.</strong> Determinado computador é ligado dentro da rede e precisa de um endereço ip válido e único e outras informações para poder se comunicar (estamos supondo que suas configurações de IP não são estáticas e que, claro, há um servidor DHCP na rede). Então essa máquina irá enviar um pacote <a href="http://www.freesoft.org/CIE/RFC/2131/23.htm" target="_blank"><em>DHCPDISCOVER</em></a> do tipo <a href="https://pt.wikipedia.org/wiki/User_Datagram_Protocol" target="_blank">UDP</a> em broadcast (todos na rede recebem, mas apenas o servidor responde).

<strong>2.</strong> O servidor DHCP responde com um pacote <a href="http://www.omnisecu.com/tcpip/dhcp-dynamic-host-configuration-protocol-messages.php" target="_blank"><em>DHCPOFFER</em></a> que oferece ao cliente um determinado ip e outras informações como máscara e gateway.

<strong>3.</strong> Então, um <a href="http://www.omnisecu.com/tcpip/dhcp-dynamic-host-configuration-protocol-messages.php" target="_blank"><em>DHCPREQUEST</em></a> é enviado ao servidor pelo cliente com a função de comunicar que aceita o ip e as informações oferecidas pelo servidor.

<strong>4.</strong> Enviando um pacote <em><a href="http://www.omnisecu.com/tcpip/dhcp-dynamic-host-configuration-protocol-messages.php" target="_blank">DHCPACK</a></em> como resposta, o servidor confirma e atribui o determinado endereço ip ao endereço MAC do cliente.

<a href="https://brenn0.files.wordpress.com/2015/01/dhcp.jpg"><img class="aligncenter size-full wp-image-1087" src="https://brenn0.files.wordpress.com/2015/01/dhcp.jpg" alt="dhcp" width="400" height="390" /></a>

Tentei não entrar em tantos detalhes e acabar fugindo do ponto central, mas se quiser saber mais, segue alguns links:
<a href="http://support.microsoft.com/kb/169289/pt-br" target="_blank">http://support.microsoft.com/kb/169289/pt-br</a>
<a href="http://www.rnp.br/newsgen/9911/dhcp.html" target="_blank">http://www.rnp.br/newsgen/9911/dhcp.html</a>

Agora vamos entender como funciona esse <a href="https://pt.wikipedia.org/wiki/Ataque_de_nega%C3%A7%C3%A3o_de_servi%C3%A7o" target="_blank">ataque de negação de serviço</a> que é conhecido como <strong>DHCP Starvation</strong>.

Como em computação a criatividade não tem limites, alguém pensou: "se o servidor DHCP atribui um ip a cada endereço MAC diferente, o que aconteceria se, usando vários endereços MAC (<em><a href="https://br.answers.yahoo.com/question/index?qid=20070925101855AAg064j" target="_blank">MAC Spoofing</a></em>), eu enviasse toneladas de requisições fazendo com que o servidor atribua vários ips a endereços MAC fantasmas?"

Esse alguém que pensou nisso foi lá e fez. E viu o servidor DHCP ficar saturado e sem nenhum endereço ip disponível para atribuir a clientes que fizessem requisições legítimas.

E ninguém mais conseguiu se conectar a rede enquanto esse alguém continuou com o ataque.

Agora vamos ver na prática usando a ferramenta <a href="http://www.yersinia.net/download.htm" target="_blank">Yersinia</a> (já incluida no <a href="https://www.kali.org/" target="_blank">Kali Linux</a>) que permite cometer um ataque desse tipo com poucos cliques no mouse. Hora de descrever os passos no estilo receita de bolo:

<strong>1.</strong> Após instalado, inicie o Yersinia com o comando <em>yersinia -G</em>.

<strong>2.</strong> É isso aí, há até uma interface gráfica. Coisa de criança.

<strong><a href="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_33_351.png"><img class="aligncenter size-large wp-image-710" src="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_33_351.png?w=700" alt="Screenshot from 2014-01-14 15_33_35" width="700" height="400" /></a></strong>

<strong> 3.</strong> Agora basta clicar em <em>Launch Attack</em>, DHCP e selecionar a opção <em>sending DISCOVER packet</em>.

<a href="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_34_58.png"><img class="aligncenter size-large wp-image-711" src="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_34_58.png?w=700" alt="Screenshot from 2014-01-14 15_34_58" width="700" height="401" /></a>

<strong>4.</strong> Clique em OK e observe a quantidade absurda de pacotes enviados. Essa é a hora que o servidor DHCP deve estar enlouquecendo e não conseguindo atender as requisições de outros usuários da rede.

<a href="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_35_16.png"><img class="aligncenter size-large wp-image-712" src="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_35_16.png?w=700" alt="Screenshot from 2014-01-14 15_35_16" width="700" height="394" /></a>

<strong>5.</strong> Clicando em <em>List Attacks</em> você terá a opção de para-lo.

<a href="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_39_15.png"><img class="aligncenter size-large wp-image-713" src="http://brenn0.files.wordpress.com/2014/01/screenshot-from-2014-01-14-15_39_15.png?w=700" alt="Screenshot from 2014-01-14 15_39_15" width="700" height="400" /></a>

<strong>6.</strong> Beba uma cerveja.
