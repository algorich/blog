# algorich.github.com

Este é o código do Blog da [Algorich][http://algorich.com.br], que utiliza o [Jekyll bootstrap](http://jekyllbootstrap.com/) e é hospedado pelo github.

## Instalação

    gem install jekyll
    sudo apt-get install python-pygments

## Padrões

### Nomes de arquivos

Em itálico (usando o *)

### Links externos

Devem ter o atributo `target="_blank"`.

### Imagens

Devem ser salvas seguindo o seguinte padrão:

    /assets/images/<ano>/<mês>

### Dados do post

Cada post deve ter os seguintes dados:

    ---
    layout: post
    author: identificador do autor (que deve estar setado no _config.yml)
    title: titulo do post
    category : <categoria do post (com todas as letras minúsculas)>
    tags : [tags, do, post, com, todas, letras, minúsculas]
    description: 'Descrição do post.'
    keywords: 'palavras, chave, do, post'
    facebook_image: '/assets/images/<ano>/<mes>/arquivo.extensão'
    ---

Para criar um novo post, use o comando `rake post`.

### Blocos de código

Utilize o bloco de código do [Liquid](https://github.com/shopify/liquid/wiki/liquid-for-designers):

    {% highlight language %}
    # código aqui
    {% endhighlight %}

Para fazer o highlighting, é utilizado o pygments. Para ver as linguagens suportadas use o comando `pygmentize -L`. Como são muitas, utilize o grep para buscar o que deseja, por exemplo:

    $ pygmentize -L | grep apache
    * apacheconf, aconf, apache:
    ApacheConf (filenames .htaccess, apache.conf, apache2.conf)

Neste caso você pode utilizar `apacheconf`, `aconf` ou `apache` para fazer o highlighting de arquivos de configuração do Apache.