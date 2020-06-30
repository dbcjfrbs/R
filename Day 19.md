```r
# [연습문제 - 함수에 벡터가 대입되는 경우]
# emp.csv파일을 읽고 
# 1.sal과 comm의 합을 구하는함수 생성 및 적용
# f_salcomm(emp$SAL,emp$COMM) 형태, COMM이 NA인 경우 0으로 치환
f1<- function(x,y) {
  return(x+ifelse(is.na(y),0,y))
};f1(emp$SAL,emp$COMM) # 벡터내부 원소끼리 계산됨

=>
 [1]  800 1900 1750 2975 2650 2850 2450 3000 5000 1500 1100  950 3000 1300
```
------------------
----------
#### *self call(재귀함수)
- 함수 내부에서 본인함수를 또 호출하는 형식
- 함수 호출 반복으로 반복문 없이 반복작업 수행 가능
- stop point 필요
```r
#예제) fsum(100)=1~100의 합 함수를 재귀함수로 생성
fsum<-function(x) {
      if (x==1) { 
        return(1)         # for문의 대체연산으로 이해
      }
  else {
    fsum(x-1)+x
  }
}
fsum(10) # 55
```
-------------
--------------
#### *가변형인자
```r
### 문법
f1<- function(...) {  # ...을 인자로 생각하면 이해쉬움
  v_key<-list(...)    
  for (i in v_key)
    ...
}

### [연습문제]
# fsum3(1,10,100....)=111

fsum3<-function(...) {
  v_key<-list(...)
  vsum<-0
  for (i in v_key) {
    vsum<-vsum+i
  }
  return(vsum)
}
fsum3(1,10,100)  # 111
```
----------
----------

#### *지역변수와 전역변수
- 지역변수 : 특정 함수, 프로그램 내에서 유효한 변수
- 전역변수 : 특정 함수, 프로그램 밖에서도 유효한 변수
```r
### 1) 전역변수 
v1<-1                  #해당 session 내에서는 전역변수이다
f1<-function(x) {      #함수밖에서 정의된 변수를 함수내에서 사용할 수 있다
    return(v1)           
}


### 2) 지역변수 
# 2-1)
f2<-function(x) {
  v1<-5
  return(v1)
};f2() # 5출력, 좁은 의미의 지역변수가 더 우선순위를 가진다

# 2-2)
f_test<-function(x) {
  vsum5<- sum(1:x)
  return(vsum5)
}

f_test(100)
vsum5      # x, 밖에서 꺼내 쓸 수 없고 함수 내에서 작용됨


### 3) 함수 내에서 전역변수 설정
f3<- function(x) {
  vv1<<-1                #전역변수화 '<<-'
  return(vv1)
}

f4<- function(x) {
  return(vv1)
}

f3() #1
f4() #1
```
-----------
-----------
#### *파일 입출력 함수

- 엑셀은 메모리를 크게 잡아먹는 프로그램이라 빅데이터를 저장하기 위한 용도로는 엑셀을 이용하지 않는다
- 데이터는 데이터베이스에 저장하며 DB가 있는 PC에 접근해 파일형태로 데이터를 불러온다 (read.csv)
##### 1. read.csv()
```r
read.csv('read_test1.txt')
=>
<read.csv test file: read_test1.txt>
a:b:c:d
1:2:3:4
5:6:7:8

### 인자설명
# - skip = 0: 스킵할 행의 갯수
read.csv('read_test1.txt',skip = 1)

# - sep = "": 파일의 분리 구분 기호
read.csv('read_test1.txt',skip = 1,sep = ':')

# - header = FALSE: 첫번쨰 행을 컬럼화 할지 여부
read.csv('read_test1.txt',skip = 1,sep = ':',header = T)  #기본값
read.table('read_test1.txt',skip = 1,sep = ':',header = F)#기본값
=>
  V1 V2 V3 V4
1  a  b  c  d
2  1  2  3  4
3  5  6  7  8

# - na.strings = "NA": NA처리할 문자열
# - nrows = -1: 불러올 행의 갯수
# - stringsAsFactors =T: 문자형 컬럼의 factor선언 여부
# - encoding = "unknown": 파일 인코딩




