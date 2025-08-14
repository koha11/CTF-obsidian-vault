- Bookmarklet là một bookmark (dấu trang) mà phần URL bắt đầu bằng javascript:. Khi bạn bấm vào nó, trình duyệt sẽ chạy đoạn JavaScript đó ngay trên trang web đang mở. Dùng để tự động hoá các tác vụ nhỏ (ẩn quảng cáo tạm thời, trích xuất dữ liệu trên trang, định dạng lại nội dung, v.v.).

- Ví dụ bookmarklet (sao chép tiêu đề + URL trang hiện tại vào clipboard)
```
javascript:(()=>{const s=`${document.title} - ${location.href}`;try{navigator.clipboard.writeText(s);alert('Đã sao chép!');}catch(e){prompt('Copy:',s);}})()
```
