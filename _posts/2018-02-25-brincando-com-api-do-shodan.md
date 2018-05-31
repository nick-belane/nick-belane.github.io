---
layout: default
title: Brincando com API do Shodan
date: 2018-02-25 11:45
author: brennords
comments: true
categories: [api, Projetos, python, shodan]
---
<p style="text-align:justify;">Se não conheces, o Shodan é um buscador que vive escaneando a internet e indexando os metadados (os banners) que são retornados pelos dispositivos que estejam aceitando conexões indiscriminadamente.</p>

<p style="text-align:justify;">Por exemplo, uma busca <a href="https://www.shodan.io/search?query=apache" target="_blank" rel="noopener">'apache'</a> vai trazer como resultado dispositivos que, ao receberem um pedido de conexão, responderam com alguma coisa que contenha o termo 'apache'. Como pode se ver, a maioria dos itens encontrados são de servidores Web rodando o (pasmem!) <a href="https://pt.wikipedia.org/wiki/Servidor_Apache" target="_blank" rel="noopener">Apache</a>.</p>

<p style="text-align:justify;">Ele não é algo novo. A maioria da galera já sabe que dá para encontrar <a href="https://www.shodan.io/explore/tag/webcam" target="_blank" rel="noopener">câmeras de segurança sem senha</a>, <a href="https://www.shodan.io/explore/tag/ftp" target="_blank" rel="noopener">ftps desprotegidos</a>, roteadores, switchs, impressoras e essas porra. Diz a lenda que se encontra até sistemas SCADA com senhas default e facilmente se destrói uma indústria nuclear com poucos cliques.</p>

<img class=" size-full wp-image-1555 aligncenter" src="https://brenn0.files.wordpress.com/2018/02/td-heavy-shit.gif" alt="TD-Heavy-Shit.gif" width="500" height="278" />

<p style="text-align:justify;">Não é a toa que, por jornalistas, ele é considerado "<a href="https://www.vice.com/pt_br/article/mgqgzx/o-shodan-e-realmente-o-mecanismo-de-busca-mais-perigoso-do-mundo" target="_blank" rel="noopener">o mecanismo de busca mais perigoso do mundo</a>".</p>

<p style="text-align:justify;">Mas chega de merda e vamos ao código.</p>

<p style="text-align:justify;">Estava eu numa daquelas tarde. Queria programar algo, não sabia bem por onde começar... Só queria passar o tempo.</p>

<p style="text-align:justify;">Então lembrei que tinha umas ideias de usar o Shodan para procurar uns roteadores de determinada empresa porque eles tinham um servidor telnet que, provavelmente, em 80% dos casos, mantinham suas senhas padrão de fábrica. Até encontrei alguns, mas o processo manual de buscar, acessar, cumprir minha missão e sair fora do roteador não parecia ser a forma mais inteligente de <del>ownar a porra toda </del>finalizar minha pesquisa, a qual me fez invadir apenas dispositivos que eu tinha permissão.</p>

<p style="text-align:justify;">Logo, como um bom homem preguiçoso, decidi que poderia codar um script para trabalhar por mim.</p>

<p style="text-align:justify;">Foi divertido e rendeu um bom aprendizado.</p>

<p style="text-align:justify;">Joguei no <a href="https://github.com/brerodrigues/shodan-search" target="_blank" rel="noopener">github</a> porque é chique e faz parecer mais importante do que realmente é. E ah, claro, documentei e fiz o código todo no english porque fica bonito para as empresas.</p>

https://gist.github.com/brerodrigues/2e014019ce835f913ee5500021ae04d6

<p style="text-align:justify;">Para usar a classe ShodanSearch, é necessário ter uma API KEY. Se consegue facilmente após se criar uma conta no Shodan e acessar a <a href="https://account.shodan.io/" target="_blank" rel="noopener">página com propriedades de sua conta</a>. Aí é só criar um objeto e começar a usar.</p>

[code language="python"]

import shodansearch

shodan_api = 'aTPPqKASuQKJi34A291P0123I'
shodan = shodansearch.ShodanSearch(shodan_api)

[/code]

&nbsp;

<p style="text-align:justify;">Criei dois métodos: '<strong>search</strong>' e '<strong>filter</strong>'.</p>

<p style="text-align:justify;">O método '<strong>search</strong>' faz uma busca no Shodan. O único parâmetro obrigatório é o '<strong>term</strong>', que é o termo a ser buscado. Os opcionais são o '<strong>page</strong>', que é a página em que a obtenção dos dados vai iniciar, '<strong>limit</strong>' limitará a quantidade de resultados e '<strong>offset</strong>' escolhe a posição, tipo, o quadragésimo item da busca, para iniciar a obtenção dos resultados.</p>

<p style="text-align:justify;">Caso não tenha lido as entrelinhas dos termos de sua conta no Shodan, não sabes que, se não for desembolsada uma grana para comprar créditos, as buscas usando sua API são limitadas há: não poderem ser iniciadas além da primeira página, limitadas aos 100 primeiros resultados, e o mais debilitante que é: não se pode usar os termos de filtragem na busca.</p>

<p style="text-align:justify;">Claro que você pode pagar $20 dólares por mês e ter 10.000 query credits para fazer umas buscas filtradas envenenadas mas, caso não precise de buscas complexas, pode usar o método '<strong>filter</strong>' que codei especificamente para esse propósito: poder filtrar os resultados como se estivesse usando os filtros do Shodan. Ou quase isso.</p>

<p style="text-align:justify;">O resultado que a API devolve vem bem organizado e não foi difícil meter um <em>for</em> e iterar o json filtrando os dados. Eu tô começando a ficar com sono, então vou deixar o <a href="https://developer.shodan.io/api" target="_blank" rel="noopener">link da documentação da API</a>. É suficiente para entender como criei o método arcaico de filtragem.</p>

<p style="text-align:justify;">Os parâmetros do método 'filter' são: '<strong>filter by</strong>', que pode ser por '<strong>port</strong>', ou seja, filtrar pelo número da porta, '<strong>protocol</strong>' que é pelo protocolo (http, telnet, ftp...), '<strong>city</strong>' e '<strong>country</strong>'. O parâmetro seguinte é o '<strong>filter_term</strong>', o qual o nome já diz tudo. E o último, e opcional, parâmetro é o '<strong>search_results</strong>', que espera receber o valor de uma busca feita previamente. Caso esse valor não seja encontrado, mais na frente o código vai buscar na propriedade search_results do objeto.</p>

Abaixo segue uma pequena implementação de minha classe:

https://gist.github.com/anonymous/8776c55f1ff210717e3fba1ad80c1a1d

O código acima vai procurar pelo termo "NET" e depois filtrar os usem o protocolo FTP. Logo depois, ele printa o banner do servidor encontrado e o endereço IP do mesmo.

E rodando o script acima, o resultado é esse:

https://gist.github.com/anonymous/40e8e2c8a191980acd2bc357bc046b3c

Obviamente censurei os endereços dos servidores FTP porque estou velho demais para ser processado por uma merda dessas. Nunca se sabe.

E é isso aí.
