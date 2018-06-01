---
layout: default
title: Escalação de privilégios Windows Vista / 7
date: 2011-12-16 01:44
author: brennords
comments: true
categories: [Hacking]
---
Tudo começou quando a <a href="https://twitter.com/#!/Ju_Pereeira" target="_blank">Ju_Pereeira</a> trocou a senha da conta de administrador (Windows Vista) e acabou esqueçendo da mesma, eu como estava de mãos limpas (sem cds bootaveis para explorar a famosa falha do utilman.exe) e com acesso a uma única conta (com direitos administrativos limitados), tive que me virar por lá. Dei uma googlada rápida e só encontrei textos sobre como recuperar a senha usando tal distro linux, tal software que precisa de direitos administrativos pra ser instalado... Enfim, nada útil.

Já estava ficando sem idéias, até que tomei uma cerveja e encontrei esse link: <a href="http://www.nsai.it/tag/windows-vista-7-privilege-escalation/" target="_blank">http://www.nsai.it/tag/windows-vista-7-privilege-escalation/</a>

O exploit já tem pouco mais de um ano, eu não sei como não o tinha no meu pobre arsenal mental. De qualquer maneira, foi ele que me serviu, e muito bem por sinal.

É uma vulnerabilidade to tipo <a href="http://under-linux.org/blogs/fellipeugliara/o-que-e-um-buffer-overflow-923/" target="_blank">buffer overflow </a>a nível de Kernel, o que faz com que se burle o UAC (User Acess Control, aquela coisa chata de permitir ou não aplicativos e etc). A coisa toda começa se criando uma chave maliciosa no registro do Windows, depois disso o código chama uma função para ler essa chave, e durante esse processo também chama a parte vulnerável contida na win32k.sys pra fazer parte da festa.

<!--more-->

Se explorada com sucesso, é possível conseguir permissões a nível de sistema, o que significa total poder sobre o Windows.

Muito mais informações técnicas podem ser vistas aqui (em inglês): <a href="http://www.exploit-db.com/bypassing-uac-with-user-privilege-under-windows-vista7-mirror/" target="_blank">http://www.exploit-db.com/bypassing-uac-with-user-privilege-under-windows-vista7-mirror/</a>

Era tudo que eu precisava naquele momento, então rapidamente baixei o PoC: <a href="http://dl.packetstormsecurity.net/1011-exploits/uacpoc.zip">http://dl.packetstormsecurity.net/1011-exploits/uacpoc.zip</a>

Existe uma versão já compilada e o código fonte do exploit nesse arquivo zip, mais adiante eu vou explicar o motivo pelo qual talvez seja preciso mecher no código (o que não é muito fácil). Fica a seu critério compilar ou usar o que já está compilado, usei o já compilado (contrariando uma das regras número 1 sobre exloits já compilados e com backdoors) pelo simples motivo de ter preguiça de arrumar um compilador. Extrai os arquivos, abri o prompt e rodei o poc.exe pra ver o que iria rolar.

<img class="aligncenter" title="fff" src="http://brenn0.files.wordpress.com/2011/12/poc.png" alt="" width="451" height="182" />

Acho que já deu pra perceber não é?

No primeiro comando (whoami) pode se ver que se está logado com uma conta normal, mais logo após a execução da PoC e rodar o comando outra vez, já pude conseguir o que eu queria.

Com o prompt em mãos foi simples acabar o serviço, com um só comando troquei a senha do administrador ("net user Administrador lala") e problema resolvido.

O único aviso sobre esse exploit é que ele é meio instável (também, ele mexe com uma parte bem sensível do sistema). Dependendo da versão do Kernel você pode dar de cara com uma BSOD (tela azul da morte ^^) depois de alguns minutos. Isso aconteceu comigo, a sorte é que eu só precisava de alguns segundos pra mandar um único comando.

```

// For Vista, there is a Pool address on the stack which is going to be passed to ExFreePool before the function returns,
 // so we need a valid pool address to avoid BSOD.

```

Aqui se encontra um vídeo onde a falha é explorada no Windows 7: <a href="http://www.nsai.it/wp-content/uploads/video.swf" target="_blank">http://www.nsai.it/wp-content/uploads/video.swf</a>

E aqui mais umas informações (não tão técnicas e mais "legiveis" que as do exploit-db.com): <a href="http://isc.sans.edu/diary.html?storyid=9988" target="_blank">http://isc.sans.edu/diary.html?storyid=9988</a>
