Conforme você for aprendendo sobre o Ember, você vai encontrar alguns codigos 
parecidos com  `Ember.Component.extend()` ou `DS.Model.extend()`. Aqui, você
vai aprender mais sobre esse metodo `extend()`, assim como as maiores
features do object model do Ember.

### Definindo classes

Para definir uma nova classe Ember Ember _class_, chame o metodo [`extend()`][1] do
[`Ember.Object`][2]:

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_extend
[2]: http://emberjs.com/api/classes/Ember.Object.html

```javascript
Person = Ember.Object.extend({
  say(thing) {
    alert(thing);
  }
});
```

Isso define uma nova classe `Person` com um metodo chamado `say()`.

Você tambem pode criar uma  _subclass_ a partir de qualquer classe existente chamando o 
metodo `extend()`. Por exemplo, se você quiser criar uma subclass do [`Ember.Component`][1]:

[1]: http://emberjs.com/api/classes/Ember.Component.html

```app/components/todo-item.js
export default Ember.Component.extend({
  classNameBindings: ['isUrgent'],
  isUrgent: true
});
```

### Overriding Metodos da Classe Pai

Quando definimos uma sub-classe, você pode fazer um override nos metodos da classe pai, mais ainda ter acesso
à implementação original chamndo o metodo especial `_super()`:

```javascript
Person = Ember.Object.extend({
  say(thing) {
    var name = this.get('name');
    alert(`${name} says: ${thing}`);
  }
});

Soldier = Person.extend({
  say(thing) {
    // this will call the method in the parent class (Person#say), appending
    // the string ', sir!' to the variable `thing` passed in
    this._super(`${thing}, sir!`);
  }
});

var yehuda = Soldier.create({
  name: 'Yehuda Katz'
});

yehuda.say('Yes'); // alerts "Yehuda Katz says: Yes, sir!"
```

Em alguns cassos, você vai querer passar argumentos para o `_super()` antes ou depois do override.

Isso permite que o metodo original continue funcionando normalmente, como se não tivesse sido feito o override.

Um exemplo commum é quando nos fazemos isos com o hook [`normalizeResponse()`][3] em um serializer do Ember-Data's.

O jeito mais facil de fazer isso é usar um "spread operator", como por exemplo `...arguments`:

[3]: http://emberjs.com/api/data/classes/DS.JSONAPISerializer.html#method_normalizeResponse

```javascript
normalizeResponse(store, primaryModelClass, payload, id, requestType)  {
  // Customize my JSON payload for Ember-Data
  return this._super(...arguments);
}
```

O exemplo acima retorna o argumento original (depois de suas constumizações) de volta para a classe pai,
assim você pode continuar com suas operações normais.

### Criando instancias

Uma vez que você tenha definido uma classe, você pode criar novas _instancias_ dessa classe
chamando o metodo [`create()`][1]. Qualquer metodo, propriedade e _computed properties_ 
que você definir na sua classe estarão disponiveis nas suas instancias:

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_create

```javascript
var person = Person.create();
person.say('Hello'); // alerts " says: Hello"
```

Quando criamos uma instancia, você pode iniciar os valore sde suas propriedades
passando um hash como parametro opcional do metodo `create()`:

```javascript
Person = Ember.Object.extend({
  helloWorld() {
    alert(`Hi, my name is ${this.get('name')}`);
  }
});

var tom = Person.create({
  name: 'Tom Dale'
});

tom.helloWorld(); // alerts "Hi, my name is Tom Dale"
```

Por motivos de performace, perceba que você não pode redefinir a instancia de uma 
_computed properties_ ou metodo quando chama create `create()`, você tambem não pode 
definir novos. Só sete propriedades simples passando uma hash para o metodo `create()`.
Se você precisar redefinir metodos ou _computed properties_, crie uma nova subclasse e instancie ela.

Por convenção, propriedades ou variaveis que um classe possuem são em
PascalCased, sendo instancias ou não. Então por exemplo, a variavel 
`Person` irá apontar para uma classe, enquanto `person` para uma instancia
(normalmente da classe `Person`). Você deve manter esse padrão enquanto estiver
desenvolvendo aplicações Ember.

### Inicializando instancias

Quando uma nova instancia é criada seu metodo [`init()`][1] é invocado automaticamente. 
Isso é ideal para lugares onde você precisa implementar um setup nas novas instancias:

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_init

```js
Person = Ember.Object.extend({
  init() {
    var name = this.get('name');
    alert(`${name}, reporting for duty!`);
  }
});

Person.create({
  name: 'Stefan Penner'
});

// alerts "Stefan Penner, reporting for duty!"
```

Se você estiver fazendo uma subclasse de uma classe do framework, tipo `Ember.Component` por exemplo, 
e na sua subclasse tiver um override do metodo `init()`, tenha certeza de chamar o `this._super(...arguments)`!
Se você não fizer isso, pode ser que a classe perca algum algum setup importante e ela não vai se comportar 
corretamente na sua aplicação.

Arrays e objetos definidos diretamente em qualquer subclasse do `Ember.Object` são compartilhados 
para todas as instancias desse objeto.

```js
Person = Ember.Object.extend({
  shoppingList: ['eggs', 'cheese']
});

Person.create({
  name: 'Stefan Penner',
  addItem() {
    this.get('shoppingList').pushObject('bacon');
  }
});

Person.create({
  name: 'Robert Jackson',
  addItem() {
    this.get('shoppingList').pushObject('sausage');
  }
});

// Stefan and Robert both trigger their addItem.
// They both end up with: ['eggs', 'cheese', 'bacon', 'sausage']
```

Para evitar esse comportamento, nos recomendamos você inicializar esses arrays e objetos durante o `init()`.
Fazendo assim que cada instancia seja unica.

```js
Person = Ember.Object.extend({
  init() {
    this.set('shoppingList', ['eggs', 'cheese']);
  }
});

Person.create({
  name: 'Stefan Penner',
  addItem() {
    this.get('shoppingList').pushObject('bacon');
  }
});

Person.create({
  name: 'Robert Jackson',
  addItem() {
    this.get('shoppingList').pushObject('sausage');
  }
});

// Stefan ['eggs', 'cheese', 'bacon']
// Robert ['eggs', 'cheese', 'sausage']
```

### Acessando Propriedade de um Objeto

Quando acessando as propriedades de um objeto utilize os metodos [`get()`][1]
e [`set()`][2]:

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_get
[2]: http://emberjs.com/api/classes/Ember.Object.html#method_set

```js
var person = Person.create();

var name = person.get('name');
person.set('name', 'Tobias Fünke');
```

Tenha certeza de usar esses metodos de acesso; Caso você não use _computed properties_ não serão
recalculadas, _observers_ não irão triggar, e templates não farão update.
