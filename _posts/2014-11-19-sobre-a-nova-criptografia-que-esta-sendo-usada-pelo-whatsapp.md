---
layout: default
title: Sobre a nova criptografia que está sendo usada pelo Whatsapp
date: 2014-11-19 14:02
author: brennords
comments: true
categories: [Rant]
---
Ontem, enquanto bebia cerveja e vagava pela internet ao som dessa inspiradora <a href="https://www.youtube.com/watch?v=FoUWHfh733Y" target="_blank">música</a>, dei de cara com essa notícia: "<a href="http://g1.globo.com/tecnologia/noticia/2014/11/whatsapp-adota-criptografia-para-proteger-mensagens-em-transito.html" target="_blank">WhatsApp adota criptografia para proteger mensagens em trânsito</a>".

Com pouca confiança, fui verificar que criptografia era essa. Então, logo após ler o início da notícia, fiquei surpreso com o que dizia:

<blockquote>Uma ferramenta que evita que as mensagens enviadas pelo aplicativo WhatsApp sejam espionadas foi anunciada nesta terça-feira (18) por empresas vinculadas ao setor. Apoiado por Edward Snowden, o projeto criptografa todo o conteúdo trocado pelo app enquanto ele viaja pela internet.

A empresa Open Whisper Systems anunciou uma aliança com o Facebook, dono do WhatsApp, com a finalidade de usar um protocolo de textos seguros (TextSecure) para codificar mensagens em trânsito e escondê-las de olhares indiscretos. O WhatsApp confirmou o anúncio à agência France Presse, mas se recusou a fazer mais comentários.</blockquote>

Hum, TextSecure? Open Whisper Systems? Apoiado por Edward Snowden? Era difícil acreditar ao ver isso vindo de uma companhia que foi <a href="http://g1.globo.com/economia/negocios/noticia/2014/10/preco-de-compra-do-whatsapp-pelo-facebook-sobe-us-22-bilhoes.html" target="_blank">recentemente comprada pelo Facebook.</a>

Então, resolvi pesquisar para saber se era realmente possível confiar nessa criptografia.

<!--more-->

Descobri que a implementação da <a href="https://whispersystems.org/about/" target="_blank">Open Whisper Systems</a> é sim coisa séria. Que são eles os desenvolvedores do famoso (e open source) aplicativo <a href="http://info.abril.com.br/downloads/android/textsecure" target="_blank">TextSecure</a> e que realmente ganharam a <a href="http://securitywatch.pcmag.com/security/321511-snowden-to-sxsw-here-s-how-to-keep-the-nsa-out-of-your-stuff" target="_blank">indicação do Edward Snowden</a>.

Depois de confirmar os fatos, bastava saber como tudo isso iria funcionar em conjunto com o Whatsapp.

O protocolo <a href="https://github.com/WhisperSystems/TextSecure/wiki/ProtocolV2" target="_blank">TextSecure</a> é baseado no <a href="https://en.wikipedia.org/wiki/Off-the-Record_Messaging" target="_blank">OTR</a>. O que significa que faz o uso de uma forma de encriptação "<a href="https://en.wikipedia.org/wiki/End-to-end_encryption" target="_blank">end-to-end</a>", ou seja, as mensagens são criptografadas no dispositivo da pessoa que envia e só são descriptografadas no dispositivo da pessoa que recebe, fazendo com que seja praticamente impossível para quem estiver no meio da conexão (provedores de internet e até mesmo a própria empresa) tenha acesso as informações trocadas. A explicação técnica sobre o funcionamento do protocolo é bem foda e longa demais para esse post. Caso se interesse, há esse link onde eles contam tudo em detalhes e até as diferenças fundamentais entre o TextSecure e o OTR: <a href="https://whispersystems.org/blog/advanced-ratcheting/" target="_blank">https://whispersystems.org/blog/advanced-ratcheting/</a>

O sistema de criptografia está disponível por enquanto apenas para a versão do Android e não funciona para conversas em grupo e nem as mídias trocadas durante as conversas mas, <a href="https://whispersystems.org/blog/whatsapp/" target="_blank">de acordo com os desenvolvedores</a>, em breve o sistema funcionará em todos dispositivos e formas de mensagens.

Foi uma surpresa para todos ver uma empresa que tem o Facebook como sua dona dar um passo desse tamanho para aumentar a privacidade dos usuários. Enquanto lia os comentários de vários sites que noticiavam o quase incoerente evento, vi bastante ceticismo. Será mesmo que dá para confiar no Whatsapp e sua nova forma de criptografia?

Na minha humilde <del>e bêbada</del> opinião, pode se acreditar sim no novo sistema de criptografia. Mas talvez não da forma que muita gente possa definir confiança, afinal, o Whatsapp ainda é um aplicativo de código fechado e claro que eles podem implementar um pequeno backdoor para o caso de funcionários da NSA queiram saber sobre a possível infidelidade de seus cônjugues. Mas isso é só uma teoria conspiratória de quem desconfia de tudo de uma forma muito saudável (essa frase é para você mesmo, <a href="https://twitter.com/RaizaFeitoza" target="_blank">Raíza Rodrigues</a>).

Não podemos esquecer também que privacidade nas comunicações enquanto elas não são feitas de forma anônima é inútil para muita gente, como jornalistas por exemplo.

Mas mesmo que o pior cenário seja real e houvesse um backdoor ou coisa do tipo que permitisse que a companhia acessasse as informações, ainda estaríamos protegidos contra qualquer um que tente farejar nossas conexões (provedores de internet, governos autoritários, parceiros ciumentos...) e isso é obviamente uma coisa boa para a privacidade.

Ainda vi muitas pessoas comentando que essa ação não combina muito com o "modelo de negócios" do Facebook (gravamos tudo e monetizamos a porra toda) e por isso não se podia confiar. Mas essas pessoas esqueceram que são apenas o conteúdo das conversas que serão criptografadas. Não vamos esquecer o valor dos metadados dos seus usuários para uma empresa como o Facebook.

E dos milhares de comentários que vi, esse foi o melhor:

<a href="https://brenn0.files.wordpress.com/2014/11/lol.png"><img class="size-medium wp-image-1051" src="https://brenn0.files.wordpress.com/2014/11/lol.png?w=300" alt="Bom saber que agora minhas piadas estão encriptadas e seguras." width="300" height="47" /></a>


