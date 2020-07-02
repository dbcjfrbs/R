
##### [참고 - path설정] 나중에 다시 정리하기!!
```r
# 윈도우 뿐만 아니라 리눅스에서는 필수, 윈도우에서는 자동으로 파일위치가 설정됨 

# abc라는 프로그램을 깔면 원래는 abc.exe라는 실행파일을 찾아 클릭해야함

# sqlplus 라는 게 sqlplus.exe를 실행시키라는 거였음

# cmd -> sqlpuls -> sqlplus.exe, 즉 path설정을 하면 찾아서 실행하는거다

# server용 sqlplus.exe과 client용 sqlplus.exe 두 개가 있으면 path에 가서 
# 순서대로 뒤지기 때문에 상위에 있는 server용이 실행되는거다 23:00,24:00
```
---------------
---------------
### *적용함수 : 반복연산을 도와주는 함수
#### 1) apply
```r
### 문법
#   apply(x,               #apply계열은 출력결과 형식의 차이가 있을뿐
#         MARGIN = ,
#         FUN,
#         ...)
```
- x에는 1차원(벡터) 데이터가 올 수 없음(2차원 이상 적용가능)
- 주로 행별, 열별 반복연산을 연산하기 위한 함수
- R에서는 2차원 데이터의 '원소별' 적용도 가능, 파이썬은 원소별 불가
- 출력결과는 벡터, 리스트, 행렬, 배열
- 데이터프레임 출력 불가!!
```r
# 연습문제)
# card_history.csv 파일을 읽고 fsum(1)하면 1일 총 지출 금액을 출력하도록 하여라.
# 단, NUM 컬럼은 '일'을 나타냄
card <- read.csv('card_history.csv', stringsAsFactors = F)
=>
NUM 식료품    의복 외식비    책값 온라인소액결제 의료비
1    1 19,400 143,000  8,600  29,000          5,600 19,200
2    2 22,200 120,400  7,000  26,000          3,300 13,000
...
-----------------------------------------------

### 천단위 구분기호 제거 후 숫자 변경
1) str_replace사용
library(stringr)
str_replace_all(card,',','')   # 데이터프레임 형식 유지 불가함

card$식료품 <- as.numeric(str_replace_all(card$식료품,',',''))
card$의복 <- as.numeric(str_replace_all(card$의복,',',''))
...
card$의료비 <- as.numeric(str_replace_all(card$의료비,',',''))

=> 반복되는 작업 필요...


2) 사용자 정의 함수 생성 후 전체 적용(apply)
f_re <- function(x) {
  as.numeric(str_replace_all(x,',',''))
}

card <- as.data.frame(apply(card, c(1,2), f_re)) 
card$total <- apply(card[,-1], 1, sum)
```
----------------
----------------
#### 2) lapply
 - 원소별 적용가능
 - 출력결과 주로 리스트!!
```r
df1<-data.frame(a=c('1,000','2,000'), 
                b=c('3,000','4,000'),
                stringsAsFactors = F)
rownames(df1)<-c('A','B')

lapply(df1,str_remove_all,',')
=>
$a
[1] "1000" "2000"

$b
[1] "3000" "4000"
```
----------------
----------------
#### 3) sapply
 - 벡터의 원소별 함수 적용
- 출력결과 주로 벡터
 - 함수의 추가적 인자 전달 가능
```r
sapply(df1,str_remove_all,',') 
=> # 데이터프레임의 원소별 적용도 가능 다만 출력결과는 매트릭스
     a      b     
[1,] "1000" "3000"
[2,] "2000" "4000"
```
-------------------
-------------------
#### 4) tapply
- oracle group by 기능과 비슷
 - 그룹컬럼을,그룹을 표현하는 벡터를 인덱스에 전달
 - 출력결과 주로 벡터

```r
### 문법
tapply(vector,       # 연산대상 
        index,       # group by 컬럼
       function)     # 적용함수


tapply(iris$Sepal.Length,iris$Species,mean) #분리,적용,결합 프로세스
=>
setosa versicolor  virginica 
5.006      5.936      6.588


tapply(iris[,-1]$Sepal.Width,iris$Species=='setosa',mean) 
# group by 에 조건적용 가능
=>
FALSE  TRUE 
2.872 3.428 
 ```
------------------
------------------
#### 5) mapply
- 인자 위치가 다른 apply함수와 다르다  
```r
mapply(str_remove_all,df1,',')    
```
##### [적용함수 비교]

1차원 데이터 적용시 
- apply - 불가,  sapply lapply mapply - 가능

--------------------
--------------------
#### *연습문제
- apply.csv파일을 읽고 부서별 판매량의 총 합을 구하세요. 단, 각 꼴이 -인 경우는 0으로 치환 후 계산 (치환함수의 적용으로 풀이)
```r
df1<-read.csv('apply_test.csv');df1
=>
  deptno.name X2010 X2011 X2012 X2013
1    10-smith     -     -     -     -
2    20-allen  <NA>  3600  4000  5000
3     30-ford  2800  3000     -  4100
4     1-scott  3100  3700  4100  4700
5      1-word     -  2100  4300  5000

----------------------------------------
***결측치 숫자로 변경)
ifelse(is.na(df1),0,df1) # x, ifelse는 벡터일 때 가능
str_replace_all(df1,'-','0') # 마찬가지로 2차원 데이터 셋에 적용적절x


f1<-function(x) {
  if (is.na(x) | x=='-') {
    return(0)
  } else {
    return(x)
  }
}
df1[,]<-apply(df1,c(1,2),f1) #matrix형식이긴 하나 데이터셋은 유지된다
df1[,-1]<-sapply(df1[,-1],as.numeric) #as.numeric은 2차원은 안되고 벡터전달됨
df1$total<-apply(df1[,-1],1,sum)


***첫번째 컬럼 dept만 뽑고 tapply적용
f2<-function(x) {
  str_split(x,'-')[[1]][1]
}
df1$deptno<-sapply(df1$deptno.name,f2)
tapply(df1$total,df1$deptno,sum)
```
##### [정리 : sapply가 2차원 데이터셋에 적용 가능한 경우]
```r
sapply(data.frame,function)
- 1.data.frame의 첫번째 컬럼을 벡터형식으로 function에 전달
- 2.function이 벡터를 전달받아 벡터를 리턴하면 수행가능
-              벡터를 전달받아 벡터를 리턴하지 못하면 수행불가능

ex)
f3<-function(x) {
  if (x<1.1) {
    return(0)
  } else {
    return(2)
  }
}
sapply(iris[,-5],sum) #o  
sapply(iris[,-5],f3) #x