# Twint: coleta de dados do twitter com python

[![Naiara Cerqueira](https://miro.medium.com/fit/c/56/56/2*eykDWztHtcF-Ts6nenYVQA.jpeg)](https://naiaracerqueira.medium.com/?source=post_page-----dac3dec8a1c5-----------------------------------)

[Naiara Cerqueira](https://naiaracerqueira.medium.com/?source=post_page-----dac3dec8a1c5-----------------------------------)

[Sep 21, 2020·3 min read](https://naiaracerqueira.medium.com/twint-coleta-de-dados-do-twitter-com-python-dac3dec8a1c5?source=post_page-----dac3dec8a1c5-----------------------------------)







![img](https://miro.medium.com/max/306/1*2tcbT85mR5gP0EPJbmpPog.png)

Logo do Twitter

O twitter é uma fonte de dados em constante crescimento. Porém, quando iniciamos a coleta de dados, percebemos que a API limita o número de tweets coletados a *3.200*.

Para nossa sorte, existe a *Twint*, uma biblioteca do python que tem a vantagem de conseguir buscar quase todos os tweets, ter configuração rápida e poder ser utilizada anonimamente, sem necessidade de login no twitter. Uma desvantagem é que, até o momento, ela não funciona em notebooks jupyter.

A instalação pode ser feita de diversas maneiras (para mais informações, [clique aqui](https://pypi.org/project/twint/)):

**Git**:

```
git clone https://github.com/twintproject/twint.git pip3 install -r requirements.txt
```

**Pip**:

```
pip3 install twint
```

**Ou**:

```
pip3 install --user --upgrade -e git+https://github.com/twintproject/twint.git@origin/master#egg=twint
```

**Pipenv**:

```
pipenv install -e git+https://github.com/twintproject/twint.git#egg=twint
```

A busca pode ser realizada pela linha de comando. Alguns exemplos são:

- `twint -u *usuário*` - busca todos os tweets do usuário
- `twint -u *usuário* -s *palavra*` - busca tweets do usuário contendo uma palavra
- `twint -s *palavra*` - busca todos os tweets contendo determinada palavra
- `twint -u *usuário* --year *2018*` - busca tweets postados antes de 2018
- `twint -u *usuário* --since "*2020-09-18 20:30:15*"` — busca tweets desde a data / hora citados, se hora ocultada, o padrão é `00:00:00` .
- `twint -u *usuário* -o *arquivo.txt*` - salva os tweets no `*arquivo.txt*`
- `twint -u *usuário* -o *arquivo.csv* --csv` -salva os tweets no `*arquivo.csv*` *.* O mesmo pode ser feito com arquivos json *(*`file.json --json) ou bando de dados SQLite` ou banco de dados SQLite (`--database tweets.db` )
- `twint -u *usuário* --email --phone` - mosta tweets que possuem telefones e emails
- `twint -s "*palavra*" --verified` - mosta tweets de pessoas verificadas sobre algum tema/palavra.
- `twint -u *usuário* --followers` - busca por seguidores de um usuário ou quem um usuário segue (`--following` )
- `twint -u *usuário* --favorites` - busca os tweets favoritados de um usuário (~3200 tweets).

Diversas outras opções de configurações de busca podem ser encontradas [aqui](https://github.com/twintproject/twint/wiki/Basic-usage).

Muito legal, certo? Mas é ainda **mais interessante** realizar uma configuração mais detalhada utilizando uma IDE:

![img](https://miro.medium.com/max/842/1*WUZNsdEN-6rGb0gNyLb64Q.png)

Importação de tweets sobre Bolsonaro e Covid

Por exemplo, nessa busca aqui, eu procurei todos os tweets em português que citavam ‘Bolsonaro’ e ‘Covid’, no intervalo do dia 01 de março até dia 31 de agosto, salvando um arquivo csv numa pasta ‘bolsonaro_pandemia’.

**Vamos entender em detalhes**: primeiro criamos um objeto de configuração, que chamei de `c` :

```
c = twint.Config()
```

Agora, podemos configurar os parâmetros, como, por exemplo, nome do usuário, termo de pesquisa, língua desejada, intervalo de tempo, etc. (as configurações disponíveis estão [aqui](https://github.com/twintproject/twint/wiki/Configuration)):

```
c.Username = "usuário"
c.Search = "termo de pesquisa"
c.Lang = "pt"
c.Year = "2019"
```

Para salvar um arquivo de saída com os dados de busca, por exemplo, em formato csv:

```
c.Output = "nome_do_arquivo.csv"
c.Store_csv = True
```

Depois de tudo configurado, é só rodar a busca:

```
twint.run.Search(c)
```

Pronto, agora você terá um arquivo salvo no seu computador com todos os dados buscados!

Fontes:

[twintproject/twintTwint is an advanced Twitter scraping tool written in Python that allows for scraping Tweets from Twitter profiles…github.com](https://github.com/twintproject/twint/wiki)

[twintNo authentication. No API. No limits. Twint is an advanced Twitter scraping tool written in Python that allows for…pypi.org](https://pypi.org/project/twint/)

Eu quero me tornar cientista de dados, logo, se tiver algo errado, alguma observação, crítica ou elogio: **me avise**!

Obrigada a todos pelas dicas ❤