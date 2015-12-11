As vezes você vai precisar usar uma _computed property_ cujo valor dependa de uma propriedade que é de item de um array. Por exemplo, se você estiver desenvolvendo um aplicativo de _todo list_, e voce quiser calcular quantos itens dessa lista estão incompletos baseados na propriedade `isDone` de cada item desse array.

Para facilitar isso, nos podemos usar uma chave que o ember nos dá chamada `@each`, veja como ela é usada abaixo:

```app/components/todos.js
export default Ember.Component.extend({
  todos: [
    Ember.Object.create({ isDone: true }),
    Ember.Object.create({ isDone: false }),
    Ember.Object.create({ isDone: true })
  ],

  remaining: Ember.computed('todos.@each.isDone', function() {
    var todos = this.get('todos');
    return todos.filterBy('isDone', false).get('length');
  })
});
```

Aqui, a chave dependente `todos.@each.isDone` dispara essa _computed propertie_ sempre que um desses eventos abaixo acontecer:

1. A propriedade `isDone` de qualquer objeto do array `todos` mudar.
2. Um item for adicionado no array `todos`.
3. Um item for removido do array `todos`.
4. A propriedade do array `todos` for mudada para um diferente array.

No exemplo abaixo, o `remaining count` é `1`

```javascript
import TodosComponent from 'app/components/todos';

let todosComponent = TodosComponent.create();
todosComponent.get('remaining');
// 1
```

Se nos mudarmos a propriedade `isDone` do array `todo`, a propriedade `remaining` é atualizada automaticamente:

```javascript
let todos = todosComponent.get('todos');
let todo = todos.objectAt(1);
todo.set('isDone', true);

todosComponent.get('remaining');
// 0

todo = Ember.Object.create({ isDone: false });
todos.pushObject(todo);

todosComponent.get('remaining');
// 1
```

Note que `@each` só funciona em um nível abaixo. Você não pode fazer coisas do tipo
`todos.@each.owner.name` ou `todos.@each.owner.@each.name`.


Algumas vezes, você não se importa com as propriedades individuais de cada item do array, e sim com os itens em si, nesse caso você pode usar a chave `[]` no lugar do `@each`. 

As _computed properties_ que dependem de um array e usam a chave  `[]` só irão ser atualizadas quando um item for adicionado ou removido desse array, ou se a estrutura desse array for alterada. Como por exemplo:

```app/components/todos.js
export default Ember.Component.extend({
  todos: [
    Ember.Object.create({ isDone: true }),
    Ember.Object.create({ isDone: false }),
    Ember.Object.create({ isDone: true })
  ],

  selectedTodo: null,
  indexOfSelectedTodo: Ember.computed('selectedTodo', 'todos.[]', function() {
    return this.get('todos').indexOf(this.get('selectedTodo'));
  })
});
```

Aqui o `indexOfSelectedTodo` depende do `todos.[]`, então ele só irá atualizar se nós adicionarmos um item para o `todos`. Se nos só mudarmos o valor de alguma propriedade dos itens desse array, nada vai acontecer.

Muitos dos macros [Ember.computed](http://emberjs.com/api/classes/Ember.computed.html) usam a chave `[]` para implementar alguns casos mais comuns. Por exemplo, para criar uma _computed property_ que mapeie as propriedade de um array você pode usar [Ember.computed.map](http://emberjs.com/api/classes/Ember.computed.html#method_map)
ou construir essa _computed property_ você mesmo:

```javascript
const Hamster = Ember.Object.extend({
  excitingChores: Ember.computed('chores.[]', function() {
    return this.get('chores').map(function(chore, index) {
      return `CHORE ${index}: ${chore.toUpperCase()}!`;
    });
  })
});

const hamster = Hamster.create({
  chores: ['clean', 'write more unit tests']
});

hamster.get('excitingChores'); // ['CHORE 1: CLEAN!', 'CHORE 2: WRITE MORE UNIT TESTS!']
hamster.get('chores').pushObject('review code');
hamster.get('excitingChores'); // ['CHORE 1: CLEAN!', 'CHORE 2: WRITE MORE UNIT TESTS!', 'CHORE 3: REVIEW CODE!']
```

Para comparar, abaixo você pode ver uma versão do código acima usando macros:

```javascript
const Hamster = Ember.Object.extend({
  excitingChores: Ember.computed.map('chores', function(chore, index) {
    return `CHORE ${index}: ${chore.toUpperCase()}!`;
  })
});
```

Os macros esperam serem utilizados em um array, então você não precisa usar a chave `[]` nesses casos. No entanto, é preciso especificar essa chave quando for construir sua própria computed property, fazendo assim o Ember.js, escutar esse array.



