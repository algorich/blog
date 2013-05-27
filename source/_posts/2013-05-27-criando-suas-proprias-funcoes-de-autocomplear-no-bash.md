---
layout: post
author: Hugo Maia Vieira
title: 'Criando suas próprias funções de autocomplear no Bash'
description: 'Um dos melhores amigo de quem usa a linha de comando é o TAB. Mas e quando o comando não tem um autocompletar? Neste post vou mostrar como você pode criar suas próprias funções de autocompletar!'
keywords: 'bash, shell script, autocompletar, completion'
comments: true
categories: [shell script, hugo maia vieira]
---

Um dos melhores amigo de quem usa a linha de comando é o TAB. Além de ajudar a
economizar um bocado de digitação, ele te lembra das opções dos comandos e dos
diretórios ou arquivos que está tentando acessar.

Mas e quando o comando não tem um autocompletar? Ou quando você gostaria de
seus próprios scripts tivessem um autocompletar?

Neste post vou mostrar como você pode criar suas próprias funções de auto
completar! =)

<!-- more -->

Para essa tarefa, o Bash nos fornece os comandos `complete` e `compgen`, além de
algumas variáveis:

- **COMP_LINE**: a linha de comando no momento
- **COMP_WORDS**: lista de argumentos passados para o comando no momento
- **COMP_CWORD**: o índice do argumento onde o cursor está no momento
- **COMP_WORDBREAKS**: lista de caracteres separadores de palavras
- **COMPREPLY**: um array contendo as possíveis "completadas"

## Capturar o argumento atual

Utilizando as variáveis fornecidas pelo Bash, podemos fazer o seguinte:

``` bash
current=${COMP_WORDS[COMP_CWORD]}
```

Assim, se estivermos digitando "nome mari" e apartarmos [TAB][TAB], o valor de
`current` será "mari".


## Lista de possíveis "completadas"

Para isso vamos utilizar o comando `compgen` com a opção `-W`. Ele vai receber
uma lista de palavras, comparar com a palavra desejada e retornar uma lista de
possíveis "completadas", por exemplo:

``` bash
$ compgen -W 'pedro mario maria joão mariana' mari
mario
maria
mariana
```

Sendo assim, podemos utilizar o resultado do `compgen` para popular a variável
COMPREPLY que será utilizada pelo comando `complete`.


## Criando nossa primeira função

As funções de autocompletar podem ficar em um arquivo na a pasta
*/etc/bash_completion.d* (que inclusive é um ótimo lugar para ver exemplos de
código) ou ficar direto no seu arquivo *~/.bashrc*. Para simplificar, vou usar o
*~/.bashrc* e adicionar o código:

``` bash
_nome() {
  local current=${COMP_WORDS[COMP_CWORD]}
  COMPREPLY=( $(compgen -W 'pedro mario maria joão mariana' $current) )
}
complete -F _nome nome
```

Estamos dizendo ao comando `complete` que utilize a função `_nome` para
modificar a variável `COMPREPLY` com as possíveis "completadas" para o comando
`nome`. Recarregue o seu *~/.bashrc* e teste:

``` bash
$ souce ~/.bashrc

$ nome [TAB][TAB]
joão     maria    mariana  mario    pedro
$ nome ma[TAB]
$ nome mari[TAB][TAB]
maria    mariana  mario
$ nome marian[TAB]
$ nome mariana
```

Agora que já entendemos como funciona, vamos para um exemplo real.

## Oh my gems!

Um exemplo bem simples é o autocompletar que criei para o
[Oh my gems!](http://blog.zenspider.com/blog/2012/09/ohmygems.html) (um
substituto para os gemsets do [rvm](https://rvm.io), geralmente usado em
conjunto com o [rbenv](http://rbenv.org/)). Além da opção *reset*, o comando
`ohmygems` pode receber como parâmetro o nome de um novo "gemset" ou dos
"gemsets" existentes, que são nada mais que os os subdiretórios de
*~/.gem/repos*:

``` bash
_ohmygems() {
  local current=${COMP_WORDS[COMP_CWORD]}
  local options="reset `ls ~/.gem/repos`"
  COMPREPLY=( $(compgen -W "$options" $current) )
}
complete -F _ohmygems ohmygems
```

Agora vamos para um exemplo um pouco mais complexo.

## Rake

Acredito que todo programador ruby utiliza o [Rake](http://rake.rubyforge.org/)
(Ruby Make). Contudo, diferente do [Make](http://www.gnu.org/software/make/), o
Rake não cria automaticamente um autocompletar com as tasks que você define no
Rakefile. Mas nós podemos criar criar um! =)

Para ver todas as tasks disponíveis, podemos utilizar a opção `-P` ou
`--prereqs` do `rake`, que mostra as tasks e suas dependências:

``` bash
$ rake -P
rake clean
rake deploy
rake gen_deploy
    integrate
    generate
    deploy
rake generate
...
```

Beleza. Agora, para pegar apenas as tasks vamos usar o `grep` e o `cut`:

``` bash
$ rake -P | grep '^rake' | cut -d ' ' -f 2
clean
deploy
gen_deploy
generate
...
```

Já temos nossa lista de possíveis "completadas", então vamos criar nossa função:

``` bash
_rake() {
    local current=${COMP_WORDS[COMP_CWORD]}
    local tasks=`rake -P | cut -d ' ' -f 2`
    COMPREPLY=( $(compgen -W "$tasks" $current) )
}
complete -F _rake rake
```

Geralmente, as tasks do `rake` são separadas por contextos e esses contextos são
separados por ":". Por exemplo `rake db:drop:all`. A variável COMP_WORDBREAKS,
que guarda os caracteres separadores de palavras para o autocompletar, tem como
valor original a lista `"'><;|&(:`. Repare que o : está entre esses caracteres,
mas não queremos que ele seja um separador de palavras. Para removê-lo, vamos
adicionar o seguinte antes da nossa função no *~/.bashrc*:

``` bash
export COMP_WORDBREAKS=${COMP_WORDBREAKS//:}
```

Com isso você já tem um autocompletar para o `rake`. No entanto, se estiver em
um projeto Rails, o `rake` carrega a aplicação antes de executar as tasks.
Assim, a cada vez que você faz `rake [TAB][TAB]` vai levar alguns segundos para
te mostrar as possíveis tasks, o que torna o auto complete inútil. Para resolver
esse problema, podemos criar um arquivo que será nosso cache de tasks do `rake`
e apenas atualizá-lo se algum dos arquivos de tasks for alterado depois da
criação do cache. Podemos verificar também se existem o arquivo *Rakefile* antes
de executar qualquer coisa. Nossa função de autocompletar final para o `rake`
fica assim:

``` bash
#!/bin/bash

export COMP_WORDBREAKS=${COMP_WORDBREAKS//:}

_rake() {
    if [ -f Rakefile ]; then
        local cache_file='.rake_tasks_cache'
        local current=${COMP_WORDS[COMP_CWORD]}
        local recent=`ls -t $cache_file Rakefile **/*.rake 2> /dev/null | head -n 1`

        if [[ $recent != $cache_file ]]; then
            rake -s -P | grep '^rake' | cut -d ' ' -f 2 > $cache_file
        fi
        COMPREPLY=($(compgen -W "`cat $cache_file`" $current))
    fi
}

complete -o default -F _rake rake
```

No [manual de referência do bash](http://migre.me/epSSg) do site do projeto GNU
você encontra a [documentação](http://migre.me/epT2p)
[completa](http://migre.me/epSUw) para a criação de funções para autocompletar.
Você pode criar funções bem mais complexas, que completam de acordo com a opção
anterior e etc. Só depende da sua criatividade e habilidade com shell script =)
