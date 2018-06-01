---
layout: default
title: Unfinished business [Pragyan CTF 2018] - write up
date: 2018-03-07 12:00
author: brennords
comments: true
categories: [CTFs]
---
Acredito que esse desafio era o primeiro ou segundo da categoria Web no Pragyan. Não foi nem um pouco complexo porque, se fosse, provavelmente eu não estaria fazendo um write up já que ainda sou mais noob nos challs de web do que nos que não me considero tão noob assim.

A descrição do bagulho era a seguinte:

<blockquote>
There was a miscellaneous platform being built for various purposes, but it had to be shelved halfway through.
Wanna check it out? Here is the link: http://128.199.224.175:25000/
Note: Use your Pragyan CTF credentials to log in.
</blockquote>

Eles disseram que havia uma plataforma criada para propósitos variados mas que havia sido arquivada no meio do seu desenvolvimento e então passam o link para darmos uma olhada. Também disseram que se usasse as credenciais do próprio ctf para poder logar.

A cara inicial do site era essa:

<img class="alignnone size-full wp-image-1566" src="https://brenn0.files.wordpress.com/2018/03/1.png" alt="1" width="1366" height="740" />

Um form de autenticação aparentemente normal, se não contarmos com a checkbox que diz "Sou administrador".

Iniciei meu <a href="https://pt.wikipedia.org/wiki/Burp_Suite" target="_blank" rel="noopener">Burp</a> e <a href="https://support.portswigger.net/customer/portal/articles/1783066-configuring-firefox-to-work-with-burp" target="_blank" rel="noopener">configurei o mesmo e meu firefox</a> para poder interceptar o tráfego http e analisar essa tal plataforma em busca de flags.

Tentei logar usando valores aleatórios e uns óbvios sql injections. Nada funcionou e nada parecia fora do lugar nas requisições. Então usei minhas credenciais para o CTF e loguei sem marcar a caixinha "I'm admin".

<img class="alignnone size-full wp-image-1567" src="https://brenn0.files.wordpress.com/2018/03/2.png" alt="2" width="1366" height="740" /> E essa era a tela de login bem sucedido.

A mensagem dizia que o painel de controle de usuários da plataforma estava sendo desenvolvido e mandava voltar mais tarde.

Dei uma sacada no código fonte da página, nas requisições pelo burp e nada vi fora do ordinário.

Sem demora, voltei a página de login, repeti minhas credenciais do ctf mas dessa vez marquei a checkbox "<em>I'm admin</em>".

A única mudança na requisição <em>POST</em> feita era o parâmetro <em>is_admin=on</em>. Nada que realmente saltasse aos olhos.

Interceptando tudo pelo Burp, vi uma requisição <em>GET</em> inocente sendo feita para a página <strong>/admin.php</strong>, mas não achei anormal.

<img class="alignnone size-full wp-image-1568" src="https://brenn0.files.wordpress.com/2018/03/3.png" alt="3.png" width="1000" height="740" />

Após dar um '<em>Forward</em>' nessa requisição, vi que era feita outra logo em seguida para buscar a página /<strong>unavailable.php</strong>.

<img class="alignnone size-full wp-image-1569" src="https://brenn0.files.wordpress.com/2018/03/4.png" alt="4." width="1000" height="740" /> Tirei altos prints para deixar a porra do post bonito. E agora vou usar todos mesmo.

E no firefox, a mensagem que surgiu foi a seguinte:

<img class="alignnone size-full wp-image-1570" src="https://brenn0.files.wordpress.com/2018/03/5.png" alt="5" width="1366" height="740" />

Que fala quase a mesma merda da primeira mensagem, só que dessa vez direcionada ao admin.

Mas e aquela <em>GET</em> para <strong>/admin.php</strong>? O que tinha lá? Pelo visto fui redirecionado direto para unavailable.php. Então ela tem algo a esconder.

E tinha mesmo. O Burp é uma mão na roda para challs de Web e estou me habituando a usa-lo de forma mais completa. Indo além da tab '<em>Proxy</em>'. Foi assim que, verificando o sitemap montado pelo programa, pude ver a página admin.php e a requisição que fiz para a mesma. Assim como a resposta que ela deu:

<img class="alignnone size-full wp-image-1571" src="https://brenn0.files.wordpress.com/2018/03/flag.png" alt="flag" width="1000" height="740" />

Ela me deu a flag! O único motivo de não conseguir ver ao tentar acessar /admin.php é que a resposta para a requisição usa o <a href="https://pt.wikipedia.org/wiki/HTTP_302" target="_blank" rel="noopener">código 302 do HTTP</a>, que diz ao navegador: "Hey, esta página foi movida temporariamente desse local para /unavailable.php". E aí o navegador segue alegremente para a página que foi indicada.
