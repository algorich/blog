---
layout: post
author: Douglas Oliveira Camata
title: "Integração e Deploy Contínuos (Parte 1)"
date: 2014-05-07 14:00
comments: true
description: 'Como integração e deploy contínuos podem ajudar você, sua equipe e seu cliente a perderem menos tempo no dia-a-dia'
keywords: 'integracao continua, deploy continuo, jenkins, capistrano'
categories: [gerencial, douglas oliveira camata]
facebook_image: '/posts-assets/images/2014/05/jenkins.png'
published: true
---

Quando se tem projetos com uma grande quantidade de desenvolvedores é comum nos
encontrar em uma situação onde erros com relação ao código e testes
desenvolvidos acontecem. Você pode dizer, "ah, meu projeto tem 100% de cobertura
de testes, isso nunca acontecerá", (mesmo sabendo que 100% de cobertura de
testes é apenas uma ilusão) mas você sabe que acontece. Primeiramente, somos
humanos. Eventualmente vamos errar, até mesmo o teste pode estar errado.

O que fazer quando temos aquela clássica situação de "funciona na minha máquina,
o problema é na sua"? Todos já passaram por isso pelo menos uma vez na vida.
Agora, a parte que costuma ser esquecida: rodar os testes somente no seu
computador não é o suficiente! Por que? Porque ele está viciado. Sim, você já
estava com o projeto "semi pronto" e isso pode deixar passar alguns problemas.

O ideal, seria que você construísse um novo ambiente, do zero, realizasse todo o
processo de preparação dele para rodar a aplicação e então rodasse os testes.
Mas se o projeto é grande, rodar todos os testes já deve tomar um tempo
considerável, imagina então montar o ambiente do zero... Para nos ajudar com
este problema, nós, na Algorich a técnica de **integração contínua**.

<!-- more -->

## Integração Contínua

Mas que diabos é isso?

{% blockquote Martin Fowler, http://martinfowler.com/articles/continuousIntegration.html %}
Integração Contínua é uma pratica de desenvolvimento de software onde os membros de um time integram seu trabalho frequentemente, geralmente cada pessoa integra pelo menos diariamente – podendo haver multiplas integrações por dia. Cada integração é verificada por um build automatizado (incluindo testes) para detectar erros de integração o mais rápido possível. Muitos times acham que essa abordagem leva a uma significante redução nos problemas de integração e permite que um time desenvolva software coeso mais rapidamente.
{% endblockquote %}

Resumidamente, o tal "build automatizado" é quem vai fazer aquele procedimento
trabalhoso de montar o projeto em um ambiente limpo e então verificar se os
testes do projeto estão passando. Em um caso de falha de algum dos testes,
toda a equipe é rapidamente notificada via email (o que pode facilitar
integração com outros serviços externos, como o Trello). A partir dai, a equipe
pode rapidamente idenfiticar a fonte deste erro e corrigî-lo, para que não
sejam introduzidos erros no projeto junto com uma nova funcionalidade ou
refatoração.

Mas não para por ai, calma, jovem. Um processo de integração contínua pode
fazer muito mais por você do que você imagina. Vamos pensar: o que você precisa,
ou desejaria, que fosse feito a cada commit enviado para o repositório? Aposto
que você pensou naquele deploy para o servidor de staging. Se não pensou, ou
nem tem um servidor de staging, ou nem sabe o que é isso, meu amigo... Recomendo
que você leia <a href="http://www.kalzumeus.com/2010/12/12/staging-servers-source-control-deploy-workflows-and-other-stuff-nobody-teaches-you/" target="_blank">esse material</a>.

## Deploy contínuo

Então, se a idéia de integração contínua é automatizar as coisas (inicialmente,
a integração do seu código com o repositório), por que não automatizar também
o deploy para o ambiente staging? Assim, seu cliente terá um ambiente muito
próximo ao de produção para poder testar as funcionalidades e encontrar bugs
enquanto isso. Acredite, a quantidade de bugs que seus clientes encontrarão
no servidor de staging vai te assustar, porque nós, desenvolvedores, não
pensamos como usuários, por mais que nos esforcemos e acabamos deixando muita
coisa passar. E nada melhor do que resolver isso antes do projeto entrar em
produção, certo?

Mas peraí, faltou um detalhe: não podemos deixar o ambiente de staging uma
bagunça! Vamos atualizar o código de staging se, se somente se, todos os testes
do build passarem, assim podemos ter (e dar ao cliente também) uma garantia maior
que o sistema não possui algum comportamento indesejado ou bug.

Agora você deve estar pensando em copiar e colar todos aqueles comandos que você
usa para fazer seu deploy, né? Pois é, vamos dar um jeito nisso. A idéia é
automatizar tudo! Testes foram automatizados para poupar tempo e evitar falha
durante este processo delicado e repetitivo dentro da metodologia ágil, por que
não automatizar o deploy também? Para cada caso, cada linguagem e até framework,
existe a ferramenta de automatização de deploy adequada. Para nossos projetos
web, onde usamos Ruby on Rails, utilizamos o <a href="http://capistranorb.com/">Capistrano</a>
para esta tarefa, que com os scripts e recipes pré-definidos, basta um simples
comando para fazermos um deploy para o servidor de staging ou produção, quando
necessário, sem qualquer preocupação, sem erros.


## Colocando tudo isso pra funcionar

Como dito anteriormente, nós da Algorich, utilizamos integração e deploy
contínuos em nossos projetos. Para isso, usamos o
<a href="http://jenkins-ci.org/">Jenkins</a> (uma conhecida ferramenta de
integração contínua, software livre, e em constante evolução),
<a href="https://gitlab.com/">Gitlab</a> (nosso repositório de código) e o
<a href="https://trello.com">Trello</a> (organização e gerenciamento dos
projetos).

<img class="alignright" title="Jenkins" src="/posts-assets/images/2014/05/jenkins.png" alt="Jenkins" width="200" height="200" />
E como isso tudo se junta? Bem, através do plugin
<a href="https://wiki.jenkins-ci.org/display/JENKINS/Gitlab+Hook+Plugin">Gitlab Hook</a>
para o Jenkins, ele ficará escutando em uma determinada URL para receber
requests que informarão sobre novos commits enviados ao repositório remoto. Em
seguida, configuramos nos nossos repositórios para que eles enviem essa esperada
requisição a cada push feito, através da interface de administração do Gitlab, e
pronto, temos uma build disparado no projeto. Novamente no Jenkins, dizemos
qual é o tipo de versionamento do projeto, o endereço do repositório remoto e
quais comandos ele executará após clonar o projeto. Estes comandos são:
instalação das dependências, um drop em qualquer banco de dados do projeto
existente, execução das migrações do banco de dados ou criação das tabelas,
execução dos testes e o deploy para staging. É importante notar que, caso sua
ferramenta de testes gere uma saída de erro para o terminal, o build não chegará
na parte do deploy. Caso contrário, você precisará fazer algo para "descobrir"
quando aconteceu algum erro e evitar manualmente que o deploy seja iniciado.

O Jenkins é uma ferramenta completíssima e com inúmeros plugins, então pensamos:
vamos integrar isso com o Trello? Vamos! E qual é a alternativa mais simples?
Não sei se vocês sabem, mas é possível criar cards em um quadro do Trello
via email. Dito e feito. Após a execução do build, em caso de falha, um email é
enviado para um board do Trello específico para nossas builds (previamente
criado por nós), que transforma-o em um card na lista correta, bingo!

## Conclusão

Através desse grupo de ferramentas, conseguimos, dentre muitos outros pontos:

1. Diminuir a quantidade de deploys enviados para staging com bugs, e ainda mais
  para produção;

+ Diminuir a quantidade de pedidos do tipo "ai cara, faz um deploy pra mim"
  durante todo o dia de trabalho;

+ Ter um feedback ainda mais rápido dos nossos clientes, que viam as
  funcionalidades assim que o build e deploy finalizava com sucesso e o card
  da tarefa no Trello era movido para "Pronto";

Bom galera, tentamos fazer aqui apenas uma descrição sucinta do que é deploy e
integração contínua, apresentar as ferramentas que usamos para realizar estes
importantes processos e como interligamos elas. Aguardem novos posts com
tutoriais passo-a-passo de como configurar todas elas para fazer sua própria
integração contínua!

