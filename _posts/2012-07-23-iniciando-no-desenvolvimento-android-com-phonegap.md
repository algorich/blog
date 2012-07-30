---
layout: post
author: hugo
title: Iniciando no desenvolvimento Android com PhoneGap
category : mobile
tags : [phonegap, cordova, android]
description: 'Aprenda como começar a desenvolver apps para Android utilizando o PhoneGap, um framework que te dá liberdade de criar apps mobile usando as tecnologias da web que você conhece e adora: HTML, CSS e Javascript.'
keywords: 'mobile, dispositivos, móvel, móveis, phonegap, android, ubuntu, linux'
facebook_image: '/assets/images/2012/07/phonegap.png'
---
{% include JB/setup %}

Aqui na Algorich nós utilizamo o <a href="http://phonegap.com/" target="_blank">PhoneGap</a> para desenvolver aplicações mobile. Como diz no site, desenvolver com PhoneGap te dá a liberdade de criar aplicações mobile para <a href="http://phonegap.com/about/feature" target="_blank">7 plataformas diferentes</a> (com suporte completo à Android, iOS e Windows Phone) usando as tecnologias da web que você conhece e adora: HTML, CSS e Javascript.

<img class="alignright" title="PhoneGap" src="/assets/images/2012/07/phonegap.png" alt="PhoneGap" width="216" height="250" />

Os apps criados com o <a href="http://phonegap.com/" target="_blank">PhoneGap</a> não são nativos, porém, se comportam como tal. Eles são instalados no dispositivo e rodam em uma instância do browser padrão do sistema, ocupando 100% da tela. Além disso, o <a href="http://phonegap.com/" target="_blank">PhoneGap</a> fornece uma API que te possibilita acessar funcionalidades nativas do sistema operacional (como a câmera, acelerômetro e etc) usando JavaScript. Os apps são criados utilizando o modelo <a href="http://en.wikipedia.org/wiki/Single-page_application" target="_blank">single-page</a>, onde a lógica do app fica em uma única página HTML que nunca é eliminada da memória, e todas as transições e apresentação dos dados são feitas através de atualizações no HTML <a href="http://www.w3.org/DOM" target="_blank">DOM</a>. Um exemplo de app criado com o <a href="http://phonegap.com/" target="_blank">PhoneGap</a> é o <a href="http://phonegap.com/app/wikipedia/" target="_blank">app do Wikipedia</a>.

Neste post irei mostrar como começar a desenvolver apps para Android utilizando o PhoneGap. O <a href="http://docs.phonegap.com/en/2.0.0/guide_getting-started_android_index.md.html" target="_blank">getting started</a> do PhoneGap indica a utilização do <a href="http://www.eclipse.org/downloads/" target="_blank">Eclipse</a> para montar o ambiente de desenvolvimento. Contudo, como não gosto de IDEs complexas e pesadas como o Eclipse (quando não estou usando Java), vou mostrar aqui como fazer o setup sem o uso dele, utilizando a linha de comando e seu editor preferido (no meu caso, o <a href="http://www.sublimetext.com/">Sublime Text</a>). Além disso, eu utilizo o sistema operacional Ubuntu Linux (versão 12.04 64bits), então algumas coisas que mostrarei aqui podem não ser válidas para outros sistemas.

## Instalando o Android SDK

Primeiro, você deve ter o java instalado. Caso não tenha, instale assim:

{% highlight bash %}
sudo apt-get install openjdk-7-jdk
{% endhighlight %}

Após instalar o java, baixe o Android SDK no <a href="http://developer.android.com/sdk/index.html" target="_blank">site de desenvolvedores do android</a>. Para instalar descompacte o tar.gz em algum diretório de sua escolha. No meu caso, eu utilizo o diretório *~/install/*. Em seguida, adicione a seguinte linha no seu arquivo *~/.bashrc*:

{% highlight bash %}
PATH=$PATH:~/install/android-sdk-linux/tools:~/install/android-sdk-linux/platform-tools
{% endhighlight %}

Isso irá adicionar os diretórios onde estão as ferramentas do SDK (tools e plataform-tools) ao path de executáveis. Desta forma, será possível invocar as ferramentas sem necessidade de colocar todo o caminho onde elas estão, como comandos padrão do shell.

Vejamos algumas ferramentas essenciais do Android SDK:

- **android**
Cria e atualiza projetos Android, atualiza seu SDK, além de criar, mover e deletar AVDs (Android Virtual Devices). Mais informações na <a href="http://developer.android.com/guide/developing/tools/android.html" target="_blank">documentação</a>.
- **emulador (Android Emulator)**
Executa suas aplicações em uma plataforma Android emulada. Mais informações na <a href="http://developer.android.com/guide/developing/devices/emulator.html" target="_blank">documentação</a>.
- **adb (Android Debug Bridge)**
Faz a interface com seu emulador ou dispositivo conectado. Entre outas coisas, é responsável por installar apps. Mais informações na <a href="http://developer.android.com/tools/help/adb.html" target="_blank">documentação</a>.

## Instalando pacotes Android

Você deve baixar pacotes (packages) com imagens de sistemas Android, também chamadas de plataformas. Para abrir o SDK Manager use o comando `android` com a opção `sdk`.

{% highlight bash %}
android sdk
{% endhighlight %}

O SDK Manager irá abrir. Você deve escolher uma (ou mais) versão do Android para instalar. Você só precisa instalar o pacote *SDK Platform*, sendo o restante opcional. Como é apresentado na figura 1, no meu caso, escolhi instalar apenas a versão 2.3.3 que é a versão que roda no meu celular.

<div class="aligncenter">
  <img title="SDK Manager" src="/assets/images/2012/07/sdk-manager.png" alt="SDK Manager" width="620" height="349" />
  <span class="caption">Figura 1: Instalando pacotes Android com o SDK Manager</span>
</div>

Depois de instalar os pacotes, pode fechar o SDK Manager.

## Manipulando AVDs

Um AVD (Android Virtual Device) é um emulador de configurações que te permite criar um modelo virtual de dispositivo, definindo o hardware e o software que serão emulados pelo Android Emulator. Desta forma, você pode emular diferentes configurações de dispositivos que rodam Android. Você pode criar quantos AVDs você precisar, de acordo com as configurações dos dispositivos os quais seu app é compatível (por exemplo, com diferentes tamanhos de tela e versões do Android), para testar seu app em cada uma delas. Você pode <a href="http://developer.android.com/guide/developing/devices/managing-avds-cmdline.html">criar um AVD pela linha de comando</a>, contudo, existe o AVD Manager que facilita um pouco. Para abrir o AVD Manager use o comando `android` com a opção `avd`:

{% highlight bash %}
android avd
{% endhighlight %}

Em seguida crie um novo AVD como apresentado na figura 2.

<div class="aligncenter">
  <img title="AVD Manager" src="/assets/images/2012/07/avd-manager-new.png" alt="AVD Manager" width="399" height="643" />
  <span class="caption">Figura 2: Criando um novo AVD com o AVD Manager</span>
</div>

**Obs**: Caso você esteja rodando o Ubuntu 64 bits pode ser que você tenha o problema apresentado na figura 3 ao criar o AVD.

<div class="aligncenter">
  <img title="Problema ao criar um AVD" src="/assets/images/2012/07/avd-manager-problem.png" alt="Problema ao criar um AVD" width="613" height="191" />
  <span class="caption">Figura 3: Problema ao criar um AVD</span>
</div>

Caso isso ocorra, é porque você não tem o pacote `ia32-libs` instalado. Para instalá-lo:

{% highlight bash %}
sudo apt-get install ia32-libs
{% endhighlight %}

Depois de criado o AVD corretamente, pode frechar o AVD Manager.

## Criando um projeto Android

O Android SDK espera que seu projeto siga uma estrutura específica de arquivos e diretório para que ele possa compilar e empacotar sua aplicação corretamente. A ferramenta `android` criar um novo projeto com toda a estrutura definida. Recomendo que você veja a <a href="http://developer.android.com/tools/projects/index.html#ApplicationProjects" target="_blank">documentação</a> para conhecer a estrutura de um projeto, entendendo para que serve cada diretório e arquivo do seu projeto.

Para criar um projeto Android, rode o seguinte comando:

{% highlight bash %}
android create project \
        --target <target_ID> \
        --name <nome_do_seu_projeto> \
        --path caminho/para/seu/projeto \
        --activity <nome_da_sua_activity> \
        --package <namespace_do_seu_package>
{% endhighlight %}

Onde:

- **target** é a plataforma Aldroid alvo para a construção da sua aplicação. Para ver a lista de targets disponíveis e seus IDs correspondentes, use o comando `android list targets`.
- **name** é o nome do seu aplicativo.
- **path** é a localização do diretório do seu projeto. Se o diretório não existir, ele será criado para você.
- **activity** é o nome para a classe padrão <a href="http://developer.android.com/reference/android/app/Activity.html" target="_blank">Activity</a>.
- **package** é o namespace do package do seu projeto, seguindo as mesmas regras para os packages em Java.

Para este artigo, vou criar um app HelloWorld:

{% highlight bash %}
android create project \
        --target 1 \
        --name HelloWorld \
        --path ~/projetos/hello_world \
        --activity HelloWorldActivity \
        --package br.com.algorich.hello_world
{% endhighlight %}

Com isso, será criada a estrutura do projeto Android, como apresentada na figura 4.

<div class="aligncenter">
  <img title="Estrutura do projeto Android" src="/assets/images/2012/07/project-tree.png" alt="Estrutura do projeto Android" width="370" height="334" />
  <span class="caption">Figura 4: Estrutura de arquivos e diretórios do projeto Android</span>
</div>

Se você utilizar um sistema de controle de versão (se não utiliza, deveria) como o <a href="http://git-scm.com/" target="_blank">git</a>, faça com que o arquivo *local.properties* e os diretórios *bin* e *gen* sejam ignorados pelo controle de versão.

## Adicionando o PhoneGap

Com a estrutura do projeto criada, é hora de adicionar o PhoneGap. Para isso, faça o <a href="http://phonegap.com/download" target="_blank">download do PhoneGap</a> e descompacte o .zip em algum diretório de sua escolha. No meu caso, eu utilizo o diretório *~/install/*. Em seguida, no terminal, entre no diretório da sua aplicação e execute os seguintes comandos:

{% highlight bash %}
# cria o diretório assets e dentro dele o diretório www
mkdir -p assets/www
cp ~/install/phonegap/lib/android/cordova-2.0.0.js assets/www/
cp ~/install/phonegap/lib/android/cordova-2.0.0.jar libs/
cp -r ~/install/phonegap/lib/android/xml/ res/
{% endhighlight %}

Abra o arquivo da classe `Activity` (arquivo java contido no último subdiretório do diretório *src*), que no exemplo é o arquivo *HelloWorldActivity.java*, e edite o seguinte:

<ol>
  <li>A classe herda de <code>Activity</code>, mude para herdar de <code>DroidGap</code></li>
  <li>Troque a linha <code>setContentView(R.layout.main);</code> por <code>super.loadUrl("file:///android_asset/www/index.html");</code></li>
  <li>Adicione o import <code>import org.apache.cordova.*;</code></li>
  <li>Remova o import <code>import android.app.Activity;</code></li>
  <li>Adicione o trexo de código seguinte ao arquivo <em>AndroidManifest.xml</em>, abaixo da linha que contém <code>android:versionName</code>:
{% highlight xml %}
<supports-screens
  android:largeScreens="true"
  android:normalScreens="true"
  android:smallScreens="true"
  android:resizeable="true"
  android:anyDensity="true"
/>
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.WRITE_CONTACTS" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="android.permission.BROADCAST_STICKY" />
{% endhighlight %}
  </li>
  <li>Adicione <code>android:configChanges="orientation|keyboardHidden"</code> a tag <code>activity</code> no <em>AndroidManifest.xml</em></li>
  <li>Crie o arquivo <em>assets/www/index.html</em> e adicione o seguinte conteúdo:
{% highlight html %}
<!DOCTYPE HTML>
<html>
  <head>
    <title>Android + PhoneGap</title>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <script type="text/javascript" charset="utf-8" src="cordova-2.0.0.js"></script>
  </head>

  <body>
    <h1>Hello World! =)</h1>
  </body>
</html>
{% endhighlight %}
  </li>
</ol>

**Obs**: A única permissão realmente necessária para iniciar um hello world com o PhoneGap é a seguinte:
{% highlight xml %}
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
{% endhighlight %}

O restante pode ser adicionado quando/se houver necessidade.

Você pode mudar o nome de apresentação do seu app alterando a tag `<string>` com o atributo `name="app_name"` no arquivo *res/values/strings.xml*.

## Construindo o app

Existem duas formas de construir o seu app: release e debug. Neste post vou mostrar apenas como construir em modo debug, ficando o modo release para um outro post. Para construir o app, é utilizado o <a href="http://ant.apache.org/" target="_blank">Ant</a>. Para instalar o Ant faça o seguinte:

{% highlight bash %}
sudo apt-get install ant
{% endhighlight %}

Com o Ant instalado, basta usá-lo para construir seu app:

{% highlight bash %}
ant debug
{% endhighlight %}

Isto irá criar seu arquivo .apk de debug no diretório *bin*, *chamado nome_do_app-debug.apk*. Este arquivo será utilizado para instalar o app no emulador ou no dispositivo real, como veremos a seguir. Recomendo a leitura da <a href="http://developer.android.com/tools/building/building-cmdline.html" target="_blank">documentação</a> para entender os passos que o Ant executa internamente para a construção do app.

## Usando o Emulador

O Emulador te permite prototipar, desenvolver e testar dois apps sem a necessidade de utilizar um dispositivo real, através da simulação das configurações dos AVDs existentes. Assim, para iniciar o Emulador, você deve especificar qual AVD deseja carregar. Cada AVD funciona como um dispositivo independente, com seu banco de dados privado de informações de usuário, cartão SD e etc. Quando você inicia o Emulador com um AVD, ele automaticamente carrega todas as informações do diretório do AVD, onde estas informações são salvas por padrão. Para iniciar uma instância do Emulador, basta usar o comando `emulator` definindo qual AVD será utilizado:

{% highlight bash %}
emulator -avd nome_do_avd
# ou, de forma simplificada:
emulator @nome_do_avd
{% endhighlight %}

Uma janela com o Emulador será aberta como mostrado na figura 5. É normal o emulador demorar alguns segundos até terminar de carregar.

<div class="aligncenter">
  <img title="Emulador Android" src="/assets/images/2012/07/emulator.png" alt="Emulador Android" width="620" height="579" />
  <span class="caption">Figura 5: Emulador Android</span>
</div>

Para encerrar o Emulador, basta fechar a janela. O Emulador é uma ferramenta muito completa. Você pode interconectar diferentes instâncias do Emulador, manipular o cartão SD, simular envio de sms, entre outras diversas coisas. Veja na <a href="http://developer.android.com/tools/devices/emulator.html" target="_blank">documentação</a> todas as funcionalidades do Emulador.

### Executando o app no Emulador

Para executar o app no Emulador, você deve primeiro estar com uma instância do Emulador rodando, como mostrado acima, e ter construído seu app com o Ant. Em seguida, basta usar a ferramenta adb para instalar o app no Emulador:

{% highlight bash %}
adb install bin/nome_do_app-debug.apk
{% endhighlight %}

Após estar instalado, para atualizar (reinstalar), deve-se utilizar o parâmetro -r:

{% highlight bash %}
adb install -r bin/nome_do_app-debug.apk
{% endhighlight %}

Para (re)compilar e (re)instalar ao mesmo tempo usando você poderia concatenar os comandos:

{% highlight bash %}
ant debug; adb install -r bin/nome_do_app-debug.apk
{% endhighlight %}

Contudo, você pode fazer isso diretamente com o Ant da seguinte maneira:

{% highlight bash %}
ant debug install
{% endhighlight %}

Após ter sido instalado, o app pode ser encontrado no menu de apps do dispositivo emulado. Na figura 6 podemos ver o Hello World sendo executado no Emulador.

<div class="aligncenter">
  <img title="Hello World no Emulador" src="/assets/images/2012/07/hello-world.png" alt="Hello World no Emulador" width="620" height="579" />
  <span class="caption">Figura 6: HelloWorld sendo executado no Emulador</span>
</div>

## Usando dispositivos reais

Você também pode utilizar dispositivos reais para desenvolver e testar seu app, especialmente nos casos em que o emulador não oferece suporte. Para isso, você deve seguir alguns passos:

1. Declarar o app "debuggable" no Android Manifest

    No arquivo *AndroidManifest.xml* adicione `android:debuggable="true"` ao elemento `<application>`.

    **Obs**: Lembre-se de remover isso quando for lançar a release do seu app.

2. Ativar a "Depuração USB" no seu dispositivo

    No seu dispositivo, vá em **Configurações > Aplicativos > Desenvolvimento** e ative a **Depuração USB** (em dispositivos com Android 4.0, esta configuração está localizada em **Configurações > Opções de desenvolvimento**).

    **Dica**: Ative também a opção **Permanecer ativo** para quando o dispositivo estiver carregando (ou seja, ligado ao computador pelo cabo USB) a tela não apague.

3. Configurar o sistema para reconhecer seu dispositivo (se não usa o Ubuntu, veja a <a href="http://developer.android.com/tools/device.html" target="_blank">documentação</a>)

    Você precisa adicionar um arquivo de regras udev que deve conter as configurações USB para cada dispositivo que deseja usar para desenvolvimento. No arquivo de regras, cada fabricante de dispositivos é identificado por um id único. Você deve ver na <a href="http://developer.android.com/tools/device.html#VendorIds" target="_blank">lista de fabricantes</a> qual é o id do fabricante do seu dispositivo. Para configurar a detecção do dispositivo, faça o seguinte:

    1. Faça login como root e crie este arquivo: */etc/udev/rules.d/51-android.rules*.

        Use este formato para adicionar cada fabricante ao arquivo:
        <br/>
        `SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", GROUP="plugdev"`

        O atributo `ATTR{idVendor}` indica o id do fabricante, que neste exemplo é Sony Ericsson. O atributo `MODE` especifica as permissões de leitura/escrita e o `GROUP` define a que grupo do sistema o dispositivo pertence.

    2. Agora execute o comando: `chmod a+r /etc/udev/rules.d/51-android.rules`


Após ter seguido estes passos e estar com o dispositivo ligado à porta USB, você pode usar o comando <span class="lang:sh highlight:0 decode:true  crayon-inline">adb devices</span> para listas os dispositivos.
<h4>Executando o app em dispositivos reais</h4>
Para executar o app em dispositivos reais você usar o mesmos comandos apresentados para o Emulador, sendo que ao usar o adb deve ser adicionado o parâmetro -d. Já ao usar o Ant, não precisa modificar nada no comando, uma vez que o Ant tenta instalar em uma instância do Emulador e, caso não encontre uma instância rodando, tenta instalar em algum dispositivo conectado.

Assim como no Emulador, após ter sido instalado, o app pode ser encontrado no menu de apps do dispositivo. Na figura 7 podemos ver o Hello World sendo executado no meu telefone.

<div class="aligncenter">
  <img title="Hello World no dispositivo" src="/assets/images/2012/07/hello-world-device.png" alt="Hello World no dispositivo" width="620" height="348" />
  <span class="caption">Figura 7: Hello World sendo executado no dispositivo</span>
</div>

## PhoneGap Command Line Tooling

Na versão 2.0 do PhoneGap, <a href="http://phonegap.com/2012/07/20/adobe-phonegap-2-0-released.md/" target="_blank">lançada no dia 20 deste mês</a>, foi adicionado o <a href="http://docs.phonegap.com/en/2.0.0/guide_command-line_index.md.html" target="_blank">Command Line Tooling</a>, que é um conjunto de scripts para realizar tarefas repetitivas e comuns à todas as plataformas como criar, construir e emular apps.

Todos os passos realizados anteriormente para criar um projeto Android e adicionar o PhoneGap são resumidos à um único comando.

Contudo, para executar no Ubuntu devemos fazer uma alteração no script, pois ele utiliza o interpretador padrão do sistema (que pode ser acessado pelo `/bin/sh`) que no Ubuntu é o <a href="https://wiki.ubuntu.com/DashAsBinSh" target="_blank">`dash`</a>. Porém, devido aos erros que encontrei ao rodar o script, acredito que ele foi feito para ser interpretado pelo `bash`. Assim, basta mudar a primeira primeira linha para: `#! /bin/bash`.

Para criar o projeto basta rodar o script `create` encontrado no diretório do PhoneGap:

{% highlight bash %}
/install/phonegap/lib/android/bin/create caminho_para_o_projeto \
                                          namespace_do_seu_package \
                                          nome_do_seu_projeto
{% endhighlight %}

No caso do nosso exemplo seria o seguinte:

{% highlight bash %}
./install/phonegap/lib/android/bin/create ~/projetos/hello_world \
                                          br.com.algorich.hello_workd \
                                          HelloWorld
{% endhighlight %}

Com isso, será criada a estrutura do projeto Android, como apresentada na figura 8.

<div class="aligncenter">
  <img title="Estrutura do projeto Android criado pelo PhoneGap" src="/assets/images/2012/07/tree-phonegap-create.png" alt="Estrutura do projeto Android criado pelo PhoneGap" width="314" height="708" />
  <span class="caption">Figura 8: Estrutura de arquivos e diretórios do projeto Android criado pelo PhoneGap</span>
</div>

Repare que é criado um diretório cordova com alguns scripts. Para saber o que cada um desses scripts faz, veja a <a href="http://docs.phonegap.com/en/2.0.0/guide_command-line_index.md.html#Command-Line%20Usage_android" target="_blank">documentação</a>, mas vamos ver dois que fazem tarefas que mostrei anteriormente.

Para iniciar o Emulador:

{% highlight bash %}
./cordova/emulate
{% endhighlight %}

Para construir o app:

{% highlight bash %}
./cordova/debug
{% endhighlight %}

Na figura 9 podemos ver o Hello World criado pelo `create` do PhoneGap sendo executado no Emulador.

<div class="aligncenter">
  <img title="Hello World criado pelo PhoneGap sendo executado no Emulador" src="/assets/images/2012/07/hello_world_phonegap.png" alt="Hello World criado pelo PhoneGap sendo executado no Emulador" width="620" height="567" />
  <span class="caption">Figura 9: Hello World criado pelo PhoneGap sendo executado no Emulador</span>
</div>

O legal desse app padrão criado é que ele tem vários exemplos de utilização da API do PhoneGap que você pode ver, modificar e testar.

## Finalizando

Neste post mostrei como criar um Hello World do zero usando o PhoneGap e as ferramentas de linha de comando do Android SDK, sem a necessidade da utilização do Eclipse. A partir desse ponto, você tem condições de brincar tranquilamente com a API do PhoneGap =)

Até a próxima!