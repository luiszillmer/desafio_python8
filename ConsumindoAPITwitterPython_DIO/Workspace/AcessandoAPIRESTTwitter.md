# ACESSANDO A API REST DO TWITTER

Dando sequência ao [post anterior sobre APIs REST](https://pythonhelp.wordpress.com/2014/07/25/acessando-apis-rest-com-python/), este post irá mostrar como utilizar uma API REST “de verdade”: [a API do Twitter](http://dev.twitter.com/docs/api/1.1).

## A API do Twitter

A API REST do twitter, em sua versão 1.1, fornece acesso a vários recursos, dentre os quais podemos destacar: Tweets, Search, Direct Messages, Users, Friends & Followers e Trends. Para uma lista exaustiva dos recursos, veja a documentação oficial em [dev.twitter.com/docs/api/1.1](https://dev.twitter.com/docs/api/1.1).

Porém, boa parte dos recursos oferecidos pela API necessita de autenticação para que sejam fornecidos para sua aplicação. Dessa forma, vamos ver rapidamente como sua aplicação pode se autenticar junto ao serviço do Twitter.

## Autenticação

A API REST do twitter permite a autenticação através de dois mecanismos baseados no padrão [OAuth](https://en.wikipedia.org/wiki/OAuth):

1. **Autenticação \*application-only\***: a autenticação não fica vinculada a um usuário específico, mas sim a uma aplicação previamente registrada. Quando autenticado com esse mecanismo, sua aplicação não poderá realizar algumas operações típicas de um usuário, como postar tweets, por exemplo. Esse tipo de autenticação é mais indicado para aplicações que não terão um usuário interagindo com a rede social. Um exemplo seria um *app* que vai extrair dados do twitter para realizar análises.
2. **Autenticação de usuário**: a autenticação se dá diretamente por um usuário, de forma que a aplicação possa realizar operações comuns a usuários. Esse tipo de mecanismo é mais indicado para o caso de aplicativos que vão interagir com a rede social pelo usuário, como um *app* que posta as músicas mais ouvidas pelo usuário na semana.

Neste post, usaremos somente o primeiro tipo de autenticação (*application only*).

### Autenticação Application-only

Antes de mais nada, precisamos conhecer alguns conceitos básicos de autenticação usando o padrão OAuth. Para que possamos nos identificar como usuários de um serviço que utiliza um mecanismo de autenticação baseado em OAuth, é preciso que tenhamos os seguintes dados:

1. *API Key*: uma chave utilizada para que o nosso *app* se identifique perante o Twitter.
2. *API Secret*: um segredo usado pelo nosso *app* para provar que é o dono da *API Key*.
3. *Access Token*: depois de identificado junto ao serviço, nosso *app* precisa enviar o *access token* para que o serviço possa verificar qual é o nível de acesso que o *app* possui.
4. *Access Token Secret*: segredo usado pelo nosso *app* para provar que é o dono do *access token*.

### Como Obter Essas Chaves com o Twitter

Para obter as chaves de acesso ao serviço é preciso primeiramente registrar um aplicativo junto ao Twitter. Acesse [apps.twitter.com](http://apps.twitter.com/) e clique no botão *“Create new app”* para registrar um aplicativo para acesso à API. Primeiramente, você terá que preencher alguns dados básicos, como mostrado na imagem abaixo:



[![twitter1](https://pythonhelp.files.wordpress.com/2014/08/twitter1.png?w=269&h=318)](https://pythonhelp.files.wordpress.com/2014/08/twitter1.png)

Após isso, você será redirecionado para a página de gerenciamento do seu *app*, como mostra a imagem abaixo.

[![twitter2](https://pythonhelp.files.wordpress.com/2014/08/twitter2.png?w=269&h=293)](https://pythonhelp.files.wordpress.com/2014/08/twitter2.png)

Vá até a aba *“API Keys”* para acessar a página de gerenciamento das chaves de acesso do seu *app* ao serviço REST do twitter. Você verá uma página semelhante à da imagem abaixo.

[![twitter3](https://pythonhelp.files.wordpress.com/2014/08/twitter3.png?w=300&h=288)](https://pythonhelp.files.wordpress.com/2014/08/twitter3.png)



Anúncios

DENUNCIAR ESTE ANÚNCIO



Nela, clique no botão *“Create my access token”* (em vermelho na imagem acima) para que seja criado o token de acesso do app ao serviço. A imagem abaixo mostra a aba *“API Keys”* após termos criado as chaves de acesso necessárias.

 

[![twitter4](https://pythonhelp.files.wordpress.com/2014/08/twitter4.png?w=300&h=280)](https://pythonhelp.files.wordpress.com/2014/08/twitter4.png)

Agora que já temos todos os dados que precisamos para autenticar nosso aplicativo, vamos ver como fazer a autenticação junto ao serviço.

## Autenticando no serviço

Para nos autenticarmos no serviço do Twitter, precisaremos da biblioteca `requests-oauthlib` (instalável via `pip install requests_oauthlib`). De posse dela e das chaves geradas na etapa anterior, vamos criar uma sessão OAuth da seguinte forma:

```
>>> from requests_oauthlib import OAuth1Session
>>> session = OAuth1Session(API_KEY, API_SECRET, ACCESS_TOKEN, ACCESS_TOKEN_SECRET)
```

Agora, podemos usar o objeto **`session`** recém criado para realizar acesso à API.

## Acessando os recursos de busca da API

Vamos começar fazendo uma busca pelo termo `#python`, usando o recurso `search `fornecido pela API. De acordo com [a documentação](https://dev.twitter.com/docs/api/1.1/get/search/tweets), uma busca simples pode ser feita passando um parâmetro **`q`** à URL `https://api.twitter.com/1.1/search/tweets.json`. Veja no código abaixo que estamos executando as requisições sobre o objeto `session` criado anteriormente:

```
>>> response = session.get('https://api.twitter.com/1.1/search/tweets.json?q=%23python')
>>> print response.status_code
200
```



Anúncios

DENUNCIAR ESTE ANÚNCIO



Observe que passamos a string `"%23python"` ao parâmetro **`q`**.  `"%23"` é a representação do caractere `"#"` no [esquema de codificação de URLs](http://www.w3schools.com/tags/ref_urlencode.asp) usado na web, por isso a utilizamos na URL enviada ao serviço ao invés do caractere `"#"`. Para não termos que codificar a URL “na mão”, podemos utilizar a função `requests.utils.quote()` para codificar os caracteres da URL pra gente:

```
>>> print requests.utils.quote("#python")
%23python
>>> url = "https://api.twitter.com/1.1/search/tweets.json?q=%s"
>>> url = url % (requests.utils.quote("#python"))
>>> response = session.get(url)
```

O conteúdo retornado pelo serviço é uma string em formato JSON, e podemos decodificá-la usando a função [`json.loads`](https://docs.python.org/2/library/json.html):

```
>>> import json
>>> tweets = json.loads(response.content)
```

A chamada à função `loads` retorna um dicionário:

```
>>> print tweets.keys()
[u'search_metadata', u'statuses']
```

Os tweets encontrados são representados em uma lista dentro do dicionário, na posição de chave `statuses`:

```
>>> print len(tweets['statuses'])
15
```

Cada tweet é um dicionário dentro dessa lista:

```
>>> print type(tweets['statuses'][0])
<type 'dict'>
```



Anúncios

DENUNCIAR ESTE ANÚNCIO



E para cada dicionário representando um tweet, temos os seguintes atributos:

```
>>> print tweets['statuses'][0].keys()
[u'contributors', u'truncated', u'text', u'in_reply_to_status_id', u'id',
 u'favorite_count', u'source', u'retweeted', u'coordinates', u'entities',
 u'in_reply_to_screen_name', u'in_reply_to_user_id', u'retweet_count', u'id_str',
 u'favorited', u'user', u'geo', u'in_reply_to_user_id_str', u'possibly_sensitive',
 u'lang', u'created_at', u'in_reply_to_status_id_str', u'place', u'metadata']
```

Podemos ver alguns atributos interessantes, como `text` e `user`:

```
>>> print tweets['statuses'][0]['text']
#python wanna *split* a #Flask application into separates modules?blueprints: 1)create it; 2)register in the *root* http://t.co/DlZxJrw1qq
```

Já o atributo `user` contém outro dicionário, com as informações do usuário:

```
>>> print tweets['statuses'][0]['user']['description']
Algorithms. Program languages theory. UML. Design pattens. Python. C. C++. PHP. Perl. vim. PostgreSQL. MariaDB. Unix. Me.
```

Enfim, de cada tweet podemos tirar várias informações interessantes. Podemos, por exemplo, imprimir os tweets ordenados pela quantidade de retweets (do mais para o menos retweetado). Cada tweet possui um atributo chamado `"retweet_count"`, que usaremos na ordenação da lista de tweets contida em `tweets["statuses"]`. Veja o código abaixo:

```
>>> for t in sorted(tweets["statuses"], key=lambda x: x["retweet_count"], reverse=True):
        print t["text"]
```

## Limites no acesso

Para evitar abusos, a maioria dos provedores de serviços web impõem limites na quantidade de requisições que um cliente pode fazer dentro de uma janela de tempo. Cada serviço possui suas regras, portanto é bom conhecê-las para que seu *app* não deixe de funcionar de forma inesperada. Esse tipo de limite é por vezes chamado de *rate limiting* e você pode descobrir como isso funciona no twitter lendo a [seção da documentação deles que fala sobre isso](https://dev.twitter.com/docs/rate-limiting/1.1).



Anúncios

DENUNCIAR ESTE ANÚNCIO



Além desses limites, requisições de busca como as apresentadas anteriormente possuem limites na quantidade de valores que retornam por vez. Para evitar respostas muito grandes, o serviço do Twitter não retorna mais do que 100 tweets como resposta a uma única requisição. Por padrão, são retornados 15 tweets, mas esse valor pode ser alterado através do parâmetro `count`. Para obter 80 resultados nas requisições de pesquisa usadas anteriormente, poderíamos adicionar o parâmetro `count` com valor `80` à URL do serviço:

```
>>> url = "https://api.twitter.com/1.1/search/tweets.json?q=%s&count=%d"
>>> url = url % (requests.utils.quote("#python"), 80)
>>> response = session.get(url)
>>> tweets = json.loads(response.content)
>>> print len(tweets['statuses'])
80
```

### E se precisarmos mais do que 100 resultados?

Se precisamos obter os primeiros 1000 resultados, teremos que fazer 10 requisições solicitando 100 resultados de cada vez. Porém, 10 requisições à mesma URL possuem boas chances de terem como resultado os mesmos tweets. Para que não recebamos sempre os mesmos 100 resultados, a API possibilita que informemos o `id` do tweet mais antigo recebido na última resposta, de forma que, na próxima resposta, somente serão inclusos aqueles tweets que possuírem `id` menor que o especificado. Assim, basta pegar o menor id dentro do grupo de tweets recebidos para solicitar os próximos 100 tweets. Assim como anteriormente, vamos solicitar os 100 primeiros tweets contendo a palavra `"python"`:

```
>>> url = 'https://api.twitter.com/1.1/search/tweets.json?q=python&count=100'
>>> response = session.get(url)
>>> tweets = json.loads(response.content)
>>> print len(tweets['statuses'])
100
```

Para obtermos os próximos 100 tweets, precisamos descobrir o `id` do tweet mais antigo retornado na última resposta:

```
>>> oldest = min( for tweet in tweets['statuses']])-1
```



Anúncios

DENUNCIAR ESTE ANÚNCIO



Vamos passar esse valor na próxima requisição em um parâmetro chamado `max_id`, que indica ao serviço o `id` do tweet mais recente que queremos na requisição. Ou seja, iremos pegar agora os 100 tweets seguintes aos 100 que já obtivemos anteriormente. Para isso, basta:

```
>>> url = 'https://api.twitter.com/1.1/search/tweets.json?q=python&count=100&max_id='
>>> response = session.get(url + str(oldest))
>>> tweets = json.loads(response.content)
>>> print len(tweets['statuses'])
100
```

E assim, sucessivamente. Para obter os próximos 100 tweets, buscaríamos novamente o tweet de menor `id` do conjunto de resultados e passaríamos o mesmo como `max_id` da próxima requisição.

#### O campo next_results

Para facilitar a vida do desenvolvedor, o serviço já retorna um campo `next_results` dentro do campo `search_metadata` no resultado de uma requisição. Esse campo já contém a parte da URL relativa aos parâmetros prontinha, com o `max_id` corretamente configurado para pegarmos os próximos resultados. Veja um exemplo:

```
>>> response = session.get('https://api.twitter.com/1.1/search/tweets.json?q=brazil&count=100')
>>> tweets = json.loads(response.content)
>>> print tweets['search_metadata']['next_results']
?max_id=508446478730551295&q=python&count=100&include_entities=1
```

O valor de `max_id` é o mesmo que obteríamos pegando o menor `id` da requisição anterior e subtraindo `1`. Agora, podemos pegar os próximos 100 resultados usando `next_results:`

```
>>> url = 'https://api.twitter.com/1.1/search/tweets.json'
>>> response = session.get(url + tweets['search_metadata']['next_results'])
>>> tweets = json.loads(response.content)
>>> print len(tweets['statuses'])
100
```

## Obtendo os trending topics

Outra possibilidade (dentre muitas) é obter os *trending topics* de determinada região através do recurso [`trends`](https://dev.twitter.com/docs/api/1.1/get/trends/place). Sua utilização é muito simples, bastando passar à URL `https://api.twitter.com/1.1/trends/place.json` um identificador indicando o local de interesse. Esse identificador deve ser do tipo [WOEID (*Where On Earth IDentifier*)](https://developer.yahoo.com/geo/geoplanet/guide/concepts.html). Nesse esquema de representação, o id `1` representa o mundo inteiro, enquanto que o Brasil é representado como `23424768`. Abaixo, obtemos os 10 primeiros *trending topics* do mundo todo:

```
>>> response = session.get("https://api.twitter.com/1.1/trends/place.json?id=1")
>>> worlds = json.loads(response.content)[0]["trends"]
>>> for trend in worlds:
        print trend["name"]
```



Anúncios

DENUNCIAR ESTE ANÚNCIO



E também do Brasil:

```
>>> response = session.get("https://api.twitter.com/1.1/trends/place.json?id=23424768")
>>> brazils = json.loads(response.content)[0]["trends"]
>>> for trend in brazils:
        print trend["name"]
```

Agora que temos acesso aos dados, poderíamos começar a brincar com eles. Por exemplo, obter a lista de tópicos que estão no topo tanto no mundo quanto no Brasil:

```
>>> set([t['name'] for t in worlds]).intersection([t['name'] for t in brazils])
```

Mas é meio chato ter que ficar fazendo chamadas HTTP a cada vez que queremos obter dados do Twitter em nosso código. Que tal escrevermos uma classe com alguns métodos para nos auxiliar?

## Uma classe para acesso ao Twitter

Vamos agora criar uma classe Python que implemente algumas tarefas pré-definidas pra nós, como fazer uma busca por palavras-chave.

```
import json
from requests_oauthlib import OAuth1Session

MAX_TWEETS = 100
BASE_URL = "https://api.twitter.com/1.1/search/tweets.json"

class MyTwitterSearchClient(object):
    # preencha com os dados do seu app
    API_KEY = "sua API KEY"
    API_SECRET = "sua API SECRET"
    ACCESS_TOKEN = "SEU ACCESS TOKEN"
    ACCESS_TOKEN_SECRET = "SEU ACCESS TOKEN SECRET"
    
    
    def __init__(self):
        self.session = OAuth1Session(self.API_KEY,
                                     self.API_SECRET,
                                     self.ACCESS_TOKEN,
                                     self.ACCESS_TOKEN_SECRET)
    
    
    def get_tweets(self, keyword, n=15, max_id=None):
        if n > 0:
            url = BASE_URL + ("?q=%s&count=%d" % (keyword, n))
            if max_id is not None:
                url = url + "&max_id=%d" % (max_id)
            response = self.session.get(url)
            if response.status_code == 200:
                tweets = json.loads(response.content)
                oldest_id = min( for tweet in tweets['statuses']])-1
                return tweets['statuses'] + \
					self.get_tweets(keyword, n-MAX_TWEETS, oldest_id)
        return []
```



Anúncios

<iframe src="https://c0.pubmine.com/sf/0.0.3/html/safeframe.html" id="safeframe-sf-inline-ad-7" frameborder="no" scrolling="no" allowtranparency="true" hidefocus="true" tabindex="-1" marginwidth="0" marginheight="0" style="border: none; font-family: inherit; font-size: 14px; font-style: inherit; font-weight: inherit; margin: 0px; outline: 0px; padding: 0px; vertical-align: baseline; display: block; max-width: 100%; height: 90px; left: 0px; position: absolute; top: 0px; visibility: inherit; width: 728px; z-index: 0;"></iframe>

DENUNCIAR ESTE ANÚNCIO



Agora ficou mais fácil de buscar as informações que desejamos. Para obter os últimos 500 tweets contendo a palavra `"python"`, basta fazer:

```
>>> client = MyTwitterSearchClient()
>>> tweets = client.get_tweets("python", 500):
```

Assim como criamos uma classezinha para facilitar a nossa vida, existem alguns *wrappers* para a API REST do Twitter. Usando eles, não é preciso fazer requisições HTTP explicitamente. Basta invocar métodos em objetos Python para obter os dados desejados. Se quiser conhecer essas ferramentas, siga os links abaixo:

- python-twitter: https://github.com/bear/python-twitter
- tweepy: https://github.com/tweepy/tweepy
- twython: http://twython.readthedocs.org/en/latest/(dica do [@cassiobotaro](http://twitter.com/cassiobotaro))
- Outros wrappers: http://dev.twitter.com/docs/twitter-libraries#python