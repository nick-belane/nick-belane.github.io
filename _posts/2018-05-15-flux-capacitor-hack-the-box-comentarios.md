---
layout: default
title: Flux Capacitor [Hack The Box] Comentários
date: 2018-05-15 11:52
author: brennords
comments: true
categories: [CTFs]
---
Nem sempre sou 1337 suficiente pra rootear as máquinas do Hack The Box. Você pode comprovar esse fato dando uma olhada no meu movimentado <a href="https://www.hackthebox.eu/home/users/profile/5731" target="_blank" rel="noopener">perfil</a>. Mas não quer dizer que eu não tente, algumas vezes exaustivamente, conseguir umas flags. Foi o que aconteceu com a máquina aposentada recentemente, 12/05/2018, Flux Capacitor.

<img class="aligncenter size-full wp-image-1625" src="https://brenn0.files.wordpress.com/2018/05/htb.png" alt="" width="560" height="112" />

Eu falhei miseravelmente. Poderia dizer que completei cerca de 15-20% do exigido para obter root nesse servidor. Tentei bastante nos últimos dias após receber um e-mail avisando que a máquina seria aposentada em breve porque sabia que caso travasse em algum momento, os write-ups não demorariam para surgir e poderia descobrir exatamente onde me perdi.

O write-up que trouxe as respostas foi o do famoso IppSec. O canal do cara deve ter vídeo de todas as máquinas do HTB e é uma fonte legal de aprendizado.

https://www.youtube.com/watch?v=XLIBbkQJKuY

Agora deixe-me contar até onde cheguei e o que aprendi ao descobrir onde falhei.

Como de costume, comecei com um scan padrão do nmap: <em>nmap -v -A 10.10.10.69</em>

```Starting Nmap 7.01 ( https://nmap.org ) at 2018-05-12 22:17 EDT
NSE: Loaded 132 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 22:17
Completed NSE at 22:17, 0.00s elapsed
Initiating NSE at 22:17
Completed NSE at 22:17, 0.00s elapsed
Initiating Ping Scan at 22:17
Scanning 10.10.10.69 [2 ports]
Completed Ping Scan at 22:17, 0.24s elapsed (1 total hosts)
Initiating Connect Scan at 22:17
Scanning node1.fluxcapacitor.htb (10.10.10.69) [1000 ports]
Discovered open port 80/tcp on 10.10.10.69
NSE: Script scanning 10.10.10.69.
Initiating NSE at 22:18
Completed NSE at 22:18, 5.82s elapsed
Initiating NSE at 22:18
Completed NSE at 22:18, 0.00s elapsed
Nmap scan report for node1.fluxcapacitor.htb (10.10.10.69)
Host is up (0.23s latency).
Not shown: 999 closed ports
PORT STATE SERVICE VERSION
80/tcp open http SuperWAF
| http-methods:
|_ Supported Methods: GET HEAD
|_http-server-header: SuperWAF
|_http-title: Keep Alive

```

Nada demais além da porta 80 e seu servidor web. Hora de checar o que era oferecido via HTTP:

<img class="alignnone size-full wp-image-1620" src="https://brenn0.files.wordpress.com/2018/05/web_principal.png" alt="web_principal" width="597" height="266" />

Ok, não fazia ideia o que porra isso poderia significar. Node1... Algo com NodeJS? Acho que não.

Então dei uma sacada no código fonte a procura de algumas respostas e me deparei com um comentário que poderia ter alguma utilidade:

<img class="alignnone size-full wp-image-1621" src="https://brenn0.files.wordpress.com/2018/05/web_source.png" alt="web_source" width="705" height="339" />

Mas, ao acessar /sync, recebo um 403 na cara:

<img class="alignnone size-full wp-image-1622" src="https://brenn0.files.wordpress.com/2018/05/sync_forbidden.png" alt="sync_forbidden" width="753" height="256" />

Aí lembrei do <a href="https://www.owasp.org/index.php/Category:OWASP_DirBuster_Project" target="_blank" rel="noopener">DirBuster</a>, sempre pronto para mostrar algum caminho oculto.

<img class="alignnone size-full wp-image-1623" src="https://brenn0.files.wordpress.com/2018/05/dirbuster.png" alt="dirbuster" width="773" height="539" />

Observe que escolhi tentar adivinhar apenas os diretórios por ainda não saber se o servidor web estava rodando PHP ou algo do tipo.

<img class="alignnone size-full wp-image-1624" src="https://brenn0.files.wordpress.com/2018/05/dirbuster_resuls.png" alt="dirbuster_resuls" width="765" height="537" />

Calma lá, porque o DirBuster recebeu um <a href="https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status/200" target="_blank" rel="noopener">HTTP 200</a> de sync? E ao tentar acessar todos os outros diretórios que o DirBuster supostamente encontrou, continuei ganhando um 403 Forbidden.

Resolvi usar o curl para tentar descobrir o conteúdo da resposta que o DirBuster recebia e tive um pouco de sorte ao ter um palpite correto:

```

brenno@budweiser ~curl -v http://10.10.10.69/sync
* Trying 10.10.10.69...
* Connected to 10.10.10.69 (10.10.10.69) port 80 (#0)
GET /sync HTTP/1.1
Host: 10.10.10.69
User-Agent: curl/7.47.0
Accept: */*

HTTP/1.1 200 OK
Date: Sun, 13 May 2018 02:35:40 GMT
Content-Type: text/plain
Transfer-Encoding: chunked
Connection: keep-alive
Server: SuperWAF

20180513T04:35:40

* Connection #0 to host 10.10.10.69 left intact
```

Então descobri que com o curl era possível acessar /sync e com o parâmetro -v pude ver a requisição feita e a recebida. O que chamou atenção foi o Server: esse tal de SuperWAF.

Caso não saiba, WAF é uma sigla para <a href="https://www.gocache.com.br/waf/" target="_blank" rel="noopener">Web Application Firewall</a>. Como o nome já implica, funciona como um Firewall na aplicação Web. É capaz de filtrar as requisições com base no que o usuário escolher. Era bem provável que minha requisição usando o Firefox estivesse sendo barrada pelo Super.

Mas por qual razão?

A coisa mais óbvia que pensei foi o User-Agent. Para comprovar minha tese, foi só usar o próprio curl com o parametro -A e setar o User-Agent do Firefox:

```brenno@budweiser ~curl -A "Mozilla/5.0 (Android 4.4; Mobile; rv:41.0) Gecko/41.0 Firefox/41.0" -v http://10.10.10.69/sync
* Trying 10.10.10.69...
* Connected to 10.10.10.69 (10.10.10.69) port 80 (#0)
GET /sync HTTP/1.1
Host: 10.10.10.69
User-Agent: Mozilla/5.0 (Android 4.4; Mobile; rv:41.0) Gecko/41.0 Firefox/41.0
Accept: */*

HTTP/1.1 403 Forbidden
Date: Sun, 13 May 2018 02:54:09 GMT
Content-Type: text/html
Content-Length: 175
Connection: keep-alive


403 Forbidden</pre>
<h1>403 Forbidden

<hr />

<pre>openresty/1.13.6.1

```

E agora com um user-agent qualquer:

```brenno@budweiser ~curl -A "ai dento" -v http://10.10.10.69/sync
* Trying 10.10.10.69...
* Connected to 10.10.10.69 (10.10.10.69) port 80 (#0)
GET /sync HTTP/1.1
Host: 10.10.10.69
User-Agent: ai dento
Accept: */*

HTTP/1.1 200 OK
Date: Sun, 13 May 2018 02:56:18 GMT
Content-Type: text/plain
Transfer-Encoding: chunked
Connection: keep-alive
Server: SuperWAF

20180513T04:56:18
```

Então o WAF não gostava de navegadores.

É, mas e agora? Sync (e todos outros Sync* encontrados no DirBuster) retornava um timestamp do momento atual, não parecia ter muito espaço para hackinagens. Foi aí que a dor de cabeça começou.

Pesquisei sobre o openresty/1.13.6.1 e nada. Procurei sobre um "SuperWAF" e ainda estava perdido. Minha limitada fonte de ideias estava secando, então apelei para os <a href="https://forum.hackthebox.eu/discussion/331/fluxcapacitor" target="_blank" rel="noopener">fóruns do hack the box</a> que é onde a galera chora por umas hints e uma vez ou outra alguém comenta algo que te dá uma luz.

Foi lá que vi todos comentado sobre usar um fuzzing para fazer brute-force de determinado paramêtro. E descobri que o criador da máquina havia publicado dois (<a href="https://medium.com/secjuice/waf-evasion-techniques-718026d693d8" target="_blank" rel="noopener">1</a>, <a href="https://medium.com/secjuice/web-application-firewall-waf-evasion-techniques-2-125995f3e7b0" target="_blank" rel="noopener">2</a>) textos sobre bypass em WAFs.

Beleza, aparentemente eu tinha que encontrar um paramêtro usando força bruta e, pelo que comentaram, havia certeza que esse tal paramêtro era vulnerável a alguma forma de RCE e era preciso burlar as regras do WAF para executar comandos no servidor.

Como fazer isso? Foi aí que descobri o <a href="https://github.com/xmendez/wfuzz" target="_blank" rel="noopener">wfuzz</a>. Ele é o cara quando se trata de <a href="https://www.vivaolinux.com.br/dica/O-que-e-um-Fuzzer-em-Penetration-Testing-(Pentesting)" target="_blank" rel="noopener">fuzzer</a> (que trocadilho massa) uma aplicação web.

O funcionamento básico é simples, tipo, com o comando:

```wfuzz -w wordlist/general/common.txt --hc 404 http://testphp.vulnweb.com/parametro=FUZZ```

O wfuzz pega todos os termos de common.txt, substitui a palavra "FUZZ" da URL, faz a requisição e, com --hc 404 filtra as respostas para apenas as que não forem um 404.

A documentação do programa é extensa e se quiser saber mais dos seus possíveis usos é só dar olhada <a href="http://wfuzz.readthedocs.io/en/latest/" target="_blank" rel="noopener">neste link</a>.

Então era o wfuzz que me ajudaria a descobrir o parametro que todos comentavam. Uma pena que não era exatamente o wfuzz e sim a wordlist usada. Acabei cometendo o erro de usar apenas as wordlists padrões que já vinham no wfuzz e não encontrei o parametro. Acabei tentando outros métodos mas não focando no fato de simplesmente não estar usando a wordlist que continha o termo. Foi meu erro, foi até onde cheguei.

Agora vendo o writeup que percebi: nos fóruns do hack the box o pessoal havia comentado das wordlists encontradas <a href="https://github.com/danielmiessler/SecLists/" target="_blank" rel="noopener">neste link do github</a>, mas não dei a devida importância. Foi hoje que descobri que <a href="https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/burp-parameter-names.txt" target="_blank" rel="noopener">nesta wordlist</a> de, obviamente, nomes-de-parametros, que se encontra o que tanto procurei rodando o wfuzz. Acredite, rodei o wfuzz em dezenas de formas imaginaveis.

A partir daí era necessário burlar as regras do WAF. No write-up, ippsec, de uma forma bem interessante, mapeou as regras de filtragem do WAF usando o wfuzz para fazer requisições ao servidor passando como argumento ao parametro valores retirados de uma lista de caracteres especiais (* ' /...) que são usados para burlar filtros. Isso fez com que ele já soubesse os caracteres necessários para fazer os comandos passarem pelo WAF e executa-los no servidor.

Da execução remota para uma shell reversa trabalho ainda teve de ser feito. Por culpa da proibição de vários caracteres, foi criativo usar um shellscript hospedado como index.html no próprio computador e passar usar o curl para receber o shellscript no servidor. Não havia maneira de executar o comando diretamente por causa do WAF.

Para obter root foi necessário observar a saída do comando sudo -l e perceber que nobody podia rodar /home/themiddle/.monit como root. Esse é o tipo de coisa que quase nunca está por acaso em uma máquina do HTB. Dentro do arquivo .monit podia se ver o seguinte conteúdo:

```

#!/bin/bash

if [ "$1" == "cmd" ]; then
echo "Trying to execute ${2}"
CMD=$(echo -n ${2} | base64 -d)
bash -c "$CMD"
fi

```

É um shellscript que tenta executar um comando. Para isso, espera que o argumento passado seja um base64. Pode-se ver na terceira linha o script tentando traduzi-lo e armazena-lo em $CMD para na próxima linha executa-lo.

Logo, é só mandar um <em>echo -n bash | base64 </em>para ter um base64 da string bash, passar o argumento para o script e ter uma shell como root.

E era isso.

A lição aprendida foi: use a porra da wordlist mais apropriada para seus fins. E também aprendi e adicionei o wfuzz ao meu arsenal mental.
