-> Sử dụng gobuster để tìm các endpoint có thể truy cập được của 1 trang web

```
gobuster dir -u <http://URL> \
  -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt \
  -s 200,204,301,302,307,401,403 -t 40 -o gobuster.txt
```

```
gobuster dir -k -u <https://URL> \
  -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt \
  -s 200,204,301,302,307,401,403 -t 40 -o gobuster.txt
```