# Rapport de TP – Hardening des systèmes avec SELinux  
**Durée :** 3h  
**Nom :** Ferran Maxance  
**Date :** 03/04/2025

**Machine utilisée :** Rocky Linux 9 (sans interface graphique)

---

# 1 Aperçu du lab

Le TP vise à sécuriser un serveur web Rocky Linux en appliquant le principe de défense en profondeur.
Nous devons configurer une maquette fidèle du serveur final, tester les mesures de sécurité, et documenter toutes les actions en autonomie.

# 2 Objectifs du lab

Les objectifs de ce lab sont multiples :

- Découverte et prise en main de SELinux
- Durcissement partiel du système

# 3.0 Les consignes
# 3.1 Installation du système d’exploitation

### Objectifs :
- Installer Rocky Linux 9 minimal
- Appliquer les dernières mises à jour de sécurité

### Commandes utilisées :

```
sudo dnf update -y
```

# 3.2. Sécurisation de l’administration du serveur

## Renforcement de la configuration SSH

Fichier modifié :
``/etc/ssh/sshd_config``

Paramètres ajoutés/modifiés :
```
PermitRootLogin no
PasswordAuthentication no
AllowUsers fmaxance
```
## Clé SSH

- Génération de la clé : ``ssh-keygen -t ed25519 -C "fmaxance@selinux"``
- Copie sur le serveur : ``ssh-copy-id fmaxance@192.168.170.10``

## Redémarrage SSH

```
sudo systemctl restart sshd
```

## Configuration du pare-feu (firewalld)

```
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

# 3.3 Installation d'un serveur Web

## Installation Apache

```
sudo dnf install httpd -y
sudo systemctl enable --now httpd
```

## Vérification du service

```
[fmaxance@selinux ~]$ sudo systemctl status httpd
[sudo] password for fmaxance:
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running) since Thu 2025-04-03 11:48:48 CEST; 43min ago
       Docs: man:httpd.service(8)
   Main PID: 36177 (httpd)
     Status: "Total requests: 3; Idle/Busy workers 100/0;Requests/sec: 0.00115; Bytes served/sec:   9>
      Tasks: 177 (limit: 23141)
     Memory: 25.9M
        CPU: 5.071s
     CGroup: /system.slice/httpd.service
             ├─36177 /usr/sbin/httpd -DFOREGROUND
             ├─36178 /usr/sbin/httpd -DFOREGROUND
             ├─36179 /usr/sbin/httpd -DFOREGROUND
             ├─36180 /usr/sbin/httpd -DFOREGROUND
             └─36181 /usr/sbin/httpd -DFOREGROUND

Apr 03 11:48:48 selinux systemd[1]: Starting The Apache HTTP Server...
Apr 03 11:48:48 selinux httpd[36177]: AH00558: httpd: Could not reliably determine the server's fully>
Apr 03 11:48:48 selinux httpd[36177]: Server configured, listening on: port 80
Apr 03 11:48:48 selinux systemd[1]: Started The Apache HTTP Server.
```

## Accès via curl

```
[fmaxance@selinux ~]$ curl localhost | head -n 20
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7620  100  7620    <!doctype html>     0 --:--:-- --:--:-- --:--:--     0
<html>
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
0     0   744k      0 --:--:-- --:--:-- --:--:--  744k
    <title>HTTP Server Test Page powered by: Rocky Linux</title>
    <style type="text/css">
      /*<![CDATA[*/

      html {
        height: 100%;
        width: 100%;
      }
        body {
  background: rgb(20,72,50);
  background: -moz-linear-gradient(180deg, rgba(23,43,70,1) 30%, rgba(0,0,0,1) 90%)  ;
  background: -webkit-linear-gradient(180deg, rgba(23,43,70,1) 30%, rgba(0,0,0,1) 90%) ;
  background: linear-gradient(180deg, rgba(23,43,70,1) 30%, rgba(0,0,0,1) 90%);
  background-repeat: no-repeat;
  background-attachment: fixed;
```

## Vérification SELinux

```
[fmaxance@selinux ~]$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33
```

## Modes de SELinux

| Mode | Description |
|------|-------------|
| Enforcing | Applique strictement les politiques de sécurité |
| Permissive | N’applique pas, mais journalise ce qu’il bloquerait (pratique lors de phase de test)|
| Disabled | Complètement désactivé |

## Profil SELinux en « enforce » mais mal adapté

SELinux **bloque les actions non autorisées**, ce qui peut empêcher un service de fonctionner.

**Exemple :**
Si Apache lit un fichier dans /srv/monsite/ sans le bon contexte (httpd_sys_content_t), alors en mode enforce, l'accès est refusé → le site affiche une erreur 403.

