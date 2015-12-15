É fácil começar a desenvolver com Ember. Você vai poder criar e manter seu projeto utilizando uma ferramenta chamada Ember CLI. Resumidamente, essa ferramenta é um conjunto de comandos que você pode digitar no terminal e que vai facilitar bastante sua vida.

O Ember CLI nos ajuda nas seguintes funções:

* Gerenciamento moderno dos assets da nossa aplicação (incluindo concatenação, minificação e versionamento)
* Geradores para nos ajudar a criar componentes, rotas e muito mais
* Um layout padrão para o projeto, fazendo a aplicação ser muito mais fácil de entender.
* Suporte ao ES2015/ES6 Javascript através do projeto [Babel](http://babeljs.io/docs/learn-es2015/). Inclui suporte a [módulos JavaScript], que são usados neste guia
* Um ambiente de testes completo usando [QUnit](https://qunitjs.com/).
* A possibilidade de usar Addons de um ecosistema crescente de desenvolvedores Ember.

## Dependências

### Node.js and npm

Ember CLI foi desenvolvido com JavaScript, e por isso ele usa o runtime do [Node.js](https://nodejs.org/). O que também requer ter o [npm](https://www.npmjs.com/) instalado na sua máquina. O npm é instalado junto com o node, então se você tiver o node em sua maquina, provavelmente terá o npm.

As versões minimas que o ember utiliza são 0.12 para o node **(Node.js 0.12 >)**, e 2.7 para o npm (npm 2.7 >).

Se você não tiver certeza se tem Node.js instalado na sua máquia ou qual versão tem, basta digitar a seguinte linha de comando:

```bash
node --version
``

A versão instalada na sua máquina será exibida. Mas caso ele responda com um erro do tipo "command not found", isso significa que você não tem ele instalado. Para instalar basta seguir os passos abaixo:

* **Windows ou Mac**: Acesse o site do node e baixe o [Node.js installer](http://nodejs.org/download/).
* **Mac (homebrew)**: Se você preferir instalar usando o homebrew basta executar o comando `brew install node`.
* **Linux**: use esse [guia de instalação](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager).

Quando a instalação terminar tente executar o comando `node --version`, se ele responder com uma versão significa que tudo foi instalado corretamente.

### Git

O Ember usa o Git para gerenciar várias dependências. Git vem instalado por default no Mac OS X e na maioria das distribuições Linux. O instalador para Windows pode ser encontrado nesse [instalador Git](http://git-scm.com/download/win).

### Watchman (opcional)

No Mac ou no Linux, você pode melhorar a performance do _file watching_ instalando [Watchman](https://facebook.github.io/watchman/docs/install.html).

### PhantomJS (opcional)

Usando o PhantomJS você pode rodar seus testes direto no terminal, sem precisar de um navegador. Consulte as [instruções de instalação do PhantomJS](http://phantomjs.org/download.html).

## Instalação

Instale Ember usando npm:

```bash
npm install -g ember-cli
```

Para verificar se deu tudo certo na instalação, escreva no terminal:

```bash
ember -v
```

Se o resultado for algo parecido com `version: 1.13.0`, excelente! Você terminou a instalação e já pode continuar.