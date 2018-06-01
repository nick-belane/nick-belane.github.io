---
layout: default
title: Ociosidade = Coding And Cracking
date: 2010-09-07 02:45
author: brennords
comments: true
categories: [Assembly, Crackeando, Cracker, Cracking, Debugger, Funções Strings, Hacking, OLLYDBG, Patch Ollydbg, Patching]
---
Here we go again ...

Depois de um longo periodo sem postar nada, resolvi postar algo, não muito útil pra variarmos um pouco, mas não importa.
<p style="text-align:center;"><img class="aligncenter" src="http://img689.imageshack.us/img689/4711/progocio.jpg" alt="" width="323" height="67" />
<img src="/Users/Breno/AppData/Local/Temp/moz-screenshot-2.png" alt="" />

Como eu [<em>ironia</em>] estou dando passos largos, no meu aprendizado sobre C [<em>/ironia</em>], resolvi postar esse meu complexo código de autenticação, aproveitando também para, apenas por curiosidade, tentar descobrir a senha no executável já compilado, usando um debugger.

Here is the code:

<!--more-->

```

#include stdio.h
#include stdlib.h
#include string.h

int main () {
 char entry[10] = , password[10]=password;
printf (Put The Password: );
 gets (entry);
 if(!strcmp(entry, password)) {
 printf (Password Correct!\n);
 }
 else {
 printf (Password Incorrect!\n);
 }
 system(pause);
 return(0);
}

```

Eu sei, muito complexo, ainda não posso fazer melhor, porque ainda estou lendo sobre funções para tratar strings, meus estudos sobre C andam meio bagunçados, mas juro que um dia, ainda me transformo em um programador de verdade (antes de 2012).

Vamos a uma breve explicação sobre o meu programinha:

Como se pode ver, declaro duas variáveis, uma com o nome "entry", que é a que irá receber a entrada que obviamente alguém vai pôr, e outra com o nome "password" que tem em seu conteúdo a string "password", que é o que irá ser nossa senha. Logo depois imprimimos uma mensagem na tela, pedindo para que o usuário coloque a senha, depois disso pegamos o que o cara digitou com o <em>gets</em> (sem proteção contra overflow), e comparamos a string que foi digitada, com o password usando a função <em>strcmp</em>, que retorna 0 se as strings forem iguais, e um outro número caso elas forem diferentes. Caso a senha esteja certa, vamos ver a mensagem "Password Correct" na tela, caso contrário, veremos a outra mensagem. Logo depois o programa pede que se pressione uma tecla pra continuar.

E é apenas isso que o belo programa faz, agora qual seria a utilidade dele? Eu não sei, usem e me contem (: .

Logo depois de perder preciosos dias para escrever esse programa, resolvi tentar "crackea-lo", justamente para desenferrujar a pouca prática que eu aprendi lendo o curso sobre o <a href="http://brenn0.wordpress.com/2010/05/29/introducao-ao-cracking-with-ollydbg/" target="_blank">OLLYDBG</a> (o qual eu juro que irei terminar antes de 2012 também).

Depois de ter aberto meu programa com o Olly, passei um tempo observando a beleza do código, e logo depois fui analisar as strings de texto do programinha (ViewReferences), e lá acabei encontrando isso:
<p style="text-align:center;"><img class="aligncenter" src="http://img525.imageshack.us/img525/8273/progocio2.jpg" alt="" width="528" height="93" />
Eu sei, são as mensagens que o programa me manda, a primeira mandando pôr o password, as outras 2 são as que vão ser mostradas caso eu acerte ou não a senha, e as outras mais abaixo nem importam tanto nesse caso.

Então o que fazer agora? Correr pro endereço onde tem a mensagem de "password incorrect", e analizar como rola a verificação, pra assim então tentar burlar isso de alguma forma.
<p style="text-align:center;"><img class="aligncenter" src="http://img197.imageshack.us/img197/4760/progocio3.jpg" alt="" width="499" height="236" />
Essa é a parte do código, onde mais ou menos começa e termina a função principal do programa (função no sentido conotativo).

Como já deu pra sacar, no adress <span style="text-decoration:underline;">00401314</span> é onde se encontra o call pra função strcmp, que compara as 2 strings, na instrução abaixo se vê <span style="text-decoration:underline;">TEST EAX, EAX</span>, essa função irá verificar se EAX é zero ou não, provavelmente é assim que o nosso programa verifica se a senha está certa, esse vai ser o papel da próxima instrução, que é: <span style="text-decoration:underline;">JNZ SHORT test.0040132B.</span> Caso a instrução TEST retorne zero, a flag Z vai se ativar, se ela se ativar o programa não vai saltar para o endereço <span style="text-decoration:underline;">0040132B</span>, que se você verificar, é o endereço que irá nos mostrar a mensagem "Password incorrect".

Bom, vou agora enganar o inocente programa, o colocando no seu devido lugar, que é sob o meu controle =P.

Como eu sou burro e preguiçoso, vou criar um patch arcáico do meu programa, vou pegar a função <span style="text-decoration:underline;">TEST EAX, EAX </span>e substitui-la por um JMP, ao invés de fazer a comparação, essa função irá "pular" para o endereço que nos mostra a mensagem de "Password Correct", para fazer isso, apenas dei 2 cliques encima da função, e no seu lugar coloquei <span style="text-decoration:underline;">JMP 0040131D</span>.
<p style="text-align:center;"><img class="aligncenter" src="http://img225.imageshack.us/img225/9001/progocio4.jpg" alt="" width="474" height="343" />
Simples, fácil e rápido não é? Agora qualquer senha que eu colocar, o programa irá aceitar e dizer que é a senha correta.

Mas também é totalmente sem graça seguir esse caminho, bem que pelo menos eu poderia substituir a função <span style="text-decoration:underline;">JNZ SHORT test.0040132B</span>, por <span style="text-decoration:underline;">JZ SHORT test.0040132B</span>, que inverteria o sentido da função, fazendo com que o programa só pule para a mensagem de "Password Incorrect", caso a senha esteja errada, chegaria até a ser mais elegante =P .

Mas eu não quero isso, vamos fazer de conta que eu perdi o código fonte do programa, esqueçi a senha, e quero descobri-la.

Você se pergunta se seria possível ...

Eu digo que nada é impossível para um gênio!

Uma pena eu não ser um gênio ...

Mas mesmo não sendo um gênio, eu digo a vocês que sim, eu consigo descobrir a senha (como se fosse grande coisa, um programa idiota, escrito por um idiota, "crackeado" por um idiota).

Chegou a hora de descobrir essa misteriosa senha, não é dificil de lembrar, mas caso tenha esqueçido, eu lhe digo para voltar a imagem acima onde se encontra parte do código, e dar uma olhada no adress 00401314, sim é o call pra a função strcmp, que compara as 2 strings.

Só que você não se ligou no que eu quis dizer, nem sabe muito menos no que isso irá ajudar, e seu nível de QI é tão surpreendente, que não pode ser expresso usando o conjunto dos números naturais (meu parabéns buddy).

Me deixa então explicar melhor, para comparar as 2 strings, eu suponho que meu programa as guarda em algum lugar, e então chama a função strcmp mostrando a ela, onde estão guardadas as 2 strings, e então a função strcmp checa se elas são idênticas.

Para descobrir em que lugar estão guardadas as 2 strings, eu seto um breakpoint encima da coitada da strcmp, fazendo com que o programa pise no freio quando chegar naquele endereço.

Breakpoint setado, vamos rodar o programa, insiro uma senha qualquer (cuidado com o overflow), e teclo enter, o programa como era de se esperar, para e espera que eu veja o que eu quero, e depois o mande terminar o que estava fazendo, volto a tela do Olly e dou uma sacada nos registros, ai então o que eu vejo?

Você tem 5 segundos para adivinhar a resposta<sub>.</sub>

5...

...4...

...3...

...2...

...1...

Chega de palhaçada, a imagem fala por sim mesma:
<p style="text-align:center;"><img class="aligncenter" src="http://img42.imageshack.us/img42/7025/progocio5.jpg" alt="" width="364" height="628" />
Escolha de onde você quer, registros ou stack, nesses 2 lugares pode ser visto o famigerado password.

Este post ficou longo pra caralho, o que era pra ser só uma besteira, acabou virando praticamente um livro, caso alguém tenha lido até aqui, espero que tenha sido útil pra algo (: .

http://www.youtube.com/watch?v=vI0mwLaW1qc

Provavelmente passo mais tempo sem postar nada, culpa do Tetsuo ...

=<span style="color:#ff0000;">*</span>
