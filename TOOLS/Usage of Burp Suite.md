### Intercept
### Repeater
### Intruder
##### Positions
Burp sẽ tự động đánh dấu các tham số có thể thay đổi bằng ký hiệu §.

Bạn có thể thêm/bớt vị trí bằng nút "Add §" hoặc "Clear §".
Đây là nơi mà Intruder sẽ thử payload.
##### Attack type
Sniper: thay thế từng payload một vị trí một lần (test riêng lẻ).

Battering ram: cùng một payload thay cho tất cả vị trí.

Pitchfork: thay đồng thời nhiều vị trí, mỗi vị trí lấy payload tương ứng.

Cluster bomb: thử tất cả kết hợp payload (brute force nhiều chiều).
##### Payloads
Chọn danh sách payload (tự nhập, copy/paste, hoặc load từ wordlist).

Có nhiều loại: simple list, numbers, dates, brute force, fuzz string, regex, hash…

##### ví dụ
``` 
POST /login HTTP/1.1
username=§admin§&password=§PASS§
```

`GET /product?id=§123§`
