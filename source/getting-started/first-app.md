Agora que você tem o CLI instalado, você pode criar sua primeira aplicação Ember digitando no terminal:

```shell
ember new my-new-app
```

Isso irá criar um novo diretório com sua aplicação chamado `my-new-app`.

Quando o `ember new` acabar, execute o servidor de desenvolvimento do Ember:

```shell
cd my-new-app
ember server
```

Abra o navegador e vá para a pagina `http://localhost:4200` para ver sua aplicação funcionando.

##O servidor de desenvolvimento do Ember CLI
Ele nos dá varias features como como fazer o rebuilding da aplicação toda vez que um arquivo mudar e até
um servidor mock para configurar algumas _fake data_ para seus testes.


Em um servidor de produção, você vai querer uma versão otimizada de todos os assets do seu site.
Para fazer isso você precisa fazer a build da aplicação especificando que seu environment será
em produção. Então rode no terminal:

```shell
ember build --environment=production
```

Quando esse processo acabar sua aplicação vai estar pronta para
uso em produção dentro da pasta `dist/`. Basta copiar o conteúdo desse diretório para seu servidor.
Se você precisa de mais recursos você pode usar o addon [ember-cli-deploy](http://ember-cli.github.io/ember-cli-deploy/)



