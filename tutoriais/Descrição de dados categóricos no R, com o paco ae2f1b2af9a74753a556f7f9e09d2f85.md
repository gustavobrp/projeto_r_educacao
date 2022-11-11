# Descrição de dados categóricos no R, com o pacote sjPlot

**[Gustavo Bruno de Paula](https://gustavobrp.github.io/)**

---

Nas ciências humanas lidamos o tempo todo com dados de natureza categórica, como sexo, raça, nível de escolaridade completo, região de origem, dentre outros. Frequentemente, a depender dos propósitos da pesquisa, também transformamos variáveis contínuas para categóricas, por exemplo, quando determinamos faixas de idade ou de salário. 

O R conta com algumas funções de base que permitem leituras de dados categóricos, mas elas são muito rudimentares em comparação com as funções direcionadas para variáveis contínuas (como `plot()`, `hist()` ou `boxplot()`, que apesar de não contar com as mesmas opções de personalização que o `[ggplot2](https://ggplot2.tidyverse.org/)`, podem ser usadas diretamente em trabalhos). 

Esse tutorial apresenta análises básicas com dados categóricos no R utilizando o pacote `[sjPlot](http://www.strengejacke.de/sjPlot/index.html)` que, dentre suas várias funcionalidades, permite visualizar esse tipo de informação em tabelas cruzadas com percentuais (ou tabelas de contingência), formatadas, que podem ser utilizadas diretamente para publicações. Além disso, permite algumas personalizações e exibe estatísticas prontas sobre os dados.

> Para o tutorial, vamos utilizar uma versão tratada, para fins didáticos, dos **[microdados do **Enade de 2010**](https://github.com/gustavobrp/projeto_r_educacao/raw/main/bases/bd_enade_2010.zip)**. Nela, excluí casos com poucas respostas ou informações faltantes, modifiquei algumas variáveis para fatores e criei outras para facilitar a análise. A base de dados original pode ser acessada e baixada pelo site do **[Inep](https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/microdados/enade)**.
> 

> **Observação:** Atualmente o Inep retirou o acesso público aos microdados de várias pesquisas nacionais sobre educação. Mas a base utilizada no exemplo pode ser baixada no link acima.
> 

```r
tibble(bd.enade)
```

```r
# A tibble: 223,727 x 16
   var_setor var_raca_etnia var_sexo var_regiao_curso var_estado_civil var_onde_vive   var_quant_famil~
   <fct>     <fct>          <fct>    <fct>            <fct>            <fct>           <fct>           
 1 Federal   Branca         Feminino Centro-Oeste     Casado(a)        Em casa ou apa~ Duas a quatro p~
 2 Federal   Negra          Feminino Centro-Oeste     Solteiro(a)      Em casa ou apa~ Duas a quatro p~
 3 Federal   Branca         Feminino Centro-Oeste     Casado(a)        Em casa ou apa~ Duas a quatro p~
 4 Federal   Branca         Feminino Centro-Oeste     Outros           Em casa ou apa~ Duas a quatro p~
 5 Federal   Branca         Feminino Centro-Oeste     Solteiro(a)      Em casa ou apa~ Duas a quatro p~
 6 Federal   Negra          Feminino Centro-Oeste     Casado(a)        Em casa ou apa~ Duas a quatro p~
 7 Federal   Branca         Feminino Centro-Oeste     Casado(a)        Em casa ou apa~ Duas a quatro p~
 8 Federal   Branca         Feminino Centro-Oeste     Solteiro(a)      Em casa ou apa~ Duas a quatro p~
 9 Federal   Branca         Feminino Centro-Oeste     Solteiro(a)      Em casa ou apa~ Duas a quatro p~
10 Federal   Branca         Feminino Centro-Oeste     Solteiro(a)      Em casa ou apa~ Vive sozinho    
# ... with 223,717 more rows, and 9 more variables: var_renda <fct>, var_trabalho <fct>,
#   var_sustento <fct>, var_bolsa_mensalidade <fct>, var_modalidade <fct>, var_bolsa_curso <fct>,
#   var_escolaridade_mae <fct>, var_escolaridade_pai <fct>, var_nota_geral <dbl>
```

Antes de começar pelo `sjPlot`, vale repassar brevemente algumas funções existentes na base do R. 

A primeira função é `table()`. Ela exibe apenas as contagens de casos de cada categoria da variável que você deseja analisar. Seguindo a lógica básica do R, escrevemos a função e dentro dela o nome da base (ou objeto carregado) e usamos o sinal do dólar para selecionar a variável desejada. Nesse caso, o sexo informado pelos estudantes.

```r
table(bd.enade.2010$sexo)
```

```r
Masculino  Feminino 
    61842    161885
```

Para exibir essa contagem, mas em proporções, temos que incluir outra função, `prop.table()` como abaixo.

```r
prop.table(table(bd.enade.2010$sexo))
```

```r
Masculino  Feminino 
0.2764172 0.7235828
```

> *Observamos que 72% dos participantes do Enade de 2010 são do sexo feminino. O que está ligado ao fato das características dos cursos que foram avaliados naquele ano (como Farmácia, Enfermagem, Fisioterapia, Nutrição, dentre outros), os quais são predominantemente acessados por mulheres.*
> 

Agora vamos passar para as funcionalidades do `sjPlot`. Abaixo carregamos o pacote, junto com o `tidyverse` (que na realidade é carregado junto com ele, pois o `sjPlot` possui dependências com o `tidyverse`).

```r
library(tidyverse)
library(sjPlot)
```

Para a criação de tabelas cruzadas, a função principal é `sjt.xtab()`. De maneira semelhante à função base, as variáveis são selecionadas utilizando o nome da base (ou objeto) e então extraímos a variável. 

```r
sjt.xtab(bd.enade$var_sexo,
         bd.enade$var_raca_etnia)
```

Diferente das funções bases, o resultado é exibido diretamente no painel *viewer* do R Studio.

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled.png)

Para exibir os percentuais, usamos o argumento *show.row.prc* como `TRUE`. 

```r
sjt.xtab(bd.enade$var_sexo,
         bd.enade$var_raca_etnia,
         show.row.prc = TRUE)
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%201.png)

Podemos utilizar ainda outros argumentos para deixar a tabela mais personalizada segundo o objetivo da análise. Abaixo, modifiquei os nomes das variáveis, rotulando-as para facilitar a leitura (isso pode ser feito também previamente por outras funções, o `sjPlot` lê automaticamente os rótulos). Dessa vez, analisamos o cruzamento entre raça e recebimento de bolsa, exibindo os percentuais por linha e  Também mudei a cor dos percentuais da linha para preto, já que por padrão o pacote os deixa de azul.

```r
sjt.xtab(bd.enade$var_raca_etnia,
         bd.enade$var_bolsa_curso,
         show.row.prc = TRUE,
         show.obs = FALSE,
         tdcol.row = "black", ## cor das linhas de percentuais
         encoding = "UTF-8", ## para corrigir erros de codificação na variável
         var.labels = c("Raça", "Se é bolsista"))
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%202.png)

As tabelas geradas pelo `sjt.xtab()` exibem automaticamente algumas estatísticas e coeficientes. Como o cruzamento tem mais de três categorias, a função exibe o chi-quadrado; o índice de Cramer’s V, que indica o grau de associação entre as variáveis; e o p valor dos dois indicadores. Evidentemente, utilizamos o exemplo apenas para fins de ilustração do pacote, já que estabelecer a associação, e principalmente causalidade, entre variáveis deve estar bem amparado teoricamente e por um desenho de pesquisa adequado. Caso prefira retirar as estatísticas, basta usar o argumento *show.summary* como `FALSE`.

```r
sjt.xtab(bd.enade$var_raca_etnia,
         bd.enade$var_bolsa_curso,
         show.row.prc = TRUE,
         show.obs = FALSE,
         tdcol.row = "black",
         encoding = "UTF-8", ## para corrigir erros de codificação na variável
         var.labels = c("Raça", "Se é bolsista"),
         show.summary = FALSE)
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%203.png)

Podemos também pelo pacote exibir esses dados em gráficos prontos. Para isso o `sjPlot` utiliza o `ggplot2`. A função é parecida: `plot_xtab()`.

```r
plot_xtab(bd.enade$var_renda,
          bd.enade$var_bolsa_curso)
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%204.png)

Podemos ainda fazer alguns ajustes no gráfico, usando ainda outros argumentos para deixá-lo mais simplificado.

```r
plot_xtab(bd.enade$var_renda,
          bd.enade$var_bolsa_curso,
          axis.titles = c("Renda familiar"),
          legend.title = "Se recebe bolsa",
          show.n = FALSE,
          show.total = FALSE)
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%205.png)

É possível também virar o gráfico e deixá-lo em escala de cinza, para publicações que não permitem cores. Nesse caso usamos o argumento `coord.flip`, como `TRUE`, e em `geom.colors` usamos “gs”, de *gray scale*. 

```r
plot_xtab(bd.enade$var_renda,
          bd.enade$var_bolsa_curso,
          axis.titles = c("Renda familiar"),
          legend.title = "Se recebe bolsa",
          show.n = FALSE,
          show.total = FALSE,
          geom.colors = "gs",
          coord.flip = TRUE)
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%206.png)

Os elementos de aparência dos gráficos podem ser modificados com a função `set_theme()`. Por ela se pode selecionar um tema básico do gráfico, com o argumento *base*, tal como é possível fazer com o `ggplot2,` mas também se pode personalizar várias opções - como exposto na documentação da [função](https://strengejacke.github.io/sjPlot/reference/set_theme.html).  Depois de usar a função, selecionando todos os argumentos, basta usar normalmente o `plot_xtab()`. Como mencionado anteriormente, o esquema de cores, por sua vez, pode ser modificado pelo argumento *geom.colors*. Uma sugestão é usar o site [ColorBrewer](https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3) para selecionar o esquema que preferir. 

```r
set_theme(base = theme_bw(),
          axis.title.size = 1.2,
          axis.textsize = 1.2,
          legend.title.size = 1.2,
          geom.label.size = 4)

plot_xtab(bd.enade$var_renda,
          bd.enade$var_bolsa_curso,
          axis.titles = c("Renda familiar"),
          legend.title = "Se recebe bolsa",
          show.n = FALSE,
          show.total = FALSE,
          geom.colors = "GnBu",
          coord.flip = TRUE)
```

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%207.png)

> As informações do gráfico indicam, de maneira esperada, que entre os bolsistas há maior proporção de estudantes das faixas de renda mais baixas.
> 

Para quem usa R Markdown, um limite do pacote é que as tabelas não são incorporadas aos documentos, mas no caso dos gráficos não há problemas. Para as tabelas, deve-se salvá-las antes em html ou Word, ou salvá-las como imagens pela captura de tela. Para quem usa o editor do Word, basta abrir a tabela no navegador clicando em *show in new window* e, depois, selecionar toda a tabela, copiá-la e colá-la no programa. 

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%208.png)

![Untitled](Descric%CC%A7a%CC%83o%20de%20dados%20catego%CC%81ricos%20no%20R,%20com%20o%20paco%20ae2f1b2af9a74753a556f7f9e09d2f85/Untitled%209.png)

A referência completa de funções do `sjPlot` pode ser acessada por [aqui](https://strengejacke.github.io/sjPlot/reference/index.html). Aliás, o criador do pacote, [Daniel Lüdeke](https://github.com/strengejacke), elaborou vários outros ótimos para análise de dados no R, tendo uma contribuição enorme na comunidade. No futuro pretendo introduzir outros pacotes muito úteis.