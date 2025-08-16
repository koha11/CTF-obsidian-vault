### Files, Folders
#### `/etc/passwd` 
- là **tập tin hệ thống** trên các hệ điều hành Unix/Linux, chứa **thông tin về tất cả người dùng** trên hệ thống.
- Mỗi dòng đại diện cho một tài khoản người dùng, gồm 7 trường, ngăn cách bởi dấu hai chấm (`:`):
	- tên_đăng_nhập:x:UID:GID:thông_tin_bổ_sung:thư_mục_gốc:shell
#### `/home/username/.ssh/`
- Thư mục mặc định chứa thông tin SSH của mỗi người dùng (với `username` là tên người dùng).
- Các file bên trong:
	- `id_rsa`: Private key (bí mật)
	- `id_rsa.pub`:Public key (chia sẻ được)
	- `authorized_keys`: Danh sách public keys được phép SSH vào tài khoản này
	- `known_hosts`: Danh sách các máy chủ đã từng kết nối và fingerprint của chúng
	- `config`: Cấu hình tùy chỉnh cho SSH client (nếu có)
### Commands
###### `nc -lvnp 4444`
- **Netcat (`nc`)** là một công cụ dòng lệnh dùng để gửi/nhận dữ liệu qua TCP hoặc UDP.
- **Listener** là chế độ mà `nc` **mở một cổng và chờ kết nối** từ máy khác.
- Nó thường được dùng trong reverse shell, chat qua terminal, test cổng, hoặc truyền file.
- Các tham số
    -l = listen (nghe)
    -v = verbose (hiện chi tiết)
    -n = không dùng DNS lookup (tránh chậm)
    -p = port (chỉ định cổng
###### Basic commands
- `whoami` : Xem user hiện tại tên gì
- `cd`
- `cat`: coi nội dung file
- `pwd`: coi full path của folder hiện tại
- `find -name FILE_NAME`: tìm path của file
- `grep CONTENT FILE_NAME`: tìm văn bản có chứa CONTENT trong file
###### Operators
- `&`: chạy lệnh nền
- `&&`: gộp 2 lệnh cùng chạy trên một dòng
- `>`: redirector -> nhận output từ câu lệnh trước đó để làm input cho câu lệnh phía sau
- `>>`: giống `>` nhưng chèn input vào chứ không phải ghi đè
