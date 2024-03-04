# Partie 1 : Script carte d'identité

### 🌞 Vous fournirez dans le compte-rendu Markdown, en plus du fichier, un exemple d'exécution avec une sortie

- script

```
#!/bin/bash
#dany
#23/02/2024

echo "Machine name : $(hostnamectl | grep Static | cut -d' ' -f4)"
echo "OS $(cat /etc/redhat-release) and kernel version is $(uname -r)"
echo "IP : $(ip a | grep "2: " -A 3 | grep "inet " | cut -d' ' -f6)"
echo "RAM : $(free | grep "Mem" | tr -s ' ' | cut -d' ' -f 4) memory available on $(free | grep "Mem" | tr -s ' ' | cut -d' ' -f 2) "
echo "Disk : $(df -h / | grep / | cut -d' ' -f 9) space left"
echo "Top 5 processes by RAM usage :"
echo " - $(ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -2 | tail -1)"
echo " - $(ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -3 | tail -1)"
echo " - $(ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -4 | tail -1)"
echo " - $(ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -5 | tail -1)"
echo " - $(ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -6 | tail -1)"
echo "Listening ports :"
while read super_line; do # déclaration de la variable super_line à la volée

  # à chaque itération de la boucle, $super_line contient une ligne donnée :
  port="$(echo $super_line | tr -s ' ' | cut -d':' -f2 | cut -d' ' -f1)"
  protocol="$(echo $super_line | cut -d' ' -f1)"
  program="$(echo $super_line | tr -s ' ' | cut -d'*' -f2 | cut -d' ' -f2 | cut -d'"' -f2)"
  echo " - $port $protocol : $program"

done <<< "$(sudo ss -lntu4p)"
echo "PATH directories :"
echo "$PATH" | tr ':' '\n' | while read -r super_line; do
  echo - "$super_line"
done
image_url=$(curl -s "https://api.thecatapi.com/v1/images/search" | jq -r '.[0].url')
echo "Here is your random cat (jpg file) :" $image_url
```

- execution 
```
[dany@node1 idcard]$ ./idcard.sh 
Machine name : node1.tp2.b1
OS Rocky Linux release 9.2 (Blue Onyx) and kernel version is 5.14.0-284.11.1.el9_2.x86_64
IP : 10.2.1.11/24
RAM : 217928 memory available on 790244 
Disk : 15G space left
Top 5 processes by RAM usage :
 -    1646    1574 /home/dany/.vscode-server/b  6.1  0.1
 -    1719    1646 /home/dany/.vscode-server/b  4.4  0.1
 -    1906    1646 /home/dany/.vscode-server/b  3.7  0.0
 -       1       0 /usr/lib/systemd/systemd --  1.4  0.0
 -   23822       1 /usr/lib/systemd/systemd-ho  1.1  0.0
Listening ports :
 - Port Netid : State
 - 111 udp : rpcbind
 - 323 udp : chronyd
 - 2176 tcp : sshd
 - 111 tcp : rpcbind
 - 36545 tcp : node
PATH directories :
- /home/dany/.vscode-server/bin/019f4d1419fbc8219a181fab7892ebccf7ee29a2/bin/remote-cli
- /home/dany/.local/bin
- /home/dany/bin
- /usr/local/bin
- /usr/bin
- /usr/local/sbin
- /usr/sbin
Here is your random cat (jpg file) : https://cdn2.thecatapi.com/images/34u.jpg
```

# II. Script youtube-dl

## 1. Premier script youtube-dl

### 📁 Le script /srv/yt/yt.sh
```
#!/bin/bash
#dany
#04/03/2024
if test -d /srv/yt/downloads/ && test -d /var/log/yt/; then
    nomVDO=$(youtube-dl --get-title $1)
    sudo mkdir "$nomVDO"
    youtube-dl -o "/srv/yt/downloads/${nomVDO}/%(${nomVDO})s.%(ext)s" $1 >/dev/null
    youtube-dl --write-description -o "/srv/yt/downloads/${nomVDO}/description" $1 >/dev/null
    echo "Video $1 was downloaded."
    cd "/srv/yt/downloads/${nomVDO}"
    echo "File path : $(pwd)"
    echo "[ $(date +'%y/%m/%d %H:%M:%S') ] Video $1 was downloaded. File path : $(realpath "/srv/yt/downloads/$nomVDO")" | sudo tee -a /var/log/yt/download.log >/dev/null

else
    echo exit
    exit
fi
```

### 📁 Le fichier de log
```
[dany@node1 yt]$ cat download.log 
[ 24/03/04 19:38:04 ] Video https://www.youtube.com/watch?v=uGi6EeKnWSk was downloaded. File path : /srv/yt/downloads/Boot Camp Day 44: Try not to Trade
```

### 🌞 Vous fournirez dans le compte-rendu, en plus du fichier, un exemple d'exécution avec une sortie
```
[dany@node1 yt]$ ./yt.sh https://www.youtube.com/watch?v=uGi6EeKnWSk
Video https://www.youtube.com/watch?v=uGi6EeKnWSk was downloaded.
File path : /srv/yt/downloads/Boot Camp Day 44: Try not to Trade
```

