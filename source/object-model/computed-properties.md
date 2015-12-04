## O que são Computed Properties?

Resumindo, computed properties deixam você declarar funções como propriedades. É uma função que você declara como computed properties, mas você pode usar ela como se fosse uma propriedade comum, direto no template, e toda vez que ela for chamada, o ember irá executar essa função e te retornar o `return` dela.

Isso facilita muito o trabalho quando você precisa manipular diversas propriedades para criar um novo valor.

### Computed properties em ação

Nos iremos começar com um exemplo bem simples:

```javascript
Person = Ember.Object.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  })
});

var ironMan = Person.create({
  firstName: 'Tony',
  lastName:  'Stark'
});

ironMan.get('fullName'); // "Tony Stark"
```

Isso declara a função como uma computed property, e os argumentos dizem para o ember que ela depende dos atributo `firstName` e do `lastName`.

Sempre que você acessar a propriedade `fullName`, essa função será chamada, e retornará o valor dela, que no nosso exemplo é `firstName` + `lastName`.

### Corrente de computed properties

Você pode usar uma computed properties como valor para criar outras computed properties. Vamos adicionar a  computed property `description` no exemplo anterior, e agora usar a propriedade `fullName` e adicionar em algumas outras:

```javascript
Person = Ember.Object.extend({
  firstName: null,
  lastName: null,
  age: null,
  country: null,

  fullName: Ember.computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  }),

  description: Ember.computed('fullName', 'age', 'country', function() {
    return `${this.get('fullName')}; Age: ${this.get('age')}; Country: ${this.get('country')}`;
  })
});

var captainAmerica = Person.create({
  firstName: 'Steve',
  lastName: 'Rogers',
  age: 80,
  country: 'USA'
});

captainAmerica.get('description'); // "Steve Rogers; Age: 80; Country: USA"
```

### Update Dinamico

Computed properties, por default, observão qualquer mudança feita nas propriedades em que eles depentem e são automaticamente atualizados quando são chamados. Vamos testar essa feature de update dinamico.

```javascript
captainAmerica.set('firstName', 'William');

captainAmerica.get('description'); // "William Rogers; Age: 80; Country: USA"
```

Então isso ira mudar o `firstName` que estava sendo observado pelo `fullName`, que estava sendo observado pela propriedade `description`.

Setar qualquer propriedade irá propagar as mudanças por todas as computed properties que depende delas.

### Setando Computed Properties

Você pode definir oque o Ember deve fazer quando você seta uma computed property. Se você tentar 
setar uma computed property, ele irá invocar com a key (nome da propriedade), e o valor que você quer setar para.

```javascript
Person = Ember.Object.extend({
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', {
    get(key) {
      return `${this.get('firstName')} ${this.get('lastName')}`;
    },
    set(key, value) {
      var [firstName, lastName] = value.split(/\s+/);
      this.set('firstName', firstName);
      this.set('lastName',  lastName);
      return value;
    }
  })
});


var captainAmerica = Person.create();
captainAmerica.set('fullName', 'William Burnside');
captainAmerica.get('firstName'); // William
captainAmerica.get('lastName'); // Burnside
```
