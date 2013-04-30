---
layout: post
author: eduardo
title: Configurar ssh com acesso por chave pública
description: 'Aprenda a configurar SSH para liberar o acesso por chave pública.'
keywords: 'ssh, chave pública, segurança, algorich, debian, ubuntu, software, desenvolvimento, chave, pública'
facebook_image: '/assets/images/2012/07/phonegap.png'
---
{% include JB/setup %}

Olá pessoal,

Hoje vamos mostrar como deixar o seu servidor com acesso SSH mais seguro. Por padrão, o SSH aceita conexão através de senha em texto livre. Quem souber a senha, pode se logar no servidor. Isso é bastante perigoso, pois se a senha não for muito complicada, um *bot* pode descobri-la através da força bruta.

Uma forma de evitar isso é configurar o SSH para aceitar apenas conexões através da chave pública (<a title="Como criar chave SSH" href="http://en.wikipedia.org/wiki/Ssh-keygen" target="_blank">saiba como criar uma chave</a>). Funciona da seguinte forma: você envia sua chave pública para o servidor (na pasta home do usuário que você quer logar) e configura o SSH. Quando você tentar fazer o login, ele vai realizar a verificação da sua chave pública e requisitar que você digite a senha. Caso esteja tudo OK, você se loga normalmente.

Lembrando que essa configuração foi testada no Debian/Ubuntu.

Antes de configurar o SSH, copie a sua chave pública para uma pasta qualquer no servidor.

{% highlight bash %}
scp ~/.ssh/id_rsa.pub usuario@meuservidor:~/.ssh/minha_chave.pub
{% endhighlight %}

E quando estiver logado no servidor:

{% highlight bash %}
cat ~/.ssh/minha_chave.pub >> ~/.ssh/authorized_keys
{% endhighlight %}

Com isso feito, podemos configurar o SSH. Para isso, como root, abra o arquivo */etc/ssh/sshd_config* e altere as seguintes opções:

{% highlight lighttpd %}
# isso evita que alguém tente se logar como root
PermitRootLogin no
# permite a autenticação por chave pública
PubkeyAuthentication yes
# impede a autenticação por senha
PasswordAuthentication no
{% endhighlight %}

Em seguida, reinicie o SSH:

{% highlight bash %}
service ssh restart
{% endhighlight %}

Pronto! Sempre que você acessar o servidor, a autenticação será por chave pública. Caso queira que mais pessoas tenham acesso ao servidor, basta adicionar as chaves públicas no arquivo */home/.ssh/authorized_keys*.

Abraços e até a próxima!
