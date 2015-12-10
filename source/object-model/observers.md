No Ember você pode criar um `observer` para qualquer propriedade, mesmo que ela for uma _computed properties_.

`Observers` são funções que executam quando o valor de alguma propriedade for alterado,
eles são especialmente uteis quando você precisa fazer um determinado comportamento depois que certos binds forem sincronizados.

É importante não exagerar no uso de `observer`, pois apesar de ser usado em diversos lugares do próprio framework, normalmente desenvolvedores iniciantes usam ele para resolver problemas onde uma _computed properties_ seria uma solução mais adequada.

Você pode criar um `observer` de um objeto usando o `Ember.observer`:

```javascript
Person = Ember.Object.extend({
  // Esses parametros serão carregados pelo `create`
  firstName: null,
  lastName: null,

  fullName: Ember.computed('firstName', 'lastName', function() {
    return `${this.get('firstName')} ${this.get('lastName')}`;
  }),

  fullNameChanged: Ember.observer('fullName', function() {
    // lida com a mudança na propriedade
    console.log(`fullName changed to: ${this.get('fullName')}`);
  })
});

var person = Person.create({
  firstName: 'Yehuda',
  lastName: 'Katz'
});

// observer não irá ser disparado até o `fullName` ser consumido primeiro
person.get('fullName'); // "Yehuda Katz"
person.set('firstName', 'Brohuda'); // fullName changed to: Brohuda Katz
```

Por que a computed property `fullName` depende do `firstName`,
atualizando `firstName` irá disparar os observers do `fullName` tambem.

### Observers e sincronismo

Atualmente os `observers` do Ember são síncronos. Isso significa que eles irão disparar
assim que uma das propriedades que observam mudar. Por causa disso, é fácil de se
encontrar bugs onde as propriedades ainda não foram sincronizadas:

```javascript
Person.reopen({
  lastNameChanged: Ember.observer('lastName', function() {
    // O observer depende do lastName e do fullName. 
    // Por ser síncrono ele irá ser disparado depois que o lastName for 
    // for mudado, mesmo que o fullName ainda não tenha sido atualizado
    console.log(this.get('fullName'));
  })
});
```

Esse comportamento síncrono também pode fazer com que um `observer` seja disparado diversas vezes quando estiver observado mais que uma propriedades:

```javascript
Person.reopen({
  partOfNameChanged: Ember.observer('firstName', 'lastName', function() {
    // Por causa que ambos firstName e lastName foram alterados
// esse observer será disparado duas vezes.
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

Para resolver esses problemas você pode fazer uso do [`Ember.run.once()`][1].
Isso vai garantir que qualquer processamento que você precisar executar apenas uma vez, sera executado uma única vez por _run loop_, isso é, depois que os bindings forem sincronizados:

[1]: http://emberjs.com/api/classes/Ember.run.html#method_once

```javascript
Person.reopen({
  partOfNameChanged: Ember.observer('firstName', 'lastName', function() {
    Ember.run.once(this, 'processFullName');
  }),

  processFullName: Ember.observer('fullName', function() {
    // Isso fará com que ele só seja disparado uma vez se você mudar
// os valores das propriedades ao mesmo tempo, 
    // e tambem depois que o proximo run loop terminar
// e todas as propriedades forem sincronizadas
    console.log(this.get('fullName'));
  })
});

person.set('firstName', 'John');
person.set('lastName', 'Smith');
```

### Observers e inicialização de objeto

Observers não podem ser disparados antes que a inicialização de um objeto terminar.

Se você precisar disparar que um observer durante a inicialização de um objeto você não 
vai poder usar o método `set` dele. E você também precisará especificar um `observer` para ser rodado depois do método  `init` usando o [`Ember.on()`][1]:

[1]: http://emberjs.com/api/classes/Ember.html#method_on

```javascript
Person = Ember.Object.extend({
  init() {
    this.set('salutation', 'Mr/Ms');
  },

  salutationDidChange: Ember.on('init', Ember.observer('salutation', function() {
    // Alum side effect da mudança do propriedade salutation
  }))
});
```

### Computed Properties não consumidas Não Disparam Observers

Se você nunca usar o `get()` de um _computed property_ especifica, os observer dessa propriedade nunca irão ser disparados. Você pode pensar que seria algo como um valor desconhecido estar mudando para outro também desconhecido.

Isso normalmente não afeta sua aplicação porque na maioria das vezes que  _computed properties_ são observadas, isso acontece no mesmo tempo em que são manipulados. Por exemplo, se você usar `get` para pegar o valor de _computed property_, e colocar ela na DOM, e então o `observer` irá ser disparado quando essa propriedade mudar.

Se você precisar usar um `observer` em uma _computed property_ antes de fazer um get, você vai precisar adicionar esse código ao seu método `init`.



### Usando Observers Fora das Classes

Você pode adicionar observers à um objeto fora das suas classes do Ember usando o [`addObserver()`][1]:

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_addObserver

```javascript
person.addObserver('fullName', function() {
  // deal with the change
});
```

