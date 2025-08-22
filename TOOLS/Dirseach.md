- dirsearch là một công cụ fuzzing directory/file trên web viết bằng Python, rất hay dùng trong CTF (Capture The Flag) hoặc pentest.
- Mục đích

Tự động tìm các thư mục và file ẩn trên website mà admin không công khai.

Giúp bạn khám phá:

thư mục chứa source code (vd: /backup/, /old/)

file config (vd: config.php, .git/)

admin panel ẩn

các điểm có thể chứa flag trong bài CTF.

- `python3 dirsearch.py -u "http://localhost:897/" -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt`
