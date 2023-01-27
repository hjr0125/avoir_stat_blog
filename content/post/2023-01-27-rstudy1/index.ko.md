---
title: 응용미시를 위한 R(1)
author: 한재륜
date: '2023-01-27'
slug: rstudy1
categories:
  - R
tags:
  - R
  - 응용미시
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

## 데이터 불러오기
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

이제 STATA의 *dta*파일을 불러오자, *dta* 파일을 불러오는데 여러 패키지가 있지만 나는 `haven`을 사용하였다.


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

마지막으로 *xlsx*파일을 불러오자.`dplyr`의 `glipse`를 활용하여 데이터를 둘러볼 수도 있다.


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

