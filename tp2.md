### Partie 1 : Files and users

## I. Fichiers

## 1. Find me

### 🌞 Trouver le chemin vers le répertoire personnel de votre utilisateur

```
┌──(dany㉿dany)-[~]
└─$ pwd
/home/dany
```

### 🌞 Trouver le chemin du fichier de logs SSH

```
┌──(dany㉿dany)-[/var/log]
└─$ sudo journalctl -xe -u ssh
```

### 🌞 Trouver le chemin du fichier de configuration du serveur SSH
```
┌──(dany㉿dany)-[/etc/ssh]
└─$ ls
moduli  ssh_config  ssh_config.d  ssh_host_ecdsa_key  ssh_host_ecdsa_key.pub  ssh_host_ed25519_key  ssh_host_ed25519_key.pub  ssh_host_rsa_key  ssh_host_rsa_key.pub  sshd_config  sshd_config.d
```


## II. Users

## 1. Nouveau user

### 🌞 Créer un nouvel utilisateur

```
sudo useradd -m marmotte
sudo passwd marmotte    
sudo usermod -d /home/papier_alu/ marmotte
```


## 2. Infos enregistrées par le système

### 🌞 Prouver que cet utilisateur a été créé
```
┌──(dany㉿dany)-[/etc]
└─$ cat passwd | grep marmotte
marmotte:x:1001:1001::/home/papier_alu:/bin/sh
```


### 🌞 Déterminer le hash du password de l'utilisateur marmotte

```
┌──(dany㉿dany)-[/etc]
└─$ sudo cat shadow | grep marmotte
marmotte:!:19744:0:99999:7:::
```


## 3. Hint sur la ligne de commande

## 3. Connexion sur le nouvel utilisateur

### 🌞 Tapez une commande pour vous déconnecter : fermer votre session utilisateur

```
┌──(dany㉿dany)-[~]
└─$ xfce4-session-logout -l
```

### 🌞 Assurez-vous que vous pouvez vous connecter en tant que l'utilisateur marmotte

```
$ ls                      
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
$ ..
/bin/sh: 2: ..: Permission denied
$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
$ sudo ..
[sudo] password for marmotte:
marmotte is not in the sudoers file.
$ pwd
/home/papier_alu
```

# Partie 2 : Programmes et paquets

## I. Programmes et processus

## 1. Run then kill

### 🌞 Lancer un processus sleep
```
┌──(dany㉿dany)-[~]
└─$ ps a | grep sleep  
   3588 pts/0    S+     0:00 sleep 1000
   4912 pts/1    S+     0:00 grep --color=auto sleep
```


### 🌞 Terminez le processus sleep depuis le deuxième terminal
```
┌──(dany㉿dany)-[~]
└─$ kill 3588
```

## 2. Tâche de fond

### 🌞 Lancer un nouveau processus sleep, mais en tâche de fond
```
┌──(dany㉿dany)-[~]
└─$ sleep 1000 &
[1] 8702
```

### 🌞 Visualisez la commande en tâche de fond

```
┌──(dany㉿dany)-[~]
└─$ jobs
[1]  + running    sleep 1000
```
                        
                             
## 3. Find paths

### 🌞 Trouver le chemin où est stocké le programme sleep
```
┌──(dany㉿dany)-[/bin]
└─$ ls -al | grep sleep
-rwxr-xr-x  1 root root         43888 Sep 20  2022 sleep
```
                                                  
                                                           
### 🌞 Tant qu'on est à chercher des chemins : trouver les chemins vers tous les fichiers qui s'appellent .bashrc
```
┌──(dany㉿dany)-[~]
└─$ sudo find / -name *.bashrc
/usr/share/kali-defaults/etc/skel/.bashrc
/usr/share/doc/adduser/examples/adduser.local.conf.examples/skel/dot.bashrc
/usr/share/doc/adduser/examples/adduser.local.conf.examples/bash.bashrc
/usr/share/base-files/dot.bashrc
/root/.bashrc
find: ‘/run/user/1000/doc’: Permission denied
find: ‘/run/user/1000/gvfs’: Permission denied
/home/dany/.bashrc
/home/papier_alu/.bashrc
/etc/skel/.bashrc
/etc/bash.bashrc
```

## 4. La variable PATH

### 🌞 Vérifier que
```
┌──(dany㉿dany)-[~]
└─$ which sleep
/usr/bin/sleep
                                                                                                                ┌──(dany㉿dany)-[~]
└─$ which ssh  
/usr/bin/ssh

┌──(dany㉿dany)-[~]
└─$ which ping
/usr/bin/ping
```

## II. Paquets

### 🌞 Installer le paquet firefox
```
┌──(dany㉿dany)-[~]
└─$ sudo apt install firefox
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package firefox is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  gnome-browser-connector

E: Package 'firefox' has no installation candidate
```

### 🌞 Utiliser une commande pour lancer Firefox

```
┌──(dany㉿dany)-[~]
└─$ firefox
```



### 🌞 Installer le paquet nginx
```
┌──(dany㉿dany)-[~]
└─$ sudo apt install nginx  
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  nginx-common
Suggested packages:
  fcgiwrap nginx-doc
The following packages will be upgraded:
  nginx nginx-common
2 upgraded, 0 newly installed, 0 to remove and 844 not upgraded.
Need to get 643 kB of archives.
After this operation, 3072 B of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://mirror.init7.net/kali kali-rolling/main amd64 nginx amd64 1.24.0-2 [532 kB]
Get:2 http://kali.download/kali kali-rolling/main amd64 nginx-common all 1.24.0-2 [111 kB]
Fetched 643 kB in 22s (29.5 kB/s)                
Preconfiguring packages ...
(Reading database ... 398459 files and directories currently installed.)
Preparing to unpack .../nginx_1.24.0-2_amd64.deb ...
Unpacking nginx (1.24.0-2) over (1.24.0-1) ...
Preparing to unpack .../nginx-common_1.24.0-2_all.deb ...
Unpacking nginx-common (1.24.0-2) over (1.24.0-1) ...
Setting up nginx (1.24.0-2) ...
Setting up nginx-common (1.24.0-2) ...
Installing new version of config file /etc/nginx/mime.types ...
nginx.service is a disabled or a static unit not running, not starting it.
Processing triggers for man-db (2.11.2-3) ...
Processing triggers for kali-menu (2023.4.3) ...
```

### 🌞 Déterminer
```
┌──(dany㉿dany)-[/var/log/nginx]
└─$ sudo find /var/log -name nginx
/var/log/nginx


┌──(dany㉿dany)-[/var/log/nginx]
└─$ sudo find /etc -name nginx    
/etc/default/nginx
/etc/init.d/nginx
/etc/ufw/applications.d/nginx
/etc/logrotate.d/nginx
/etc/nginx
```
    
               
### 🌞 Mais aussi déterminer...
```
┌──(dany㉿dany)-[/var/log/nginx]
└─$ cat /etc/apt/sources.list
# See https://www.kali.org/docs/general-use/kali-linux-sources-list-repositories/
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware

# Additional line for source packages
# deb-src http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
```
### 🌞 Récupérer le fichier meow

```
┌──(dany㉿dany)-[~/Downloads]
└─$ wget "https://gitlab.com/it4lik/b1-linux-2023/-/blob/master/tp/2/meow"       
--2024-01-29 03:15:02--  https://gitlab.com/it4lik/b1-linux-2023/-/blob/master/tp/2/meow
Resolving gitlab.com (gitlab.com)... 172.65.251.78, 2606:4700:90:0:f22e:fbec:5bed:a9b9
Connecting to gitlab.com (gitlab.com)|172.65.251.78|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 38536 (38K) [text/html]
Saving to: ‘meow’

meow                                                       100%[=======================================================================================================================================>]  37.63K  --.-KB/s    in 0.001s  

2024-01-29 03:15:09 (37.5 MB/s) - ‘meow’ saved [38536/38536]

```

### 🌞 Trouver le dossier dawa/

 - utilisez la commande file /path/vers/le/fichier pour déterminer le type du fichier:

```
┌──(dany㉿dany)-[~/Downloads]
└─$ file meow                                                             
meow: HTML document, Unicode text, UTF-8 text, with very long lines (15796)                                                    
```

- renommez-le fichier correctement (si c'est une archive compressée ZIP, il faut ajouter .zip à son nom) :

```                                                   
┌──(dany㉿dany)-[~/Downloads]
└─$ mv meow meow.zip  
```



