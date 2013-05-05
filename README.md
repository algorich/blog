# Blog

Este é o código do [Blog da Algorich](http://blog.algorich.com.br), que utiliza
o [octopress](http://octopress.org/) e é hospedado aqui como um github page.

## Padrões

### Nomes de arquivos

Em itálico (usando o *)

### Links externos

Devem ter o atributo `target="_blank"`.

### Imagens

Devem ser salvas seguindo o seguinte padrão:

    /posts-assets/images/<ano>/<mês>

Para centralizar uma imagem, ela deve estar dentro de uma tag `<div class="aligncenter">`.

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

Para criar um novo post, use o comando `rake post` que deve ser usado da seguinte maneira:

    rake post title="A Title" [date="2012-02-09"]

Se não for passada a data, é adicionada a data de hoje.

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

### Iframe

Para adicionar um iframe, deve-se adicionar algo entre as tags:

    <iframe src="source">alguma coisa</iframe>

Se não for adicionado algum conteúdo entre as tags, o conteúdo da página após o iframe não será renderizado.