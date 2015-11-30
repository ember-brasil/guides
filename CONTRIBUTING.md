# Contribuindo

Os Ember Guides são mantidos e atualizados de maneira voluntaria por membros da comunidade Ember. Nós adorariamos se você se tornasse parte dessa comunidade! Aqui vamos listar algumas maneiras em que você pode nos ajudar.

* _Resolvendo problemas_: Se você encontrou um problema em alguma pagina em particular do guia, você pode abrir um pull request. Se você não tiver certeza de como concerta-lo você pode abrir uma issue.
* _Resolvendo duvidas_: Nos tentamos fazer com que seja facil para as pessoas contribuirem com os guides, você pode colocar uma tag de [help wanted](https://github.com/emberjs/guides/issues?q=is%3Aopen+is%3Aissue+label%3A%22help+wanted%22) quando for necessario. A melhor maneira de começar a contribuir é trabalhar em uma dessas issues.
* _Melhorando nosso conteudo_: Se você gostaria de contribuir com conteudo que você acha que esta faltando, por favor comece pesquisando na pagina de issues. Talvez já exista algum plano para adicionar esse conteudo. Caso ele não existir abra uma issue para você, assim você receberá feedback mesmo antes de começar à escrever. Nossos contribuidoes core podem pedir para você começar escrevendo um post sobre o assunto no lugar ou antes de aceitar seu pull request nos guias. Isso nos ajuda a manter o guia consistente e organizado.
* _Melhorando a estrutura do guia_: Você tambem pode nos ajudar nos guia melhorando o codigo que é usado para manter o conteudo. Issues relacionadas à esse tipo de contribução devem ter a label label [infrastructure](https://github.com/emberjs/guides/issues?q=is%3Aopen+is%3Aissue+label%3A%22infrastructure%22).

Por favor, note que não existirá esforço para atualizar o conteudo antigo (seja conteudo, estilo ou layout). Eles são considerados estaticos e imutaveis, é muito dificil manter o conteudo atualizado para cada versão lançada.Issues resolvidas só serão aplicadas nos proximos releases.

## Style Guide

Antes de abrir um PR com qualquer coisa a não ser uma pequena correção, por favor se familiarize com o estilo desse guia, assim podemos manter um padoa de qualidade alto, alem da consistencia durante a escrita de toda a documentação.

- Os **Guides** se referem à totalidade do Ember Guide projetc. note que aqui, _Guides_ esta com a primeira letra maiscula.
- O **guide** é uma pagina dos Guides, por exemplo, "Definindo sua Routes". aqui, _guide_ esta em letra minuscula.
- Uma **section** é um dos grupos que reune diversos guides, como por exemplo, "Routing".

Escreva senteças completas. Use um tom amigavel de voz. Write in complete sentences. Use a friendly tone of voice. Use title case para todos os titulos e cabeçalhos.

Seja o mais breve possivel, mas não sacrifique consistencia para fazer seu texto ser menor. Se um guide é maior que alguns scrools de tela, considere quebrar ele em multiplas paginas.

A audiencia alvo para esse guia seus desenvolvedores cujo skills vão de um desenvolvedor iniciante com talvez alguma experiencia em jQuery, até um desenvolvedor avançado tentando entender uma nova feature. Tenha certeza de escrever conteudo que atenda à ambas necessidades: manter explicações para os iniciantes, mas tambem incluir topicos avançados para um desenvolvedor mais experiente.

Os Guides tem como objetivo primario cobrir toda a parte de "caminho feliz", e não são feitos para se aprofundar muito em nenhum tema em especifico. Deixe casos raros ou features que não são muito utilizadas fazerem parte da documentação da API. Comece cada guide e cada sessão com uma lista simples das features mais utilizadas e assim vá se aprofundando até topicos mais avançados e features que normalmente não são utilizadas.

Cada guide deve explicar a feature por si mesmo e tambem incluir um link para a documentação da API. Links para autoridades authoritative em determinados assuntos tambem é encorajado: por exemplo, o guia do _Handlebars Basics_ de maneira correta encaminha o usuario para http://handlebarsjs.com/. Não faça links para blogs ou para apresentações meetup.

Sempre use exemplos na sua escrita. Por exemplo, a frase "Templates podem conter expressões dentro de chaves duplas" deve ser expandida para algo como: "Templates podem podem conter expressões dentro de chaves duplas, como \`&lt;h1&gt;Welcome {{user.name}}&lt;/h1&gt;\`". Um pequeno exemplo as vezes é mais claro que uma longa explicação. Na verdade, seria sempre interessante deixar mais que um exemplo para fazer as coisas ainda mais claras.

Quando estiver escrevendo: When writing prose

* Use a single space after periods.
* Manually break lines that are longer than 120 characters.

Nos exemplos de Codigo:

* Siga o [Ember Style Guide](https://github.com/emberjs/ember.js/blob/master/STYLEGUIDE.md).
* Use aspas duplas em templates, por exemplo, `<div class="awesome">{{foo-bar title="Tomster"}}</div>`.
* Não poste a parte gerada pelo Ember CLI, especialmente o `import Ember from 'ember'` que fica no topo de todos os arquivos.
* Ao escrever codigos de exmplo, inclua o nome do arquivo ou a linguagem depois de trés crases \´`´\´, como <code>&#96;&#96;&#96;routes/kittens.js</code> or <code>&#96;&#96;&#96;hbs</code>.
* Sempre escreva os paths relativos ao root do projeto.

Quando linkar para paginas da API:

* Use crases como parte do link, exemplo, \[<code>&#96;store.push()&#96;</code>] (api link)
* inclua parendeses queando estiver linkado com m metodo API, por exemplo, <code>&#96;store.push()&#96;</code>

Escreva uma vez, e edit duas (pelo menos antes de abrir seu PR. Quando você edita sua propria escrita você deve se perguntar.

* Eu estou usando gramatica e a ortografia corretamente?
* Eu posso deixar mais claro simplicicar alguma das sintuações ou exemplos?
* Eu inclui exemplos claros de qualaquer coisa que eu tenha documentado?
* Eu posso incluir algum link interessante? Did I include links where appropriate?

Você ficará surpreso em quaõ melhor fica suas habilidades de escrita quando começar a revisar uma ou duas vezes antes de entregar. 	