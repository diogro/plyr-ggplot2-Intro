plyr & ggplot2
========================================================
author: Diogo Melo
date: 2015/04/30
font-family: 'Helvetica'
width: 1366 
height: 768

plyr
========================================================

Plyr é um conjunto de funções que substituem a familia apply.

Queremos aplicar uma mesma função em subconjuntos

- Mais flexivel 
- Fácil de paralelizar
- Com algumas facilidades importantes

***

![](slides-figure/splitApplyCombine.png)
 
Syntaxe geral
========================================================


```r
__ply()
```

Primeira letra indica o tipo da entrada:


```r
a_ply(), d_ply(), l_ply()
```

Segunda letra indica o tipo da saída:


```r
_aply(), _dply(), _lply()
```

 - (a)rray
 - (l)ist
 - (d)ata.frame

l_ply
========================================================

  Listas tem divisão natural, então basta identificar o objeto de entrada e a função
  

```r
library(plyr)
simple_list <- list('zero' = rnorm(5), 
                    'cinco' = rnorm(5, 5), 
                    'dez' = rnorm(5, 10))
print(simple_list)
```

```
$zero
[1]  0.6502850 -0.8812580 -0.1360354 -0.1370653  0.3005485

$cinco
[1] 5.317638 5.011367 5.148355 5.678946 5.461897

$dez
[1] 10.856734  8.923886 10.632235 10.000242  8.713037
```

l_ply
========================================================

  Listas tem divisão natural, então basta identificar o objeto de entrada e a função


```r
llply(simple_list, sum)
```

```
$zero
[1] -0.2035251

$cinco
[1] 26.6182

$dez
[1] 49.12613
```

```r
#identico a:
lapply(simple_list, sum)
```

l_ply
========================================================

  Podemos também mudar o tipo da saida (diferente do lapply())


```r
laply(simple_list, mean)
```

```
[1] -0.04070503  5.32364063  9.82522673
```

```r
ldply(simple_list, quantile)
```

```
    .id        0%        25%        50%        75%      100%
1  zero -0.881258 -0.1370653 -0.1360354  0.3005485  0.650285
2 cinco  5.011367  5.1483553  5.3176382  5.4618966  5.678946
3   dez  8.713037  8.9238857 10.0002421 10.6322352 10.856734
```

l_ply
========================================================

  Bom para conversões


```r
laply(simple_list, identity)
```

```
             1         2          3          4         5
[1,]  0.650285 -0.881258 -0.1360354 -0.1370653 0.3005485
[2,]  5.317638  5.011367  5.1483553  5.6789461 5.4618966
[3,] 10.856734  8.923886 10.6322352 10.0002421 8.7130366
```


```r
ldply(simple_list, identity)
```

```
    .id        V1        V2         V3         V4        V5
1  zero  0.650285 -0.881258 -0.1360354 -0.1370653 0.3005485
2 cinco  5.317638  5.011367  5.1483553  5.6789461 5.4618966
3   dez 10.856734  8.923886 10.6322352 10.0002421 8.7130366
```

a_ply
========================================================

  Arrays não tem divisão natural, então um segundo argumento é necessario para
  indicar o sentido da operação sendo feita
  

```r
str(iris3)
```

```
 num [1:50, 1:4, 1:3] 5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
 - attr(*, "dimnames")=List of 3
  ..$ : NULL
  ..$ : chr [1:4] "Sepal L." "Sepal W." "Petal L." "Petal W."
  ..$ : chr [1:3] "Setosa" "Versicolor" "Virginica"
```

```r
aaply(iris3, 3, colMeans)
```

```
            
X1           Sepal L. Sepal W. Petal L. Petal W.
  Setosa        5.006    3.428    1.462    0.246
  Versicolor    5.936    2.770    4.260    1.326
  Virginica     6.588    2.974    5.552    2.026
```

Levemente diferente do apply.


```r
apply(iris3, 3, colMeans)
```

```
         Setosa Versicolor Virginica
Sepal L.  5.006      5.936     6.588
Sepal W.  3.428      2.770     2.974
Petal L.  1.462      4.260     5.552
Petal W.  0.246      1.326     2.026
```

a_ply
========================================================


```r
aaply(iris3, 3, colMeans)
```

```
            
X1           Sepal L. Sepal W. Petal L. Petal W.
  Setosa        5.006    3.428    1.462    0.246
  Versicolor    5.936    2.770    4.260    1.326
  Virginica     6.588    2.974    5.552    2.026
```

Ou...


```r
aaply(iris3, c(2, 3), mean)
```

```
          X2
X1         Setosa Versicolor Virginica
  Sepal L.  5.006      5.936     6.588
  Sepal W.  3.428      2.770     2.974
  Petal L.  1.462      4.260     5.552
  Petal W.  0.246      1.326     2.026
```

a_ply
========================================================

Novamente podemos mudar a saída

 - data.frame:
 

```r
adply(iris3, 3, colMeans, .id = 'Species')
```

```
     Species Sepal L. Sepal W. Petal L. Petal W.
1     Setosa    5.006    3.428    1.462    0.246
2 Versicolor    5.936    2.770    4.260    1.326
3  Virginica    6.588    2.974    5.552    2.026
```

a_ply
========================================================

Novamente podemos mudar a saída

- list: 


```r
alply(iris3, 3, colMeans, .dims = TRUE)
```

```
$Setosa
Sepal L. Sepal W. Petal L. Petal W. 
   5.006    3.428    1.462    0.246 

$Versicolor
Sepal L. Sepal W. Petal L. Petal W. 
   5.936    2.770    4.260    1.326 

$Virginica
Sepal L. Sepal W. Petal L. Petal W. 
   6.588    2.974    5.552    2.026 

attr(,"split_type")
[1] "array"
attr(,"split_labels")
          X1
1     Setosa
2 Versicolor
3  Virginica
```

a_ply
========================================================

O a_ply() tb é útil para converter arrays:

 - data.frames


```r
iris_df = adply(iris3, 3, .id = 'Species')
head(iris_df)
```

```
  Species Sepal L. Sepal W. Petal L. Petal W.
1  Setosa      5.1      3.5      1.4      0.2
2  Setosa      4.9      3.0      1.4      0.2
3  Setosa      4.7      3.2      1.3      0.2
4  Setosa      4.6      3.1      1.5      0.2
5  Setosa      5.0      3.6      1.4      0.2
6  Setosa      5.4      3.9      1.7      0.4
```

a_ply
========================================================

O a_ply() tb é útil para converter arrays:

 - listas


```r
iris_list = alply(iris3, 3, .dims = TRUE)
names(iris_list)
```

```
[1] "Setosa"     "Versicolor" "Virginica" 
```

```r
class(iris_list[[1]])
```

```
[1] "matrix"
```

```r
str(iris_list[[1]])
```

```
 num [1:50, 1:4] 5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
 - attr(*, "dimnames")=List of 2
  ..$ : NULL
  ..$ : chr [1:4] "Sepal L." "Sepal W." "Petal L." "Petal W."
```

d_ply
========================================================

d_ply é a função mais complexa

 - seu uso geralmente envolve o uso de funções auxiliares; 
 - muita da funcionalidade básica é mais simples no dplyr;
 - ainda é útil para operações complicadas, que operam em mais de uma linha simultaneamente.
 - básicamente um aggregate-on-steroids
 
d_ply
========================================================


```r
library(gapminder)
str(gapminder)
```

```
'data.frame':	1704 obs. of  6 variables:
 $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
 $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
 $ year     : num  1952 1957 1962 1967 1972 ...
 $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
 $ pop      : num  8425333 9240934 10267083 11537966 13079460 ...
 $ gdpPercap: num  779 821 853 836 740 ...
```
 
d_ply
========================================================

O segundo argumento é um coluna do data.frame que será usada para subdividi-lo


```r
dlply(gapminder, 'country')[[1]]
```

```
       country continent year lifeExp      pop gdpPercap
1  Afghanistan      Asia 1952  28.801  8425333  779.4453
2  Afghanistan      Asia 1957  30.332  9240934  820.8530
3  Afghanistan      Asia 1962  31.997 10267083  853.1007
4  Afghanistan      Asia 1967  34.020 11537966  836.1971
5  Afghanistan      Asia 1972  36.088 13079460  739.9811
6  Afghanistan      Asia 1977  38.438 14880372  786.1134
7  Afghanistan      Asia 1982  39.854 12881816  978.0114
8  Afghanistan      Asia 1987  40.822 13867957  852.3959
9  Afghanistan      Asia 1992  41.674 16317921  649.3414
10 Afghanistan      Asia 1997  41.763 22227415  635.3414
11 Afghanistan      Asia 2002  42.129 25268405  726.7341
12 Afghanistan      Asia 2007  43.828 31889923  974.5803
```

d_ply
========================================================

Principal ponto de discórdia: 
  - O data.frame é subdividido de acordo com uma (ou mais) coluna(s) de indicadores, mas os "sub"-data.frames ainda contém essas colunas!


```r
dlply(gapminder, 'country')[[1]]
```

```
       country continent year lifeExp      pop gdpPercap
1  Afghanistan      Asia 1952  28.801  8425333  779.4453
2  Afghanistan      Asia 1957  30.332  9240934  820.8530
3  Afghanistan      Asia 1962  31.997 10267083  853.1007
4  Afghanistan      Asia 1967  34.020 11537966  836.1971
5  Afghanistan      Asia 1972  36.088 13079460  739.9811
6  Afghanistan      Asia 1977  38.438 14880372  786.1134
7  Afghanistan      Asia 1982  39.854 12881816  978.0114
8  Afghanistan      Asia 1987  40.822 13867957  852.3959
9  Afghanistan      Asia 1992  41.674 16317921  649.3414
10 Afghanistan      Asia 1997  41.763 22227415  635.3414
11 Afghanistan      Asia 2002  42.129 25268405  726.7341
12 Afghanistan      Asia 2007  43.828 31889923  974.5803
```

d_ply
========================================================

Isso é um problema quando queremos fazer operações nas colunas numéricas apenas:


```r
head(ddply(gapminder, 'country', mean))
```

```
      country V1
1 Afghanistan NA
2     Albania NA
3     Algeria NA
4      Angola NA
5   Argentina NA
6   Australia NA
```

d_ply
========================================================

Isso é um problema quando queremos fazer operações nas colunas numéricas apenas:


```r
head(ddply(gapminder, 'country', mean))
```

A função numcolwise() resolve esse problema, mas ela é aplicada na função!!


```r
head(ddply(gapminder, .(country), numcolwise(mean)))
```

```
      country   year  lifeExp      pop  gdpPercap
1 Afghanistan 1979.5 37.47883 15823715   802.6746
2     Albania 1979.5 68.43292  2580249  3255.3666
3     Algeria 1979.5 59.03017 19875406  4426.0260
4      Angola 1979.5 37.88350  7309390  3607.1005
5   Argentina 1979.5 69.06042 28602240  8955.5538
6   Australia 1979.5 74.66292 14649312 19980.5956
```


d_ply
========================================================

A função .() permite omitir as aspas e fazer operações nas colunas:


```r
head(ddply(gapminder, .(country, log(year)), numcolwise(mean)), 12)
```

```
       country log(year) lifeExp      pop gdpPercap
1  Afghanistan  7.576610  28.801  8425333  779.4453
2  Afghanistan  7.579168  30.332  9240934  820.8530
3  Afghanistan  7.581720  31.997 10267083  853.1007
4  Afghanistan  7.584265  34.020 11537966  836.1971
5  Afghanistan  7.586804  36.088 13079460  739.9811
6  Afghanistan  7.589336  38.438 14880372  786.1134
7  Afghanistan  7.591862  39.854 12881816  978.0114
8  Afghanistan  7.594381  40.822 13867957  852.3959
9  Afghanistan  7.596894  41.674 16317921  649.3414
10 Afghanistan  7.599401  41.763 22227415  635.3414
11 Afghanistan  7.601902  42.129 25268405  726.7341
12 Afghanistan  7.604396  43.828 31889923  974.5803
```


d_ply
========================================================

Podemos também usar formulas:


```r
head(ddply(gapminder, ~ country + log(year), numcolwise(mean)), 12)
```

```
       country log(year) lifeExp      pop gdpPercap
1  Afghanistan  7.576610  28.801  8425333  779.4453
2  Afghanistan  7.579168  30.332  9240934  820.8530
3  Afghanistan  7.581720  31.997 10267083  853.1007
4  Afghanistan  7.584265  34.020 11537966  836.1971
5  Afghanistan  7.586804  36.088 13079460  739.9811
6  Afghanistan  7.589336  38.438 14880372  786.1134
7  Afghanistan  7.591862  39.854 12881816  978.0114
8  Afghanistan  7.594381  40.822 13867957  852.3959
9  Afghanistan  7.596894  41.674 16317921  649.3414
10 Afghanistan  7.599401  41.763 22227415  635.3414
11 Afghanistan  7.601902  42.129 25268405  726.7341
12 Afghanistan  7.604396  43.828 31889923  974.5803
```

d_ply
========================================================

- Se quisermos operar sobre mais de uma coluna numérica simultaneamente, o numcolwise não serve
- Nesse caso, o melhor é usar uma função que faça a indexação desejada:


```r
l_iris <- dlply(iris, ~ Species, '[', 1:3)
llply(l_iris, cor)[[1]]
```

```
             Sepal.Length Sepal.Width Petal.Length
Sepal.Length    1.0000000   0.7425467    0.2671758
Sepal.Width     0.7425467   1.0000000    0.1777000
Petal.Length    0.2671758   0.1777000    1.0000000
```

d_ply
========================================================

Ou, usando o pipe (%>%) do pacote magrittr


```r
library(magrittr)
dlply(iris, ~ Species, '[', 1:3) %>% llply(., cor)
```

```
$setosa
             Sepal.Length Sepal.Width Petal.Length
Sepal.Length    1.0000000   0.7425467    0.2671758
Sepal.Width     0.7425467   1.0000000    0.1777000
Petal.Length    0.2671758   0.1777000    1.0000000

$versicolor
             Sepal.Length Sepal.Width Petal.Length
Sepal.Length    1.0000000   0.5259107    0.7540490
Sepal.Width     0.5259107   1.0000000    0.5605221
Petal.Length    0.7540490   0.5605221    1.0000000

$virginica
             Sepal.Length Sepal.Width Petal.Length
Sepal.Length    1.0000000   0.4572278    0.8642247
Sepal.Width     0.4572278   1.0000000    0.4010446
Petal.Length    0.8642247   0.4010446    1.0000000
```

d_ply
========================================================


```r
library(evolqg)
dlply(iris, ~ Species, '[', 1:3) %>% llply(cov) %>% ldply(MeanMatrixStatistics, .id = 'Species')
```

```
     Species MeanSquaredCorrelation      pc1%      ICV respondability
1     setosa              0.2181119 0.7901496 1.187993      0.1257807
2 versicolor              0.3864523 0.7959035 1.202102      0.2425761
3  virginica              0.3722595 0.8434123 1.326824      0.3532388
  evolvability conditional.evolvability  autonomy flexibility constraints
1    0.1014528               0.05704135 0.6113320   0.7811733   0.8867327
2    0.1937692               0.10738468 0.6126724   0.7742508   0.8888388
3    0.2711857               0.12109552 0.5107985   0.7234510   0.9181776
```
