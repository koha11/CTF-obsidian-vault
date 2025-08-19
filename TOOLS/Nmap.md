### Scan types
##### TCP
##### SYN
##### UDP
##### NULL, FIN & Xmas
##### ICMC
### NSE (Nmap Scripting Engine) Script
### useful command
```
# Quét nhanh top 1000 port + nhận diện dịch vụ
sudo nmap -sS -sV -T4 -n 10.10.10.10

# Quét toàn bộ TCP port, bỏ ping
sudo nmap -sS -p- -T4 -n -Pn 10.10.10.10

# Phát hiện host trong LAN bằng ARP
sudo nmap -sn -PR 192.168.1.0/24

# Quét UDP chọn lọc
sudo nmap -sU --top-ports 200 -n -T3 10.10.10.10

# Liệt kê port mở + lý do
sudo nmap -sS -p1-1000 --reason 10.10.10.10

# Xuất log đầy đủ
sudo nmap -A -p- -oA scan_full 10.10.10.10

```
