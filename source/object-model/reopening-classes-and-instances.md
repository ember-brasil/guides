Você não precisa defniir a classe toda de uma vez. Você pode reabri-la e definir novas propriedades 
usando o metodo [`reopen()`][1].

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_reopen

```javascript
Person.reopen({
  isPerson: true
});

Person.create().get('isPerson') // true
```
Quando usando o `reopen()`, você tambem pode fazer override de metodos que já existem e chamar o `this._super`.

```javascript
Person.reopen({
  // override `say` to add an ! at the end
  say(thing) {
    this._super(thing + '!');
  }
});
```

`reopen()` é usado para adicionar metodos de instancia e propriedades que são compartilhados
por todas as instancias de uma classe. Ele não adiciona nenhum metodo ou propriedade
para a instancia original do JavaScript (sem usar o prototype).

Mas quando você precisar adicionar metodos estaticos ou propriedades estaticas 
para a propria classe você pode usar o [`reopenClass()`][1].

[1]: http://emberjs.com/api/classes/Ember.Object.html#method_reopenClass

```javascript
// add static property to class
Person.reopenClass({
  isPerson: false
});
// override property of Person instance
Person.reopen({
  isPerson: true
});

Person.isPerson; // false - because it is static property created by `reopenClass`
Person.create().get('isPerson'); // true
```
