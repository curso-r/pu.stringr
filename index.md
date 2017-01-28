---
title: Stringr
date: '2017-01-28'
---





Variáveis do tipo texto são muito comuns nos bancos de dados e geralmente são
colunas que dão bastante trabalho para serem manipuladas. É muito comun encontrar
bancos de dados em que os textos de uma coluna não estão padronizados, por exemplo
uma coluna `Estado` em que são encontrados "SP", "sp", "Sao Paulo", "São Paulo", etc
todas as variações indicando o mesmo estado.

Para manipular esses textos o R possui diversas funções para manipular textos, no entanto,
as funções do `base` não possuem um interface consistente e parece que cada uma 
tem a sua forma de passar os parâmetros, dificultando o processo durante a análise.

Por isso, é recomendado usar o pacote `stringr` que possui a sintaxe consistente 
permitindo que o usuário realize qualquer manipulação com textos com maior facilidade.

### Vantagens do stringr em relação ao base

- Sintaxe unificada, o que auxilia na memorização e leitura do código.
- Todas as funções são vetorizadas.
- Construído sobre a [biblioteca ICU](http://site.icu-project.org/), implementada em `C` e `C++`, apresentando resultados rápidos e confiáveis.

### Regras básicas do pacote

- As funções de manipulação de texto começam com `str_`. Caso esqueça o nome de uma função, basta digitar `stringr::str_` e apertar `TAB` para ver quais são as opções.
- O primeiro argumento da função é sempre uma `string` ou um vetor de `strings`.

### Curiosidade

Inicialmente, o `stringr` era um *wrapper* de funções do `base`. Depois disso, 
surgiu um novo pacote `stringi`, com sintaxe similar ao `stringr`, mas funcionando como 
*wrapper* da biblioteca ICU. No entanto, Wickham gostou tanto do pacote `stringi` 
que decidiu reescrever o `stringr` como um *wrapper* do `stringi`. 
Veja [essa página](https://github.com/tidyverse/stringr/blob/master/NEWS.md) para detalhes.



## Conceitos básicos



Todas as funções do `stringr` começam com o prefixo `str`, isso ajuda na hora de 
encontrar a função que você está procurando. No Rstudio, basta digitar `str_` e
apertar tab que você verá algo parecido com a imagem a baixo. Você pode ir descendo
com as setas do teclado e ver o que cada função faz até encontrar a função que estava
procurando.

![str_tab](figures/str_tab.png)

Nesta sessão vamos utilizar as funções mais simples do `stringr`, depois vamos
ensinar um pouco de Regex e em seguida veremos as funções mais avançadas do 
pacote.

### str_length

A função mais simples do `stringr` é a função `str_length`. Esta função recebe 
apenas uma string como argumento e retorna o número de caracteres. Por exemplo:


```r
library(stringr)
str_length("olá")
## [1] 3
length("olá")
## [1] 1
```

Veja `str_length` é diferente de `length`. O primeiro retorna o número de caracteres
e o segundo retorna o comprimento do objeto. Isso fica mais claro no seguinte exemplo:


```r
s <- c("olá mundo", "olá universo")
str_length(s)
## [1]  9 12
length(s)
## [1] 2
```

Agora o `str_length` retornou um vetor com o número de caracteres de cada elemento do vetor `s`, 
e o `length` retornou o comprimento do vetor `s`. Note que espaço é considerado como um caractere. 

### str_trim

É muito comum encontrar textos que vêm com espaços a mais, principalmente de dados
provenientes de formulários em que cada usuário escreve da forma que prefere. 


```r
string <- '\nessa      string é muito suja       \n'
str_trim(string)
## [1] "essa      string é muito suja"
```

A função `str_trim` ajuda removendo os espaços excedetes antes e depois da string.

### str_sub

As vezes você precisa obter alguma parte fixa de uma string. Por exemplo, as vezes você 
encontra variáveis com valores da forma:


```r
s <- c("01-Feminino", "02-Masculino", "03-Indefinido")
```

Você pode querer manipular essa string para retirar obter apenas a parte final da string.
Neste caso pode usar a função `str_sub`


```r
str_sub(s, start = 4) # pegar do quarto até o último caractere
## [1] "Feminino"   "Masculino"  "Indefinido"
```

É possível obter também apenas os números


```r
str_sub(s, end = 2) # pegar apenas os dois primeiros caracteres
## [1] "01" "02" "03"
```

Em outros casos você precisa obter os últimos 2 caracteres.


```r
s <- c("Feminino-01", "Masculino-02", "Indefinido-03")
str_sub(s, end = -4)
## [1] "Feminino"   "Masculino"  "Indefinido"
str_sub(s, start = -2)
## [1] "01" "02" "03"
```

É possível também usar os argumentos `start` e `end` conjuntamente.


```r
s <- c("__SP__", "__MG__", "__RJ__")
str_sub(s, 3, 4)
## [1] "SP" "MG" "RJ"
```

### str_to_upper, str_to_lower, str_to_title

Essas funções servem para modificar a caixa das letras. Por exemplo:


```r
s <- "Olá, tudo bem?"
str_to_lower(s)
## [1] "olá, tudo bem?"
str_to_upper(s)
## [1] "OLÁ, TUDO BEM?"
str_to_title(s)
## [1] "Olá, Tudo Bem?"
```

Essas são as funções mais simples do pacote `stringr` e mão exigem nenhum conhecimento
de regex. Note que nenhuma delas possui o parâmetro `pattern`, você verá como especificar
esse parâmetros nas próximas sessões.



## Expressões Regulares

Trabalhar com textos exige um certo conhecimento de expressões regulares (*regex*).
[Expressões regulares](https://pt.wikipedia.org/wiki/Express%C3%A3o_regular) permitem
identificar conjuntos de caracters, palavras, e outros padrões por meio de uma sintaxe
concisa. 

O `stringr` utiliza regex da forma descrita [neste documento](http://www.gagolewski.com/software/stringi/manual/?manpage=stringi-search-regex). 
A própria [definição](https://stat.ethz.ch/R-manual/R-devel/library/base/html/regex.html) de 
regex do R é um ótimo manual.

Vamos estudar expressões regulares através de exemplos e com a função `str_detect()`. 
Essa função retorna `TRUE` se uma string atende à uma expressão regular e `FALSE` 
em caso contrário.

Por exemplo:


```r
library(stringr)
str_detect("sao paulo", pattern = "paulo$")
## [1] TRUE
str_detect("sao paulo sp", pattern = "paulo$")
## [1] FALSE
```

A regex/pattern "paulo$" indica que o texto deve ser terminado em "paulo". Existem 
diversos de caracteres auxiliares que vão auxiliar na manipulação dos textos, assim como
o "$" neste caso. É importante notar que sempre que você estiver passando algum
valor para o argumento `pattern` de qualquer função do `stringr` ele o entenderá
como uma regex. 

A tabela abaixo mostra a aplicação de seis `regex` a seis strings distintas.




|testes          |^ban  |b ?an |ban   |BAN   |ban$  |
|:---------------|:-----|:-----|:-----|:-----|:-----|
|abandonado      |FALSE |TRUE  |TRUE  |FALSE |FALSE |
|ban             |TRUE  |TRUE  |TRUE  |FALSE |TRUE  |
|banana          |TRUE  |TRUE  |TRUE  |FALSE |FALSE |
|BANANA          |FALSE |FALSE |FALSE |TRUE  |FALSE |
|ele levou ban   |FALSE |TRUE  |TRUE  |FALSE |TRUE  |
|pranab anderson |FALSE |TRUE  |FALSE |FALSE |FALSE |

### Quantificadores

Os caracteres `+`, `*` e `{x,y}` indicam quantas vezes um padrão se repete:

- `ey+` significa `e` e depois `y` "**uma vez** ou mais". Por exemplo, reconhece `hey`, `heyy`, `a eyyy`, mas não reconhece `e`, `y` nem `yy`.
- `ey*` significa "**zero vezes** ou mais". Por exemplo, reconhece `hey`, `heyy`, `a eyyy` e `e`, mas não reconhece `y` nem `yy`.
- `ey{3}` significa "exatamente três vezes". Por exemplo, reconhece `eyyy` e `eyyyy`, mas não reconhece `eyy`.
- `ey{1,3}` significa "entre uma e três vezes".

Para aplicar um quantificador a um conjunto de caracteres, use parênteses. Por exemplo, `(ey )+` reconhece `ey ey `.

### Conjuntos

Colocando caracteres dentro de `[]`, reconhecemos quaisquer caracteres desse conjunto. Alguns exemplos práticos:

- `[Cc]asa` para reconhecer "casa" em maiúsculo ou minúsculo.
- `[0-9]` para reconhecer somente números. O mesmo vale para letras `[a-z]`, `[A-Z]`, `[a-zA-Z]` etc.
- O símbolo `^` dentro do colchete significa negação. Por exemplo, `[^0-9]` significa pegar tudo o que não é número.
- O símbolo `.` fora do colchete indica "qualquer caractere", mas dentro do colchete é apenas ponto.
- Use `[[:space:]]+` para reconhecer espaços e `[[:punct:]]+` para reconhecer pontuações.

### Miscelânea

- Use `abjutils::rm_accent()` para retirar os acentos de um texto.
- Use `|` para opções, por exemplo `desfavor|desprov` reconhece tanto "desfavorável" quanto "desprovido"
- `\n` pula linha, `\f` é final da página, `\t` é tab. Use `\` para transformar caracteres especiais em literais.
- `tolower()` e `toupper()` para mudar o case de uma string. 

A lista de possibilidades com expressões regulares é extensa. 
Um bom lugar para testar o funcionamento de expressões regulares é o [regex101](https://regex101.com/).




## Funções avançadas

Agora que já vimos as funções básicas do `stringr`, e aprendemos um pouco de regex,
vamos às funções mais avançadas. Basicmante, essas funções buscarão `patterns` em
um vetor de strings e farão alguma coisa quando encontrá-lo.

Como já vimos na sessão sobre regex, a função mais simples que possui o argumento
`pattern` é a `str_detect`.

### str_detect()` 

Retorna `TRUE` se a regex é compatível com a string e `FALSE` caso contrário.


```r
library(stringr)
str_detect("sao paulo", pattern = "paulo$")
## [1] TRUE
str_detect("sao paulo sp", pattern = "paulo$")
## [1] FALSE
```

### str_replace() e str_replace_all() 

Substituem um padrão (ou todos) encontrado para um outro padrão


```r
frutas <- c("uma maçã", "duas pêras", "três bananas")
str_replace(frutas, "[aeiou]", "-") # substitui a primeira vogal de cada string por "-"
## [1] "-ma maçã"     "d-as pêras"   "três b-nanas"
str_replace_all(frutas, "[aeiou]", "-") # substitui todas as vogais por "-"
## [1] "-m- m-çã"     "d--s pêr-s"   "três b-n-n-s"

yyyy <- "yyyyy yyyyy ll zz"
str_replace(yyyy, 'y+', 'x') # substitui o primeiro 1 ou mais y's por x
## [1] "x yyyyy ll zz"
str_replace_all(yyyy, 'y+', 'x') # substitui todos os 1 ou mais y por somente 1 x
## [1] "x x ll zz"
str_replace_all(yyyy, 'y', 'x') # substitui y por x
## [1] "xxxxx xxxxx ll zz"

str_replace_all('string     com    muitos espaços', ' +', ' ') # tirar espaços extras
## [1] "string com muitos espaços"
```

Muitas vezes queremos remover alguns caracteres especiais de um texto, mas esses 
caracteres fazem parte de comandos de regex, por exemplo:


```r
string <- "1 + 2 + 5"
str_replace_all(string, "+", "-")
## Error in stri_replace_all_regex(string, pattern, fix_replacement(replacement), : Syntax error in regexp pattern. (U_REGEX_RULE_SYNTAX)
```

Essa forma retorna um erro, pois a função tenta montar uma regex. Você poderia 
tentar de outras formas, que não retornariam erro, mas também não retornariam o 
resultado esperado.


```r
str_replace_all(string, " + ", " - ")
## [1] "1 + 2 + 5"
```

Nesse caso, use a função `fixed` para indicar para o `stringr` que você não deseja
que o parâmetro seja reconhecido como uma regex.


```r
str_replace_all(string, fixed("+"), "-")
## [1] "1 - 2 - 5"
```

### str_extract() e str_extract_all()

Extraem padrões de uma string. Por exemplo:


```r
r_core_group <- c(
  'Douglas Bates', 'John Chambers', 'Peter Dalgaard',
  'Robert Gentleman', 'Kurt Hornik', 'Ross Ihaka', 'Tomas Kalibera',
  'Michael Lawrence', 'Friedrich Leisch', 'Uwe Ligges', '...'
)
sobrenomes <- str_extract(r_core_group, '[:alpha:]+$')
sobrenomes
##  [1] "Bates"     "Chambers"  "Dalgaard"  "Gentleman" "Hornik"   
##  [6] "Ihaka"     "Kalibera"  "Lawrence"  "Leisch"    "Ligges"   
## [11] NA
```

### str_match() e str_match_all() 

Extrai pedaços da string identificados pela regex. Caso queira extrair 
somente a parte identificada, use parênteses. Isso é útil quando você 
está interessado em uma parte do padrão, mas para identificá-lo precisa


```r
# Exemplo de pergunta SOPt: http://pt.stackoverflow.com/q/150024/6036
presidentes <- c("da Fonseca, DeodoroDeodoro da Fonseca", 
"Peixoto, FlorianoFloriano Peixoto", "de Morais, PrudentePrudente de Morais", 
"Sales, CamposCampos Sales")
nomes_presidentes <- str_match(presidentes, '(.*), ([a-zA-Z]{1,})[A-Z]{1}')
nomes_presidentes
##      [,1]                   [,2]         [,3]      
## [1,] "da Fonseca, DeodoroD" "da Fonseca" "Deodoro" 
## [2,] "Peixoto, FlorianoF"   "Peixoto"    "Floriano"
## [3,] "de Morais, PrudenteP" "de Morais"  "Prudente"
## [4,] "Sales, CamposC"       "Sales"      "Campos"
str_c(nomes_presidentes[,3], nomes_presidentes[,2], sep = ' ')
## [1] "Deodoro da Fonseca" "Floriano Peixoto"   "Prudente de Morais"
## [4] "Campos Sales"
```

### str_split() e str_split_fixed()

Separa uma string em várias de acordo com um separador.


```r
string <- 'Durante um longo período de tempo o "R" foi escrito "P" como no alfabeto cirílico. O seu nome no alfabeto fenício era "rech". Seu significado era o de uma cabeça, representada pela adaptação do hieróglifo egípcio de uma cabeça. Transformou-se no "rô" dos gregos. Os romanos modificaram o rô acrescentando um pequeno traço para diferenciá-lo do no nosso P.'
str_split(string, fixed('.'))
## [[1]]
## [1] "Durante um longo período de tempo o \"R\" foi escrito \"P\" como no alfabeto cirílico"                
## [2] " O seu nome no alfabeto fenício era \"rech\""                                                         
## [3] " Seu significado era o de uma cabeça, representada pela adaptação do hieróglifo egípcio de uma cabeça"
## [4] " Transformou-se no \"rô\" dos gregos"                                                                 
## [5] " Os romanos modificaram o rô acrescentando um pequeno traço para diferenciá-lo do no nosso P"         
## [6] ""
```

O `str_split_fixed` faz o mesmo que `str_split()`, mas separa apenas `n` vezes.


```r
str_split_fixed(string, fixed('.'), 3)
##      [,1]                                                                                   
## [1,] "Durante um longo período de tempo o \"R\" foi escrito \"P\" como no alfabeto cirílico"
##      [,2]                                          
## [1,] " O seu nome no alfabeto fenício era \"rech\""
##      [,3]                                                                                                                                                                                                                                      
## [1,] " Seu significado era o de uma cabeça, representada pela adaptação do hieróglifo egípcio de uma cabeça. Transformou-se no \"rô\" dos gregos. Os romanos modificaram o rô acrescentando um pequeno traço para diferenciá-lo do no nosso P."
```

### str_subset() 

Retorna somente as strings compatíveis com a regex.


```r
frases <- c('a roupa do rei', 'de roma', 'o rato roeu')
str_subset(frases, 'd[eo]')
## [1] "a roupa do rei" "de roma"
```

É o mesmo que fazer o subset do R e a função `str_detect`.


```r
frases[str_detect(frases, "d[eo]")]
## [1] "a roupa do rei" "de roma"
```




## Exercícios

**1.** Faça uma regex que capture múltiplas versões da palavra 'casa'. Ela deve funcionar
com as palavras 'Casa', 'CASA', 'CaSa', 'CAsa'. Teste-a usando a função `str_detect`.


```r
palavras <- c('Casa', 'CASA', 'CaSa', 'CAsa')
```

**2.** Imagine que você possui a seguinte string que é a parte final de uma URL:

* `/ac/rio-branco/xpto-xyz-1-0-1fds2396-5`

Você precisa transformá-la em 'AC - Rio Branco'. **Dica**: Use a função `str_split`.


```r
url <- c('/ac/rio-branco/xpto-xyz-1-0-1fds2396-5')
```

**3.** Você possui o seguinte vetor de caracteres: `x <- c('Alto', 'Médio', 'Baixo')`. A partir desse vetor,
crie um vetor com os valores `'03 - Alto', '02 - Médio' e '03 - Baixo'.

**4.** Crie uma função que retorna `TRUE` quando a string é um [palíndromo](https://pt.wikipedia.org/wiki/Pal%C3%ADndromo) e `FALSO` caso não seja.
*Palíndromo* é uma sequência que é lida da mesma forma tanto da direita pra esquerda quanto
da esquerda para a direita. O nome 'Ana', por exemplo, é um palíndromo.

**5.** De acordo com as regras da língua portuguesa, antes de “P” ou “B” devemos usar a letra “M”. Em outras palavras, com outras consoantes, usamos a letra “N”. Suponha que você tem o seguinte
texto com erros gramaticais.


```r
texto <- 'Nós chamamos os bonbeiros quando começou o incêndio.'
```

Crie uma função para corrigí-lo. 

**6.** O CPF é um número de 11 dígitos, por exemplo: 54491651884. No entanto para facilitar
a visualização costumamos mostrá-lo com separadores a cada 3 casas: 544.916.518-84.
Crie uma função que transforma um número de 11 dígitos em uma string com as separações, como um CPF.



