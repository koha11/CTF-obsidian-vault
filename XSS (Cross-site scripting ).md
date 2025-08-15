- **XSS (Cross-Site Scripting)** là lỗ hổng cho phép kẻ tấn công **chèn mã JavaScript chạy trên trình duyệt của nạn nhân** khi họ truy cập web. Hệ quả: đánh cắp session, giả mạo hành động, thay đổi nội dung trang, keylogging, v.v.
### Reflected XSS
- payload nằm trong URL/param → phản chiếu ngay vào response.
### Stored XSX
- payload được lưu (bình luận/bài viết/field DB) → mọi ai xem trang đều bị chạy.
### DOM-based XSS
- JS phía client xử lý dữ liệu không an toàn (location.hash, search, postMessage…) rồi chèn vào DOM → code chạy **không cần** server đổi HTML.
### Ref
- https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html
- 