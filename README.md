# Guia do ember em português
Esse repositório é uma versão traduzida do conteúdo do repositório [original](https://github.com/emberjs/guides), caso você encontre alguma diferença entre os dois leve em consideração que o oficial é o em inglês (e também mande um PR com a correção pra gente).

## Como contribuir
Se você encontrar algum erro você pode reportar uma issue e resolveremos o mais breve possível, mas se você tiver algum tempo você pode preparar um PR e mandar pra gente. :D

## Layout do projeto
* __Source__: Conteúdo do guia.
* __data/pages.yml__: Menu da esquerda com as sessões do guia.
* __lib__: Contem os plugins do ´Middleman´.
* _spec__: Os testes para esses plugins
 
## Dependências
Esse guia foi desenvolvido com Middleman, que é suportado em Ruby 1.9.3 ou mais novo (preferencialmente Ruby 2.0.0).
Durante a build, ele irá pedir para você instalar Aspell, para procurar erros ortográficos. No mac ele pode ser instalando usando o Homebrew:

`brew install aspell --with-lang-en`

Se você estiver usando windows você pode baixar o [installer](http://aspell.net/win32/), porem ele não esta sendo mais mantido e assim não recebe mais atualizações. No linux você pode baixar direto do seu gerenciador de pacotes ou fazer uma [build direto da ultima versão](http://aspell.net/man-html/Installing.html) .

# Fazendo deploy local
Para começar você vai precisar clonar esse repositório na sua maquina e depois executar um bundle para instalar o Middleman corretamente. Digite no seu terminal:

```bash
git clone https://github.com/EmberJSBrasil/guides
cd guides
bundle
bundle exec middleman
```

Pronto, agora é só acessar [http://localhost:4567/](http://localhost:4567/).

## Erros ortográficos
Se você encontrar uma palavra que você sabe que esta correta, porem ela não esta no dicionario do Aspell você pode adicionar ela dentro do arquivo `data/spelling-exceptions.txt`. Esse arquivo aceita palavras separadas por linha e não é case-sensitive.

### Problemas que podem acontecer no ambiente windows
Essa é uma areá realmente complicada para darmos suporte, recomendamos que você utilize um Mac ou instale linux na sua maquina.

Mas se você realmente quiser desenvolver nesse ambiente acesse esse [link](https://github.com/emberjs/guides#troubleshooting-tips-for-windows-devs) para mais informações.


