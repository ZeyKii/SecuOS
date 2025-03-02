# Part I : Filtrey des paqueys

🌞 Proposer une configuration restrictive de firewalld

```
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources: 10.1.1.0/24
  services:
  ports: 22/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

# Part II : PAM

🌞 Proposer une configuration de politique de mot de passe

```
[fmaxance@vbox ~]$ cat /etc/security/pwquality.conf | grep -E "^#" -v
minlen = 14       # length minimum
dcredit = -1      # chiffre
ucredit = -1      # majuscule
lcredit = -1      # minuscule
ocredit = -1      # special char
enforce_for_root  # règle strict pour ne pas avoir seulement une alerte
```

# Part III : OpenSSH

🌞 Proposer une configuration du serveur OpenSSH

[sshd_config.conf](sshd_config.conf)

MDP it4 : L3sup3rMDP!deLeO

🌞 Une fois en place, tu m'appelles en cours pour que je me connecte

```
10.33.70.218
6789
```

# Part IV : Gestion d'utilisateurs

🌞 Gestion d'utilisateurs

Voici le script que j'ai utilisé en bash pour la création des users : [script](script.sh)

🌞 Gestion de permissions

```
[fmaxance@vbox /]$ sudo ls -alR /data
/data:
total 0
drwxr-x---   4 root root      34 Mar  2 22:06 .
dr-xr-xr-x. 19 root root     267 Mar  2 22:05 ..
drwxr-xr-x+  2 root root      23 Mar  2 22:07 conf
drwxr-xr-x   6 root managers  89 Mar  2 22:06 projects

/data/conf:
total 0
drwxr-xr-x+ 2 root root 23 Mar  2 22:07 .
drwxr-x---  4 root root 34 Mar  2 22:06 ..
-rw-r--r--+ 1 root root  0 Mar  2 22:07 test.conf

/data/projects:
total 0
drwxr-xr-x  6 root managers 89 Mar  2 22:06 .
drwxr-x---  4 root root     34 Mar  2 22:06 ..
drwxr-xr-x  4 root root     36 Mar  2 22:06 client_data
-r--r--r--  1 root root      0 Mar  2 22:06 README.docx
drwxrwxr-x+ 2 root root     24 Mar  2 22:06 the_zoo
drwxrwxr-x+ 2 root root     24 Mar  2 22:06 website
drwxr-xr-x+ 2 root root     21 Mar  2 22:06 zoo_app

/data/projects/client_data:
total 0
drwxr-xr-x  4 root root     36 Mar  2 22:06 .
drwxr-xr-x  6 root managers 89 Mar  2 22:06 ..
drwxrwxr-x+ 2 root root     23 Mar  2 22:06 client1
drwxrwxr-x+ 2 root root     23 Mar  2 22:06 client2

/data/projects/client_data/client1:
total 0
drwxrwxr-x+ 2 root root 23 Mar  2 22:06 .
drwxr-xr-x  4 root root 36 Mar  2 22:06 ..
-rw-rw-r--+ 1 root root  0 Mar  2 22:06 data.docx

/data/projects/client_data/client2:
total 0
drwxrwxr-x+ 2 root root 23 Mar  2 22:06 .
drwxr-xr-x  4 root root 36 Mar  2 22:06 ..
-rw-rw-r--+ 1 root root  0 Mar  2 22:06 data.docx

/data/projects/the_zoo:
total 0
drwxrwxr-x+ 2 root root     24 Mar  2 22:06 .
drwxr-xr-x  6 root managers 89 Mar  2 22:06 ..
-rw-rw-r--+ 1 root root      0 Mar  2 22:06 ideas.docx

/data/projects/website:
total 0
drwxrwxr-x+ 2 root root     24 Mar  2 22:06 .
drwxr-xr-x  6 root managers 89 Mar  2 22:06 ..
-rw-rw-r--+ 1 root root      0 Mar  2 22:06 index.html

/data/projects/zoo_app:
total 0
drwxr-xr-x+ 2 root root     21 Mar  2 22:06 .
drwxr-xr-x  6 root managers 89 Mar  2 22:06 ..
-rwsrwxr-x+ 1 root sadia     0 Mar  2 22:06 zoo_app
```

```
[fmaxance@vbox /]$ sudo getfacl -R /data
getfacl: Removing leading '/' from absolute path names
# file: data
# owner: root
# group: root
user::rwx
group::r-x
other::---

# file: data/projects
# owner: root
# group: managers
user::rwx
group::r-x
other::r-x

# file: data/projects/the_zoo
# owner: root
# group: root
user::rwx
user:suha:rwx
group::r-x
group:managers:r-x
group:artists:rwx
group:devs:rwx
mask::rwx
other::r-x
default:user::rwx
default:user:suha:rwx
default:group::r-x
default:group:managers:r-x
default:group:artists:rwx
default:group:devs:rwx
default:mask::rwx
default:other::r-x

# file: data/projects/the_zoo/ideas.docx
# owner: root
# group: root
user::rw-
user:suha:rw-
group::r--
group:managers:r--
group:artists:rw-
mask::rw-
other::r--

# file: data/projects/website
# owner: root
# group: root
user::rwx
user:daniel:rwx
user:alysha:rwx
user:rose:rwx
user:nginx:r-x
group::r-x
group:managers:r-x
group:artists:r-x
group:devs:rwx
mask::rwx
other::r-x

# file: data/projects/website/index.html
# owner: root
# group: root
user::rw-
user:daniel:rw-
user:alysha:rw-
user:nginx:r--
group::r--
group:managers:r--
group:artists:r--
group:devs:rw-
mask::rw-
other::r--

# file: data/projects/client_data
# owner: root
# group: root
user::rwx
group::r-x
other::r-x

# file: data/projects/client_data/client1
# owner: root
# group: root
user::rwx
user:grace:rwx
user:oliver:rwx
group::r-x
group:managers:r-x
group:rh:r-x
mask::rwx
other::r-x

# file: data/projects/client_data/client1/data.docx
# owner: root
# group: root
user::rw-
user:grace:rw-
user:oliver:rw-
group::r--
mask::rw-
other::r--

# file: data/projects/client_data/client2
# owner: root
# group: root
user::rwx
user:noah:rwx
user:grace:rwx
user:lucia:rwx
group::r-x
group:managers:r-x
group:rh:r-x
mask::rwx
other::r-x

# file: data/projects/client_data/client2/data.docx
# owner: root
# group: root
user::rw-
user:grace:rw-
user:lucia:rw-
group::r--
mask::rw-
other::r--

# file: data/projects/zoo_app
# owner: root
# group: root
user::rwx
user:suha:r-x
user:sadia:r-x
user:jakub:r-x
group::r-x
mask::r-x
other::r-x

# file: data/projects/zoo_app/zoo_app
# owner: root
# group: sadia
# flags: s--
user::rwx
user:suha:rw-
user:sadia:rw-
user:jakub:r--
group::r--
mask::rwx
other::r-x

# file: data/projects/README.docx
# owner: root
# group: root
user::r--
group::r--
other::r--

# file: data/conf
# owner: root
# group: root
user::rwx
user:daniel:r-x
user:rose:r-x
group::r-x
group:admins:r-x
group:sysadmins:r-x
mask::r-x
other::r-x

# file: data/conf/test.conf
# owner: root
# group: root
user::rw-
user:daniel:r--
user:rose:r--
group::r--
group:admins:r--
group:sysadmins:r--
mask::r--
other::r--
```

```
[fmaxance@vbox /]$ sudo lsattr -R /data
---------------------- /data/projects

/data/projects:
---------------------- /data/projects/the_zoo

/data/projects/the_zoo:
---------------------- /data/projects/the_zoo/ideas.docx

---------------------- /data/projects/website

/data/projects/website:
---------------------- /data/projects/website/index.html

---------------------- /data/projects/client_data

/data/projects/client_data:
---------------------- /data/projects/client_data/client1

/data/projects/client_data/client1:
---------------------- /data/projects/client_data/client1/data.docx

---------------------- /data/projects/client_data/client2

/data/projects/client_data/client2:
---------------------- /data/projects/client_data/client2/data.docx


---------------------- /data/projects/zoo_app

/data/projects/zoo_app:
---------------------- /data/projects/zoo_app/zoo_app

----i----------------- /data/projects/README.docx

---------------------- /data/conf

/data/conf:
---------------------- /data/conf/test.conf
```

🌞 Gestion de sudo

Le fichier ``sudoers`` produit : [sudoers_vuln](sudoers_vuln.conf)

🌞 Misconf ?

🌞 Proposer une meilleure conf

Le fichier ``sudoers`` cette fois sécure : [sudoers_secur](sudoers_secur.conf)

# V. FTP

🌞 Mettre en place un serveur FTP + TLS

```
sudo dnf install -y vsftpd

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.pem

sudo chmod 600 /etc/ssl/private/vsftpd.key
sudo chmod 600 /etc/ssl/certs/vsftpd.pem

sudo vi /etc/vsftpd/vsftpd.conf

sudo firewall-cmd --permanent --add-service=ftp
sudo firewall-cmd --permanent --add-port=30000-31000/tcp
sudo firewall-cmd --reload

sudo systemctl restart vsftpd
sudo systemctl enable vsftpd

sudo systemctl status vsftpd
```

Fichier de conf de ``vsftpd`` : [vsftpd.conf](vsftpd.conf)