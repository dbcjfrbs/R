#### [apply실습정리]
```r
library(stringr)
df1<-read.csv('apply_test2.csv');df1
=>
  name X2007.1 X2007.2 X2007.3 X2007.4 X2008.1 X2008.2 X2008.3 X2008.4
1    a  1,695   1,474   1,658   1,027   1,540   1,224   1,953   1,514 
2    b  1,040       -   1,943   1,170       ?   1,092   1,920   1,605 
3    c  1,706   1,380       .   1,044   1,853   1,494   1,337   1,175 
4    d  1,663   1,828   1,695   1,021   1,539   1,236   1,452   1,931 


rownames(df1)<-df1$name
df1<-df1[,-1]                         #또는 df1$name<-NULL

# 0.각 문자열 공백 제거
df1[,]<-apply(df1,c(1,2),str_trim)
  
# 1.NA처리: 전체적용 => apply
f1<-function(x) {
  if (x=='-'|x=='?'|x=='.') {         #또는 %in% 사용
    return(0)
  } else {
    return(x)
  }          
}
df1[,]<-apply(df1,c(1,2),f1)
###또는 밑에처럼 사용가능###
str_replace_all(df1,'[?.-]','0')
df1[,]<-apply(df1,c(1,2),str_replace_all,'[?.-]','0')

# 2.천단위 구분 기호 제거 밒 숫자 변경: 전체적용 => apply
f2<-function(x) {
  as.numeric(str_replace_all(x,',',''))
}
df1[,]<-apply(df1,c(1,2),f2)
df1[,]<-sapply(df1,f2)
class(df1)

# 3.년도와 분기 분리: 벡터적용 => sapply
v_year<- substr((colnames(df1)),2,5)
v_qt<- substr((colnames(df1)),7,7)

str_split(colnames(df1),'\\.')[[1]][1]
# .은 글자의 갯수로서 의미를 가져서 일반문자열 처리를 해야한다  

f3<-function(x,ord) {
  str_remove(str_split(x,'\\.')[[1]][ord],'X')
}  
  
sapply(colnames(df1),f3,1)  
sapply(colnames(df1),f3,2)  

# 문제)각 지점의 1분기 매출의 합
df1[,v_qt=='1']
apply(df1[,v_qt=='1'],1,sum)
```
--------------
--------------

#### *order 
```R
# 1.order(...,             #데이터(벡터들)
#         na.last = ,      #NA 배치 순서
#         decreasing = F)  #내림차순 정렬 여부
# -출력값은 정렬된 벡터가 아닌 위치값
# -위치값을 사용한 색인을 통해서만 순서대로 배치할 수 있다
v1<-c(10,1,3,2,9,5)
order(v1) # [1] 2 4 3 6 5 1
v1[order(v1)] # [1]  1  2  3  5  9 10
```
---------------
---------------
#### *sort
```r
# 2.sort(x,                #데이터(벡터)
#        decreasing = F)   #내림차순 정렬 여부
sort(v1) # [1]  1  2  3  5  9 10  
sort(df1[,1])


#[연습문제] 
# 부서번호 순으로 정렬
# 단, 값은 부서내에서는 연봉이 큰 순서대로 정렬해 출력
emp[order(emp$DEPTNO,emp$SAL,decreasing = c(F,T)),]
=>
 EMPNO  ENAME       JOB  MGR        HIREDATE  SAL COMM DEPTNO
9   7839   KING PRESIDENT   NA 1981-11-17 0:00 5000   NA     10
7   7782  CLARK   MANAGER 7839 1981-06-09 0:00 2450   NA     10
14  7934 MILLER     CLERK 7782 1982-01-23 0:00 1300   NA     10
8   7788  SCOTT   ANALYST 7566 1987-04-17 0:00 3000   NA     20
13  7902   FORD   ANALYST 7566 1981-12-03 0:00 3000   NA     20
4   7566  JONES   MANAGER 7839 1981-04-02 0:00 2975   NA     20
11  7876  ADAMS     CLERK 7788 1987-05-23 0:00 1100   NA     20
1   7369  SMITH     CLERK 7902 1980-12-17 0:00  800   NA     20
6   7698  BLAKE   MANAGER 7839 1981-05-01 0:00 2850   NA     30
2   7499  ALLEN  SALESMAN 7698 1981-02-20 0:00 1600  300     30
10  7844 TURNER  SALESMAN 7698 1981-09-08 0:00 1500    0     30
3   7521   WARD  SALESMAN 7698 1982-02-22 0:00 1250  500     30
5   7654 MARTIN  SALESMAN 7698 1981-09-28 0:00 1250 1400     30
12  7900  JAMES     CLERK 7698 1981-12-03 0:00  950   NA     30
```
-----------------
-----------------
#### *doBy::orderBy
```r
#doBy 패키지
install.packages('doBy')
library('doBy')
#1. doBy::orderBy
orderBy(formula = ,           # Y~X1+X2, ... ,Xn
        data = )              # 데이터(데이터프레임)

orderBy(~ DEPTNO - SAL,data=emp)
=>
 EMPNO  ENAME       JOB  MGR        HIREDATE  SAL COMM DEPTNO
9   7839   KING PRESIDENT   NA 1981-11-17 0:00 5000   NA     10
7   7782  CLARK   MANAGER 7839 1981-06-09 0:00 2450   NA     10
14  7934 MILLER     CLERK 7782 1982-01-23 0:00 1300   NA     10
8   7788  SCOTT   ANALYST 7566 1987-04-17 0:00 3000   NA     20
13  7902   FORD   ANALYST 7566 1981-12-03 0:00 3000   NA     20
4   7566  JONES   MANAGER 7839 1981-04-02 0:00 2975   NA     20
11  7876  ADAMS     CLERK 7788 1987-05-23 0:00 1100   NA     20
1   7369  SMITH     CLERK 7902 1980-12-17 0:00  800   NA     20
6   7698  BLAKE   MANAGER 7839 1981-05-01 0:00 2850   NA     30
2   7499  ALLEN  SALESMAN 7698 1981-02-20 0:00 1600  300     30
10  7844 TURNER  SALESMAN 7698 1981-09-08 0:00 1500    0     30
3   7521   WARD  SALESMAN 7698 1982-02-22 0:00 1250  500     30
5   7654 MARTIN  SALESMAN 7698 1981-09-28 0:00 1250 1400     30
12  7900  JAMES     CLERK 7698 1981-12-03 0:00  950   NA     30
```
----------------
----------------
#### doBy::summaryBy
- 각 컬럼 요약 값 얻기
```r
#   1. summary(iris)
# -숫자컬럼은 최대,최소,각 분위값 등 출력
# -문자컬럼은 각 항목별 count
summary(iris)
=>
Sepal.Length    Sepal.Width     Petal.Length    Petal.Width          Species  
 Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100   setosa    :50  
 1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300   versicolor:50  
 Median :5.800   Median :3.000   Median :4.350   Median :1.300   virginica :50  
 Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199                  
 3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800                  
 Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500                  

#   2. doBy::summaryBy 
# summary(formula=,             #요약컬럼~ 그룹정렬
#         data=)                #데이터(테이터프레임)
# -전체컬럼을특정그룹으로 나눠서 요약
# -summary처럼 많은 정보를 한 번에 할 수는 없음
> summaryBy(Sepal.Length~Species,data = iris)
     Species Sepal.Length.mean
1     setosa             5.006
2 versicolor             5.936
3  virginica             6.588


> summaryBy(Sepal.Length~Species,data = iris,FUN = max)
     Species Sepal.Length.max
1     setosa              5.8
2 versicolor              7.0
3  virginica              7.9


> summaryBy(Sepal.Length+Sepal.Width~Species,data = iris)
     Species Sepal.Length.mean Sepal.Width.mean
1     setosa             5.006            3.428
2 versicolor             5.936            2.770
3  virginica             6.588            2.974
