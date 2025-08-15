- Khi nó hỏi có tìm thấy source code không -> tra repo github của nó
- How can I keep Google from indexing my website? -> check file robots.txt, Kiểm tra meta/header “robots” hoặc sitemap.xml
- Apache Server -> check path ".htaccess", ".htpasswd"
- Store on Mac -> check path ".DS_Store"
- Mirror: dùng wget để mirror xong rồi dùng rg để tìm
	- `rg -n -i -I --glob '!.git/**' 'picoCTF\{|flag'`
	- `rg -n -i -I 'picoCTF\{|flag|secret|key|passwd|token|base64|=='`
	- `rg --line-number --ignore-case -I 'picoCTF\{|flag|secret|key|passwd|token|base64|=='`
	- mirror
```
# Tạo bản sao ngoại tuyến
mkdir -p ~/mirror && cd ~/mirror
wget -e robots=off --mirror -k -E -p -np "url"

# Xem offline (mở trình duyệt vào http://127.0.0.1:8000)
python3 -m http.server 8000
```
- Typically, web applications are hosted on `/var/www/html`
