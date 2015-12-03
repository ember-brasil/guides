Você vai perceber que os padrões de classe Javascript e as novas classes do ES2015
ainda não são completamente utilizadas no Ember. Objetos puros ainda podem ser encontrados
e nos iremos nos referir à eles como "hashes".

Objetos puros do JavaScript não suportam um recurso chamado `observer` (_observer_ é uma 
função que você seta para ser executada toda vez que uma propriedade de determinado objeto mudar),
então se um objeto esta participando do binding do ember você poderá ver que ele é declarado como 
`Ember.Object` em vez de ser um objeto comum.

Alem disso o [Ember.Object](http://emberjs.com/api/classes/Ember.Object.html) tambem nos permite 
um sistema de classes, e suporte a fetures como mixins e metodos contrutores. 
Algumas features no object model do ember não estão presentes no JavaScript puro, porem 
todas as são direcionadas para terem padrões com a linguagem.

Ember tambem extende o prototype do `Array` com a interface 
[Ember.Enumerable](http://emberjs.com/api/classes/Ember.Enumerable.html) 
que tambem nos da a capacidade de designar um `observer` para ele.

Alem de tudo isso, O Ember extende o prototype da `String` com algumas [metodos de formatação e localização](http://emberjs.com/api/classes/Ember.String.html).