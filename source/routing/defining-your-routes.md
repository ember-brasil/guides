Quando sua aplicação rodar, o router procura uma combinação da sua url que
você tenha definido no seu _routes_. As rotas, por sua vez, são responsaveis
por mostrar o template, carregar os dados, ou então configurar o estado da sua aplicação.

## Routes Basicas

O metodo [`map()`](http://emberjs.com/api/classes/Ember.Router.html#method_map)
da rota de sua aplicação Ember pode ser invocado para definir seus 
mapeamentos de URL. Quando chamarmos o `map()`, você deve passar uma função como parametro
que sera invocada com o valor `this` no objeto em que você quer criar as rotas.

```app/router.js
Router.map(function() {
  this.route('about', { path: '/about' });
  this.route('favorites', { path: '/favs' });
});
```

Agora, quando o usuario visitar `/about`, o ember vai renderizar o template `about`. 
acessando `/favs` irá renderizar o template `favorites`.

Você pode deixar sem o _path_ se a rota tiver o mesmo nome da url. 
Nesse caso, nosso exemplo poderia ficar assim:

```app/router.js
Router.map(function() {
  this.route('about');
  this.route('favorites', { path: '/favs' });
});
```

Dentro dos seus templates, você pode usar [`{{link-to}}`][1] para navegar entre as rotas,
usando o nome que você deu no metodo `route`.

[1]: http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_link-to

```handlebars
{{#link-to "index"}}<img class="logo">{{/link-to}}

<nav>
  {{#link-to "about"}}About{{/link-to}}
  {{#link-to "favorites"}}Favorites{{/link-to}}
</nav>
```

O helper `{{link-to}}` tambem ira adicionar uma classe `active` no link que estiver apontando
para a rota atual.

## Routes Aninhadas

Muitas vezes você pode querer que um template seja renderizado dentro de outro template.
Por exemplo, em um sistema de blog, em vez de ir de uma lista de posts para outra pagina em que
alguem cria um post, você pode querer que exista uma lista de posts na pagina de criação deles.

Nesses casos, você precisa aninhar routes para exibir um template dentro do outro.

Você pode aninhar rotas passando um _callback_ para o `this.route`:

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('new');
  });
});
```

E então adicionar o helper `{{outlet}}` no seu template pai daqueles que você aninhou:

```templates/posts.hbs
<h1>Posts</h1>
<!-- Display posts and other content -->
{{outlet}}
```

Esse router cria uma rota para `/posts` e para `/posts/new`. Quando o usuario visita `/posts`, 
Eles irá ver o template `posts.hbs`. (Abaixo, [index
routes](#toc_index-routes) explica a importante adição ao _this_.) Quando o 
usuario visita `posts/new`, ele irá ver o template `posts/new.hbs` renderizado
dentro do `{{outlet}}` do template do `posts`.

O nome de uma rota aninhada contem o nome das rotas superiores.
Se você quiser uma transição para uma rota (tanto usando o `transitionTo` ou `{{#link-to}}`), se certifique
de usar o nome completo da rota, (`posts.new`, e não `new`).

## A route application

A `application` é acessada na primeira vez que seu sistema é acessado. Como as outras rotas
ela irá carregar um template com o mesmo nome (no nosso caso `application`) por default.
Você pode colocar seu _header_, _footer_, e qualquer outro conteudo decorativo nesse arquivo. 
Todas as outras rotas irão renderizar seus templates dentro do `{{outlet}}` do template do `application.hbs`.

Essa rota faz parte de todas as aplicações, você não precisa especificar ela no seu `app/router.js`.

## Routes Index

Em todos os leveis de aninhamento (incluindo o level superior), o Ember.js
automaticamente irá nos prover uma rota para o path `/` chamada `index`.

Por exmeplo, se você codificar esse router abaixo:

```app/router.js
Router.map(function(){
  this.route('favorites');
});
```

Ele será o equivalente à:

```app/router.js
Router.map(function(){
  this.route('index', { path: '/' });
  this.route('favorites');
});
```

O template `index` será renderizado dentro do `{{outlet}}` do template da
`application`. Se o seu usuario navegar para o `/favorites`,
o ember irá trocar o template do `index` pelo template do `favorites`.

Uma rota aninhada do tipo:

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('favorites');
  });
});
```

Seria a mesma coisa que:

```app/router.js
Router.map(function(){
  this.route('index', { path: '/' });
  this.route('posts', function() {
    this.route('index', { path: '/' });
    this.route('favorites');
  });
});
```

Se o usuario navegar para `/posts`, a route atual irá ser
`posts.index`, usando o template `posts/index` que será renderizado 
dentro do `{{outlet}}` no template do `posts`.

Se eu usuario navegar para `/posts/favorites`, o template do `{{outlet}}` irá 
deixar de ser `posts` e passará a ser o template do `posts/favorites`.

## Seguimentos Dinamicos

Uma das responsabilidades de uma rota é carregar um model.

Por exemplo, imagine que temos uma rota chamada `this.route('posts');`, ela 
irá carregar todos os posts de nossa aplicação de blog.

Porque o `/posts` representa um model fixo, nos não precisamos de nenhuma informação 
aidcionar para saber quais dados recuperar. No entanto, se nos queremos uma rota 
para um unico post, não seria nada pratica ter que especificar cada post possivel dentro
de um router.

Aqui que entramos nos _seguimentos dinamicos_.

Um segmento dinamico é uma parte da URL que começa com `:` e é seguida por um identificador.

```app/router.js
Router.map(function() {
  this.route('posts');
  this.route('post', { path: '/post/:post_id' });
});
```

Se o usuario navegar para o `/post/5`, a rota então terá o `post_id` igual à `5` 
para então usar esse numero e carregar o post correto. Veja [Specifying a Route's
Model](../specifying-a-routes-model) para saber mais sobre como carregar um model.

## Wildcard / englobamento dos routes

Você pode definir routes wildcard que irão corresponder à um determinados multimos conjutos de URL. 
Isso pode ser usado, por exemplo, se você gosta de pegar todas as rotas não especificadas no seu app
e atribuilas para uma rota especifica.

```app/router.js
Router.map(function() {
  this.route('page-not-found', { path: '/*wildcard' });
});
```

## Resetando o Namespace de Routes Aninhados

Quando estamos aninhamos rotas, talvez seja interessante que uma das rotas não herde o nome de sua ancestral. 
Isso vai permitir que você referencie e reuse determinada rota em varios lugares da mesma _tree_ assim como
manter o nome dessa classe curto.

Você pode fazer isso passando como parametro uma opção chamada `resetNamespace: true`.

```app/router.js
Router.map(function() {
  this.route('post', { path: '/post/:post_id' }, function() {
    this.route('edit');
    this.route('comments', { resetNamespace: true }, function() {
      this.route('new');
    });
  });
});
```

Do mesmo jeito que antes, o template `comments` irá ser renderizado dentro do template do `{{outlet}}` no `post`, e todos
os templates dentro do `comments` (`comments/index`
e `comments/new`) irão ser renderizados dentro do _outlet_ nos `comments`.

Mas de qualquer forma, o path `/post/:id/comments` irá carregar o tempalte `comments.hbs`,
em vez do template `post/comments.hbs`.

## Route Handlers

Quando vocÊ cria uma rota para fazer algo alem de renderizar um template com o mesmo nome, você 
irá precisar criar um route handler. As seguintes sessões irão explorar diferentes features dosroute handlers. For more information on routes, veja a especificação para [o router](http://emberjs.com/api/classes/Ember.Router.html) e para os [route
handlers](http://emberjs.com/api/classes/Ember.Route.html).
