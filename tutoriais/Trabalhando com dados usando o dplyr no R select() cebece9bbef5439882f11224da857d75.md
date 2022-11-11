# Trabalhando com dados usando o dplyr no R: select(), filter(), mutate() e case_when()

[Gustavo Bruno de Paula](https://gustavobrp.github.io/)

---

Em análise de dados, grande parte do tempo é dedicado a tratá-los: verificar e corrigir possíveis erros; retirar casos com problemas ou que não interessam à análise; modificar variáveis conforme os objetivos da análise; entre outras ações. 

Além dos propósitos específicos de cada pesquisa, isso deve ser feito também porque raramente as bases não contém erros de inserção das informações, como também não foram geradas se ajustando perfeitamente aos nossos interesses de pesquisa. Frequentemente elas nem mesmo foram desenhadas para análises científicas, tendo sido produzidas para outros propósitos (por exemplo, administrativos). Em suma, grande parte do nosso tempo é gasto “limpando” os dados.

![Untitled](Trabalhando%20com%20dados%20usando%20o%20dplyr%20no%20R%20select()%20cebece9bbef5439882f11224da857d75/Untitled.png)

Nesse tutorial vou cobrir algumas funções do `dplyr`, pacote que faz parte do [Tidyverse](https://www.tidyverse.org/) e traz várias funções muito úteis para manipular dados, facilitando e acelerando nossas análises. Vou cobrir principalmente quatro funções que, atualmente, tenho usado com mais frequência: `select()`, `filter()`, `mutate()` e `case_when()`. 

Basicamente realizaremos três ações: **seleção, filtro, criação e transformação** de variáveis.

> Para esse tutorial vamos utilizar a [base de dados do Enade 2010](https://github.com/gustavobrp/projeto_r_educacao/raw/main/bases/MICRODADOS_ENADE_2010.zip), para fins didáticos, sem qualquer tipo de tratamento. O dicionário das variáveis pode ser baixado por [aqui](https://github.com/gustavobrp/projeto_r_educacao/raw/main/bases/Dicion%C3%A1rio%20de%20vari%C3%A1veis%20dos%20Microdados%20do%20Enade_Edi%C3%A7%C3%A3o%202010.zip). A base de dados original pode ser acessada e baixada pelo site do [Inep](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/enade).
> 

> **Observação:** Atualmente o Inep retirou o acesso público aos microdados de várias pesquisas nacionais sobre educação. Mas a base utilizada no exemplo pode ser baixada no link acima.
> 

> Nos exemplos apresentados utilizaremos o operador *pipe*: `%>%` do pacote `[magrittr](https://magrittr.tidyverse.org/)`.  O operador é carregado automaticamente com o `tidyverse`, o qual também carrega o `dplyr`. Esse operador facilita a análise, pois tem a grande vantagem de permitir que em poucas linhas você possa usar várias funções de maneira muito legível. O pessoal do [Curso-R](https://livro.curso-r.com/6-1-o-operador-pipe.html) tem um manual com boas explicações sobre o operador, mas em seguida vou tentar deixar evidente a sua utilidade.
> 

```r
library(tidyverse)
```

```r
-- Attaching packages -------------------------------------------------------------- tidyverse 1.3.1 --
v ggplot2 3.3.5     v purrr   0.3.4
v tibble  3.1.6     v dplyr   1.0.7
v tidyr   1.1.4     v stringr 1.4.0
v readr   2.1.0     v forcats 0.5.1
-- Conflicts ----------------------------------------------------------------- tidyverse_conflicts() --
x dplyr::filter() masks stats::filter()
x dplyr::lag()    masks stats::lag()
```

A função `select()` permite que você selecione colunas, seja para visualizar variáveis específicas ou aplicar outras funções somente para determinadas variáveis. 

No exemplo abaixo, selecionamos as colunas com os códigos dos munícipios e do estado dos cursos de graduação em que os estudantes estavam matriculados.

```r
bd.enade.2010 %>% select(CO_MUNIC_CURSO, CO_UF_CURSO)
```

```r
# A tibble: 422,896 x 2
   CO_MUNIC_CURSO CO_UF_CURSO
            <dbl>       <dbl>
 1        5103403          51
 2        5103403          51
 3        5103403          51
 4        5103403          51
 5        5103403          51
 6        5103403          51
 7        5103403          51
 8        5103403          51
 9        5103403          51
10        5103403          51
# ... with 422,886 more rows
```

Veja que usamos o pipe para fazer essa operação. A ideia básica é que a função do lado direito é passada para o objeto do lado esquerdo. Em outros termos, utilizamos a função `select()` para a base de dados. Nos exemplos seguintes ficará mais evidente a utilidade do pipe. 

Para facilitar ainda a operação de seleção de dados, a função ainda aceita argumentos que possibilitam selecionar colunas a partir de seus nomes. Por exemplo, na base do Enade tem várias colunas que começam pelo prefixo “CO_”. Podemos selecionar todas elas usando o argumento `starts_with()`, e então deixando o prefixo, ou termo que desejamos utilizar para selecionar, entre aspas. 

```r
bd.enade.2010 %>% 
  select(starts_with("CO_"))
```

```r
# A tibble: 422,896 x 18
   CO_IES CO_CATEGAD CO_ORGACAD CO_GRUPO CO_CURSO CO_MODALIDADE CO_MUNIC_CURSO CO_UF_CURSO
    <dbl>      <dbl>      <dbl>    <dbl>    <dbl>         <dbl>          <dbl>       <dbl>
 1      1      10002      10028       38        7             1        5103403          51
 2      1      10002      10028       38        7             1        5103403          51
 3      1      10002      10028       38        7             1        5103403          51
 4      1      10002      10028       38        7             1        5103403          51
 5      1      10002      10028       38        7             1        5103403          51
 6      1      10002      10028       38        7             1        5103403          51
 7      1      10002      10028       38        7             1        5103403          51
 8      1      10002      10028       38        7             1        5103403          51
 9      1      10002      10028       38        7             1        5103403          51
10      1      10002      10028       38        7             1        5103403          51
# ... with 422,886 more rows, and 10 more variables: CO_REGIAO_CURSO <dbl>, CO_RS_I1 <chr>,
#   CO_RS_I2 <chr>, CO_RS_I3 <chr>, CO_RS_I4 <chr>, CO_RS_I5 <chr>, CO_RS_I6 <chr>, CO_RS_I7 <chr>,
#   CO_RS_I8 <chr>, CO_RS_I9 <chr>
```

Podemos usar vários prefixos em um mesmo argumento utilizando a função `c()`.

```r
bd.enade.2010 %>% 
  select(starts_with(c("CO_", "TP_")))
```

```r
# A tibble: 422,896 x 32
   CO_IES CO_CATEGAD CO_ORGACAD CO_GRUPO CO_CURSO CO_MODALIDADE CO_MUNIC_CURSO CO_UF_CURSO
    <dbl>      <dbl>      <dbl>    <dbl>    <dbl>         <dbl>          <dbl>       <dbl>
 1      1      10002      10028       38        7             1        5103403          51
 2      1      10002      10028       38        7             1        5103403          51
 3      1      10002      10028       38        7             1        5103403          51
 4      1      10002      10028       38        7             1        5103403          51
 5      1      10002      10028       38        7             1        5103403          51
 6      1      10002      10028       38        7             1        5103403          51
 7      1      10002      10028       38        7             1        5103403          51
 8      1      10002      10028       38        7             1        5103403          51
 9      1      10002      10028       38        7             1        5103403          51
10      1      10002      10028       38        7             1        5103403          51
# ... with 422,886 more rows, and 24 more variables: CO_REGIAO_CURSO <dbl>, CO_RS_I1 <chr>,
#   CO_RS_I2 <chr>, CO_RS_I3 <chr>, CO_RS_I4 <chr>, CO_RS_I5 <chr>, CO_RS_I6 <chr>, CO_RS_I7 <chr>,
#   CO_RS_I8 <chr>, CO_RS_I9 <chr>, TP_SEXO <chr>, TP_SEMESTRE <dbl>, TP_INSCRICAO <dbl>,
#   TP_PRES <dbl>, TP_PR_GER <dbl>, TP_PR_OB_FG <dbl>, TP_PR_DI_FG <dbl>, TP_PR_OB_CE <dbl>,
#   TP_PR_DI_CE <dbl>, TP_SFG_D1 <dbl>, TP_SFG_D2 <dbl>, TP_SCE_D1 <dbl>, TP_SCE_D2 <dbl>,
#   TP_SCE_D3 <dbl>
```

Existem ainda outros argumentos. Caso queira selecionar pelo fim do nome da coluna, você pode usar o argumento `ends_with()` , ou selecionar todas que contenham alguma uma determinada palavra, use `contains()`. Para selecionar um intervalo de colunas, use o `:` entre os nomes das colunas. 

Parte da rotina de análise de dados é composta por filtragem de casos. Para isso podemos utilizar a função `filter()`. Como exemplo, pensemos que desejamos focalizar nossa análise para o grupo de participantes do exame que estavam matriculados no curso de Medicina. Para isso, basta filtrar a variável CO_GRUPO, em que há o código do curso de Medicina - no caso, 12. No código abaixo eu exemplifico de duas formas, usando o operador igual a, `==`, e operador está contido em, `%in%`. Os resultados são os mesmos.

```r
## usando operador ==
bd.enade.2010 %>% 
  filter(CO_GRUPO == 12)

## usando operador %in%
bd.enade.2010 %>% 
  filter(CO_GRUPO %in% 12)
```

```r
# A tibble: 30,004 x 115
   NU_ANO CO_IES CO_CATEGAD CO_ORGACAD CO_GRUPO CO_CURSO CO_MODALIDADE CO_MUNIC_CURSO CO_UF_CURSO
    <dbl>  <dbl>      <dbl>      <dbl>    <dbl>    <dbl>         <dbl>          <dbl>       <dbl>
 1   2010      1      10002      10028       12       12             1        5103403          51
 2   2010      1      10002      10028       12       12             1        5103403          51
 3   2010      1      10002      10028       12       12             1        5103403          51
 4   2010      1      10002      10028       12       12             1        5103403          51
 5   2010      1      10002      10028       12       12             1        5103403          51
 6   2010      1      10002      10028       12       12             1        5103403          51
 7   2010      1      10002      10028       12       12             1        5103403          51
 8   2010      1      10002      10028       12       12             1        5103403          51
 9   2010      1      10002      10028       12       12             1        5103403          51
10   2010      1      10002      10028       12       12             1        5103403          51
# ... with 29,994 more rows, and 106 more variables: CO_REGIAO_CURSO <dbl>, NU_IDADE <dbl>,
#   TP_SEXO <chr>, ANO_FIM_2G <dbl>, ANO_IN_GRAD <dbl>, TP_SEMESTRE <dbl>, IN_MATUT <dbl>,
#   IN_VESPER <dbl>, IN_NOTURNO <dbl>, ID_STATUS <dbl>, AMOSTRA <dbl>, TP_INSCRICAO <dbl>,
#   NU_ITEM_OFG <dbl>, NU_ITEM_OCE <dbl>, DS_VT_GAB_OFG_FIN <chr>, DS_VT_GAB_OCE_FIN <chr>,
#   DS_VT_ESC_OFG <chr>, DS_VT_ACE_OFG <chr>, DS_VT_ESC_OCE <chr>, DS_VT_ACE_OCE <chr>, TP_PRES <dbl>,
#   TP_PR_GER <dbl>, TP_PR_OB_FG <dbl>, TP_PR_DI_FG <dbl>, TP_PR_OB_CE <dbl>, TP_PR_DI_CE <dbl>,
#   TP_SFG_D1 <dbl>, TP_SFG_D2 <dbl>, TP_SCE_D1 <dbl>, TP_SCE_D2 <dbl>, TP_SCE_D3 <dbl>, ...
```

Lembre-se que para filtrar casos em que a resposta está em caractere ou fator, deve-se usar aspas para que não ocorra erros. No exemplo abaixo filtramos também usando outras condições: estudantes do curso de Medicina ou Odontologia, matriculados em Alagoas ou Sergipe, e que eram solteiros. 

```r
bd.enade.2010 %>% 
  filter(CO_GRUPO %in% c(6, 12),
         CO_UF_CURSO %in% c(27, 28),
         QE_I01 %in% c("A"))
```

```r
# A tibble: 660 x 115
   NU_ANO CO_IES CO_CATEGAD CO_ORGACAD CO_GRUPO CO_CURSO CO_MODALIDADE CO_MUNIC_CURSO CO_UF_CURSO
    <dbl>  <dbl>      <dbl>      <dbl>    <dbl>    <dbl>         <dbl>          <dbl>       <dbl>
 1   2010      3      10002      10028       12      303             1        2800308          28
 2   2010      3      10002      10028       12      303             1        2800308          28
 3   2010      3      10002      10028       12      303             1        2800308          28
 4   2010      3      10002      10028       12      303             1        2800308          28
 5   2010      3      10002      10028       12      303             1        2800308          28
 6   2010      3      10002      10028       12      303             1        2800308          28
 7   2010      3      10002      10028       12      303             1        2800308          28
 8   2010      3      10002      10028       12      303             1        2800308          28
 9   2010      3      10002      10028       12      303             1        2800308          28
10   2010      3      10002      10028       12      303             1        2800308          28
# ... with 650 more rows, and 106 more variables: CO_REGIAO_CURSO <dbl>, NU_IDADE <dbl>,
#   TP_SEXO <chr>, ANO_FIM_2G <dbl>, ANO_IN_GRAD <dbl>, TP_SEMESTRE <dbl>, IN_MATUT <dbl>,
#   IN_VESPER <dbl>, IN_NOTURNO <dbl>, ID_STATUS <dbl>, AMOSTRA <dbl>, TP_INSCRICAO <dbl>,
#   NU_ITEM_OFG <dbl>, NU_ITEM_OCE <dbl>, DS_VT_GAB_OFG_FIN <chr>, DS_VT_GAB_OCE_FIN <chr>,
#   DS_VT_ESC_OFG <chr>, DS_VT_ACE_OFG <chr>, DS_VT_ESC_OCE <chr>, DS_VT_ACE_OCE <chr>, TP_PRES <dbl>,
#   TP_PR_GER <dbl>, TP_PR_OB_FG <dbl>, TP_PR_DI_FG <dbl>, TP_PR_OB_CE <dbl>, TP_PR_DI_CE <dbl>,
#   TP_SFG_D1 <dbl>, TP_SFG_D2 <dbl>, TP_SCE_D1 <dbl>, TP_SCE_D2 <dbl>, TP_SCE_D3 <dbl>, ...
```

Outra parte fundamental do trabalho de análise de dados é criar ou transformar variáveis: criar médias, somas, subtrações, dentre outras operações. Exemplificaremos usando a função `mutate()`. 

A base do Enade contém variáveis com as notas dos estudantes nas questões dos componentes de formação geral e específica. Vamos criar uma variável a partir do cálculo simples da soma das notas do estudante nas duas provas. 

No exemplo eu usei também a função `select()` logo depois da `mutate()` apenas para que possa verificar e comparar as duas colunas no resultado no console. Mas já é possível ver melhor o operador pipe em prática.

```r
bd.enade.2010 %>% 
  mutate(NT_soma_notas = NT_FG + NT_CE) %>% 
  select(NT_FG, NT_CE, NT_soma_notas)
```

```r
# A tibble: 422,896 x 3
   NT_FG NT_CE NT_soma_notas
   <dbl> <dbl>         <dbl>
 1  58.3  25.8          84.1
 2  67.8  39.7         108. 
 3  48.7  58.4         107. 
 4  85.4  34.1         120. 
 5  82.4  37.2         120. 
 6  57.8  34            91.8
 7  16.6  11.5          28.1
 8  60.8  41.1         102. 
 9  81.4  35           116. 
10  53.7  54.5         108. 
# ... with 422,886 more rows
```

Um cuidado em relação ao `mutate()` quando for calcular médias. Nesse caso, deve-se usar a função `rowwise()` antes do `mutate()`. Isso é necessário porque a função do cálculo de médias na base R, `mean()`, é feita considerando toda a coluna e não por linhas. A função `rowwise()` indica que a operação deve ser feita por linha, considerando o valor de um mesmo caso (linha) para cada coluna que desejar. Abaixo calculamos a média do desempenho dos estudantes nas provas objetivas e discursivas dos componentes de formação geral e específica.

```r
bd.enade.2010 %>% 
  rowwise() %>% 
  mutate(NT_media_notas = mean(c(NT_OBJ_FG, NT_DIS_FG, NT_OBJ_CE, NT_DIS_CE))) %>% 
  select(NT_OBJ_FG, NT_DIS_FG, NT_OBJ_CE, NT_DIS_CE, NT_media_notas)
```

```r
# A tibble: 422,896 x 6
# Rowwise: 
   NT_OBJ_FG NT_DIS_FG NT_OBJ_CE NT_DIS_CE NT_media_notas NT_GER
       <dbl>     <dbl>     <dbl>     <dbl>          <dbl>  <dbl>
 1      57.1      60        30.4       0             36.9   33.9
 2      71.4      62.5      43.5      18.3           48.9   46.7
 3      42.9      57.5      65.2      20             46.4   56  
 4      85.7      85        34.8      30             58.9   46.9
 5      85.7      77.5      43.5       1.7           52.1   48.5
 6      71.4      37.5      39.1       5             38.2   40  
 7      14.3      20        13         3.3           12.6   12.8
 8      71.4      45        47.8       3.3           41.9   46  
 9      85.7      75        39.1      11.7           52.9   46.6
10      42.9      70        60.9      18.3           48.0   54.3
# ... with 422,886 more rows
```

O `mutate()` pode ser usado também como uma forma de renomear as variáveis, ainda que haja uma função específica para isso chamada `rename()`. 

```r
bd.enade.2010 %>% 
  mutate(var_sexo = TP_SEXO,
         var_idade = NU_IDADE) %>% 
  select(var_sexo,
         TP_SEXO,
         var_idade,
         NU_IDADE)
```

```r
# A tibble: 422,896 x 4
   var_sexo TP_SEXO var_idade NU_IDADE
   <chr>    <chr>       <dbl>    <dbl>
 1 F        F              28       28
 2 F        F              44       44
 3 F        F              39       39
 4 F        F              21       21
 5 F        F              28       28
 6 F        F              39       39
 7 F        F              29       29
 8 F        F              18       18
 9 F        F              19       19
10 F        F              34       34
# ... with 422,886 more rows
```

Para certos objetivos, pode ser muito útil transformar variáveis contínuas em categóricas. No próximo exemplo vamos tornar a variável idade, contínua, para uma nova, faixas de idade. Para isso, vamos combinar o uso da função `mutate()` e `case_when()`. Essa última função cria novas categorias ou respostas a partir da verificação lógica de condições. Ela é uma ótima alternativa para criar novas variáveis se baseando em várias condições.

Como pode ser observado em prática abaixo, o `case_when()` (nesse caso) é utilizado dentro da função `mutate()`. Basicamente, a função é dividida em duas fórmulas. A do lado direito você deve designar as condições que devem ser identificadas pela função e, do lado esquerdo, o novo valor que deseja designar. Elas são conectadas com o operador `~`. 

Pode-se descrever da seguinte maneira o que está acontecendo na segunda linha do código abaixo: *desejo criar a variável FAIXA_IDADE e, nos casos em que a variável, ou coluna, NU_IDADE tiver valores que estejam entre 15 e 19, o nome da categoria será “Até 19 anos”*. E assim em diante até terminar todas as condições desejadas. Cada nova categoria é separada por uma vírgula. Note também que usei os dois operadores: o `%in%` e o `>` (mais que).

```r
bd.enade.2010 %>% 
  mutate(FAIXA_IDADE = case_when(NU_IDADE %in% c(15:19) ~ "Até 19 anos",
                                 NU_IDADE %in% c(20:25) ~ "De 20 a 25 anos",
                                 NU_IDADE %in% c(26:30) ~ "De 26 a 30 anos",
                                 NU_IDADE > 30 ~ "Acima de 30 anos")) %>% 
  select(NU_IDADE, FAIXA_IDADE)
```

```r
# A tibble: 422,896 x 2
   NU_IDADE FAIXA_IDADE     
      <dbl> <chr>           
 1       28 De 26 a 30 anos 
 2       44 Acima de 30 anos
 3       39 Acima de 30 anos
 4       21 De 20 a 25 anos 
 5       28 De 26 a 30 anos 
 6       39 Acima de 30 anos
 7       29 De 26 a 30 anos 
 8       18 Até 19 anos     
 9       19 Até 19 anos     
10       34 Acima de 30 anos
# ... with 422,886 more rows
```

Pode-se usar o `case_when()` para variáveis em caracteres ou fatores. Abaixo criamos uma nova variável que combina o sexo declarado pelo estudante e a sua cor/raça ou etnia. Para isso, além dos operadores `==` e `%in%`, utiliza-se o `&`, para combinar os casos. Observe que utilizo a nomenclatura existente na base, mas no caso dos estudantes que se declararam negros(as) ou pardos(as) (tal como está registrado nos microdados), eu as combinei para uma única categoria. Evidentemente essas escolhas podem variar e devem estar amparadas por suas perguntas de pesquisa e abordagens teóricas.

```r
bd.enade.2010 %>% 
  mutate(VAR_sexo_rac_cor_etnia = case_when(TP_SEXO == "M" & QE_I02 %in% c("A") ~ "Homem cor/raça/etnia branca",
                                            TP_SEXO == "M" & QE_I02 %in% c("B", "C") ~ "Homem cor/raça/etnia negra",
                                            TP_SEXO == "M" & QE_I02 %in% c("D") ~ "Homem cor/raça/etnia amarela",
                                            TP_SEXO == "M" & QE_I02 %in% c("E") ~ "Homem cor/raça/etnia indígena",
                                            TP_SEXO == "F" & QE_I02 %in% c("A") ~ "Mulher cor/raça/etnia branca",
                                            TP_SEXO == "F" & QE_I02 %in% c("B", "C") ~ "Mulher cor/raça/etnia negra",
                                            TP_SEXO == "F" & QE_I02 %in% c("D") ~ "Mulher cor/raça/etnia amarela",
                                            TP_SEXO == "F" & QE_I02 %in% c("E") ~ "Mulher cor/raça/etnia indígena")) %>% 
  select(TP_SEXO, QE_I02, VAR_sexo_rac_cor_etnia)
```

```r
# A tibble: 422,896 x 3
   TP_SEXO QE_I02 VAR_sexo_rac_cor_etnia      
   <chr>   <chr>  <chr>                       
 1 F       NA     NA                          
 2 F       NA     NA                          
 3 F       A      Mulher cor/raça/etnia branca
 4 F       C      Mulher cor/raça/etnia negra 
 5 F       A      Mulher cor/raça/etnia branca
 6 F       A      Mulher cor/raça/etnia branca
 7 F       NA     NA                          
 8 F       NA     NA                          
 9 F       A      Mulher cor/raça/etnia branca
10 F       C      Mulher cor/raça/etnia negra 
# ... with 422,886 more rows
```

Alguns casos não houve combinações possíveis e o resultado foi `NA`. Nesses casos não havia declaração de cor/raça ou étnica na base, problema muito comum em várias bases com informações sobre educação.