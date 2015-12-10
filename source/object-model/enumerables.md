No Ember.js, um  enumerable é qualquer objeto que contem um numero de objetos filhos, 
e nos permite trabalhar com esses filhos usando a API do 
[Ember.Enumerable](http://emberjs.com/api/classes/Ember.Enumerable.html). Os enumerables mais comuns
nas maioria das aplicações Ember são os arrays nativos do Javascript, que extendem 
a interface dos enumerables.

Provendo uma interface padrão para lidar com enumerables,
Ember.js nos permite mudar completamente a as propriedades irmas de como um 
objeto é armezanado sem a necessidade de modificar partes da aplicação que o acessam.

O API do enumerable segue as especificações do ECMAScript sempre que possivel. 
Isso minimiza incompatibilidade com outras bibliotecas e permite o ember usar 
implementações nativas do array direto do brownser quando possivel.

## Uso de Obsevers e Propriedades

Para fazer com que o Ember observe as mudanças de um enumerable, você precisa usar um metodo 
especial do `Ember.Enumerable`. Por exemplo, se você adicionar um elemento em um array usando o
metodo padrão do Javascript `push()`, O Ember não será capas de observar essa mudança, 
você terá que usar o metodo `pushObject()`, assim a mudança irá propagar por sua aplicação.

Aqui vai uma lista de metodos nativos do Javascript e seus equivalentes que podem ser observados:

<table>
  <thead>
    <tr><th>Metodo nativo</th><th> Metodo que pode ser observado </th></tr>
  </thead>
  <tbody>
    <tr><td>pop</td><td>popObject</td></tr>
    <tr><td>push</td><td>pushObject</td></tr>
    <tr><td>reverse</td><td>reverseObjects</td></tr>
    <tr><td>shift</td><td>shiftObject</td></tr>
    <tr><td>unshift</td><td>unshiftObject</td></tr>
  </tbody>
</table>

Alem disso, para recuperar o primeiro e o ultimo objeto de um jeito _'observavel'_, você pode usar o `myArray.get('firstObject')` e o 
`myArray.get('lastObject')`.

## Overview da API 

No resto desse guide, nos vamos explorar algumas das utilidades que os enumerable, nos oferece. 
Para uma lista completa, veja em [Ember.Enumerable API
reference documentation.](http://emberjs.com/api/classes/Ember.Enumerable.html)

### Intinerando um Enumerable

Para intinerar por todos os valores de um objeto enumerable, use o metodo [`forEach()`][1]
method:

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_forEach

```javascript
var food = ['Poi', 'Ono', 'Adobo Chicken'];

food.forEach(function(item, index) {
  console.log(`Menu Item ${index+1}: ${item}`);
});

// Menu Item 1: Poi
// Menu Item 2: Ono
// Menu Item 3: Adobo Chicken
```

### Primeiro e Ultimos Objetos

Todos os enumerables possuem propriedades [`firstObject`][1] e [`lastObject`][2], que podem ser 
usadas para se criar um bind, por exemplo.

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#property_firstObject
[2]: http://emberjs.com/api/classes/Ember.Enumerable.html#property_lastObject

```javascript
var animals = ['rooster', 'pig'];

animals.get('lastObject');
//=> "pig"

animals.pushObject('peacock');

animals.get('lastObject');
//=> "peacock"
```

### Map

Você pode modificar cada item de um enumerable usando o metodo
[`map()`][1], que cria um novo array com os resultados da chamada da função
em cada item dentro de um enumerable.

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_map

```javascript
var words = ['goodbye', 'cruel', 'world'];

var emphaticWords = words.map(function(item) {
  return item + '!';
});
// ["goodbye!", "cruel!", "world!"]
```

Se seu enumerable for composto de objetos, existe o metodo [`mapBy()`][1]
que irá extrair as propriedade com nome de cada um desses objetos e retorna-las como 
um novo array:

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_mapBy

```javascript
var hawaii = Ember.Object.create({
  capital: 'Honolulu'
});

var california = Ember.Object.create({
  capital: 'Sacramento'
});

var states = [hawaii, california];

states.mapBy('capital');
//=> ["Honolulu", "Sacramento"]
```

### Filtering

Outra tarefa comum que pode ser feita em um enumerable é pegar um 
enumerable, e retornar um array depois de filtra-lo se baseando em algum criterio.

Para filtrar arbitrariamente, use o metodo [`filter()`][1]. Esse metodo espera que um `true`
seja retornado toda vez que o item sendo intinerado deva entrar no novo array, 
e retornar `false` ou `undefined` se ele não deve entrar.

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_filter

```javascript
var arr = [1,2,3,4,5];

arr.filter(function(item, index, self) {
  return item < 4;
})

// returns [1,2,3]
```

Quando você estiver trabalhnado em uma coleção de objetos Ember, você tambem pode querer
filtrar esses objetos se baseando em alguma propriedade, o metodo [`filterBy()`][1] nos dá uma versão
simples de se fazer esses filtros.

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_filterBy

```javascript
Todo = Ember.Object.extend({
  title: null,
  isDone: false
});

todos = [
  Todo.create({ title: 'Write code', isDone: true }),
  Todo.create({ title: 'Go to sleep' })
];

todos.filterBy('isDone', true);

// returns an Array containing only items with `isDone == true`
```

Se você só quiser retonar a primeira correspondecia em vez de um array inteiro, você pode usar o 
[`find()`][1] e o [`findBy()`][2],
que funionam da mesma maneira que o `filter()` e o `filterBy()`, porem só retornam um item.

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_find
[2]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_findBy

### Aggregate Information (Todas ou Qualquer uma)

Para verificar se todos os itens de um enumerable combinam em uma condição você pode usar o 
metodo [`every()`][1]:

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_every

```javascript
Person = Ember.Object.extend({
  name: null,
  isHappy: false
});

var people = [
  Person.create({ name: 'Yehuda', isHappy: true }),
  Person.create({ name: 'Majd', isHappy: false })
];

people.every(function(person, index, self) {
  return person.get('isHappy');
});

// returns false
```

Para verificar se pelo menos um item desse enumerable corresponde à uma condição você pode
usar o metodo [`any()`][1]:

[1]: http://emberjs.com/api/classes/Ember.Enumerable.html#method_any

```javascript
people.any(function(person, index, self) {
  return person.get('isHappy');
});

// returns true
```

Como os metodos de filtragem os metodos `every()` e `any()` tambem tem sua versão analoga com os 
metodos `isEvery()` e `isAny()`.

```javascript
people.isEvery('isHappy', true) // false
people.isAny('isHappy', true)  // true
```
