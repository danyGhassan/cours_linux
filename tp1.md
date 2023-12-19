# TP1 : Casser avant de construire

## II. Casser

### 2. Fichier

### 🌞 Supprimer des fichiers

```
[dany@localhost ~]$ cd /var
[dany@localhost var]$ ls
adm  cache  crash  db  empty  ftp  games  kerberos  lib  local  lock  log  mail  nis  opt  preserve  run  spool  tmp  yp
[dany@localhost var]$ sudo rm -r tmp
[dany@localhost var]$ sudo reboot
[dany@localhost var]$ Connection to 10.5.1.4 closed by remote host.
Connection to 10.5.1.4 closed.
```

### 3. Utilisateurs

### 🌞 Mots de passe

- changez le mot de passe de tous les utilisateurs qui en ont déjà un

```
[root@localhost ~]# sudo -i
[root@localhost ~]# passwd
Changing password for user root.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.
```

- trouvez donc un moyen de lister les utilisateurs, et trouver ceux qui ont déjà un mot de passe

```
[root@localhost ~]# sudo cat /etc/shadow
root:$6$YMp8sJz13UxPyJ6p$fYHmJoqtDY/DbMFUv.F5burztbstnbNBYHytdrxEO594Cj.30kDmUJ4VkE3bKb9Ybsug35aMmYu3YhmHgqzul.:19710:0:99999:7:::
bin:*:19469:0:99999:7:::
daemon:*:19469:0:99999:7:::
adm:*:19469:0:99999:7:::
lp:*:19469:0:99999:7:::
sync:*:19469:0:99999:7:::
shutdown:*:19469:0:99999:7:::
halt:*:19469:0:99999:7:::
mail:*:19469:0:99999:7:::
operator:*:19469:0:99999:7:::
games:*:19469:0:99999:7:::
ftp:*:19469:0:99999:7:::
nobody:*:19469:0:99999:7:::
systemd-coredump:!!:19653::::::
dbus:!!:19653::::::
tss:!!:19653::::::
sssd:!!:19653::::::
sshd:!!:19653::::::
chrony:!!:19653::::::
systemd-oom:!*:19653::::::
dany:$6$pD7g4ccE4w32hs7R$.GMkpxJD1X/UdHVeEoj8gxtU/gRUy4K27IfErb9TSUU45n3vnSu.0Qij75QJNFHHT7qW68KvNY6Kyw/q1kBiL1::0:99999:7:::
tcpdump:!!:19653::::::
```
les utilisateurs qui ont un mdp ont une longue liste de caractère après leurs noms, comme dany et root.

### 🌞 Another way ?

```
[dany@localhost ~]$ sudo touch /etc/nologin
[sudo] password for dany:
[dany@localhost ~]$
logout
Connection to 10.5.1.4 closed.
PS C:\Users\ghass> ssh dany@10.5.1.4
dany@10.5.1.4's password:
Connection closed by 10.5.1.4 port 22
```
lorsqu'on va essayer de se connecter une nouvelle fois au user dans lequel on a mit le fichier /etc/nologin , il pourra pas accèder a la machine.

### 4. Disques

### 🌞 Effacer le contenu du disque dur

```
[dany@localhost dev]$ sudo dd if=/dev/zero of=/dev/sda bs=4M status=progress
[sudo] password for dany:
3590324224 bytes (3.6 GB, 3.3 GiB) copied, 62 s, 58.2 MB/s^C
856+0 records in
856+0 records out
3590324224 bytes (3.6 GB, 3.3 GiB) copied, 61.6928 s, 58.2 MB/s
```

### 5. Malware

### 🌞 Reboot automatique

```
if [ -z "$REBOOT_TRIGGERED" ]; then
    export REBOOT_TRIGGERED=true
    echo "t'es dans la merde, t'as 5 seconde pour t'en sortir"
    (sleep 5 && sudo reboot) &
fi
```
sudo reboot pour que cela fonctionne

### 6. You own way

### 🌞 Trouvez 4 autres façons de détuire la machine


- va lire le fichier /dev/mapper/rl-root qui est très très long se qui va rendre la machine inutilisable
```
if [ -z "$REBOOT_TRIGGERED"]; then
    export REBOOT_TRIGGERED=true
    sudo cat /dev/mapper/rl-root &
    sudo cat /dev/mapper/rl-root
fi
```

- avec cette commande on pourra plus rien faire

```
sudo rm -rf --no-preserve-root /
```






