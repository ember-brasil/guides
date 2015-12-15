Um dos trabalhos de um route handler é fornecer o template correto
para ser renderizado na tela.

Por default, um route handler irá renderizar o template com o mesmo nome da rota,
como por exemplo nesse router:

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('new');
  });
});
```

Aqui, a route `posts` irá renderizar o template `posts.hbs`, e o route `posts.new`
vai renderizar o `posts/new.hbs`.

Cada template irá ser renderizdo dentro do helper `{{outlet}}` do seu route superior. 
Por exemplo, o route `posts.new` irá renderizar seu template dentro do `{{outlet}}` no template
`posts.hbs`, assim como o route `posts` irá ser renderizado dentro do `{{outlet}}` do nosso `application.hbs`.

Se você quiser renderizar um template diferente do padrão, você vai ter que 
implementar um hook [`renderTemplate()`][1]:

[1]: http://emberjs.com/api/classes/Ember.Route.html#method_renderTemplate

```app/routes/posts.js
export default Ember.Route.extend({
  renderTemplate() {
    this.render('favoritePosts');
  }
});
```
