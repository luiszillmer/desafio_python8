![img](https://miro.medium.com/max/1400/0*FOMiGfal2laAKHhb.jpg)

# Aprenda a fazer um Analisador de Sentimentos do Twitter em Python

[![Paulo Vasconcellos](https://miro.medium.com/fit/c/96/96/1*uPumelp1dA3enXiv8vVAxg.jpeg)](https://medium.com/@paulohenriquevasconcellos?source=post_page-----3979454f2d0d-----------------------------------)

[Paulo Vasconcellos](https://medium.com/@paulohenriquevasconcellos?source=post_page-----3979454f2d0d-----------------------------------)Follow

[Dec 1, 2016](https://paulovasconcellos.com.br/aprenda-a-fazer-um-analisador-de-sentimentos-do-twitter-em-python-3979454f2d0d?source=post_page-----3979454f2d0d-----------------------------------) · 5 min read













*Utilizando a API do Twitter e a biblioteca TextBlob, você poderá saber o grau de polaridade em um tweet com menos de 30 linhas de código.*

> Update: Obrigado ao 
>
> [Luis Alberto Silva Santos](https://medium.com/u/a33604316f0d?source=post_page-----3979454f2d0d-----------------------------------)
>
> ,
>
>  
>
> [Cassio Alan Garcia](https://medium.com/u/1ab44816b538?source=post_page-----3979454f2d0d-----------------------------------)
>
>  
>
> e
>
>  
>
> [Paulo Muetunda](https://medium.com/u/c09113613430?source=post_page-----3979454f2d0d-----------------------------------)
>
>  
>
> por reportarem um problema encontrado no código, o mesmo já está alterado. :)

E se você conseguisse descobrir, através de um *tweet,* se a pessoa está mais feliz que aposentado no aniversário do Guanabara ou se ela está 300% *putassa*? Isso é possível graças ao lindo Python e as bibliotecas Tweepy e TextBlob. Abaixo, trago um tutorial sobre como você mesmo pode criar seu analisador de *tweets*. Confira:

![img](https://miro.medium.com/freeze/max/60/0*X-H2e9AlvBHfAdAQ.gif?q=20)

![img](https://miro.medium.com/max/480/0*X-H2e9AlvBHfAdAQ.gif)

Gil das esfirras: 300% putasso

[Clique aqui para acessar este repositório no Github](https://github.com/paulozip/sentiments_analyst/)

# Criando seu aplicativo no Twitter

Primeiro de tudo, você precisa ter um aplicativo no [Twitter Apps](https://apps.twitter.com/) para criar seu programa. Isso permitirá você autenticar no Twitter e utilizar sua linda API. Mas, não se preocupe. Criar um aplicativo é mais fácil que lamber o cotovelo.

1. Primeiro, [acesse o site oficial do Twitter Apps](https://apps.twitter.com/) e logue com sua conta do Twitter;
2. Clique em “Create New App” e digite os dados que solicitam, como: Nome, Descrição e site. Não se esqueça de aceitas os termos;

![img](https://miro.medium.com/max/60/1*5ss05niXDFGhhH5xatuhUQ.png?q=20)

![img](https://miro.medium.com/max/700/1*5ss05niXDFGhhH5xatuhUQ.png)

Digite as informações do seu aplicativo

\3. Feito esse processo com sucesso, você poderá ver seu novo aplicativo surgir na tela principal do site. Agora, clique sobre o nome da sua aplicação para abri-la;

\4. Acesse a seção “Keys and Access Tokens” para localizar seus dados de autenticação. Encontre os seguintes dados:

- Consumer Key (API Key)
- Consumer Secret (API Secret)
- Access Token
- Access Token Secret

![img](https://miro.medium.com/max/60/1*rEXNDGZsPIIHDSKy2NqDCA.png?q=20)

![img](https://miro.medium.com/max/700/1*rEXNDGZsPIIHDSKy2NqDCA.png)

Não posso deixar você ver meu API Secret. É segredo.

Detalhe: o Access Token e Access Token Secret ficarão disponíveis apenas quando você selecionar a opção “Generate My Access Token and Token Secret”, na seção “Your Access Token”, nesta mesma página.

Agora, guarde essas informações e vamos seguir em frente!

![img](https://miro.medium.com/freeze/max/60/0*ovAVqXPJ1i8fG9KA.gif?q=20)

![img](https://miro.medium.com/max/500/0*ovAVqXPJ1i8fG9KA.gif)

# **Instalando as bibliotecas**

Bom, para começar, vamos importar as bibliotecas que precisaremos usar nessa aplicação: TextBlob, Tweepy e NumPy.

```
from textblob import TextBlob as tb
import tweepy
import numpy as np
```

Um detalhe: eu importei a biblioteca NumPy no meu código porque ao final eu desejo descobrir a média de polaridades dos *tweets* filtrados, mas, caso deseje, você não precisa importá-la, ou pode importar uma biblioteca que você gosta mais, tipo statistics.

![img](https://miro.medium.com/max/60/1*JLWnvT1BefGnQzfnWts97A.png?q=20)

![img](https://miro.medium.com/max/700/1*JLWnvT1BefGnQzfnWts97A.png)

Instalando uma biblioteca via pip

Se você não tem as bibliotecas instaladas, basta abrir o *prompt* e digitar os comandos abaixo:

```
pip install textblob
pip install tweepy
```

Tenha certeza que tem a melhor versão do Python para trabalhar. Caso não tenha ele instalado em sua máquina, [aconselho baixar e instalar o Anaconda](https://www.continuum.io/downloads), a melhor distribuição do Python, que vem com uma cacetada de bibliotecas prontas para usar. É só chamar.

![img](https://miro.medium.com/freeze/max/60/0*DhLVqfUfhNUcxX1t.gif?q=20)

![img](https://miro.medium.com/max/500/0*DhLVqfUfhNUcxX1t.gif)

# *Ok, let’s go! Leeeroy Jenkins!*

Vamos começar nosso código e vasculhar *tweets.* Primeiro, vamos criar quatro variáveis para armazenar nossas informações de autenticação:

```
consumer_key = 'SUA CONSUMER KEY'
consumer_secret = 'SUA CONSUMER SECRET'access_token = 'SEU ACCESS TOKEN'
access_token_secret = 'SEU ACCESS TOKEN SECRET'
```

Certo, temos nossos dados de autenticação, agora vamos criar duas variáveis que irão realizar o login na API:

```
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth)
```

![img](https://miro.medium.com/max/60/1*0Y3x8sNsv3lzB8HGzo2Kjg.jpeg?q=20)

![img](https://miro.medium.com/max/700/1*0Y3x8sNsv3lzB8HGzo2Kjg.jpeg)

[Clique aqui e seja um Patreon!](https://www.patreon.com/paulovasconcellos/)

O negócio *tá* começando a ficar bonito! O próximo passo é o mais divertido, pois iremos definir que palavra queremos pesquisar no Twitter. Que tal procurarmos por “Trump”?

```
#Variável que irá armazenar todos os Tweets com a palavra escolhida na função search da APIpublic_tweets = api.search('Trump')
```

No meu código, ao final da coleta desses *tweets*, eu quero realizar uma média da polaridade dos usuários que estão falando sobre nosso querido presidente americano ❤ (só que não). Caso não queira fazer isso, você pode ignorar o próximo bloco:

```
#Variável que irá armazenar as polaridadesanalysis = None
```

Beleza, nós criamos nossa autenticação e já temos acesso aos *tweets*. O que podemos fazer agora? Já sei! Vamos criar um *laço for* para imprimirmos todos os *tweets* e, em seguida, a polaridade.

```
tweets = [] # Lista vazia para armazenar scores
for tweet in public_tweets:
    print(tweet.text)
    analysis = tb(tweet.text)
    polarity = analysis.sentiment.polarity
    tweets.append(polarity)
    print(polarity)
```

O que o código acima fez, basicamente foi: para cada *tweet* dentro da variável *public_tweets*, o programa deverá imprimir o *tweet* e, em seguida, imprimir sua analise usando a função *sentiment.polarity* do TextBlob. Além disso, ele irá armazenar o valor da polaridade na variável tweets.

A função *sentiment.polarity* retornará um número entre -1 e 1, onde quanto maior esse número, menos *putassa* a pessoa está, basicamente.

E por fim, como disse anteriormente, irei realizar um cálculo médio de todos os *tweets* retornados, utilizando a função *mean* do NumPy.

```
print('MÉDIA DE SENTIMENTO: ' + str(np.mean(tweets)))
```

# Vamos rodar!

Tudo está bonito. Terminamos nosso código, realizamos nossas configurações e tudo que nos resta a fazer é rodar o programa e descobrir o que deu errado, quer dizer, descobrir o que ele retornou. Vamos lá?

![img](https://miro.medium.com/max/2000/1*pxKoxpme56dn-dD2U4rtKA.png)

Cada linha retornou o tweet seguido pela sua polaridade

Que coisa mais linda. Nosso *script* funcionou! Ele retornou as mensagens que contém a palavra “Trump” e, em seguida, mostrou o *score* de polaridade.

# Agora, a parte chata

Agora que a diversão acabou, hora de algumas explicações. A biblioteca TextBlob é um módulo de processamento de linguagem natural que, dentre suas funções, possui uma que classifica a polaridade de uma mensagem — *sentiment.polarity*. Por debaixo dos panos, ele atribui pontuações a cada palavra, retornando um resultado que representa a polaridade da *string*.

Infelizmente, o TextBlob atualmente só funciona com o idioma Inglês. Em 2016 houve uma atualização nos serviços da Google que impossibilita os usuários utilizarem os serviços de tradução — como a função *translate* e *detect_language* do TextBlob — sem pagar. Caso você tente rodar esses comandos, receberá o código de erro: HTTP Error 503: Service Unavailable

Se divertiu? Nao deixe de seguir meu blog para mais notícias e tutoriais como esse. Você também pode me seguir no [Twitter](https://twitter.com/paulo_zip) e postar suas dúvidas aqui. Até a próxima!

## Créditos

[Esse tutorial é baseado no vídeo do canal Sirajology. Recomendo muito para quem curte de Data Science](https://www.youtube.com/channel/UCWN3xxRkmTPmbKwht9FuE5A). :)