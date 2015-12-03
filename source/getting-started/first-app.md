Agora que você tem o Ember CLI instalado, você pode criar sua primeira aplicação Ember digitando no terminal:

```shell
ember new my-new-app
```

Isso irá criar um novo diretório chamado `my-new-app` com sua aplicação dentro.` Quando esse comando parar de executar digite o seguinte comando para iniciar o servidor de desenvolvimento do Ember:

```shell
cd my-new-app
ember server
```

Abra o navegador e vá para a pagina `http://localhost:4200` para ver sua aplicação funcionando.

##O servidor de desenvolvimento do Ember CLI

Ele realmente nos ajuda no processo de desenvolvimento da aplicação, ele é o responsável por fazer o rebuild da sua aplicação toda vez que ele identificar que um arquivo foi alterado,  também é possível criar um servidor mock para facilitar seu desenvolvimento usando dados mais próximos dos reais.

Para usar sua aplicação em um ambiente de produção, você pode fazer a otimização de todos os assets do seu site. Para fazer isso basta digitar o execute:

```shell
ember build --environment=production
```

Esse comando cria um diretório chamado `dist/` e colocar todos os arquivos para uso em produção lá dentro, basta copiar o conteúdo dessa pasta para seu servidor e pronto.

Um outro jeito de fazer o deploy é usar um addon chamado [ember-cli-deploy](http://ember-cli.github.io/ember-cli-deploy/) ele ira permitir diversas funcionalidades alem do deploy, vale a pena dar uma olhada.





