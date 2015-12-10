Diferente de outros frameworks que implementam algum tipo de binding,
bindings no Ember.js podem ser usados com qualquer objeto. Podemos dizer que bindings são 
mais usados dentro do proprio framework, e para a maior parte dos problemas de uma aplicação Ember, 
computed properties são quase sempre a solução mais apropriada.

A maneira mais facil de se criar um _two-way binding_ é usar um [`computed.alias()`][1],
que especifica o caminho de um outro objeto.

[1]: http://emberjs.com/api/classes/Ember.computed.html#method_alias

```javascript
wife = Ember.Object.create({
  householdIncome: 80000
});

Husband = Ember.Object.extend({
  householdIncome: Ember.computed.alias('wife.householdIncome')
});

husband = Husband.create({
  wife: wife
});

husband.get('householdIncome'); // 80000

// Someone gets raise.
wife.set('householdIncome', 90000);
husband.get('householdIncome'); // 90000
```

Note que bindings não são atualizados automaticamente. O Ember espera todo o codigo da sua 
aplicação terminar de executar antes de sincronizar as mudanças, então você mudar a mesma 
propriedade quantas vezes você quiser sem se preocupar sobre isso sobrecarregar o sistema 
com muitas transições de dados.

## One-Way Bindings

Um one-way binding só se propaga em um direção, usando
[`computed.oneWay()`][1]. De vez enquando, one-way bindings são otimizações de performance 
sendo que você sempre pode usar um _two way binding_ (que na verdarde são one-way bindings se você nunca mudar um dos lados).
Algumas vezes one-way bindings são uteis para se criar um comportamente especifico como 
por exemplo criar o default de uma outra propriedade que pode ser overridden (como o endereço de entrega
que originalmente é o endereço de cobrança, mas pode ser alterado depois).

[1]: http://emberjs.com/api/classes/Ember.computed.html#method_oneWay

```javascript
user = Ember.Object.create({
  fullName: 'Kara Gates'
});

UserComponent = Ember.Component.extend({
  userName: Ember.computed.oneWay('user.fullName')
});

userComponent = UserComponent.create({
  user: user
});

// Changing the name of the user object changes
// the value on the view.
user.set('fullName', 'Krang Gates');
// userComponent.userName will become "Krang Gates"

// ...but changes to the view don't make it back to
// the object.
userComponent.set('userName', 'Truckasaurus Gates');
user.get('fullName'); // "Krang Gates"
```
