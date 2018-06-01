---
layout: post
title: Descobrindo as senhas dos amigos
date: 2018-04-14 15:45
author: brennords
comments: true
categories: [Hacking, Projetos, python]
---
<p style="text-align:justify;">Esta ideia não é original e o link para o conteudo que me deu essa ideia já se perdeu no limbo que é meu histórico de navegação.</p>

<p style="text-align:justify;">É algo simples e não tão eficaz para descobrir a senha de alguem, mas o que me inspirou a codificar foi o fato do autor original ter decidido não compartilhar seus scripts. Eu queria ver o bagulho funcionando e publicar na internet para causar o caos e desespero.</p>

<p style="text-align:justify;">Há uns meses atrás, <a href="https://medium.com/4iqdelvedeep/1-4-billion-clear-text-credentials-discovered-in-a-single-database-3131d0a1ae14" target="_blank" rel="noopener">surgiu esse banco de 1.4 bilhões de credenciais</a> compiladas desses leaks que acontecem uma vez ou outra. No arquivo há contas advindas de vazamentos de sites como Linkedin, Badoo, Twitter, Sony e outros. É um torrent de 41gb (magnet:?xt=urn:btih:7ffbcd8cee06aba2ce6561688cf68ce2addca0a3&amp;dn=BreachCompilation&amp;tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A80&amp;tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969&amp;tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969&amp;tr=udp%3A%2F%2Fglotorrents.pw%3A6969&amp;tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337) organizado alfabeticamente a acompanhado de scripts para procurar nos arquivos a conta de alguém com base no email e até importar novos leaks.</p>

<img class="alignnone size-full wp-image-1579" src="https://brenn0.files.wordpress.com/2018/04/breac.png" alt="breac" width="475" height="40" />

<p style="text-align:justify;">Não é segredo que as pessoas costumam reutilizar a mesma senha em todo lugar, desde a conta bancária ao site fundo de quintal de joguinhos online. E é aí que elas podem se foder.</p>

<p style="text-align:justify;">De nada adianta ter uma ótima senha se você usa-la em todos os lugares. É como usar a mesma chave para sua casa, seu carro, sua caixa postal e seu cofre. E ter várias cópias dessa chave guardadas em lugares variados. Basta perder uma dessas únicas chaves e você dá acesso a porra toda. Não parece  inteligente quando se põe de forma tão pragmática, não é?</p>

<p style="text-align:justify;">Quando se tem acesso a esses vazamentos, a primeira coisa que eu, ou outra pessoa má intencionada, faria, é verificar se sua mesma senha vazada é a que usas no email. Porque normalmente acesso ao email é acesso a todas as contas associadas a ele. Vai ser uma boa dor de cabeça. Vai ser melhor ainda se você usar essa mesma senha nos serviços da empresa onde trabalha.</p>

<p style="text-align:justify;">Para os meus testes, peguei o arquivo da minha agenda e escrevi um script para extrair apenas os endereços de emails:</p>

https://gist.github.com/brerodrigues/395c25ae2f11287914b138b981ffed50

Rápido e eficiente:

<img class="alignnone size-full wp-image-1580" src="https://brenn0.files.wordpress.com/2018/04/emails.png" alt="emails" width="794" height="217" />

<p style="text-align:justify;">Com a lista de emails dos meus chegados em mãos, era o momento de tentar pegar as senhas.</p>

<p style="text-align:justify;">Existe um site chamado h<a href="https://haveibeenpwned.com/" target="_blank" rel="noopener">ttps://haveibeenpwned.com/</a> que verifica se determinado email já foi encontrado em algum dump de credenciais. Ele oferece uma <a href="https://haveibeenpwned.com/API/v2" target="_blank" rel="noopener">API</a> que usei para checar quais emails da minha lista já tinham tido alguma credencial vazada.</p>

<p style="text-align:justify;">O script responsável por isso não foi de minha autoria. Fiz algumas modificações que julguei necessárias mas mantive os créditos.</p>

https://gist.github.com/brerodrigues/92ffcb8a25b55bc78d5176cbd11d4671

Funciona perfeitamente:

<img class="alignnone size-full wp-image-1581" src="https://brenn0.files.wordpress.com/2018/04/haveibeenpwed.png" alt="haveibeenpwed" width="687" height="104" />

<p style="text-align:justify;">Ok, mas saber quem teve sua credencial vazada não é útil para nossos fins. Útil mesmo é saber a credencial.</p>

<p style="text-align:justify;">Então, criei um script que usa o módulo haveibeenpwned para checar se o email já foi associado a algum leak. Caso tenha sido, chama-se o script <em>query.sh</em> para buscar a credencial no dump.</p>

https://gist.github.com/brerodrigues/5363502ed49e4febcf5fbc5942d739f5

Agora com tudo automatizado, foi só mandar rodar e assistir.

<img class="alignnone size-full wp-image-1582" src="https://brenn0.files.wordpress.com/2018/04/search_password.png" alt="search_password" width="655" height="375" />

E algumas senhas já começaram a surgir.

<p style="text-align:justify;">60% das pessoas na minha agenda não foram encontradas nos leaks e umas 15% das que foram não tinham senhas expostas nos leaks que baixei. Não cheguei a testar as senhas porque estaria confessando um crime se dissesse que testei, mas foi uma experiência divertida escrever e ver o código funcionando. A diversão mora no processo e não na recompensa, já dizia alguma imagem inspiracional aleatória publicada no instagram de alguém.</p>

<img class="alignnone size-full wp-image-1591" src="https://brenn0.files.wordpress.com/2018/04/ba8d0093372ff3cd554d232553a7c1f8.jpg" alt="ba8d0093372ff3cd554d232553a7c1f8" width="850" height="400" />

<p style="text-align:justify;">Agora saia daqui e vá testar com os emails dos seus amigos! Só nunca acesse algo que não tenha a explicita autorização para isso.</p>

&nbsp;
