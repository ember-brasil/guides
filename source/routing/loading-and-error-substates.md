O Ember Router nos permite receber feedback do carregamento dos routes, assim como quando 
um erro acontece quando carregamos uma route.

## Sub-estados do `loading`

Durante os hooks `beforeModel`, `model`, e `afterModel`, dados talvez demorem algum tempo 
para carregar. Tecnicamente, o router pausa a transição até que as promises sejam resolvidas e cada
hook seja completado.

Considere o seguinte:

```app/router.js
Router.map(function() {
  this.route('slow-model');
});
```

```app/routes/slow-model.js
export default Ember.Route.extend({
  model() {
    return this.store.findAll('slowModel');
  }
});
```

Se você navegar para o `slow-model`, o hook `model`,
talvez demore um bom tempo para ser completado.
Durante esse tempo, seu UI não esta realmente dando nenhum feedback do que esta acontecendo. 
Se você enrar nessa rota depois de um full page
refresh, sua UI estará completamente vazia, porque você não terminou nenhuma 
transição e os templates só são carregados depois que elas acabam.

Se você esta navegando para o `slow-model` a partir de outra rota, 
Você iria continuar vendo templates da rota anterior até que seu _model_ termine de carregar,
e então sem nenhum aviso para o usuario, boom, todos os templates do `slow-model` seriam carregados.

Então, como podemos dar algum feedback visual durante as transições?

Basta definir um template chamado `loading` (e opcionalmente uma rota correspondente para ele)
para que o ember faça essas transições. A transição intermediaria para o substate de loading acontece
imediatamente de maneira sincrona. A URL não é atualizada, e dirente das outras transições,
uma transição desse tipo não pode ser abortada.

Uma vez que a transição principal para o `slow-model` termine, a route `loading`
irá terminar e a transição para o `slow-model` irá continuar.

Para routes aninhados, faça algo como:

```app/router.js
Router.map(function() {
  this.route('foo', function() {
    this.route('bar', function() {
      this.route('slow-model');
    });
  });
});
```

Ember irá alternar entre tentar encontrar o template `routeName-loading` ou `loading`
na hierarquia começando com o `foo.bar.slow-model-loading`:
in the hierarchy starting with :

1. `foo.bar.slow-model-loading`
2. `foo.bar.loading` ou `foo.bar-loading`
3. `foo.loading` ou `foo-loading`
4. `loading` ou `application-loading`

É importante perceber que para o proprio `slow-model` o Ember não vai tentar encontrar
o template `slow-model.loading` mas para o resto da hierarquia essa sintaxe é aceitavel. 
Isso pode ser util quando para criarmos paginar de loading personalizadas para uma 
rota de baixo nivel como a `slow-model`.

### O evento `loading`

Se varios hooks `beforeModel`/`model`/`afterModel` não forem resolvidos imediatamente,
o evento [`loading`][1] será disparado para essa rota.

[1]: http://emberjs.com/api/classes/Ember.Route.html#event_loading

```app/routes/foo-slow-model.js
export default Ember.Route.extend({
  model() {
    return this.store.findAll('slowModel');
  },
  actions: {
    loading(transition, originRoute) {
      let controller = this.controllerFor('foo');
      controller.set('currentlyLoading', true);
    }
  }
});
```

Se o handler para `loading` não for definido para uma rota especifica,
o evento irá continuar em forma de bubble para a transição da route pai, 
dando assim uma oportunidade para a route `application` de manipular essa ação.

Qaundo usamos o handler `loading`, nos podemos teremos certeza de quando uma a promisse de 
uma transição estiver acabado:

```app/routes/foo-slow-model.js
export default Ember.Route.extend({
  ...
  actions: {
    loading(transition, originRoute) {
      let controller = this.controllerFor('foo');
      controller.set('currentlyLoading', true);
      transition.promise.finally(function() {
          controller.set('currentlyLoading', false);
      });
    }
  }
});
```

## Sub-estados de `error`

O Ember nos dá recursos bem parecidos dos sub-estados de `loading` para quando 
encontrarmos errors durante alguma transição.

Da mesma maneira que os eventos de `loading` são implementados tambem existe um 
handler default chamado `error` que irá olhar para sub-estados dos erros, caso algum seja encontrado.

```app/router.js
Router.map(function() {
  this.route('articles', function() {
    this.route('overview');
  });
});
```

Como no sub-estado de `loading`, quando acontece um thrown ou alguma promisse é rejeitada no
hook `model` (ou `beforeModel` ou `afterModel`) da route `articles.overview` o Ember irá procurar
por template de error ou por uma route seguindo a seguinte route:

1. `articles.overview-error`
2. `articles.error` ou `articles-error`
3. `error` ou `application-error`

Se uma das condições acima for encontrada o router irá imediatamente fazer uma transição
para esse sub-estado (sem atualizar a URL). A "reason" desse erro será pasasdo 
para o estado de error como se fosse seu `model`.

Se nenhum sub-estado de error for encontrado a mensagem de error será registrada.

### O Evento `error`

Se o `model` hook da rota retornar uma promessa que foi rejeitada
(como por exemplo o servidor mandando um erro dizendo que o usuario não foi autenticado,
ou que ele não tem acesso à determinado conteudo.), o evento [`error`][1] irá ser disparado
a partir dessa rota e ira fazer bubble por toda hierarquia.
Esse evento `error` pode ser manipulado para mostrar uma notificação, redirecionar o usuario para uma
pagina de login, etc.

[1]: http://emberjs.com/api/classes/Ember.Route.html#event_error

```app/routes/articles-overview.js
export default Ember.Route.extend({
  model(params) {
    return this.store.findAll('nonexistentModel');
  },
  actions: {
    error(error, transition) {
      if (error && error.status === 400) {
        return this.transitionTo('modelNotFound');
      }
    }
  }
});
```

Da mesma maneira que o evento `loading`, você pode manipular o evento `error` 
no level application do seu projeto, assim evitando de escrever o mesmo codigo para diversas
rotas.
