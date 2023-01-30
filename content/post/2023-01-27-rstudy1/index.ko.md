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
school_data_merged <- left_join(school_data_3,school_data_merged,by = c('person_id','school_id'))
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
school_data_selected <- filter(school_data_tidy,!is.na(parental_schooling),!is.na(test_score))
skim(school_data_selected) %>% kable('markdown')
```



|skim_type |skim_variable      | n_missing| complete_rate| numeric.mean|   numeric.sd| numeric.p0| numeric.p25| numeric.p50| numeric.p75| numeric.p100|numeric.hist |
|:---------|:------------------|---------:|-------------:|------------:|------------:|----------:|-----------:|-----------:|-----------:|------------:|:------------|
|numeric   |person_id          |         0|             1| 1746.6745209| 1007.8909944|  1.0000000|  873.000000| 1746.000000| 2620.000000|  3491.000000|▇▇▇▇▇        |
|numeric   |learnings          |         0|             1|   10.0636506|    1.2795210|  5.3860845|    9.180917|   10.041026|   10.960131|    14.620796|▁▃▇▃▁        |
|numeric   |school_id          |         0|             1|   15.6557090|    8.6756899|  1.0000000|    8.000000|   15.000000|   23.000000|    30.000000|▇▇▇▇▇        |
|numeric   |summercamp         |         0|             1|    0.4590122|    0.4983251|  0.0000000|    0.000000|    0.000000|    1.000000|     1.000000|▇▁▁▁▇        |
|numeric   |female             |         0|             1|    0.5155119|    0.4997673|  0.0000000|    0.000000|    1.000000|    1.000000|     1.000000|▇▁▁▁▇        |
|numeric   |parental_schooling |         0|             1|   11.3248414|    1.1046065| 10.0000000|   11.000000|   11.000000|   12.000000|    23.000000|▇▁▁▁▁        |
|numeric   |parental_lincome   |         0|             1|   14.5628551|    0.6937543| 12.6675982|   14.114394|   14.521433|   14.947058|    19.449821|▂▇▁▁▁        |
|numeric   |letter             |         0|             1|    0.2472659|    0.4314295|  0.0000000|    0.000000|    0.000000|    0.000000|     1.000000|▇▁▁▁▂        |
|numeric   |year               |         0|             1|    6.0001913|    2.5824458|  2.0000000|    4.000000|    6.000000|    8.000000|    10.000000|▇▇▃▇▇        |
|numeric   |test_score         |         0|             1|    2.3672862|    0.7198608| -0.5700775|    1.882542|    2.327711|    2.840368|     5.045062|▁▂▇▃▁        |

### 데이터 수정
변수명을 변경하고 `test_score`를 standardization 해보자. 


```r
analysisdata <- rename(school_data_selected,summerschool = summercamp) #summerschool변수를 summercamp로 변경

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


```r
# load modelsummary
library("modelsummary")
# create a summary stat table
analysisdata%>%
  filter(year==2)%>%
  select(female,starts_with("paren"),letter,summerschool,test_score)%>%
  datasummary_skim(output = 'markdown',histogram=FALSE)
```

```
Adding missing grouping variables: `year`
```



|                   | Unique (#)| Missing (%)| Mean|  SD|  Min| Median|  Max|
|:------------------|----------:|-----------:|----:|---:|----:|------:|----:|
|year               |          1|           0|  2.0| 0.0|  2.0|    2.0|  2.0|
|female             |          2|           0|  0.5| 0.5|  0.0|    1.0|  1.0|
|parental_schooling |         12|           0| 11.3| 1.1| 10.0|   11.0| 23.0|
|parental_lincome   |       3486|           0| 14.6| 0.7| 12.7|   14.5| 19.4|
|letter             |          2|           0|  0.2| 0.4|  0.0|    0.0|  1.0|
|summerschool       |          2|           0|  0.5| 0.5|  0.0|    0.0|  1.0|
|test_score         |       3486|           0| -0.0| 1.0| -4.1|    0.0|  3.7|


test
