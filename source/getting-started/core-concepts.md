Antes de começar a programar em Ember, é bom ter uma visão geral de como uma aplicação Ember funciona.

## Templates

Templates são as interfaces da aplicação, escritos em Handlebars que é uma linguagem de markdown. Junto com HTML puro, templates podem ter expressões como `{{title}}` ou `{{author}}`, assim você pode inserir informações em um contêiner no HTML. Eles também podem conter helpers, como por exemplo,`{{#if isAdmin}}30 people have viewed your blog today.{{/if}}`. Mas em geral, eles terão componentes, como por exemplo, um template pode listar todos os posts de um blog renderizando um componente para cada post.

## Componentes

Componentes são a principal maneira em que interfaces são organizadas no Ember.
Eles são formados por duas partes: um template, e um arquivo escrito em JavaScript que
define o comportamento desse componente. Por exemplo, um blog talvez tenha um componente para listar todos os posts chamado `all-posts`, e outro para mostrar cada um chamado `view-post`. Se os usuários puderem dar upvote em um post, o componente `view-post` teria um comportamento do tipo _quando o usuário clicar no botão de upvote, incremente o valor da propriedade `vote` por 1_.

## Controllers

Controllers são muito parecidos com componentes, tão parecidos que nas futuras versões
do Ember, controllers vão ser completamente substituídos por componentes. Mas hoje,
componentes não podem ter uma route (veja abaixo), mas quando isso mudar, será
recomendado trocar todos os controllers por componentes.

## Modelos

Modelos representam o _camada persistente_ da nossa aplicação. Por exemplo,
um blog iria querer salvar o conteúdo de um post quando um usuário o publicasse,
então esse blog teria um modelo definindo esse post, provavelmente um modelo `Post`.
Um modelo normalmente gerencia informação que vai ser enviada para um servidor,
porem modelos podem ser configurados para salvar dados em qualquer lugar, como por exemplo, no _Local Storage_ do navegador.

## Routes

Routes carregam um controller e um template. Elas também pode carregar um ou mais modulos, para passar informações para o controller e que serão renderizadas pelo template.
Por exemplo, a rota `all-posts` irá carregar todos os posts vindo do modelo `Post`,
carregar o controller `all-posts`, e renderizar o template de `all-posts`.
Do mesmo jeito que uma route `view-post` iria passar os dados do modelo para o controller
`view-post` e assim renderizar o template.

## The Router

O router mapeia a URL para uma route. Por exemplo, quando o usuário visitar a URL `/posts`, o router irá carregar a rota `all-posts`. O router também pode carregar rotas aninhadas.

Por exemplo, se seu app do blog tiver uma lista de posts no canto da tela, e um post no centro, nos podemos dizer que a rota `all-posts` estava aninhada com a `view-post`.

Talvez a coisa mais importante para se lembrar é que o no Ember o estado da aplicação é obtido a partir da URL. A URL determina qual rota carregar, que depois irá definir qual modelo, controller e template vão ser usados.





