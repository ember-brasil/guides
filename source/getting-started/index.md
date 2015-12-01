Começar a desenvolver com Ember.js é facil. Projetos ember são criados e mantidos 
pelo Ember CLI, nossa tool na linha de começando. 

O Ember CLI nos ajuda nas seguintes funções:

* Um gerenciamento moderno dos assets da nossa aplicação (incluindo concatenação, minificação e versionamento)
* Generators para nos ajudar a criar componentes, rotas e muito mais
* Um layout padrão para o projeto, fazendo a aplicação ser muito mais facil de entender.
* Suporte ao JavaScript ES2015/ES6 pelo projeto [Babel](http://babeljs.io/docs/learn-es2015/).
 Isso inclue suporte aos [Modulos do JavaScript](http://exploringjs.com/es6/ch_modules.html), que serão usados 
 durante esse guide.
* Um ambiente de testes pronto utilizando [QUnit](https://qunitjs.com/).
* A habilidade de utilizar addons mais facilmente.

## Dependencias

### Node.js and npm

Ember CLI foi desenvolvido com JavaScript, usa o runtime do [Node.js](https://nodejs.org/)
runtime. Também é necessario ter o [npm](https://www.npmjs.com/) instalado na sua maquina. 
O npm esta empacotado junto com o Node.js, então você terá ele na sua maquina assim que instalar o node.

Ember precisa do Node.js 0.12 ou mais novo, assim como a npm 2.7 ou maior.
Se você estiver em duvida se tem um versão do node compativel como Ember é só você digitar isso
na sua linha de comando:

```bash
node --version
``

Ele vai exibir a versão instalada na sua maquina. 
Caso ele responda com um erro "command not found" você vai precisar instalar o node. Siga os passos abaixo:

* Para quem usa Windows ou Mac você pode acessar direto o site e baixar o [Node.js installer](http://nodejs.org/download/).
* Se você for usuario Mac você tambem pode baixar direto pelo [Homebrew](http://brew.sh/). É só ter ele instalado e rodar o comando run `brew install node` para instalar o Node.js.
* No Linux você pode usar [esse guia para instalação do Node.js no linux](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager).

Quando a instalação terminar tente rodar o codigo `node --version`, se ele respoder com uma versão significa que tudo foi instalado corretamente.

### Git

O Ember usa o Git para gerenciar algumas dependencias. Git vem instalado por default no Mac OS X e na maioria das distribuições linux. O instalador para windows pode ser encontrado nesse [link](http://git-scm.com/download/win).

### Watchman (opcional)

No Mac ou no Linux, você pode melhorar a performace do _file watching_ instalando [Watchman](https://facebook.github.io/watchman/docs/install.html).

### PhantomJS (opcional)

Usando o PhantomJS você pode rodar seus testes direto no terminal sem precisar de um navegador. Consulte as [instruções de instalação do PhantomJS](http://phantomjs.org/download.html).

## Instalação

Instale Ember usando npm:

```bash
npm install -g ember-cli
```

Para verificar se deu tudo certo na instalação, escreva no terminal:

```bash
ember -v
```

Se o resultado for algo parecido com `version: 1.13.0`, você vai ter tudo que você precisa pra desenvolver instalado.
