Imagine que você esta escrevendo uma aplicação em ember que irá gerenciar um blog.
Em um determinado momento, nos devemos responder perguntas do tipo _Em qual post
nos estamos olhando?_ ou _Estamos editando esse post?_, no Ember.js todas essas 
repostas são determinadas pela URL.

Uma URL pode ser carregada de algumas maneiras:

* O usuario carrega o aplicativo pela primeira vez.
* O usuario muda a URL manualmente, clicando no botão de voltar ou mesmo editando a barra de endereços.
* O usuario clica em um link dentro da aplicação.
* Algum outro evento da aplicação faz com que a URL seja alterada.

Independentemente de em que a URL irá se transformar, o route do Ember mapeia a URL atual para um
ou mais _route handlers_. Um _route handler_ pode fazer diversas coisas, como por exemplo:

* Eles podem renderizar um template.
* Eles podem carregar um modelo e fazer assim ele disponivel para o template.
* Eles podem redicionar para uma outra routa, como em uma autenticação por exemplo, 
em que você é redirecionado para algum lugar caso não tenha acesso à determinada parte da aplicação.
* Eles podem manipular ações que envolvam mudar o model ou criar uma nova transição para outra rota.
