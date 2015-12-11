Diferente de outros frameworks que implementam algum tipo especifico para  binding,
bindings no Ember.js podem ser usados em qualquer objeto. Podemos dizer que bindings são
mais usados dentro do próprio framework, e que os problemas que irão aparecer durante o desenvolvimento de uma aplicação Ember e que podem ser resolvidos com bindings provavelmente tem como solução melhor o uso de _computed properties _.

A maneira mais fácil de se criar um _two-way binding_ é usar um [`computed.alias()`][1],
passando como parâmetro o caminho especifico do um outro objeto.

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

// Alguém recebeu um aumento
wife.set('householdIncome', 90000);
husband.get('householdIncome'); // 90000
```

Note que bindings não são atualizados automaticamente. O Ember espera todo o código da sua aplicação terminar de executar antes de sincronizar essas mudanças, então você mudar a mesma propriedade quantas vezes você quiser sem se preocupar sobre isso sobrecarregar o sistema com muitas transições de dados.

## One-Way Bindings

Um _one-way binding_ é um binding que só se propaga em um direção, usando
[`computed.oneWay()`][1]. De vez enquanto, _one-way bindings_ são usados para alguma otimizações de performance, mas você sempre pode usar um _two-way binding_ (que na verdade são one-way bindings se você nunca mudar um dos lados).

Algumas vezes _one-way bindings_ são uteis para se criar um comportamentos específicos como por exemplo criar o default de uma outra propriedade que irá ser substituída depois, como por exemplo o endereço de entrega que originalmente é o endereço de cobrança, mas pode ser alterado depois.

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