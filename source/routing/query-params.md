_Query params_ são valores que podem aparecer depois do `?` na URL. Por exemplo, na seguitne URL existem
dois  _query params_ um chamado de `sort` e outro `page`, com os valores `ASC` and `2` respectivamente:

```text
http://example.com/articles?sort=ASC&page=2
```

_Query params_ permitem que mais estados da aplicação sejam centralizados, estados dos quais
não deveriam fazer parte da URL (como por exemplo os do nosso ultimo exemplo). Um 
uso comum para _query params_ é representar o numero da pagina atual de uma coleção paginada
ou algum tipo de criterio de filtro ou ordem nos registros.

### Especificando _Query Params_

_Query params_ são declarados nos controllers que possuem um route. Por exemplo, para configurar
os _query params_ ativos na route `articles`, eles precisam ser declarados no `controller:articles`.

Para adiciciar um _query param_ chamado `category` que irá filtrar todos 
os artigos baseados em uma das categorias que serão especificadas como `queryParams`
desse nosso `controller:article`:

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: ['category'],
  category: null
});
```

Isso configura um _binding_ entre o _query param_ `category` na url,
e a propriedade `category` no `controller:articles`. Em outras palavras,
uma vez que a route `articles` tenha sido acessada, qualquer mudança ao 
_query param_ `category` da URL irá atualizar a propriedade `category` do 
controller, e vice-versa.

Agora nos vamos precisar definir uma _computed property_ para podermos filtrar
quais posts nosso template irá renderizar.

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: ['category'],
  category: null,

  filteredArticles: Ember.computed('category', 'model', function() {
    var category = this.get('category');
    var articles = this.get('model');

    if (category) {
      return articles.filterBy('category', category);
    } else {
      return articles;
    }
  })
});
```

Com esse codigo, nos estabelecemos o seguinte comportamento:

1. Se o usuario navegar de `/articles`, `category` será `null`, então os artigos não serão filtrados.
2. Se o usuario navegar para `/articles?category=recent`, `category` irá ter o valor `"recent"`, então os artigos comecarão a ser filtrados
3. Uma vez que estivermos dentro da route `articles`, qualuqer mudança para a propriedade
  `category` no `controller:articles` irá fazer com que a URL seja atualizada. 
  Por default, um _query param_ não irá causar uma transição, ou seja nenhum dos hooks serão disparados;
  Ele só atualizará a URL.

### Helper link-to 

O helper `link-to` pode suportar _query params_ por causa da sua sub-expressão
`query-params`.

```handlebars
// Explicitly set target query params
{{#link-to "posts" (query-params direction="asc")}}Sort{{/link-to}}

// Binding is also supported
{{#link-to "posts" (query-params direction=otherDirection)}}Sort{{/link-to}}
```

Nos exemplos acima, `direction` é um  _query param_ e uma propriedade no `controller:post`, 
mas ele tambem pode ser referir à propriedade `direction` de qualquer controller associado
com a hierarquia do route `posts`, procurando o primeiro controller que seja compativel 
com o nome dessa propriedade.

O helper `link-to` leva em mcontra os _query params_ quando esta determinando
seu estado de "ativo", e irá attribuir as classes apropriadas. O estado ativo
é determinado calculando se os _query params_ continuam o mesmo depois de um link 
ser clicado. Se eles forem compativeis eles serão definidos como true.

### transitionTo

`Route#transitionTo` e `Controller#transitionToRoute`
aceitam um ultimo argumento, que pode ser um objeto com a chave `queryParams`.

```app/routes/some-route.js
this.transitionTo('post', object, { queryParams: { showDetails: true }});
this.transitionTo('posts', { queryParams: { sort: 'title' }});

// if you want to transition the query parameters without changing the route
this.transitionTo({ queryParams: { direction: 'asc' }});
```

Você tambem pode adicionar _query params_ para transições da URL:

```app/routes/some-route.js
this.transitionTo('/posts/1?sort=date&showDetails=true');
```

### Opções dentro de uma transição completa

Argumentos passados para o `transitionTo`
ou `link-to` só correpespondem à uma mudando nos valores do _query param_,
e não irá mudar a hierarquia da rota, isso não é considerado uma transição completa,
oque significa que hooks como o `model` ou `setupController` não irão ser disparados,
mas apenas as propriedades definidas como _query param_ serão atualizadas no controller
assim como na URL.

Mas algumas mudanças desse tipo precisam carregar informações do servidor,
nesse caso é desejavel passar uma opção que defina essa transição como uma transição completa. 
Para fazer isso você pode usar um configuração opcional chamado `queryParams` no `Route` associado
com esse controller, e configurar esses _query params_ com a propriedade 
`refreshModel` para ser `true`:


```app/routes/articles.js
export default Ember.Route.extend({
  queryParams: {
    category: {
      refreshModel: true
    }
  },
  model(params) {
    // This gets called upon entering 'articles' route
    // for the first time, and we opt into refiring it upon
    // query param changes by setting `refreshModel:true` above.

    // params has format of { category: "someValueOrJustNull" },
    // which we can forward to the server.
    return this.store.query('articles', params);
  }
});
```

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: ['category'],
  category: null
});
```

### Atualizar a URL com `replaceState`

Por padrão, toda vez que você mudar a propriedade do seu controller que represente um
_query param_, o ember vai usar o `pushState` para atualizar a URL na barra de endereço,
mas você pode o metodo `replaceState` no lugar (isso faz com que esse item não
seja adicionado no seu navegador), você pode especificar esses parametros dentro 
do `queryParams` na hash de configuração do seu `Route`, veja o exemplo abaixo:

```app/routes/articles.js
export default Ember.Route.extend({
  queryParams: {
    category: {
      replace: true
    }
  }
});
```

Note que o nome dessa propriedade de configuração tem seu valor 
default como `false` é similar com os helpers `link-to`, que tambem permitem
que você adicione opções para fazer um `replaceState` usando um `replace=true`.

### Mapeando a propriedade de um controller com um query param diferente

Por default, especificando `foo` como um _query param_ no seu controller irá 
fazer o bind dessa query com o mesma key na URL, por exemplo, a propriedade `foo` faz binding com `?foo=123`.
Você tambem pode mapear essas propriedade para agirem de maneira diferente usando a seguitne
sintaxe de configuração:

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: {
    category: 'articles_category'
  },
  category: null
});
```

Isso irá fazer que mudanças na `category` no `controller:articles`
atualize o _query param_ `articles_category`, e vice-versa.

Note que os _query params_ que não precisam dessa configuração adicional ainda podem ser passados 
como strings para o array `queryParams`.

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: ['page', 'filter', {
    category: 'articles_category'
  }],
  category: null,
  page: 1,
  filter: 'recent'
});
```

### Valores Default e deseralização

No exemplo abaixo, attribuimos um valor padrão (no caso `1`) para uma propriedade _query param_.

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: 'page',
  page: 1
});
```

Isso afeta o comportamento dos _query params_ em duas maneiras:

1. Os valores dos _query param_ são convertidos para o mesmo tipo valor de seu 
   default, pro exemplo, quando uma url mudar `/?page=3` para `/?page=2` irá fazer
   com que a propriedade `page` do `controller:articles` seja definida como o numero `2` (integer),
   no lugar de ser definido como `"2"` (string). O mesmo se aplica para se os valores forem booleanos.
2. Quando o parametro da URL for o apenas definida pelo seu valor default
   ela não vai ser serializada na URL. Então no exemplo acima quando o 
   `page` for igual à `1`, a url irá ser `/articles`,
   mas depois que algo mude o valor dessa propriedade para `2`, a url será atualizada para ser 
  `/articles?page=2`.

### Valores 'Sticky' para os _Query Params_

Por padrão, valores _query params_ no ember são "sticky", isso é, se você 
mudar esse valor e então mudar de rota, quando você voltar à essa rota o novo valor 
desse query param vai continuar sendo o antigo (em vez de resetar ele para o valor default). 
Isso é excelente para manter uma boa navegação se usarmos algum tipo de _query param_ para fazer filtros
ou ordenar informações.

Alem disso, esses valores são guardados baseados nos models carregados dentro desses routes.
Então se tivermos um route `team` com seguimento dinamico chamado `/:team_name` e uma propriedade
no controller para "filter",
Se você navegar para `/badgers` e filtrar por `"rookies"`, e então navegar para `/bears` 
e fitlrar por `"best"`, e navegar para o `/potatoes` e filtrar por `"lamest"`, essas serão 
as URLs geradas na sua barra de navegação.

```handlebars
{{#link-to "team" "badgers"}}Badgers{{/link-to}}
{{#link-to "team" "bears"}}Bears{{/link-to}}
{{#link-to "team" "potatoes"}}Potatoes{{/link-to}}
```

Os links gerados seriam algo

```html
<a href="/badgers?filter=rookies">Badgers</a>
<a href="/bears?filter=best">Bears</a>
<a href="/potatoes?filter=lamest">Potatoes</a>
```

Isso nos mostra qeu quando você muda um _query param_, ele e guardado e associado ao model
carregado naquele route.

Se você precisar resetar um _query param_, você tem duas opções:

1. passar explicitamente o valor default do _query default_ para o
   `link-to` or `transitionTo`
2. usar o hook `Route.resetController` para atribuir o valor default de volta para o _query param_
   antes de sair de um route, ou quando route é alterado.

No exemplo abaixo, o a propriedade _query param_ tem o valor resetado para 1, 
_enquanto ainda esta no escopo da pre-transição do model `ArticlesRoute`_.
O resultado disso é que todos os links que apontam para essa rota 
terão o um novo valor para a propriedade `page` atribuidos para `1`.

```app/routes/articles.js
export default Ember.Route.extend({
  resetController(controller, isExiting, transition) {
    if (isExiting) {
      // isExiting would be false if only the route's model was changing
      controller.set('page', 1);
    }
  }
});
```

Em alguns casos, você pode não querer que esse query param faça parte do escopo da model 
da route, mas prefirir usar esse query param mesmo quando rota mudar. para fazer isso nos podemos
passar uma nova propriedade chamada `scope` dentro do `"controller"` em que você criou a hash `queryParams`:

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: [{
    showMagnifyingGlass: {
      scope: 'controller'
    }
  }]
});
```

Abaixo você pdoe ver como fazer override tanto do escopo e o valor do _query param_
de uma propriedade no _query param_ do seu controller:

```app/controllers/articles.js
export default Ember.Controller.extend({
  queryParams: ['page', 'filter',
    {
      showMagnifyingGlass: {
        scope: 'controller',
        as: 'glass'
      }
    }
  ]
});
```
