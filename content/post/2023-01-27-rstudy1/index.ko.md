---
title: 응용미시를 위한 R
author: 한재륜
date: '2023-01-27'
slug: rstudy1
categories:
  - R
tags:
  - R
  - 응용미시
  - stata
description: ~
image: ~
math: ~
license: ~
hidden: no
comments: yes
---
<script src="{{< blogdown/postref >}}index.ko_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.ko_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.ko_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.ko_files/lightable/lightable.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index.ko_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.ko_files/lightable/lightable.css" rel="stylesheet" />

## 시작
UoB의 [Tutorial](https://hhsievertsen.shinyapps.io/applied_econ_with_R_dynamic/#section-research-question-data)을 따라가며 R 복습 겸 연습을 하려 이 글을 작성한다.

먼저 이 튜토리얼의 목적은 다음과 같은 질문에 대답하기 위함이다.
> 여름학기를 듣는 것이 시험점수를 올리는데 도움이 될까?

### 가상의 환경
- 5학년과 6학년 사이의 Summer break에 선택적으로 Summer school에 참여할 수 있다.
- Summer school은 학교 커리큘럼에 집중할 수도, 그릿<[위키](https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%A6%BF)>과 같이 학업 성적 상승에 기여하는 능력에 집중할 수도 있다.
- Summer school에 참여하는 것은 무료이지만, 등록에는 부모의 능동적인 참여가 필요하다.

### 데이터
데이터는 위의 링크에서 받을 수 있으며 csv, dta, xlsx 총 3개의 파일로 구성되어있다.

## 데이터 불러오기 및 합치기
### 패키지를 통하여 불러오기
기본적인 `Base`에 있는 패키지를 통하여 csv를 불러올 수 있지만, dta와 xlsx를 불러오려면 외부 페키지를 사용해야한다.

`tidyverse`를 이용하면 필요한 패키지들을 자동으로 불러와준다. 

설치가 되어 있지 않다면 `install.packages('tidyverse')`를 통해 설치하자.

```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.4.0      ✔ purrr   0.3.5 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
## ✔ readr   2.1.3      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
library(knitr) #마크다운 테이블 작성을 위한 패키지.
library(kableExtra)
```

```
## 
## Attaching package: 'kableExtra'
## 
## The following object is masked from 'package:dplyr':
## 
##     group_rows
```

### 파일 불러오기
위의 코드로 `tidyverse`를 불러왔다면 `readr`도 함께 로드가 되었을 것이다.

`readr`를 통하여 csv파일을 불러오자.


```r
school_data_1 <- read_csv('school_data_1.csv')
head(school_data_1)
```

```
## # A tibble: 6 × 8
##   person_id school_id summercamp female parental_schoo…¹ paren…² test_…³ test_…⁴
##       <dbl>     <dbl>      <dbl>  <dbl>            <dbl>   <dbl>   <dbl>   <dbl>
## 1         1         5          0      1               10    12.9   NA       1.64
## 2         2        14          1      0               11    14.7    1.30    2.56
## 3         3         7          1      0               14    16.1    2.76    3.53
## 4         4         8          0      0               12    14.6    1.77    2.38
## 5         5         9          1      0               11    13.8    1.18    1.96
## 6         6        26          1      1               11    14.7    2.44    2.81
## # … with abbreviated variable names ¹​parental_schooling, ²​parental_lincome,
## #   ³​test_year_5, ⁴​test_year_6
```

이제 STATA의 *.dta*파일을 불러오자, *.dta* 파일을 불러오는데 여러 패키지가 있지만 나는 `haven`을 사용하였다.


```r
library(haven)
school_data_2 <- read_stata('school_data_2.dta')
head(school_data_2)
```

```
## # A tibble: 6 × 2
##   person_id letter
##       <dbl>  <dbl>
## 1         1      0
## 2         2      0
## 3         3      0
## 4         4      0
## 5         5      0
## 6         6      0
```

마지막으로 *xlsx*파일을 불러오자.`dplyr`의 `glimpse`를 활용하여 데이터를 둘러볼 수도 있다.


```r
library(readxl)
school_data_3 <- read_excel('school_data_3.xlsx')
glimpse(school_data_3)
```

```
## Rows: 3,491
## Columns: 10
## $ person_id    <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17…
## $ test_year_2  <dbl> 1.090117, 1.246309, 2.726472, 2.693032, 1.660545, 2.22377…
## $ test_year_3  <dbl> 1.914594, 1.154470, 2.269011, 2.413203, 1.828067, 2.27566…
## $ test_year_4  <dbl> 2.065805, 1.582455, 3.247252, 1.479452, 1.361972, 2.38510…
## $ test_year_7  <dbl> 2.377697, 1.747376, 3.017764, 2.637954, 1.904636, 3.37613…
## $ test_year_8  <dbl> 2.032904, 2.444041, 3.361646, 3.021940, 2.109774, 3.24542…
## $ test_year_9  <dbl> 1.493803, 1.663050, 3.387020, 2.761513, 2.285818, 2.96503…
## $ test_year_10 <dbl> 1.880512, 1.833769, 2.968617, 2.088086, 1.845694, 3.30819…
## $ learnings    <dbl> 10.236394, 8.278911, 8.966529, 8.876466, 8.770518, 10.484…
## $ school_id    <dbl> 5, 14, 7, 8, 9, 26, 13, 11, 23, 9, 25, 15, 3, 4, 17, 7, 1…
```

### 데이터 merge
불러온 데이터들을 합쳐보자. 기본적인 `merge`함수를 이용하여 합칠 수 있지만, 데이터가 커지면 커질수록 속도가 느려짐으로 `dplyr`에 있는 함수를 사용하여 합쳐보자. 함수 사용법은 다음 [Cheat sheet](https://posit.co/resources/cheatsheets/)를 참고하면 사용할 때 편하다.


```r
school_data_merged <- left_join(school_data_1,school_data_2,by = 'person_id') 
school_data_merged <- left_join(school_data_3,school_data_merged,by = 
                                  c('person_id','school_id'))
head(school_data_merged) %>% kable('markdown')
```



| person_id| test_year_2| test_year_3| test_year_4| test_year_7| test_year_8| test_year_9| test_year_10| learnings| school_id| summercamp| female| parental_schooling| parental_lincome| test_year_5| test_year_6| letter|
|---------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|------------:|---------:|---------:|----------:|------:|------------------:|----------------:|-----------:|-----------:|------:|
|         1|    1.090117|    1.914594|    2.065805|    2.377697|    2.032904|    1.493803|     1.880512| 10.236394|         5|          0|      1|                 10|         12.93921|          NA|    1.636284|      0|
|         2|    1.246309|    1.154470|    1.582455|    1.747376|    2.444041|    1.663050|     1.833769|  8.278911|        14|          1|      0|                 11|         14.73739|    1.297036|    2.561449|      0|
|         3|    2.726472|    2.269011|    3.247252|    3.017764|    3.361646|    3.387020|     2.968617|  8.966530|         7|          1|      0|                 14|         16.08326|    2.761908|    3.526002|      0|
|         4|    2.693032|    2.413203|    1.479452|    2.637954|    3.021940|    2.761513|     2.088086|  8.876466|         8|          0|      0|                 12|         14.60808|    1.772913|    2.381062|      0|
|         5|    1.660545|    1.828067|    1.361972|    1.904637|    2.109774|    2.285818|     1.845694|  8.770518|         9|          1|      0|                 11|         13.75679|    1.180356|    1.959818|      0|
|         6|    2.223775|    2.275665|    2.385106|    3.376130|    3.245427|    2.965039|     3.308194| 10.484683|        26|          1|      1|                 11|         14.70552|    2.439802|    2.810571|      0|


|stat | person_id| test_year_2| test_year_3| test_year_4| test_year_7| test_year_8| test_year_9| test_year_10| learnings| school_id| summercamp|    female| parental_schooling| parental_lincome| test_year_5| test_year_6|    letter|
|:----|---------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|------------:|---------:|---------:|----------:|---------:|------------------:|----------------:|-----------:|-----------:|---------:|
|avg  |      1746|    2.224587|    2.254009|    2.249515|    2.444453|    2.450572|    2.453095|     2.454057|  10.06357|  15.65569|  0.4588943| 0.5153251|                 NA|         14.56281|          NA|          NA| 0.2469207|



STATA에서 Merge는 다음과 같다.


```stata
clear
insheet using C:\Users\owner\Desktop\avoir_stat_blog\content\post\2023-01-27-rstudy1\school_data_1.csv, clear
save school_data_1.dta, replace

import excel school_data_3.xlsx, sheet(Sheet 1) firstrow clear 
save school_data_3.dta, replace

use C:\Users\owner\Desktop\avoir_stat_blog\content\post\2023-01-27-rstudy1\school_data_2.dta

merge m:m person_id using school_data_1.dta
merge m:m person_id school_id using school_data_3.dta, generate(_merge1)


summarize

```

```
(8 vars, 3,491 obs)

file school_data_1.dta saved

(10 vars, 3,491 obs)

file school_data_3.dta saved

(Written by R)

    Result                      Number of obs
    -----------------------------------------
    Not matched                             0
    Matched                             3,491  (_merge==3)
    -----------------------------------------

    Result                      Number of obs
    -----------------------------------------
    Not matched                             0
    Matched                             3,491  (_merge1==3)
    -----------------------------------------

    Variable |        Obs        Mean    Std. dev.       Min        Max
-------------+---------------------------------------------------------
   person_id |      3,491        1746    1007.909          1       3491
      letter |      3,491    .2469207     .431282          0          1
   school_id |      3,491    15.65569    8.674339          1         30
  summercamp |      3,491    .4588943    .4983788          0          1
      female |      3,491    .5153251    .4998367          0          1
-------------+---------------------------------------------------------
parental_s~g |          0
parental_l~e |      3,491    14.56281    .6935299    12.6676   19.44982
 test_year_5 |          0
 test_year_6 |          0
      _merge |      3,491           3           0          3          3
-------------+---------------------------------------------------------
 test_year_2 |      3,491    2.224587    .6765006  -.5700775   4.737349
 test_year_3 |      3,491    2.254009    .6740298  -.4639758   4.388531
 test_year_4 |      3,491    2.249515     .682811   .0142506   4.349952
 test_year_7 |      3,491    2.444453    .7427602    .031388   4.771106
 test_year_8 |      3,491    2.450573    .7380146  -.1855961   5.045062
-------------+---------------------------------------------------------
 test_year_9 |      3,491    2.453095     .739265  -.2456475   4.656483
test_year_10 |      3,491    2.454057    .7366268  -.3042863   4.929489
   learnings |      3,491    10.06357     1.27901   5.386084    14.6208
     _merge1 |      3,491           3           0          3          3
```

## 데이터 클리닝
### Tidying the data

`tidyverse`에 들어있는 `tidyr`로 데이터 전처리를 진행해보자. Wide형태로 되어 있는 데이터를 Long형태로 바꿀 것이다.

`test_year_i`라는 변수가 2~10까지 존재하는 것을 `year`와 `test_score`열을 만들어서 분리시킬 것이다.


```r
# make data tidy (make long)
school_data_tidy<-school_data_merged%>%
       pivot_longer(
         cols = starts_with("test_year"), #test_year로 시작하는 열들을 long 형태로
         names_to = "year", #test_year_i에서 i에 해당하는 값을 위한 열
         names_prefix = "test_year_", #year열에 해당하는 값
         names_transform = list(year = as.integer), #year의 값을 integer로 변환
         values_to = "test_score" #각 test_year_i에서 값을 test_score에 저장
       )
# ncol to get the number of columns of the new dataset
ncol(school_data_tidy)
```

```
[1] 10
```

위 코드의 대응하는 STATA 코드는 다음과 같다.


```stata
drop _merge 
drop _merge1 
destring test_year_5 test_year_6, replace force
reshape long test_year_,i(person_id) j(year)
rename test_year_ test_score
```

```
test_year_5: contains nonnumeric characters; replaced as double
(6 missing values generated)
test_year_6: contains nonnumeric characters; replaced as double
(5 missing values generated)

(j = 2 3 4 5 6 7 8 9 10)

Data                               Wide   ->   Long
-----------------------------------------------------------------------------
Number of observations            3,491   ->   31,419      
Number of variables                  17   ->   10          
j variable (9 values)                     ->   year
xij variables:
test_year_2 test_year_3 ... test_year_10  ->   test_year_
-----------------------------------------------------------------------------
```

### 샘플 선택
결측치 확인을 위하여 `skimr`패키지를 이용하자.

```r
library(skimr)
skim(school_data_tidy) %>% kable('markdown') #kable은 markdown작성을 위한것으로 작성할 필요 없다.
```



|skim_type |skim_variable      | n_missing| complete_rate| numeric.mean|   numeric.sd| numeric.p0| numeric.p25| numeric.p50| numeric.p75| numeric.p100|numeric.hist |
|:---------|:------------------|---------:|-------------:|------------:|------------:|----------:|-----------:|-----------:|-----------:|------------:|:------------|
|numeric   |person_id          |         0|     1.0000000| 1746.0000000| 1007.7808914|  1.0000000|  873.000000| 1746.000000| 2619.000000|  3491.000000|▇▇▇▇▇        |
|numeric   |learnings          |         0|     1.0000000|   10.0635691|    1.2788475|  5.3860845|    9.180917|   10.041026|   10.959234|    14.620796|▁▃▇▃▁        |
|numeric   |school_id          |         0|     1.0000000|   15.6556860|    8.6732350|  1.0000000|    8.000000|   15.000000|   23.000000|    30.000000|▇▇▇▇▇        |
|numeric   |summercamp         |         0|     1.0000000|    0.4588943|    0.4983154|  0.0000000|    0.000000|    0.000000|    1.000000|     1.000000|▇▁▁▁▇        |
|numeric   |female             |         0|     1.0000000|    0.5153251|    0.4997730|  0.0000000|    0.000000|    1.000000|    1.000000|     1.000000|▇▁▁▁▇        |
|numeric   |parental_schooling |        45|     0.9985677|   11.3247275|    1.1044873| 10.0000000|   11.000000|   11.000000|   12.000000|    23.000000|▇▁▁▁▁        |
|numeric   |parental_lincome   |         0|     1.0000000|   14.5628066|    0.6934416| 12.6675982|   14.114416|   14.520815|   14.947058|    19.449821|▂▇▁▁▁        |
|numeric   |letter             |         0|     1.0000000|    0.2469207|    0.4312270|  0.0000000|    0.000000|    0.000000|    0.000000|     1.000000|▇▁▁▁▂        |
|numeric   |year               |         0|     1.0000000|    6.0000000|    2.5820300|  2.0000000|    4.000000|    6.000000|    8.000000|    10.000000|▇▇▃▇▇        |
|numeric   |test_score         |        11|     0.9996499|    2.3673977|    0.7195514| -0.5700775|    1.882743|    2.327884|    2.839489|     5.045062|▁▂▇▃▁        |

위 테이블에서 확인할 수 있듯이 `parental_schooling`과 `test_score`에 결측치가 있음을 확인할 수 있다. 샘플수에 비하여 결측치의 개수가 적고 이 결측치가 무작위하다는 가정하에 `filter`를 통하여 결측치를 제외한 나머지 값들을 선택하여보자.


```r
school_data_selected <- filter(school_data_tidy,!is.na(parental_schooling),
                               !is.na(test_score))
skim(school_data_selected) %>% kable('html')
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> skim_type </th>
   <th style="text-align:left;"> skim_variable </th>
   <th style="text-align:right;"> n_missing </th>
   <th style="text-align:right;"> complete_rate </th>
   <th style="text-align:right;"> numeric.mean </th>
   <th style="text-align:right;"> numeric.sd </th>
   <th style="text-align:right;"> numeric.p0 </th>
   <th style="text-align:right;"> numeric.p25 </th>
   <th style="text-align:right;"> numeric.p50 </th>
   <th style="text-align:right;"> numeric.p75 </th>
   <th style="text-align:right;"> numeric.p100 </th>
   <th style="text-align:left;"> numeric.hist </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> person_id </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1746.6745209 </td>
   <td style="text-align:right;"> 1007.8909944 </td>
   <td style="text-align:right;"> 1.0000000 </td>
   <td style="text-align:right;"> 873.000000 </td>
   <td style="text-align:right;"> 1746.000000 </td>
   <td style="text-align:right;"> 2620.000000 </td>
   <td style="text-align:right;"> 3491.000000 </td>
   <td style="text-align:left;"> ▇▇▇▇▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> learnings </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 10.0636506 </td>
   <td style="text-align:right;"> 1.2795210 </td>
   <td style="text-align:right;"> 5.3860845 </td>
   <td style="text-align:right;"> 9.180917 </td>
   <td style="text-align:right;"> 10.041026 </td>
   <td style="text-align:right;"> 10.960131 </td>
   <td style="text-align:right;"> 14.620796 </td>
   <td style="text-align:left;"> ▁▃▇▃▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> school_id </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 15.6557090 </td>
   <td style="text-align:right;"> 8.6756899 </td>
   <td style="text-align:right;"> 1.0000000 </td>
   <td style="text-align:right;"> 8.000000 </td>
   <td style="text-align:right;"> 15.000000 </td>
   <td style="text-align:right;"> 23.000000 </td>
   <td style="text-align:right;"> 30.000000 </td>
   <td style="text-align:left;"> ▇▇▇▇▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> summercamp </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.4590122 </td>
   <td style="text-align:right;"> 0.4983251 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 1.000000 </td>
   <td style="text-align:right;"> 1.000000 </td>
   <td style="text-align:left;"> ▇▁▁▁▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.5155119 </td>
   <td style="text-align:right;"> 0.4997673 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 1.000000 </td>
   <td style="text-align:right;"> 1.000000 </td>
   <td style="text-align:right;"> 1.000000 </td>
   <td style="text-align:left;"> ▇▁▁▁▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> parental_schooling </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 11.3248414 </td>
   <td style="text-align:right;"> 1.1046065 </td>
   <td style="text-align:right;"> 10.0000000 </td>
   <td style="text-align:right;"> 11.000000 </td>
   <td style="text-align:right;"> 11.000000 </td>
   <td style="text-align:right;"> 12.000000 </td>
   <td style="text-align:right;"> 23.000000 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> parental_lincome </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 14.5628551 </td>
   <td style="text-align:right;"> 0.6937543 </td>
   <td style="text-align:right;"> 12.6675982 </td>
   <td style="text-align:right;"> 14.114394 </td>
   <td style="text-align:right;"> 14.521433 </td>
   <td style="text-align:right;"> 14.947058 </td>
   <td style="text-align:right;"> 19.449821 </td>
   <td style="text-align:left;"> ▂▇▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> letter </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.2472659 </td>
   <td style="text-align:right;"> 0.4314295 </td>
   <td style="text-align:right;"> 0.0000000 </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 1.000000 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> year </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 6.0001913 </td>
   <td style="text-align:right;"> 2.5824458 </td>
   <td style="text-align:right;"> 2.0000000 </td>
   <td style="text-align:right;"> 4.000000 </td>
   <td style="text-align:right;"> 6.000000 </td>
   <td style="text-align:right;"> 8.000000 </td>
   <td style="text-align:right;"> 10.000000 </td>
   <td style="text-align:left;"> ▇▇▃▇▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> test_score </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2.3672862 </td>
   <td style="text-align:right;"> 0.7198608 </td>
   <td style="text-align:right;"> -0.5700775 </td>
   <td style="text-align:right;"> 1.882542 </td>
   <td style="text-align:right;"> 2.327711 </td>
   <td style="text-align:right;"> 2.840368 </td>
   <td style="text-align:right;"> 5.045062 </td>
   <td style="text-align:left;"> ▁▂▇▃▁ </td>
  </tr>
</tbody>
</table>

### 데이터 수정
변수명을 변경하고 `test_score`를 standardization 해보자. 


```r
analysisdata <- rename(school_data_selected,summerschool = summercamp) #summercamp변수를 summerschool로 변경

analysisdata <- analysisdata %>% group_by(year) %>% 
mutate(test_score = (test_score - mean(test_score))/sd(test_score))

print(paste('Mean of test score:',mean(analysisdata$test_score)))
```

```
[1] "Mean of test score: -1.66465669884195e-17"
```

```r
print(paste('SD of test score:',sd(analysisdata$test_score)))
```

```
[1] "SD of test score: 0.999872448979073"
```

## 통계량
### 빠르게 통계량 테이블 만들기
`datasummary_skim`을 통하여 다음과 같이 간단하게 테이블을 작성할 수 있다. `ouput =`옵션을 살펴보면 `.docx`, `latex`, `html`등 다양한 종류로 결과물을 내보낼 수 있으니 상황에 따라 적합한 옵션을 이용하면 좋을 것이다.


```r
# load modelsummary
library("modelsummary")
# create a summary stat table
analysisdata%>%
  filter(year==2)%>%
  select(female,starts_with("paren"),letter,summerschool,test_score)%>%
  datasummary_skim(output = 'html',histogram=TRUE)
```

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Unique (#) </th>
   <th style="text-align:right;"> Missing (%) </th>
   <th style="text-align:right;"> Mean </th>
   <th style="text-align:right;"> SD </th>
   <th style="text-align:right;"> Min </th>
   <th style="text-align:right;"> Median </th>
   <th style="text-align:right;"> Max </th>
   <th style="text-align:right;">    </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> year </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 2.0 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 2.0 </td>
   <td style="text-align:right;"> 2.0 </td>
   <td style="text-align:right;"> 2.0 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="-31.56" y="3.22" width="55.56" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
  <tr>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.5 </td>
   <td style="text-align:right;"> 0.5 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 1.0 </td>
   <td style="text-align:right;"> 1.0 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="1.78" y="3.73" width="4.44" height="7.94" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="6.22" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="10.67" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="15.11" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="19.56" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="24.00" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="28.44" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="32.89" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="37.33" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="41.78" y="3.22" width="4.44" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
  <tr>
   <td style="text-align:left;"> parental_schooling </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 11.3 </td>
   <td style="text-align:right;"> 1.1 </td>
   <td style="text-align:right;"> 10.0 </td>
   <td style="text-align:right;"> 11.0 </td>
   <td style="text-align:right;"> 23.0 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="1.78" y="3.22" width="3.42" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="5.20" y="9.46" width="3.42" height="2.20" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="8.62" y="10.86" width="3.42" height="0.80" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="12.03" y="11.39" width="3.42" height="0.27" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="15.45" y="11.55" width="3.42" height="0.12" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="18.87" y="11.60" width="3.42" height="0.065" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="22.29" y="11.65" width="3.42" height="0.014" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="25.71" y="11.65" width="3.42" height="0.014" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="29.13" y="11.66" width="3.42" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="32.55" y="11.66" width="3.42" height="0.0068" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="35.97" y="11.66" width="3.42" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="39.38" y="11.66" width="3.42" height="0.0034" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="42.80" y="11.66" width="3.42" height="0.0034" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
  <tr>
   <td style="text-align:left;"> parental_lincome </td>
   <td style="text-align:right;"> 3486 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 14.6 </td>
   <td style="text-align:right;"> 0.7 </td>
   <td style="text-align:right;"> 12.7 </td>
   <td style="text-align:right;"> 14.5 </td>
   <td style="text-align:right;"> 19.4 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="0.68" y="11.52" width="3.28" height="0.14" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="3.96" y="10.44" width="3.28" height="1.22" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="7.23" y="7.25" width="3.28" height="4.41" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="10.51" y="3.29" width="3.28" height="8.37" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="13.79" y="3.22" width="3.28" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="17.06" y="7.61" width="3.28" height="4.06" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="20.34" y="10.13" width="3.28" height="1.54" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="23.62" y="11.14" width="3.28" height="0.53" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="26.89" y="11.40" width="3.28" height="0.26" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="30.17" y="11.60" width="3.28" height="0.058" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="33.44" y="11.62" width="3.28" height="0.042" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="36.72" y="11.66" width="3.28" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="40.00" y="11.65" width="3.28" height="0.017" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="43.27" y="11.65" width="3.28" height="0.0083" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
  <tr>
   <td style="text-align:left;"> letter </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.2 </td>
   <td style="text-align:right;"> 0.4 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 1.0 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="1.78" y="3.22" width="4.44" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="6.22" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="10.67" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="15.11" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="19.56" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="24.00" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="28.44" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="32.89" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="37.33" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="41.78" y="8.89" width="4.44" height="2.77" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
  <tr>
   <td style="text-align:left;"> summerschool </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.5 </td>
   <td style="text-align:right;"> 0.5 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 1.0 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="1.78" y="3.22" width="4.44" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="6.22" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="10.67" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="15.11" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="19.56" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="24.00" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="28.44" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="32.89" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="37.33" y="11.66" width="4.44" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="41.78" y="4.50" width="4.44" height="7.16" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
  <tr>
   <td style="text-align:left;"> test_score </td>
   <td style="text-align:right;"> 3486 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> -0.0 </td>
   <td style="text-align:right;"> 1.0 </td>
   <td style="text-align:right;"> -4.1 </td>
   <td style="text-align:right;"> 0.0 </td>
   <td style="text-align:right;"> 3.7 </td>
   <td style="text-align:right;">  <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="svglite" width="48.00pt" height="12.00pt" viewBox="0 0 48.00 12.00"><defs><style type="text/css">
    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {
      fill: none;
      stroke: #000000;
      stroke-linecap: round;
      stroke-linejoin: round;
      stroke-miterlimit: 10.00;
    }
    .svglite text {
      white-space: pre;
    }
  </style></defs><rect width="100%" height="100%" style="stroke: none; fill: none;"></rect><defs><clipPath id="cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw"><rect x="0.00" y="0.00" width="48.00" height="12.00"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwwLjAwfDEyLjAw)">
</g><defs><clipPath id="cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw"><rect x="0.00" y="2.88" width="48.00" height="9.12"></rect></clipPath></defs><g clip-path="url(#cpMC4wMHw0OC4wMHwyLjg4fDEyLjAw)"><rect x="-0.32" y="11.65" width="2.83" height="0.012" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="2.51" y="11.66" width="2.83" height="0.00" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="5.35" y="11.59" width="2.83" height="0.073" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="8.18" y="11.41" width="2.83" height="0.26" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="11.01" y="11.07" width="2.83" height="0.60" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="13.85" y="9.85" width="2.83" height="1.81" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="16.68" y="7.63" width="2.83" height="4.03" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="19.51" y="5.36" width="2.83" height="6.30" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="22.35" y="3.95" width="2.83" height="7.71" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="25.18" y="3.22" width="2.83" height="8.44" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="28.01" y="4.90" width="2.83" height="6.77" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="30.85" y="7.82" width="2.83" height="3.85" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="33.68" y="10.10" width="2.83" height="1.56" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="36.51" y="10.97" width="2.83" height="0.69" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="39.35" y="11.43" width="2.83" height="0.23" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="42.18" y="11.59" width="2.83" height="0.073" style="stroke-width: 0.38; fill: #000000;"></rect><rect x="45.01" y="11.65" width="2.83" height="0.012" style="stroke-width: 0.38; fill: #000000;"></rect></g></svg>
</td>
  </tr>
</tbody>
</table>


### 커스텀 테이블 만들기

수식을 사용하여 원하는 테이블을 만드려면 `datasummary()`함수를 이용하면 편리하다.

`female, parental_schooling, letter, test_score`를 `summerschool`참여에 따라 분류하여 테이블을 작성하고 싶으면 다음과 같은 코드를 작성하면 된다.

```r
datasummary(female+parental_schooling+letter+test_score ~ Factor(summerschool)*
              (Mean+SD),sparse_header = FALSE,data = analysisdata %>%
              filter(year == 2),output = 'html')
```

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;" colspan="1"></th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">0</div></th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">1</div></th>
</tr>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Mean </th>
   <th style="text-align:right;"> SD </th>
   <th style="text-align:right;"> Mean </th>
   <th style="text-align:right;"> SD </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 0.51 </td>
   <td style="text-align:right;"> 0.50 </td>
   <td style="text-align:right;"> 0.52 </td>
   <td style="text-align:right;"> 0.50 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> parental_schooling </td>
   <td style="text-align:right;"> 11.03 </td>
   <td style="text-align:right;"> 0.74 </td>
   <td style="text-align:right;"> 11.67 </td>
   <td style="text-align:right;"> 1.34 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> letter </td>
   <td style="text-align:right;"> 0.09 </td>
   <td style="text-align:right;"> 0.29 </td>
   <td style="text-align:right;"> 0.43 </td>
   <td style="text-align:right;"> 0.49 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> test_score </td>
   <td style="text-align:right;"> -0.30 </td>
   <td style="text-align:right;"> 0.85 </td>
   <td style="text-align:right;"> 0.35 </td>
   <td style="text-align:right;"> 1.05 </td>
  </tr>
</tbody>
</table>

## 차트 만들기
### Scatter Plot

R에서 그래프를 그리는 것은 보통 `ggplot2`를 이용하여 그린다. 위의 Cheat sheet를 참조하면 그래프의 다양한 부분들을 쉽게 바꿀수 있다.

```r
# create a scatter plot with a fitted line
ggplot(analysisdata%>%filter(year==5),  
       aes(x=parental_lincome,y=test_score))+
       geom_smooth() +
       geom_point()
```

<img src="{{< blogdown/postref >}}index.ko_files/figure-html/unnamed-chunk-14-1.png" width="672" />

### Bar Chart and Box Plot
위의 산점도에서 부모의 소득과 시험성적이 연관되어 있음을 확인할 수 있다. 더 나아가 부모의 학력수준과 어떻게 연관되어 있을지 Bar chart와 Box plot을 그려 확인하여보자.

`patchwork`와 같은 library를 사용하면 여러 Plot을 하나의 파일로 묶어서 내보낼 수 있다. 대체할 수 있는 library들로는 `cowplot`, `gridExtra`등이 있다.


```r
library("patchwork")

rawchart <- analysisdata %>% filter(year == 5) %>% ggplot(x = as.factor(fill)) +
  theme_classic()
p1 <- rawchart + geom_smooth(aes(parental_schooling,test_score)) +
  geom_point(aes(parental_schooling,test_score),alpha=0.1)+
  labs(x='Parental Schooling',y= 'Test Score Year 5')

p2 <- rawchart + geom_bar(aes(as.factor(summerschool),test_score),
                          stat='summary',fun='mean') +
  labs(x='Attended Summer School',y= 'Test Score Year 5')

p3 <- rawchart + geom_boxplot(aes(as.factor(summerschool),parental_lincome)) +
  labs(x='Attended Summer School',y='Log Parental Income')

p1/(p2+p3)
```

<img src="{{< blogdown/postref >}}index.ko_files/figure-html/unnamed-chunk-15-1.png" width="672" />

### Histogram and Densirty Chart


```r
analysisdata %>% filter(year == 6) %>% ggplot(., aes(x = test_score, fill =
                                                       as.factor(summerschool))) +
  geom_histogram(
    aes(y = after_stat(density)),
    bins = 50,
    alpha = 0.5,
    position = 'identity',
    color = 'white'
  ) +
  geom_density(alpha = 0.3, color = 'white') +
  labs(x = 'Test Score Year 6', y = 'Density', fill = 'Attended Summer School')  
```

<img src="{{< blogdown/postref >}}index.ko_files/figure-html/unnamed-chunk-16-1.png" width="672" />

### Ridgeline plot

```r
library(ggridges)
ggplot(analysisdata,
       aes(
         x = test_score,
         y = as.factor(year),
         fill = as.factor(letter)
       )) +
  geom_density_ridges(
    alpha = 0.5,
    scale = 1.5,
    color = "white",
    from = -2.5,
    to = 2.5
  ) + theme_ridges(grid = FALSE) +
  scale_y_discrete(expand = c(0, 0)) +
  scale_x_continuous(expand = c(0, 0)) +
  scale_fill_brewer(palette = "Set1", labels = c("No letter", "Letter")) +
  labs(x = "Test Score",
       y = "Year",
       fill = " ",
       title = "Test score distribution by reminder letter status  & year")
```

<img src="{{< blogdown/postref >}}index.ko_files/figure-html/unnamed-chunk-17-1.png" width="672" />

## Balancing tables
### Pairwise t-test

`test_score`와 `summerschool`이 Correlate되어 있는지 확인하기 위하여 t-test를 진행하자.

Summer School이 있기 전인 5년도의 `test_score`를 이용하여 가설검증을 하여, 여름학기를 들은 학생과 그렇지 않은 학생간의 시험점수 차이가 있었음을 확인하여 보자.


```r
t.test(test_score ~ summerschool,data = analysisdata %>% filter(year == 5))
```

```

	Welch Two Sample t-test

data:  test_score by summerschool
t = -11.91, df = 2508.2, p-value < 2.2e-16
alternative hypothesis: true difference in means between group 0 and group 1 is not equal to 0
95 percent confidence interval:
 -0.4801885 -0.3444171
sample estimates:
mean in group 0 mean in group 1 
     -0.1893276       0.2229753 
```

### 평균값 비교 테이블


```r
library(estimatr)

testdata <- analysisdata %>% filter(year == 5) %>%
  ungroup() %>%
  mutate(Treated = ifelse(letter == 1, 'Letter', 'No Letter')) %>%
  select(female,
         parental_schooling,
         parental_lincome,
         test_score,
         Treated) %>%
  rename(
    'Female' = female,
    'Parental Schooling' = parental_schooling,
    'Log Parental Income' = parental_lincome,
    "Test Score" = test_score
  )

datasummary_balance(
  ~ Treated,
  data = testdata,
  title = 'Balance of pre-treatment variables',
  fmt = '%.3f',
  dinm_statistic = 'p.value'
)
```

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>Table 1: Balance of pre-treatment variables</caption>
 <thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;" colspan="1"></th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">Letter (N=861)</div></th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">No Letter (N=2619)</div></th>
<th style="empty-cells: hide;border-bottom:hidden;" colspan="2"></th>
</tr>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:right;"> Mean </th>
   <th style="text-align:right;"> Std. Dev. </th>
   <th style="text-align:right;"> Mean </th>
   <th style="text-align:right;"> Std. Dev. </th>
   <th style="text-align:right;"> Diff. in Means </th>
   <th style="text-align:right;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 0.520 </td>
   <td style="text-align:right;"> 0.500 </td>
   <td style="text-align:right;"> 0.514 </td>
   <td style="text-align:right;"> 0.500 </td>
   <td style="text-align:right;"> -0.007 </td>
   <td style="text-align:right;"> 0.730 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Parental Schooling </td>
   <td style="text-align:right;"> 11.314 </td>
   <td style="text-align:right;"> 0.971 </td>
   <td style="text-align:right;"> 11.330 </td>
   <td style="text-align:right;"> 1.146 </td>
   <td style="text-align:right;"> 0.016 </td>
   <td style="text-align:right;"> 0.690 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Log Parental Income </td>
   <td style="text-align:right;"> 14.565 </td>
   <td style="text-align:right;"> 0.662 </td>
   <td style="text-align:right;"> 14.563 </td>
   <td style="text-align:right;"> 0.703 </td>
   <td style="text-align:right;"> -0.002 </td>
   <td style="text-align:right;"> 0.946 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Test Score </td>
   <td style="text-align:right;"> -0.019 </td>
   <td style="text-align:right;"> 1.000 </td>
   <td style="text-align:right;"> 0.006 </td>
   <td style="text-align:right;"> 1.000 </td>
   <td style="text-align:right;"> 0.026 </td>
   <td style="text-align:right;"> 0.510 </td>
  </tr>
</tbody>
</table>

## OLS
### `lm()`
