---
layout: post
author: Hugo Maia Vieira
title: "Acelerando seu emulador Android"
date: 2013-07-25 15:50
description: 'Cansado da lentidão do emulador Android? Veja nesse post como deixá-lo mais rápido utilizando um processador Intel no seu AVD.'
keywords: 'android, emulador, phonegap,lentidão, lento'
comments: true
categories: [android, hugo maia vieira]
published: true
---

Todo mundo que desenvolve apps para o Android sabe que o emulador é bem lento.
No entanto, isso pode mudar se você utiliza um computador com um processador da
Intel que tenha a tecnologia Intel Virtualization.

A melhoria é muito grande. Só para dar uma ideia, no meu notebook a lockscreen
abriu em 10 segundos usando o AVD com o processador Intel e em 57 segundos
usando o AVD com o processador ARM-based!

No site da Intel você encontra a
<a href="http://goo.gl/8tI7u" target="_blank">documentação</a> mostrando
como fazer isso em ambientes Windows, Mac OS e Linux. Contudo, fiz minha versão
em português, com algumas outras dicas e específica para **Linux**. Eu utilizei
o Linux Mint 15, mas provavelmente funciona da mesma maneira no Ubuntu.

<!-- more -->

Em primeiro lugar, você tem que ter a SDK do Android instalada é claro. Se não
for o caso, veja como fazê-lo <a href="http://goo.gl/HD9yka" href="_blank">neste post</a>.


## Instalando a KVM (Kernel-based Virtual Machine)

A <a href="http://www.linux-kvm.org/" target="_blank">KVM</a> é uma solução
completa de virtualização para Linux que roda em máquinas com processador Intel
(x86). Para instalar e rodar a KVM primeiro você precisa checar se sua CPU
suporta a virtualização de hardware, o que pode ser feito com o seguinte
comando:

``` bash
$ egrep -c '(vmx|svm)' /proc/cpuinfo
```

Se a resposta desse comando for 0, seu CPU não tem suporte. Se a resposta for 1
ou mais, beleza, mas ainda tem que ver se está habilitado na BIOS. Primeiro
instale o pacote cpu-checker:

``` bash
$ sudo apt-get install cpu-checker
```

Em seguida, faça a verificação:

``` bash
$ kvm-ok
```

Sua resposta deve ser algo desse tipo:

``` text
INFO: Your CPU supports KVM extensions
INFO: /dev/kvm exists
KVM acceleration can be used
```

Se não, sua resposta será algo como apresentado abaixo e você terá que entrar na
BIOS para ativar a Intel Virtualization Technology:

``` text
INFO: KVM is disabled by your BIOS
HINT: Enter your BIOS setup and enable Virtualization Technology (VT),
and then hard poweroff/poweron your system
KVM acceleration can NOT be used
```

O próximo passo é instalar a KVM e alguns outros pacotes necessários:

``` bash
$ sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils
```

Depois, você deve adicionar seu usuário aos grupos *kvm* e *libvirtd*:

``` bash
$ sudo adduser seu_usuario kvm
$ sudo adduser seu_usuario libvirtd
```

Em seguida, faça logout e login novamente para que as alterações tenham efeito.


## Criando um AVD (Android Virtual Device) com o processador Intel

Primeiro, vamos intalar o pacote que dá suporte a escolha da CPU da Intel na
criação do AVD. Abra o SDK Manager com o comando `android` e instale o pacote
"Intel x86 Atom System Image" para a versão do Android que pretende usar (Figura
1). No meu caso escolhi a versão 4.2.2 porque o 4.3, que saiu ontem, ainda não
tinha suporte.

<div class="aligncenter">
  <img title="SDK Manager" src="/posts-assets/images/2013/07/sdk-manager-intel.png" alt="Instalando o pacote da Intel com o SDK Manager" width="694" height="412" />
  <span class="caption">Figura 1: Instalando o pacote da Intel com o SDK Manager</span>
</div>


Em seguida, vamos criar um AVD normalmente, porém, com o processador da Intel.
Abra o AVD Managet com o comando `android avd`. Para facilitar, usei uma
definição de dispositivo da aba "Device Definitions", no caso, o Nexus 4. Basta
clicar na definição, depois em "Create ADV...", escolher o target "4.2.2" e o
CPU "Intel Atom (x86)". Além disso, para facilitar na hora de usar com o
emulador, troquei o nome do ADV para "nexus4" como pode ser visto na Figura 2.

<div class="aligncenter">
  <img title="SDK Manager" src="/posts-assets/images/2013/07/avd-intel.png" alt="Criando um AVD com processador Intel" width="448" height="632" />
  <span class="caption">Figura 2: Criando um AVD com processador Intel</span>
</div>

Ponto, agora você tem seu dispositivo virtual com o processador Intel. Agora
vamos utilizá-lo no emulador:

``` bash
$ emulator @nexus4
```

Se você quiser ver a diferença entre utilizar o processador Intel e o ARM, feche
o emulador, edite seu AVD trocando a CPU dele para AMR e inicie o emulador
novamente. A diferença é grande, né? =)


## Corrigindo o erro da libGL.so

Inicie seu emulador e veja se acontece erro abaixo:

``` bash
$ emulator @nexus4
Failed to load libGL.so
error libGL.so: cannot open shared object file: No such file or directory
Failed to load libGL.so
error libGL.so: cannot open shared object file: No such file or directory
```

Se sim, para corrigí-lo basta encontrar o arquivo em sua máquina e criar um link
simbólico na pasta onde está o Android SDK. Para encontra o arquivo:

``` bash
$ sudo updatedb
$ locate libGL.so
/usr/lib/i386-linux-gnu/mesa/libGL.so.1
/usr/lib/i386-linux-gnu/mesa/libGL.so.1.2.0
/usr/lib/x86_64-linux-gnu/mesa/libGL.so.1
/usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0
```

Como meu sistema é 64 bits, vou usar o aquivo
*/usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0* para fazer o link:

**Obs**: mude o *~/android* para o local onde está o Android SDK na sua máquina.

``` bash
$ ln -s /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1.2.0 ~/android/sdk/tools/lib/libGL.so
```

Se isso não solucionar seu problema, dê uma olhada
<a href="http://goo.gl/lK23N" target="_blank">nessas respostas do stackoverflow</a>.


## Turbinando ainda mais seu emulador

Uma outra coisa que você pode fazer para acelerar ainda mais seu emulador é
habilitar o uso da GPU. Basta editar seu AVD e selecionar o item "Use Host GPU".
Isso fará com que o emulador carregue ainda mais rápido (em 8 segundos na
minha máquina), as animações de transição sejam mais rápidas e fluidas e os
gráficos fiquem mais bonitos e bem definidos. Repare na diferença do papel de
parede na Figura 3.

<div class="aligncenter">
  <img title="SDK Manager" src="/posts-assets/images/2013/07/comparacao-emulador-sem-e-com-gpu.png" alt="Diferença entre usar ou não a GPU" width="670" height="423" />
  <span class="caption">Figura 3: Sem e com o uso da GPU</span>
</div>

O único problema é que se você, assim como eu, utiliza o
<a href="http://phonegap.com/" target="_blank">PhoneGap</a> para desenvolver seus
apps, o uso da GPU fará com que sua aplicação não abra direito, como na Figura
4. Não encontrei nenhuma solução para isso e eu mantive essa opção desmarcada.
Se alguém solucionar esse problema, por favor deixe um comentário! =)

<div class="aligncenter">
  <img title="SDK Manager" src="/posts-assets/images/2013/07/emulador-problema-phonegap.png" alt="Problema do uso da GPU com apps PhoneGap" width="335" height="423" />
  <span class="caption">Figura 4: Problema do uso da GPU com apps PhoneGap</span>
</div>

---