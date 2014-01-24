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

### Blocos de código

Utilize o bloco de código no estilo do markdown do github:

    ``` language
    # código aqui
    ```

### Iframe

Para adicionar um iframe, deve-se adicionar algo entre as tags:

    <iframe src="source">alguma coisa</iframe>

Se não for adicionado algum conteúdo entre as tags, o conteúdo da página após o iframe não será renderizado.