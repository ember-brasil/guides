Ember suporta um `observe` de qualquer propriedade, incluindo computed properties.

Observer devem conter o comportament que deve ser executando reagindo à mudando em propriedades.
Observers são especialmente uteis quando você precisa executar algum codigo depois que um bind for 
sincronizado.

Observer normalmente são usados demais por novos desenvolvedores Ember. Observers são usandos 
em diversos lugares no proprio framework, mas a maioria dos problems provavelmente poderão 
ser resolvidos usando computed properties.

Você pode criar um observer de um objecto usando o `Ember.observer`:

```javascript
Person = Ember.Object.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  }),

  fullNameChanged: Ember.observer('fullName', function() {
    // deal with the change
    console.log(`fullName changed to: ${this.get('fullName')}`);
  })
});

var person = Person.create({
  firstName: 'Yehuda',
  lastName: 'Katz'
});

// observer won't fire until `fullName` is consumed first
person.get('fullName'); // "Yehuda Katz"
person.set('firstName', 'Brohuda'); // fullName changed to: Brohuda Katz
```

Por que a computed property `fullName` depende do `firstName`,
atualizando `firstName` irá disparar os observers do `fullName` tambem.

### Observers and assincronia

Atualmente Observers no Ember são sincronos. Isso significa que eles irão disparar 
assim que uma das propriedades que observam mudar. Por causa disso, é facil de se 
encontrar bugs onde as propriedades ainda não estão sincronizadas:

```javascript
Person.reopen({
  lastNameChanged: Ember.observer('lastName', function() {
    // The observer depends on lastName and so does fullName. Because observers
    // are synchronous, when this function is called the value of fullName is
    // not updated yet so this will log the old value of fullName
    console.log(this.get('fullName'));
  })
});
```

Esse comportamente syncrono tambem pode fazer com que um obsever seja disparado diversas vezes
quando estiver observado mais que uma propriedades:

```javascript
Person.reopen({
  partOfNameChanged: Ember.observer('firstName', 'lastName', function() {
    // Because both firstName and lastName were set, this observer will fire twice.
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

Para resolver esses problemas você irá fazer uso do [`Ember.run.once()`][1].
Isso irá garantir que qualquer processamento que você precisar só irá acontecer uma vez,
e acontecerá no proximo `run loop` depois que todo os bindings forem sincronizados:

[1]: http://emberjs.com/api/classes/Ember.run.html#method_once

```javascript
Person.reopen({
  partOfNameChanged: Ember.observer('firstName', 'lastName', function() {
    Ember.run.once(this, 'processFullName');
  }),

  processFullName: Ember.observer('fullName', function() {
    // This will only fire once if you set two properties at the same time, and
    // will also happen in the next run loop once all properties are synchronized
    console.log(this.get('fullName'));
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

### Observers e inicialização de objeto

Observers nunca disparam antes que a inicialização de um objeto terminar.

Se você precisar disparar que um observer na inicialização desse objejo você não poderá 
usar ele com o `set`. No lugar você precisará especificar que o observer tambem deve rodar depois do 
 `init` usando o [`Ember.on()`][1]:

[1]: http://emberjs.com/api/classes/Ember.html#method_on

```javascript
Person = Ember.Object.extend({
  init() {
    this.set('salutation', 'Mr/Ms');
  },

  salutationDidChange: Ember.on('init', Ember.observer('salutation', function() {
    // some side effect of salutation changing
  }))
});
```

### Computed Properties não consumidas Não Disparam Observers

Se você nuncar usar `get()` de um computed property, os observer não irão 
disparar se eles dependem dessas chaves. Se você pensa que o valor estará mudando
de um desconhecido para outro.

Isso normalmente não afeta sua aplicação porque normalmente computed properties são 
sempre observadas no mesmo tempo em que são manipulados. Por exemplo, se você usar o `get`
o valor de uma computed property, colocar ela na DOM, e então observar quando 
o observer irá atualizar a DOM depois que as propriedades mudarem.

Se você precisar observer uma computed property mas você não esta recuperando ela, você 
pode colocar isso dentro do seu metodo `init`.

### Fora das definição das classes

Você pode adicionar observers à um objeto fora das suas classes usando o [`addObserver()`][1]:

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_addObserver

```javascript
person.addObserver('fullName', function() {
  // deal with the change
});
```