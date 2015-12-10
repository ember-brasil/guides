Algumas vezes você vai ter uma computed property cujo valor depende das propriedades
de um item de um array. Por exemplo, você pode ter um array de items de uma todo list,
e você quer calcular quantos estão incompletos baseado na propriedade `isDone` de cada item
desse array.

Para facilitar isso, o ember nos da uma chave `@each` como é demostrado abaixo:

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

Aqui, a chave dependente `todos.@each.isDone` diz para o Ember.js atualizar todos os binding
e disparar todos os observes sempre que qualquer evento abaixo ocorrer: 

1. A propriedade `isDone` de qualquer objeto do array `todos` mudar.
2. Um item for adicionado no array `todos`.
3. Um item for removido do array `todos`.
4. A propriedade do array `todos` for mudada para um diferente array.

No exemplo abaixo, o `remaining` count é `1`

```javascript
import TodosComponent from 'app/components/todos';

let todosComponent = TodosComponent.create();
todosComponent.get('remaining');
// 1
```

Se nos mudarmos a propriedade `isDone` do array todo, a propriedade `remaining` é atualizada automaticamente:

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

Note que `@each` só funciona em um nivel abaixo. Você não poode aninhar form do tipo
`todos.@each.owner.name` ou `todos.@each.owner.@each.name`.

Alguma vezes, você não se importa se as propriedades individuais do array mudar. 
Nesse caso você pode usar a chave `[]` no lugar do `@each`. Computed properties depende de um array
usando o  `[]` só ira atualizar se um item for adicionado ou removido desse array,
ou se esse array for setado para um array diferente. Por exemplo:

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

Aqui o `indexOfSelectedTodo` depende do `todos.[]`, então ele irá atualizar se nos adicionarmos um item
para o `todos`, mas não irá atualizar se o valor de `isDone` de um dos itens mudar.

Muitos dos macros do [Ember.computed](http://emberjs.com/api/classes/Ember.computed.html) usa a 
chave `[]` para implementar os casos mais comuns. Por exemplo, para criar uma computed property 
que mapeie as propriedade de um array você pode usar [Ember.computed.map](http://emberjs.com/api/classes/Ember.computed.html#method_map)
ou construir uma computed property você mesmo:

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

Para comparar, usando um dos macros seu codigo seria parecido com:

```javascript
const Hamster = Ember.Object.extend({
  excitingChores: Ember.computed.map('chores', function(chore, index) {
    return `CHORE ${index}: ${chore.toUpperCase()}!`;
  })
});
```

O macros computed esperar você utiliza-los em um array, então você não precisa usar a chave
`[]` nesses casos. No entanto, é preciso especificar essa chave quando for construir sua 
propria computed property, para fazer o Ember.js, escutar esse array.
