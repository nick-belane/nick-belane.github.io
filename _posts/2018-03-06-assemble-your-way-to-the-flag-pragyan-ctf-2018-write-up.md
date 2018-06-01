---
layout: default
title: Assemble your way to the flag [Pragyan CTF 2018] - write-up
date: 2018-03-06 17:32
author: brennords
comments: true
categories: [CTFs]
---
A solução desse chall é bem simples e praticamente só exige que você saiba o que a instrução <a href="http://webcache.googleusercontent.com/search?q=cache:lfRLmgWzBvMJ:www.linguagemassembly.com.br/fundamentos/pilhas-assembly-stack/+&amp;cd=3&amp;hl=pt-BR&amp;ct=clnk&amp;gl=br" target="_blank" rel="noopener">PUSH</a> faz, e ler o que há na stack usando um debugger qualquer.

O binário do desafio pode ser encontrado <a href="https://github.com/brerodrigues/CTFs/raw/master/Pragyan%20CTF%202018/reverse/question" target="_blank" rel="noopener">neste link</a>.

Assim que se roda o programa, o resultado é:

```

brenno@budweiser ~/D/c/p/reverse1_question./question
Look for something else....```

Depois de usar o comando <em>strings</em> (porque sempre há esperança) e o <em>ltrace</em> para tentar encontrar alguma coisa mais óbvia e falhar, comecei a usar o gdb:

```

brenno@budweiser ~/D/c/p/reverse1_questiongdb -q question
Reading symbols from question...(no debugging symbols found)...done.```

E fui procurar por <em>main</em>:

```

gdb-peda$ info functions main
All functions matching regular expression "main":

Non-debugging symbols:
0x00000000000006a0 main ```

main encontrada, hora de ver o que a função principal do programa fazia:

https://gist.github.com/anonymous/e768b30b3f7babaf2342c9a441ba1b03

São muitos <em>mov</em> e <em>push</em>. O que tanto essa porra joga na stack?

Para descobrir, setei um breakpoint em main:

```gdb-peda$ b *main
Breakpoint 1 at 0x6a0
```

Rodei o programa com o comando <em>run</em> e, usando o atalho para o comando <em>next</em>, que é a letra <em>n</em>, fui instrução por instrução até chegar no primeiro <em>push rax</em>, após os <em>mov</em> e <em>xor</em> suspeitos, em <strong>main+33</strong>.

https://gist.github.com/anonymous/2efbbc0d5565c4dc36f20ffdd24ceafe

Pelo amor do espaço desperdiçado no post com um monte de lixo, cortei boa parte da saída do gdb e quero que se destaquem a <strong>linha 6</strong>, que é a instrução que acabou de ser executada, e a <strong>linha 13</strong>, que representa o valor que está no topo da stack. Parece bastante com um caracter que pode compor a flag: "}". Para confirmar essa tese, posso muito bem setar um breakpoint em <strong>main+52</strong> e usar o comando <em>c</em> (que representa o <em>continue</em>, se não estou viajando) para fazer o programa continuar rodando até lá. E assim o fiz.

https://gist.github.com/anonymous/32274c72cb0ab3808a9a1cb68dd7bc91

Você pode observar que no topo da stack agora há o caracter "<strong>y</strong>". Já era óbvio. A flag estava sendo mandada para a stack.

Então, procurei a última instrução <em>push rax</em>, que está em <strong>main+555</strong>, coloquei um breakpoint na instrução seguinte e fiz o programa continuar a rodar. Assim que ele bateu no meu breakpoint, a flag inteira estava na stack.

https://gist.github.com/anonymous/f1d25773036246b61e1bf96227b31248

Nesse momento, ainda no gdb, foi só usar o seguinte comando: <em>x/30ws $sp</em> que é responsável por examinar a memória no gdb. E os parâmetros que passei significam que quero ler <strong>30w</strong> para 30 WORDS (4 bytes) e com o <strong>s</strong> digo para printa-los como string. E o <strong>$sp</strong> representa o local inicial da memória de onde quero ler esses bytes. $sp nada mais é do que o stack pointer, o registrador que estará sempre apontando para o topo da stack.

https://gist.github.com/anonymous/7b07998825c0a53fdc8cb5476dab9cc9

<strong>pctf{l3geNds_c0d3_1n_4Ss3mb1y}</strong>.
