##### * read.csv() 계속
```r
###encoding = "unknown": 파일 인코딩
#R에서는 한글 불러올 때 안깨져서 괜찮은데 파이썬에서는 깨져서 밑에처럼 사용
read.csv('read_test1.txt', encoding = 'cp949')
```
---------------
-----------
##### 2. write.csv()
```r
### 문법
write.csv(x,file='파일이름', row.names='T')


df1<-data.frame(col1=c('a','b'),col2=1:2)
write.csv(df1,'write_test.csv')

=> # 다음과 같이 저장됨
"","col1","col2"  
"1","a",1
"2","b",2


### [참고] : 문자형 컬럼 추가시 factor변환 여부
df1$col3<-c('c','d')            #non-factor로 추가
df1<-cbind(df1,col4=c('e','f')) #factor로 추가
df1<-cbind(df1,col5=c('e','f'),
           stringsAsFactors=F)  #non-factor로 추가
```
--------------
--------------
##### 3. 바이너리 입출력 save
```r
### 문법
save(..., list = , file=)    #list로도 저장할 변수 선언 가능
save(list=ls(),file='save_test')


v1<-1;v2<-2
save(v1,v2,file='save_test') #변수를 파일로 저장, 가변형인자로 전달받음
rm(list='v1','v2')           #변수를 지워봄
load('save_test')            #저장된 변수가 session에 불러와진다
```
----------------
----------------
##### 4. scan 
 - 외부 파일을 벡터로 불러오기
- 인자 생략시 사용자로부터 직접 입력 대기
 - 기본은 숫자, what=''옵션 사용해야 문자 입력 가능
```r
scan()              #사용자에게 숫자값 입력대기
v1<-scan()          #v1에 입력된 값 변수로 저장 
v2<-scan(what = '') #문자로 입력할 때, default 값 변경해야 함

scan(file='scan1_test.txt')
```
---------------
---------------
##### 5. readLines()
- 외부파일을 벡터로 불러오기
 - 라인단위로 불러옴

##### 6. readline()
 - 사용자로부터 하나의 값 입력대기
 - 문자형으로 저장
```r
v1<-readline('파일을 삭제할까요?(Y|N):')

=> 
파일을 삭제할까요?(Y|N):y
> 
> v1
[1] "y"


# [연습문제]
# 사용자에게 삭제할 변수명을 입력받은 뒤 해당 변수 삭제
v1 <- 1
v_ans <- readline('삭제할 변수명을 입력하세요 : ')
v_ans2 <- readline('변수를 삭제할까요? (Y|N) : ')

if (v_ans2 == 'Y') {
  rm(list = v_ans)
  print('변수가 삭제되었습니다')
} else {
  print('변수 삭제가 취소되었습니다')
}


# [연습문제]
# seoul_new_txt 파일을 불러와서 다음의 형태를 갖는 데이터프레임 생성
# id text date cnt
readLines('seoul_new.txt')
=>
[1] "305 무료법률상담에 대한 부탁의 말씀 입니다. 2017-09-27 2 "                   
[2] "304 [교통불편접수] 6715 버스(신월동->상암동) 2017-09-26 2 "                  
[3] "303 경기도 시흥시 아파트 화재~ 2017-09-22 145 "   
...

----------------------------------------------------------

v_data <- readLines('seoul_new.txt')

vid <- c() ; vname <- c() ; vdate <- c() ; vcnt <- c()
for (i in 1:length(v_data)) {
  vtext <- str_split(v_data[i],' ')[[1]]
  vid <- c(vid, vtext[1])
  v_c <- str_c(vtext[2:(length(vtext) - 3)], collapse = ' ')
  vname <- c(vname, v_c)
  vdate <- c(vdate, vtext[length(vtext) - 2])
  vcnt <- c(vcnt, vtext[length(vtext) - 1])
}

data.frame(id=vid, text=vname, date=vdate, cnt <- vcnt)
=>
     id                                                    text       date cnt....vcnt
1   305        무료법률상담에 대한 부탁의 말씀 입니다. 2017-09-27           2
2   304       [교통불편접수] 6715 버스(신월동->상암동) 2017-09-26           2
3   303                     경기도 시흥시 아파트 화재~ 2017-09-22         145
4   302                         마곡지구 하자보수 관련 2017-09-22          57
...
```
----------
----------
#### *데이터베이스 연동(oracle)
```r
###1)준비사항
# target DB 접속 ip와 port정보, DB name꼭 내 pc가 아니더라도 된다 
=> 192.168.0.101, 1521, orcl

#[참고 - 서비스 포트 확인방법]
# cmd->lsnrctl status로 확인

# 접속할 userid,passwd 정보
=> scott/oracle

# R과 DB연결할 connection file
=> (oracle DB 기준 : ojdbc6.jar)
    C:\app\KITCOOP\product\11.2.0\client_1\ojdbc6.jar  # /로 바꾸기

# R에서 DB connection 도와주는 패키지 설치
install.packages("RJDBC")
library(RJDBC)


###2)connect
jdbcDriver <- JDBC(driverClass = "oracle.jdbc.OracleDriver", 
 classPath = "C:/app/KITCOOP/product/11.2.0/client_1/ojdbc6.jar")

con <- dbConnect(jdbcDriver, 
                 "jdbc:oracle:thin:@192.168.0.101:1521:orcl",
                 "scott",   # userid
                 "oracle")  # passwd


###3)쿼리 수행
q1<- 'select * from emp'
dbGetQuery(con,  #connection  이름
           q1)   #수행 쿼리

q2<-'select e1.ename, e2.ename
     from emp e1,emp e2
     where e1.mgr=e2.empno(+)'
dbGetQuery(con,  #connection 이름
           q2)   #수행 쿼리



