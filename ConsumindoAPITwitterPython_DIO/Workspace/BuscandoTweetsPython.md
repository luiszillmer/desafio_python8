# Buscando tweets com Python

![img](https://www.alura.com.br/artigos/assets/uploads/2018/04/image_0-4.png)

![Yuri Matheus](https://www.gravatar.com/avatar/796821a47f8d0c21c14e4bb0b0415f9e.png?r=PG&size=200x200&date=2021-12-04&d=https%3A%2F%2Fcursos.alura.com.br%2Fassets%2Fimages%2Fforum%2Favatar_y.png)

Yuri Matheus - 04/10/2018



[![img](https://www.alura.com.br/assets/api/formacoes/categorias/programacao.svg)Esse artigo faz parte daFormação Iniciante em Programação](https://www.alura.com.br/formacao-programacao)

Quando fazemos uma análise de dados, é muito comum utilizarmos dados provenientes de um banco de dados, arquivos de texto, como `.csv`, arquivos de log, entre outros. Além desses, existem outros locais que podemos obter dados para serem analisados.

Por exemplo, quando estamos querendo saber sobre alguma tecnologia. Nós podemos procurar em blogs, fóruns, redes sociais, na documentação da tecnologia… Todos esses locais contém que podem nos ajudar com a nossa busca.

Na análise de dados também! Podemos, por exemplo, varrer sites em busca de informações, ou então, utilizar as redes sociais para entender melhor nossos clientes. Podemos utilizar essas informações para melhorar nossos produtos, ou então sugerir o produto que melhor atenda as necessidades do cliente.

Existem vários sites e redes sociais que podemos utilizar para obter informações, o Twitter é um exemplo. Nós lemos os tweets e obtemos as informações, mas temos que ler cada tweet? Isto é, precisamos ler um tweet por vez e fazer a análise nós mesmos?

Podemos fazer dessa maneira, contudo é um pouco trabalhoso, não acha?

Nós poderíamos criar um **script** que busca os tweets para a gente com base em uma palavra, por exemplo. E, então, criamos outro script para classificá-los.

Mas como conseguimos captar os tweets?

### Buscando tweets

Para buscar os tweets, nós precisamos nos comunicar com a [**API do Twitter**](https://developer.twitter.com/en/docs/tweets/search/api-reference/get-search-tweets#). Existem algumas bibliotecas em Python que podemos utilizar para isso. Uma muito utilizada é a [Tweepy](http://www.tweepy.org/). Com ela, conseguimos, entre outras coisas, buscar tweets.

Como estamos utilizando o Python, uma maneira de instalar essa biblioteca é utilizar o `pip`. Portanto: `pip install tweepy`.

![img](https://www.alura.com.br/artigos/assets/uploads/2018/04/image_0-4.png)

Agora, para utilizá-la no nosso código, basta importá-la:

```
import tweepy
```

Se queremos ter acesso a todas funcionalidades do Twitter, precisamos ter uma conta. O mesmo acontece quanto temos que criar uma aplicação que acessa o Twitter. Nós precisamos "logar" nossa aplicação para ter acesso a sua API.

Logo, temos que [**registrar nossa aplicação no Twitter**](https://apps.twitter.com/). Aqui precisamos criar um novo app, isto é, uma aplicação que se comunicará com a nossa conta, e, com isso, temos acesso às informações que precisamos para "logar" nossa aplicação.

Na **aba Chaves e Token de Acesso (Keys and Access Token)**, temos as informações que vamos usar para nos conectar no Twitter. Isto é, a chave do consumidor (Consumer key), o segredo do consumidor (Consumer secret), os token de acesso (Access token) e seu segredo (Access token secret).

A chave de consumidor e seu segredo são como nosso "usuário", enquanto os tokens são como nossa “senha". Logo, não podemos ficar compartilhando essas informações.

Como essas chaves são muito grandes, vamos guardá-las em variáveis ficar mais fácil de manipulá-las:

```
import tweepy

chave_consumidor = 'xxxxxxx'
segredo_consumidor = 'xxxxxxx'
token_acesso = 'xxxxxxx'
token_acesso_segredo = 'xxxxxxx'
```

Mas nós ainda não nos autenticamos nossa aplicação Twitter, apenas pegamos as credenciais. Como vamos acessar pelo Tweepy, podemos dizer para o `tweepy` se encarregar dos nossos dados de autenticação.

Então, podemos falar para o Tweepy nos autenticar utilizando nossa chaves de acesso:

```
#restante do codigo

autenticacao = tweepy.OAuthHandler(chave_consumidor, segredo_consumidor)
```

A classe **OAuthHandler** é responsável por nos autenticar no Twitter. Veja que no construtor só passamos a chave do consumidor e seu segredo. Mas e os tokens?

Podemos falar quais são os tokens da nossa autenticação. Isto é, nós podemos atribuir um valor para o token de acesso:

```
#restante do codigo

autenticacao = tweepy.OAuthHandler(chave_consumidor, segredo_consumidor)
autenticacao.set_access_token(token_acesso, token_acesso_segredo)
```

Legal! Já temos todos os dados para nos autenticar. Então vamos para para o `tweepy` se conectar com a `API` do Twitter com esses dados de `autenticacao`:

```
#restante do codigo

twitter = tweepy.API(autenticacao)
```

Já estamos conectados com o Twitter, agora podemos buscar (search) nossos tweets.

```
#restante do codigo

twitter.search()
```

Mas o que vamos buscar? Isto é, qual nossa consulta (`q`)? Na [documentação do Twitter](https://developer.twitter.com/en/docs/tweets/search/api-reference/get-search-tweets#), ele diz que devemos passar um parâmetro de busca chamado `q`. Por exemplo, se queremos pesquisar sobre os tweets da Alura ou que citam ela, podemos passar como parâmetro seu nome:

```
#restante do codigo

twitter.search(q='AluraOnline')
```

Vamos rodar o código e ver se temos algum resultado:

![img](https://www.alura.com.br/artigos/assets/uploads/2018/04/image_1-4.png)

Cadê nossos tweets? Quando utilizamos o método `search`, ele nos devolve um objeto com o resultado da busca. Ou seja, podemos atribuir esse resultado em uma variável `resultados`, por exemplo.

```
#restante do codigo

resultados = twitter.search(q='AluraOnline')
```

Para visualizar os tweets, nós podemos percorrer os `resultados` e para cada tweet dentro de resultados, imprimimos o autor, ou seja, o usuário (`user`) que fez o tweet e a mensagem do tweet (`text`).

```
#restante do codigo

resultados = twitter.search(q='AluraOnline')
for tweet in resultados:
     print(f'Usuário: {tweet.user} - Tweet: {tweet.text}')
```

![img](https://www.alura.com.br/artigos/assets/uploads/2018/04/image_2-4.png)

Ele imprimiu o objeto usuário, isto é, seu id, nome, localização e muito mais. Para uma visualização, seria melhor imprimir apenas o nome do usuário, ou seja, o nome que aparece na tela quando acessamos seu perfil (`screen_name`).

```
#restante do codigo

resultados = twitter.search(q=’AluraOnline’)
for tweet in resultados:
     print(f‘Usuário: {tweet.user.screen_name} - Tweet: {tweet.text}’)
```

![img](https://www.alura.com.br/artigos/assets/uploads/2018/04/image_3-4.png)

Agora sim! Temos uma visualização mais limpa dos tweets.

### Para saber mais

Além do Tweepy, temos outras bibliotecas que podemos utilizar para nos comunicar com a API do Twitter. Uma delas é o [Twython](https://twython.readthedocs.io/en/latest/). A sua utilização é similar com a Tweepy.

Neste caso, como não utilizamos uma conta paga para fazer a busca dos tweets, não temos acesso a todos os tweets postados, porém, conseguimos um valor bom para começarmos uma análise.

Além de buscar tweets, essas bibliotecas nos permitem buscar usuários, descobrir o número de seguidores e até tuitar alguma mensagem como se estivéssemos utilizando o site ou o aplicativo.

Conseguimos captar os tweets. Agora podemos salvá-los em uma lista, ou em um arquivo e fazer nossas análises. Aqui na Alura, temos uma [**formação de machine learning**](https://www.alura.com.br/formacao-machine-learning) onde você aprenderá a criar algoritmos que processam textos e classificá-los. Por exemplo, se o tweet é ou não um spam.