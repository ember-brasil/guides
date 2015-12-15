Algumas vezes, você vai precisar que um template mostre dados vindos de um model.
Carregar o model correto para cada template é uma das responsabilidades de uma route.

Por exemplo, veja esse router:

```app/router.js
Router.map(function() {
  this.route('favorite-posts');
});
```

Para carregar um model para a route `favoritePosts`, você precisa usar um [`model()`][1] 
hook dentro do route handler do `posts`:

[1]: http://emberjs.com/api/classes/Ember.Route.html#method_model

```app/routes/favorite-posts.js
export default Ember.Route.extend({
  model() {
    return this.store.query('post', { favorite: true });
  }
});
```

Normalmente, um hook `model` deve retornar um registro [Ember Data](../../models/),
mas ele tambem pode retornar qualquer objeto [promise](https://www.promisejs.org/) (registros do Ember
Data são promessas), ou mesmo um objeto ou array nativo do JavaScript. O Ember vai 
esperar até que esses dados terminem de carregar (quando sua promessa for resolvida) antes de renderizar 
qualquer template.

O valor retornado de um `model` hook estará disponivel no seu template e no seu controller a partir da 
propriedade `model`:

```app/templates/favorite-posts.hbs
<h1>Favorite Posts</h1>
{{#each model as |post|}}
  <p>{{post.body}}</p>
{{/each}}
```

## Models Dinamicos

Algumas rotas sempre irão exibir o mesmo model. Por exemplo, a route `/photos`
irá sempre exibir a mesma lista de fotos disponiveis na sua aplicação. Se
seu uario deixar essa route e voltar para ela mais tarde, ela não irá seu model não será alterado.

No entanto, você pode ter uma route no qual o model mude dependendo da intereção do usuario. 
Por exemplo, imagine um app visualizador de fotos. A route `/photos` irá renderizar
o template `photos` com uma lista de fotos como model, esse no qual nunca muda. 
Porem quando o usuario clicar em uma foto em particular, nos queremos exibir auqle model com o 
template `photo`. Se o usuario voltar ou clicar em uma foto diferetne, nos 
vamos querer exibir o template de `photo` novamente, só que dessa vez com um model diferente.

Em casos como esse, é importante que você inclua alguma informação sobre a URL não apenas sobre 
qual template ela irá exibir, mas tambem quais models.

No ember, nos conseguimos definr routes com [seguimentos dinamicos](../defining-your-routes/#toc_dynamic-segments).

Uma vez que você definir uma rota com um seguimento dinamico, o 
Ember irá extrair o valor de cada seguimento dinamico a partir da URL
e passar para a o `model` hook como seu primeiro argumento:

```app/router.js
Router.map(function() {
  this.route('photo', { path: '/photos/:photo_id' });
});
```

```app/routes/photo.js
export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('photo', params.photo_id);
  }
});
```

Em `model` hooks para routes com seguimentos dinamicos, é nosso trabalho transformar
o ID (algo como `47` ou mesmo um `post-slug`) em um model que pode ser renderizado pelo template de um route. 
No exemplo acima nos usamos (`params.photo_id`) como sendo ID das fotos, esse argumento vai ser passado 
para o metodo `findRecord` do Ember Data's.

Nota: Uma rota com seguimento dinamico só irá ter seu `model` hook chamado
quando for acessado pela URL. Se uma rota é acessada por uma transição
(ex. quando usamos o helper [link-to](../../templates/links)), então o context desse model
já teria sido fornecido e esse hook não executado. Rotas sem seguimentos dinamicos 
irão sempre executar o _model hook_.

## Multiplos Models

Multiplos models podem ser retornados pelo
[Ember.RSVP.hash](http://emberjs.com/api/classes/RSVP.html#method_hash).
O `Ember.RSVP.hash` recebe parametros e os retorna como promisses, quando todas esses parametros promisses
forem resolvidos então o `Ember.RSVP.hash` tambem irá ser resolvido (ele tambem é uma promisse). Por exemplo:

```app/routes/songs.js
export default Ember.Route.extend({
  model() {
    return Ember.RSVP.hash({
      songs: this.store.findAll('song'),
      albums: this.store.findAll('album')
    });
  }
});
```

No template do `songs`, nos podemos especificar ambos models e usar o helper `{{#each}}` para eibir 
cada registro de um _song model_ e _album model_:

```app/templates/songs.hbs
<h1>Playlist</h1>

<ul>
  {{#each model.songs as |song|}}
    <li>{{song.name}} by {{song.artist}}</li>
  {{/each}}
</ul>

<h1>Albums</h1>

<ul>
  {{#each model.albums as |album|}}
    <li>{{album.title}} by {{album.artist}}</li>
  {{/each}}
</ul>
```
