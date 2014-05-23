---
layout: post
author: Matheus Sales
title: 'Conheça os test helpers: #travel_to e #travel_back no Rails 4.1'
tags : [rails 4.1, travel_to, travel_back, testes, timecop, gem]
description: ''
keywords: 'rails, testes, matheus sales'
date: 2014-05-23 17:00
categories: [testes, matheus sales]
comments: true
facebook_image: '/posts-assets/images/2014/05/back-future.jpg'
published: true
---

Quem trabalha com testes no Ruby on Rails certamente já teve de recorrer a gem <a href="https://github.com/travisjeffery/timecop" target="_blank">timecop</a> para cobrir os testes com dependências de tempo.

Como exemplo, posso citar a regra de negócio de um seguinte cenário:

O pagamento de um aluguel deve ser realizado em uma data específica de cada mês, exceto quando esse data cai em um fim de semana. Nesse caso, o pagamento será realizado na próxima segunda-feira.


<!-- more -->
<img class="alignright" title="ilustração: de volta para o futuro" src="/posts-assets/images/2014/05/back-future.jpg" alt="ilustração: de volta para o futuro" width="250" height="250" />

**Onde a gem timecop atua?**

Timecop é uma gem que provê à nossa aplicação a capacidade de viajar ou parar no tempo, isso permite que consigamos realizar testes com dependência de tempo de forma muito simples, e certamente assegurar os testes para a nossa regra de negócio detalhada acima.

##Os test helpers #travel_to e #travel_back

Com a <a href="http://guides.rubyonrails.org/4_1_release_notes.html" target="_blank">nova release do Rails 4.1</a>, agora temos os métodos: *#travel_to* e *#travel_back*.

Com o *#travel_to* podemos facilmente alterar o dia e horário fornecendo um valor ou duração de tempo. Já o *#travel_back* faz com que retornemos ao estágio atual, desfazendo as alterações do *#travel_to*.

Essa feature teve um destaque muito pequeno (o que não diminui sua importância, na minha opinião), sendo mencionada apenas no final das notas da release:

<img title="release notes: travel_to / travel_back" src="/posts-assets/images/2014/05/travel-to.png" alt="release notes: travel_to / travel_back" width="670" height="389" />

Referência: <a href="http://brandonhilkert.com/blog/rails-4-1-travel-to-test-helper/" target="_blank">http://brandonhilkert.com/blog/rails-4-1-travel-to-test-helper/</a>