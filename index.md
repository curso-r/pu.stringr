---
title: Introdução
date: '2017-01-20'
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

## Vantagens do stringr em relação ao base

- Sintaxe unificada, o que auxilia na memorização e leitura do código.
- Todas as funções são vetorizadas.
- Construído sobre a [biblioteca ICU](http://site.icu-project.org/), implementada em `C` e `C++`, apresentando resultados rápidos e confiáveis.

## Regras básicas do pacote

- As funções de manipulação de texto começam com `str_`. Caso esqueça o nome de uma função, basta digitar `stringr::str_` e apertar `TAB` para ver quais são as opções.
- O primeiro argumento da função é sempre uma `string` ou um vetor de `strings`.

## Curiosidade

Inicialmente, o `stringr` era um *wrapper* de funções do `base`. Depois disso, 
surgiu um novo pacote `stringi`, com sintaxe similar ao `stringr`, mas funcionando como 
*wrapper* da biblioteca ICU. No entanto, Wickham gostou tanto do pacote `stringi` 
que decidiu reescrever o `stringr` como um *wrapper* do `stringi`. 
Veja [essa página](https://github.com/tidyverse/stringr/blob/master/NEWS.md) para detalhes.





Todas as funções do `stringr` começam com o prefixo `str`, isso ajuda na hora de 
encontrar a função que você está procurando. No Rstudio, basta digitar `str_` e
apertar tab que você verá algo parecido com a imagem a baixo. Você pode ir descendo
com as setas do teclado e ver o que cada função faz até encontrar a função que estava
procurando.

![str_tab](figures/str_tab.png)

Nesta sessão vamos utilizar as funções mais simples do `stringr`, depois vamos
ensinar um pouco de Regex e em seguida veremos as funções mais avançadas do 
pacote.

## Funções básicas

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







