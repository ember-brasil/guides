Chamando o metodo [`transitionTo()`][1] a partir de uma rota, ou o [`transitionToRoute()`][2] de um 
controller irá parar qualquer transição que estiver sendo feita e começar uma nova, funcionando com
um redirecionamento. `transitionTo()` se comporta exatamente igual o helper [link-to](../../templates/links).

[1]: http://emberjs.com/api/classes/Ember.Route.html#method_transitionTo
[2]: http://emberjs.com/api/classes/Ember.Route.html#method_transitionToRoute

Se a nova rota de tem seguimentos dinamicos, você irá precisar passar ou um _model_ ou um identificador para cada _identificador_ para cada seguimento. Passando o model irá fazer com que o hook `model()` não seja executado (levando em conta que seu model já 
esta carregado).

## Transições antes do Model ser Conhecido

Se você quiser redicionar uma rota para outra, você pode fazer esse redirecionamento 
usando o hook [`beforeModel()`][1] dentro do seu route handler.

[1]: http://emberjs.com/api/classes/Ember.Route.html#method_beforeModel

```app/router.js
Router.map(function() {
  this.route('posts');
});
```

```app/routes/index.js
export default Ember.Route.extend({
  beforeModel() {
    this.transitionTo('posts');
  }
});
```

Se você precisar examinar algum estado da aplicação antes de fazer esse 
redirecionamento uma provavelmente uma melhor solução seria criar um [service](../../applications/services).

## Transições Depois do Model ser Conhecido

Se você precisar de alguma informação do model atual para tomar alguma decissão você 
pode o usar o hook [`afterModel()`][1]. Ele recebe o model como primeiro parametro e a transição como
segundo, você pode ver como isso funciona no exemplo abaixo:

[1]: http://emberjs.com/api/classes/Ember.Route.html#method_afterModel

```app/router.js
Router.map(function() {
  this.route('posts');
  this.route('post', { path: '/post/:post_id' });
});
```

```app/routes/posts.js
export default Ember.Route.extend({
  afterModel(model, transition) {
    if (model.get('length') === 1) {
      this.transitionTo('post', model.get('firstObject'));
    }
  }
});
```

When transitioning to the `posts` route if it turns out that there is only one post,
the current transition will be aborted in favor of redirecting to the `PostRoute`
with the single post object being its model.

### Child Routes

Let's change the router above to use a nested route, like this:

```app/router.js
Router.map(function() {
  this.route('posts', function() {
    this.route('post', { path: ':post_id' });
  });
});
```

Se nos redirecionarmos do `posts.post` no hook `afterModel`, `afterModel`
essencialmetne invalida a tentativa atual de entrar nessa rota, então os hooks 
`beforeModel`, `model`, and `afterModel` do route `posts` serão disparados novamente.
Isso é ineficiente, tendo em vista que eles acabaram de ser executados antes desse 
redirecionamento.

No lugar disso, nos podemos usar o metodo [`redirect()`][1], irá deixar a transição original
validata, e não irá fazer com que os hooks da route pai sejam disparados novamente:

[1]: http://emberjs.com/api/classes/Ember.Route.html#method_redirect

```app/routes/posts.js
export default Ember.Route.extend({
  redirect(model, transition) {
    if (model.get('length') === 1) {
      this.transitionTo('posts.post', model.get('firstObject'));
    }
  }
});
```
