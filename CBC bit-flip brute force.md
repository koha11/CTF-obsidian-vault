
```python
import base64, requests

def bit_flip(pos, mask, data_b64):

	raw = bytearray(base64.b64decode(data_b64)) # mutable bytes
	
	raw[pos] ^= mask # XOR in-place
	
	return base64.b64encode(bytes(raw)).decode() # trả về str Base64

ck = "R3dRcU5XUUdmL3V6MlBHMWNiVWY5cGR5T09vZmJ0R3RKVm5aWi9ncDdnc1VQUklqQWdGSjhGRC9DVXpSSXV1bkR5ci83OWJ4NXdnUEdBaGJUNzVDNjFGaWZ1cVZZYUY2UW1uYmJBdDFwRVBRVnAveXhoNTNXN0k4T0o2UzI2T2M="

url = "http://mercury.picoctf.net:10868"

found = None

raw_len = len(base64.b64decode(ck)) # 128

for i in range(128): # thử mọi byte
	for mask in range(128): # thử mọi mặt nạ khác 0
		c = bit_flip(i, mask, ck)
		r = requests.get(url, cookies={"auth_name": c}, timeout=10)
		print(i,mask,sep=", ")
		print(c)

		if "picoCTF" in r.text:
			found = r.text
			break

	if found:
		break

if found:
	print(found)	
```
- Cookie `auth_name` là **Base64 của ciphertext** (AES-CBC thường gặp trong picoCTF).
- Trong chế độ **CBC**, **lật 1 byte** ở **ciphertext khối trước** sẽ làm **đổi 1 byte** ở **plaintext khối sau** sau khi giải mã.
- Script thử **mọi vị trí byte** và **mọi mặt nạ XOR** → gửi cookie đã bị “lật bit” → nếu response chứa `"picoCTF{"` thì in ra.