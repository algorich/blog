---
layout: post
author: Oswaldo Ferreira
title: 'Boa prática em testes & cuidados com FactoryGirl'
tags : [teste, spec, rspec, rails, factorygirl]
description: 'Evitando a lentidão generalizada em testes causada pelo FactoryGirl'
keywords: 'rails, factorygirl, oswaldo ferreira'
date: 2014-04-10 23:01
categories: [testes, oswaldo ferreira]
comments: true
published: false
---

*FactoryGirl* é uma das melhores opções para criação de factories no Rails. Seu uso contínuo se torna conveniênte em equipes, já que esta aplicação
gera objetos carregados de dados de maneira simples, prontos para serem utilizados num ambiente de teste. No entanto, tal comodidade pode vir a ser a futura dor de cabeça no quesito performance.

Irei mostrar os principais motivos pelo qual devemos ter cuidado ao utilizar o FactoryGirl.

**Existiria grande diferença entre estes dois exemplos?**

```ruby
@event = Factory.build :event
```

```ruby
@event = Event.new
```

Tudo depende de nossa definição na factory Event:

```ruby
FactoryGirl.define do
  factory :event do
    organizer
    name 'Corrida'
    sequence(:slug) { |n| "corrida#{n}" }
    address 'Avenida 123 - n 12'
    phone '(22) 2734-0503'
    email 'hi@mail.com'
  end
end
```

Utilizando esta factory nós não iremos apenas criar um novo objeto Event. Criaremos também um objeto Organizer, que é uma **dependência** de Event. Ou seja, estamos acessando a factory de organizer e definindo um objeto que muitas vezes nem iremos utilizar no teste.
___

# Performance
___

```ruby
Benchmark.realtime { 100.times { FactoryGirl.build :event } }
=> 12.933986
```

```ruby
Benchmark.realtime { 100.times { Event.new } }
=> 0.021129
```

Este exemplo mostra a implicação do uso inconsequente da FactoryGirl. Imagine quanto tempo acabamos perdendo em testes unitários que devem ser rodados constantemente. O principal fator causador de problemas de performance no primeiro caso é o acesso ao banco de dados. O FactoryGirl#build, apesar de não salvar o próprio objeto Event, ele possui um comportamento que sempre salva as dependências do objeto passado para este método, neste caso, o objeto Organizer.

----------
# Alto acoplamento
___
Testes rápidos são sempre bons, porém este não é o argumento principal para evitar o uso do FactoryGirl em testes unitários.

Ambientes de teste crescem, ficam complexos (assim como a aplicação), e chegam no ponto de visível lentidão para a prática de TDD por motivos citados acima. Quando isto acontece em testes unitários, algo está errado. Acabamos aceitando uma certa comodidade criada pelo alto acoplamento dos objetos gerados pelo FactoryGirl, e perdemos a especificidade do teste em questão.

## Exemplo:

Imagine a utilização desta factory:

```ruby
FactoryGirl.define do
  factory :event do
    organizer
    expired false
    open_to_enrollment true
    name 'Corrida'
    email 'hi@mail.com'
  end
end
```

Facilmente percebemos que caso alguém
tente descobrir a utilização do método Event#can_enroll? pelo primeiro exemplo
(utilizando FactoryGirl), não entenderá de fato o que o método faz.

Ou seja, além de ser muito mais custoso, estamos sendo implícitos.

**Com FactoryGirl**

```ruby
describe Event do
  it '#can_enroll?' do
    event = FactoryGirl.build :event, open_to_enrollments: true
    expect(event.can_enroll?).to be_true
  end
end
```

**Sem FactoryGirl**

```ruby
describe Event do
  it '#can_enroll?' do
    event = Event.new open_to_enrollments: true, expired: false
    expect(event.can_enroll?).to be_true
  end
end
```

**Definição do método**

```ruby
class Event
  def can_enroll?
    open_to_enrollments && !expired
  end
end
```

A questão é que sempre haverão mais validações, e mais dependências, causando um custo muito maior a médio/longo prazo, onde técnicas como Mocking & Stubbing se encaixam perfeitamente, tornando os testes específicos e principalmente unitários novamente.
