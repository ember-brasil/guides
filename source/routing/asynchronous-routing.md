Essa sessão cobre algumas features mais complexas de um router e sua
capacidade de manipular logicas asincronas no seu app.

### Uma Palavra sobre Promises...

O jeito que o ember lida com logicas assincronas no router faz um uso
massivo do conceito de _promises_. De modo resumido, promises são objetos que 
representam um eventual valor. Uma promise pode ser tanto _completa_ (quando ela 
termina e seu resultado é apresentado) ou _rejeitada_ (quando algum erro ocorre)

A maneira de recuperar esse valor eventual, ou lidar com o casos em que essa
promisse é rejeitada é usando metodo [`then()`][1], que aceita dois callbacks
opcionais, um que será executado se ela for completada, e o outro se ela for rejeitada. 
Se uma promisse for completada, a primeira função será chamada recebendo um argumento 
como resposta dessa promisse, mas caso ela for rejeitada, ela recebera um unico objeto
com a razão dessa rejeição. Por exemplo:

[1]: http://emberjs.com/api/classes/RSVP.Promise.html#method_then

```js
var promise = fetchTheAnswer();

promise.then(fulfill, reject);

function fulfill(answer) {
  console.log('The answer is ' + answer);
}

function reject(reason) {
  console.log(`Couldn't get the answer! Reason: ${reason}`);
}
```

Muito poder pode ser obtivo dessas promises pelo fato de você poder 
fazer uma cadeia de operações assincronas:

```js
// Note: jQuery AJAX methods return promises
var usernamesPromise = Ember.$.getJSON('/usernames.json');

usernamesPromise.then(fetchPhotosOfUsers)
                .then(applyInstagramFilters)
                .then(uploadTrendyPhotoAlbum)
                .then(displaySuccessMessage, handleErrors);
```

No exemplo acima, se algum dos metodos
`fetchPhotosOfUsers`, `applyInstagramFilters`, ou
`uploadTrendyPhotoAlbum` retornar uma promise rejeitada a função
`handleErrors` será chamada para tratar esse caso de erro. 
Dessa maneira, promises são parecidas como uma forma de try-catch 
que previne que um fluxo de dados seja executado, trazendo uma forma
simples de resolver logicas complexas na sua aplicação.

Esse guia não tem a intenção de fazr você entender todas as maneiras
que promisses podem ser usadas, mas se você quiser ter um conhecimento maior sobre isso
você pode dar uma olhada no arquivo readme do [RSVP](https://github.com/tildeio/rsvp.js),
a biblioteca de promises usada no Ember.

### O Router Pausa para Promises

Durante uma transição de rotas, o Ember router recupera todos os models 
(pelo hook `model`) que serão passados para esse controller route no fim dessa 
transição. Se um hook `model` (ou algum dos outros hooks `beforeModel` ou `afterModel`) retornem objetos normais (não-promise) 
ou arrays, essa transição ser completada imediatamente. Mas se o hook `model`
retonar uma promise, ou se uma promise tiver sido fornecida para o `transitionTo`, a transição
será pausada até que essa promise acabe.

O router considera qualquer objeto com um metodo `then()` definido nele sendo um uma promise.

Se uma promise é completada, a transição irá se verificar se ela existe alguma outra para ser resolvida
nos proximos models, pausando novamente se encontrar outra promise, assim continuadamente até que todas
as routes e suas respectivas promises tenham sido resolvidas


[1]: http://emberjs.com/api/classes/Ember.Route.html#method_setupController

A basic example:

```app/routes/tardy.js
export default Ember.Route.extend({
  model() {
    return new Ember.RSVP.Promise(function(resolve) {
      Ember.run.later(function() {
        resolve({ msg: 'Hold Your Horses' });
      }, 3000);
    });
  },

  setupController(controller, model) {
    console.log(model.msg); // "Hold Your Horses"
  }
});
```

Quando fazemos a transição para `route:tardy`, o `model()` hook será chamado e retornará uma 
promise que não será resolvida até tres segundos depois, durante esse tempo 
o router ficará pausado entre transições. Quando a promessa eventualmente se completar,
o route continuará a transição chamando o `setupController()` com o objeto resolvido.

Esse comportamento _pause-on-promise_ é extremamente valioso para que você 
possa garantir que os dados que sua route irá carregar no template, estejam 100% carregados.

### Quando uma promise é rejeitada...

Nos já vimos o que acontece quando uma promise é completada, mas e se ela for rejeitada?

Por padrão se um model tiver sua promise rejeitada durante uma transição a transição é 
abortate, e nenhum novo template é renderizado, e um erro é mostrado no console.

Você pode configurar essa logica de manipulação de erros usando o handler `error` da sua 
hash de `actions` do route. Quando uma promise é rejeitata, um evento `error`e disparado
e seu acontece um bubble dele por toda a aplicação até chegar no `route:application`,
isso é, se ele não for tratado por algum handler costumizado antes:

```app/routes/good-for-nothing.js
export default Ember.Route.extend({
  model() {
    return Ember.RSVP.reject("FAIL");
  },

  actions: {
    error(reason) {
      alert(reason); // "FAIL"

      // Can transition to another route here, e.g.
      // this.transitionTo('index');

      // Uncomment the line below to bubble this error event:
      // return true;
    }
  }
});
```

No exemplo acima, o evento erro error iria parar bem no error handler do `route:good-for-nothing`
e não continuaria seu bubble. Para fazer um evento continuar com o bubble até o `route:application`,
você pode retornar o valor `true` em seu error handler.

### Recuperando uma Rejection

Promises de model rejeitadas matam as transições, mas promises podem ser encadeiadas,
se você pegar uma promise rejeitada dentro do proprio hook `model` e converter ele 
para uma completa, ela não irá impedir a transição.

```app/routes/funky.js
export default Ember.Route.extend({
  model() {
    return iHopeThisWorks().then(null, function() {
      // Promise rejected, fulfill with some default value to
      // use as the route's model and continue on with the transition
      return { msg: 'Recovered from rejected promise' };
    });
  }
});
```
