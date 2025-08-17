- Khi nó hỏi có tìm thấy source code không -> tra repo github của nó
- How can I keep Google from indexing my website? -> check file robots.txt, Kiểm tra meta/header “robots” hoặc sitemap.xml
- Apache Server -> check path ".htaccess", ".htpasswd"
- Store on Mac -> check path ".DS_Store"
- Mirror: dùng wget để mirror xong rồi dùng rg để tìm
	- `rg -n -i -I --glob '!.git/**' 'picoCTF\{|flag'`
	- `rg -n -i -I 'picoCTF\{|flag|secret|key|passwd|token|base64|=='`
	- `rg --line-number --ignore-case -I 'picoCTF\{|flag|secret|key|passwd|token|base64|==' <file cụ thể nếu cần>`
	- mirror
```
# Tạo bản sao ngoại tuyến
mkdir -p ~/mirror && cd ~/mirror
wget -e robots=off --mirror -k -E -p -np "url"

# Xem offline (mở trình duyệt vào http://127.0.0.1:8000)
python3 -m http.server 8000
```
- Typically, web applications are hosted on `/var/www/html`
- Hint liên quan đến header của HTTP/1.1 (RFC 2616)
```
GET / HTTP/1.1
Host: mercury.picoctf.net:1270
Accept-Language: en-US,en,sv-SE;q=0.9 // ngôn ngữ
Upgrade-Insecure-Requests: 1
User-Agent: PicoBrowser // trình duyệt
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: mercury.picoctf.net:1270 // ko phải truy cập từ bên ngoài
Accept-Encoding: gzip, deflate, br
Date: Mon, 31 Dec 2018 23:59:59 GMT // thời gian
DNT: 1 // do-not-track
Connection: keep-alive
Content-Length: 0
X-Forwarded-For: 193.180.64.10 // ip ở 1 quốc gia
```
- Sử dụng nmap để quét:
```
	sudo nmap -sV -p- -T4 \
  --script="broadcast-avahi-dos,http-enum,http-cookie-flags,http-dombased-xss,http-sql-injection,http-stored-xss,http-fileupload-exploiter" \
  TARGET_IP
```
- Thêm dấu `/` vào cuối endpoint nếu đó là folder :D
