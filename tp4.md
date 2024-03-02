# TP4 : Real services
## Partie 1 : Partitionnement du serveur de stockage

### 🌞 Partitionner le disque à l'aide de LVM

```
[dany@storage ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   19G  0 part
  ├─rl-root 253:0    0   17G  0 lvm  /
  └─rl-swap 253:1    0    2G  0 lvm  [SWAP]
sdb           8:16   0    2G  0 disk
sdc           8:32   0    2G  0 disk
sr0          11:0    1 1024M  0 rom
```

- créer un physical volume (PV) :

```
[dany@storage ~]$ sudo pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VB6cd12894-269c762c PVID kRMp4Uapn4k5UUqPeHFgdDoszHL86p8Q last seen on /dev/sda2 not found.
  PV         VG Fmt  Attr PSize PFree
  /dev/sdb      lvm2 ---  2.00g 2.00g
  /dev/sdc      lvm2 ---  2.00g 2.00g
```
- créer un nouveau volume group (VG)
```
[dany@storage ~]$ sudo vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VB6cd12894-269c762c PVID kRMp4Uapn4k5UUqPeHFgdDoszHL86p8Q last seen on /dev/sda2 not found.
  VG      #PV #LV #SN Attr   VSize VFree
  storage   2   0   0 wz--n- 3.99g 3.99g
```

- créer un nouveau logical volume (LV) : ce sera la partition utilisable

```
[dany@storage ~]$ sudo lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VB6cd12894-269c762c PVID kRMp4Uapn4k5UUqPeHFgdDoszHL86p8Q last seen on /dev/sda2 not found.
  LV      VG      Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  storage storage -wi-a----- 3.99g
```

### 🌞 Formater la partition

```
[dany@storage ~]$ sudo mkfs -t ext4 /dev/storage/storage
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 1046528 4k blocks and 261632 inodes
Filesystem UUID: 033b80cc-c6b4-41da-8949-63d54b61342a
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

### 🌞 Monter la partition

```
[dany@storage storage]$ df -h | grep storage
/dev/mapper/storage-storage  3.9G  401M  3.3G  11% /storage
```
- prouvez que vous pouvez lire et écrire des données sur cette partition
```
[dany@storage /]$ ls -al | grep storage
drwxr-xr-x.   3 root root 4096 Feb 19 12:14 storage
```
- définir un montage automatique de la partition (fichier /etc/fstab)
```
/dev/storage/storage /storage ext4 defaults 0 0
```

# Partie 2 : Serveur de partage de fichiers

### 🌞 Donnez les commandes réalisées sur le serveur NFS storage.tp4.linux

```
[dany@storage storage]$ sudo yum install nfs-utils
[dany@storage storage]$ sudo mkdir site_web_1
[dany@storage storage]$ sudo mkdir site_web_2
[dany@storage storage]$ sudo nano /etc/exports
(/storage/site_web_1 10.2.1.12(rw,sync,no_root_squash)
/storage/site_web_2 10.2.1.12.1 rw,sync,no_root_squash)
)
```

### 🌞 Donnez les commandes réalisées sur le client NFS web.tp4.linux

```
[dany@web ~]$ sudo yum install nfs-utils
[dany@web ~]$  sudo mkdir -p /var/www/site_web_1
[dany@web ~]$ sudo mkdir -p /var/www/site_web_2
[dany@web ~]$  sudo nano /etc/fstab
(10.2.1.11:/storage/site_web_1 /var/www/site_web_1 nfs defaults 0 0
10.2.1.11:/storage/site_web_2 /var/www/site_web_2 nfs defaults 0 0
)
[dany@web ~]$ systemctl daemon-reload
Failed to reload daemon: Access denied
[dany@web ~]$ sudo !!
sudo systemctl daemon-reload
[dany@web ~]$ ls -ld /var/www/site_web_1 /var/www/site_web_2
drwxr-xr-x. 2 root root 6 Feb 20 10:53 /var/www/site_web_1
drwxr-xr-x. 2 root root 6 Feb 20 10:53 /var/www/site_web_2
```

# Partie 3 : Serveur web

## 2. Install

### 🌞 Installez NGINX

```
[dany@web ~]$ sudo dnf install nginx
```

## 3. Analyse

### 🌞 Analysez le service NGINX

- avec une commande ps, déterminer sous quel utilisateur tourne le processus du service NGINX :

```
[dany@web ~]$ ps aux | grep nginx
root       12010  0.0  0.1  10108   956 ?        Ss   11:12   0:00 nginx: master process /usr/sbin/nginx
nginx      12011  0.0  0.6  13908  4812 ?        S    11:12   0:00 nginx: worker process
dany       12026  0.0  0.2   6408  2180 pts/0    S+   11:14   0:00 grep --color=auto nginx
```

- avec une commande ss, déterminer derrière quel port écoute actuellement le serveur web :

```
[dany@web ~]$ ss -alpnt
State          Recv-Q         Send-Q                  Local Address:Port                   Peer Address:Port         Process
LISTEN         0              511                           0.0.0.0:80                          0.0.0.0:*
LISTEN         0              4096                          0.0.0.0:111                         0.0.0.0:*
LISTEN         0              128                           0.0.0.0:22                          0.0.0.0:*
LISTEN         0              511                              [::]:80                             [::]:*
LISTEN         0              4096                             [::]:111                            [::]:*
LISTEN         0              128                              [::]:22                             [::]:*
```

- en regardant la conf, déterminer dans quel dossier se trouve la racine web :

```
[dany@web ~]$ grep -R "root" /etc/nginx/nginx.conf
        root         /usr/share/nginx/html;
#        root         /usr/share/nginx/html;
```

- inspectez les fichiers de la racine web, et vérifier qu'ils sont bien accessibles en lecture par l'utilisateur qui lance le processus

```
[dany@web ~]$  ls -l /usr/share/nginx/html
total 12
-rw-r--r--. 1 root root 3332 Oct 16 19:58 404.html
-rw-r--r--. 1 root root 3404 Oct 16 19:58 50x.html
drwxr-xr-x. 2 root root   27 Feb 20 11:10 icons
lrwxrwxrwx. 1 root root   25 Oct 16 20:00 index.html -> ../../testpage/index.html
-rw-r--r--. 1 root root  368 Oct 16 19:58 nginx-logo.png
lrwxrwxrwx. 1 root root   14 Oct 16 20:00 poweredby.png -> nginx-logo.png
lrwxrwxrwx. 1 root root   37 Oct 16 20:00 system_noindex_logo.png -> ../../pixmaps/system-noindex-logo.png
```
## 4. Visite du service web

### 🌞 Configurez le firewall pour autoriser le trafic vers le service NGINX

```
[dany@web ~]$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
[sudo] password for dany:
Warning: ALREADY_ENABLED: 80:tcp
success
[dany@web ~]$ sudo firewall-cmd --reload
success
```

### 🌞 Accéder au site web

```
PS C:\Users\ghass> curl http://10.2.1.12:80


StatusCode        : 200
StatusDescription : OK
Content           : <!doctype html>
                    <html>
                      <head>
                        <meta charset='utf-
```

### 🌞 Vérifier les logs d'accès

```
[dany@web ~]$ tail -n 3 /var/log/nginx/access.log
10.2.1.1 - - [20/Feb/2024:11:30:58 +0100] "GET /poweredby.png HTTP/1.1" 200 368 "http://10.2.1.12/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36" "-"
10.2.1.1 - - [20/Feb/2024:11:30:58 +0100] "GET /favicon.ico HTTP/1.1" 404 3332 "http://10.2.1.12/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36" "-"
10.2.1.1 - - [20/Feb/2024:11:31:46 +0100] "GET / HTTP/1.1" 200 7620 "-" "Mozilla/5.0 (Windows NT; Windows NT 10.0; fr-FR) WindowsPowerShell/5.1.22621.2506" "-"
```

## 5. Modif de la conf du serveur web

### 🌞 Changer le port d'écoute

```
[dany@web ~]$ sudo nano /etc/nginx/nginx.conf
(on modifie le 80 par le 8080)
```
- prouvez-moi que le changement a pris effet avec une commande ss
```
[dany@web ~]$ ss -tuln | grep 8080
tcp   LISTEN 0      511          0.0.0.0:8080      0.0.0.0:*
tcp   LISTEN 0      511             [::]:8080         [::]:*
```
- n'oubliez pas de fermer l'ancien port dans le firewall, et d'ouvrir le nouveau

```
[dany@web ~]$ sudo firewall-cmd --remove-port=80/tcp --permanent
success
```

- prouvez avec une commande curl sur votre machine que vous pouvez désormais visiter le port 8080

```
PS C:\Users\ghass> curl http://10.2.1.12:8080


StatusCode        : 200
StatusDescription : OK
Content           : <!doctype html>
                    <html>
```

### 🌞 Changer l'utilisateur qui lance le service

```
[dany@web ~]$  ps aux | grep nginx
root       12198  0.0  0.1  10108   956 ?        Ss   11:58   0:00 nginx: master process /usr/sbin/nginx
web        12199  0.0  0.6  13908  4792 ?        S    11:58   0:00 nginx: worker process
dany       12201  0.0  0.2   6408  2176 pts/0    S+   11:58   0:00 grep --color=auto nginx
```

### 🌞 Changer l'emplacement de la racine Web

