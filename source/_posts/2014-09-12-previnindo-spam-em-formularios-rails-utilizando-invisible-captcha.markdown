---
layout: post
author: Matheus Sales
title: 'Previnindo SPAM em formulários Rails utilizando invisible captcha'
tags: [ruby, rails, spam, captcha, invisible_captcha, formulario]
description: 'Qualquer aplicação web com formulários que não exigem autenticação do usuário, sofre do mesmo problema: o SPAM! Em sua maioria, preenchido por spambots.'
keywords: 'ruby, rails, spam, captcha, invisible_captcha, formulario,  matheus sales'
date: 2014-09-12 16:00
categories: [ruby, matheus sales]
comments: true
facebook_image: '/posts-assets/images/2014/09/spam.jpg'
published: true
---

<p class="aligncenter">
  <img class="space" title="SPAM" src="/posts-assets/images/2014/09/spam.jpg" alt="SPAM" width="500" height="360" />
</p>

Qualquer aplicação web com formulários que não exigem autenticação do usuário, sofre do mesmo problema: o SPAM! Em sua maioria, preenchido por <a href="http://pt.wikipedia.org/wiki/Spambot" target="_blank">spambots</a>.

A técnica mais utilizada para a prevenção é o uso do <a href="http://pt.wikipedia.org/wiki/ReCAPTCHA" target="_blank">reCAPTCHA</a>, oficialmente originado do <a href="http://pt.wikipedia.org/wiki/CAPTCHA" target="_blank">CAPTCHA</a>, que foi desenvolvido pioneiramente na universidade de Carnegie-Mellon, nos EUA.

<!-- more -->

Um CAPTCHA usual envolve um computador (um servidor) que pede que um usuário termine um teste. Como os bots são incapazes de resolver o CAPTCHA, todo usuário que incorpora uma solução correta é presumidamente humano.

Já o reCAPTCHA pede para usuários digitarem palavras distorcidas exibidas na tela. Além de proteger que os robôs preencham os formulários, <a href="http://bsf.org.br/2010/02/04/recaptcha-digitalizacao-ocr-luis-von-ahn-pop-tech/" target="_blank">ele ajuda a digitalizar o texto de livros</a>.

O problema é que o reCAPTCHA se torna chato para os usuários, o que atrapalha e muito na <a href="http://pt.wikipedia.org/wiki/Experi%C3%AAncia_do_usu%C3%A1rio">UX</a>.


##Utilizando a técnica de honeypot

A técnica de <a href="http://haacked.com/archive/2007/09/11/honeypot-captcha.aspx/" target="_blank">Honeypot</a> ou Invisible Captcha consiste no uso de um campo invisível no formulário. Os bots em sua maioria não carregam todo o HTML, CSS e Javascript da página. Isso faz com que eles não tenham uma visão da página como ela realmente é, não conseguindo distinguir os campos visíveis dos invisíveis.

Qual o resultado?

Eles vão preencher o nosso honeypot!

##Utilizando a gem Invisible Captcha

A gem <a href="https://github.com/markets/invisible_captcha" target="_blank">invisible_captcha</a> é muito simples, vamos ao passo a passo:

Adicione ao seu Gemfile:
```ruby
gem 'invisible_captcha'
```

Existe mais de uma forma de implementação, abordaremos aqui a implementação no Modelo.

Baste adicionar um atributo virtual a sua classe, como no exemplo abaixo e setá-lo como ‘invisible_captcha’:

```ruby
class Topic < ActiveRecord::Base
  attr_accessor :subtitle # virtual attribute, the honeypot
  validates :subtitle, invisible_captcha: true
end
```

Se você estiver usando strong_parameters, não se esqueça de colocar o atributo na hash de parâmetros.

```ruby
def topic_params
  params.require(:topic).permit(:subtitle)
end
```

##Configuração Extra

Para customizar as configurações, adicione o arquivo invisible_captcha.rb em config/initializers com o seguinte:

```ruby
InvisibleCaptcha.setup do |ic|
  ic.sentence_for_humans = 'Se você é humano, por favor, ignore este campo.'
  ic.error_message = 'Você é um robô!'
  ic.fake_fields << 'fake_field'
end
```

Agora basta colocar o formulário na view:

```ruby
<%= form_for(@topic) do |f| %>
  <%= f.invisible_captcha :subtitle %>
<% end %>
```

Para ver o funcionamento, vá ao seu formulário com o inspetor do elemento do seu navegador e procure pelo honeypot. Veja um exemplo abaixo:

![inspecionando elemento do honeypot](/posts-assets/images/2014/09/code.png)