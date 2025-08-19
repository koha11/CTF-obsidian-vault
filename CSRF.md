- **CSRF (Cross-Site Request Forgery)** là một lỗ hổng bảo mật trên web, cho phép kẻ tấn công **lừa người dùng đã đăng nhập** gửi một yêu cầu độc hại đến ứng dụng web mà họ đang có phiên đăng nhập hợp lệ
### Traditional CSRF
- `src or href`: click vào `http://mybank.thm:8080/dashboard.php?to_account=GB82MYBANK5698&amount=1000`
- Người ta sử dụng csrf_token để tránh `<input type="hidden" id="csrf_token" name="csrf_token" value="<?php echo $_COOKIE['csrf-token']; ?>">`
### XMLHttpRequest CSRF
### Flash-based CSRF
