É fácil começar a desenvolver com Ember. Você vai poder criar e manter seu projeto utilizando uma tool chamada Ember CLI, resumidamente essa tool é um conjunto de comandos que você pode digitar no terminal e que vai facilitar bastante sua vida.

O Ember CLI nos ajuda nas seguintes funções:

* Um gerenciamento moderno dos assets da nossa aplicação (incluindo concatenação, minificação e versionamento)
* Generators para nos ajudar a criar componentes, rotas e muito mais
* Um layout padrão para o projeto, fazendo a aplicação ser muito mais fácil de entender.
* Usa o [Babel](http://babeljs.io/docs/learn-es2015/) para garantir suporte ao ES2015.
* Um ambiente de testes pronto usando [QUnit](https://qunitjs.com/).
* Aumenta a simplicidade no processo de usar um addon para o ember.

## Dependências

### Node.js and npm

Ember CLI foi desenvolvido com JavaScript, e por isso ele usa o runtime do [Node.js](https://nodejs.org/). Mas alem do node, também é necessário ter o [npm](https://www.npmjs.com/) instalado na sua maquina. O npm esta normalmente é instalado junto com o node, então se você tiver o node na sua maquina você provavelmente terá o npm.

As versões minimas que o ember utiliza são 0.12 para o node **(Node.js 0.12 >)**, e 2.7 para o npm (npm 2.7 >).

Se você não tiver certeza de qual versões você tem instalado na sua maquina é só você digitar na linha de comando:

```bash
node --version
``

Ele vai exibir a versão instalada na sua maquina. Mas caso ele responda com um erro do tipo "command not found" isso significa que você não tem ele instalado. Para instalar basta seguir os passos abaixo:

* **Windows ou Mac**: Acesse o site do node e baixe o [Node.js installer](http://nodejs.org/download/).
* **Mac (homebrew)**: Se você preferir isntalar usando o homebrew basta executar o comando `brew install node`.
* **Linux**: use esse [guia de instalação](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager).

Quando a instalação terminar tente executar o comando `node --version`, se ele responder com uma versão significa que tudo foi instalado corretamente.

### Git

O Ember usa o Git para gerenciar algumas dependências. Git vem instalado por default no Mac OS X e na maioria das distribuições linux. O instalador para windows pode ser encontrado nesse [link](http://git-scm.com/download/win).

### Watchman (opcional)

No Mac ou no Linux, você pode melhorar a performance do _file watching_ instalando [Watchman](https://facebook.github.io/watchman/docs/install.html).

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

Se o resultado for algo parecido com `version: 1.13.0`, excelente, você terminou a instalação e já pode continuar o guide.