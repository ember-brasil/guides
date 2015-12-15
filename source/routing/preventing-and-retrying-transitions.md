Durante a transição de uma rota, o router do Ember passa um objeto _transition_
para os varios hooks da rotas que forem envolvidos nessa transição.
Qualquer hook que acessa esse objeto _transition_ tem a habilidade de abortar imediatamente
a transição chamando o metodo `transition.abort()`, essa mesma transição pode ser usada depois 
se for guardada em uma variavel e você chamar o metodo `transition.retry()` dela.

### Previnindo Transições via `willTransition`

Quando você tenta fazer uma transição, seja via `{{link-to}}`, `transitionTo`,
ou por uma mudança na URL, uma action chamada `willTransition` será disparada nas rotas que
estiverem ativas. Isso nos dará para cada rota ativa, começando do nivel mais baixo,
a oportunidade de decidir de uma transição deve ocorrer ou não.

Imagine que seu aplicativo esta em uma rota que esta exibindo um formulario complexo
para seu usuario preencher e ele acidentalmente navega para a pagina anterior.
A não ser que essa transição seja previnida, o usuario provavelmente irá perder todo o 
progresso do que tinha feito nesse forme até então, oque seria uma experiencia bem frustrante
para o usuario.

Aqui esta uma maneira em que podemos lidar com essa sintuação:

```app/routes/form.js
export default Ember.Route.extend({
  actions: {
    willTransition(transition) {
      if (this.controller.get('userHasEnteredData') &&
          !confirm('Are you sure you want to abandon progress?')) {
        transition.abort();
      } else {
        // Bubble the `willTransition` action so that
        // parent routes can decide whether or not to abort.
        return true;
      }
    }
  }
});
```

Quando o usario clica em um helper `{{link-to}}`, ou quando sua aplicação faz uma transição
usando o `transitionTo`, a transição iria ser abortada e a URL iria continuar a mesma. 
No entanto, se o o botão back do navegador for usado para nevagar para fora do `route:form`, 
ou o usuario mudar manualmente a URL ele irá ir para essa nova URL antes da action `willTransition`
ser chamada. Isso irá resultar no navegador exibindo a outra url mesmo que o `willTransition` chamar 
o metodo `transition.abort()`.

### Abortando Transições usando o `model`, `beforeModel`, `afterModel`

O hooks `model`, `beforeModel`, e `afterModel` são descritos em [Asynchronous Routing](../asynchronous-routing)
cada um deles recebe um objeto _transition_. Isso faz com que seja possivel rotas de destino abortarem
tentativas de transições.

```app/routes/disco.js
export default Ember.Route.extend({
  beforeModel(transition) {
    if (new Date() > new Date('January 1, 1980')) {
      alert('Sorry, you need a time machine to enter this route.');
      transition.abort();
    }
  }
});
```

### Guardando e fazendo Retry de uma Transição

Transições abortadas podem ser recuperadas mais tarde.
Um caso comum para esse tipo de comportamento é quando nos temos 
uma route autenticada e redirecionando o usuario para a pagina de login, e 
então redirecionando ele para outra area uma vez que ele já esta logado.

```app/routes/some-authenticated.js
export default Ember.Route.extend({
  beforeModel(transition) {
    if (!this.controllerFor('auth').get('userIsLoggedIn')) {
      var loginController = this.controllerFor('login');
      loginController.set('previousTransition', transition);
      this.transitionTo('login');
    }
  }
});
```

```app/controllers/login.js
export default Ember.Controller.extend({
  actions: {
    login() {
      // Log the user in, then reattempt previous transition if it exists.
      var previousTransition = this.get('previousTransition');
      if (previousTransition) {
        this.set('previousTransition', null);
        previousTransition.retry();
      } else {
        // Default back to homepage
        this.transitionToRoute('index');
      }
    }
  }
});
```
