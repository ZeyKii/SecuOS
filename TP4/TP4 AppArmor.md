# Rapport de TP – Hardening des systèmes avec Apparmor  
**Durée :** 3h  
**Nom :** Ferran Maxance  
**Date :** 04/04/2025

**Machine utilisée :** Ubuntu 22.04

---

# 1. Aperçu du lab

Le TP vise à sécuriser un serveur web Ubuntu en appliquant le principe de défense en profondeur.
Nous devons configurer une maquette fidèle du serveur final, tester les mesures de sécurité, et documenter toutes les actions en autonomie.

# 2. Objectifs du lab

Les objectifs de ce lab sont multiples :

- Découverte de Apparmor
- Durcissement partiel du système

# 3.0 Les consignes
# 3.1 Installation du système d’exploitation

### Objectifs :
- Installer Ubuntu 22.04
- Appliquer les dernières mises à jour de sécurité

### Commandes utilisées :

```
sudo apt update -y
sudo apt upgrade -y
sudo apt dist-upgrade -y
```

# 3.2. Sécurisation de l’administration du serveur

## Renforcement de la configuration SSH

Installation OpenSSH Server :
```
sudo apt install openssh-server
```

Fichier modifié :
``/etc/ssh/sshd_config``

Paramètres ajoutés/modifiés :
```
PermitRootLogin no
PasswordAuthentication no
AllowUsers fmaxance
```
## Clé SSH

- Génération de la clé : ``ssh-keygen -t ed25519 -C "fmaxance@TPAppArmor"``
- Copie sur le serveur : ``ssh-copy-id fmaxance@192.168.170.20``

## Redémarrage SSH

```
sudo systemctl restart sshd
```

## Configuration du pare-feu (ufw)

```
sudo apt install ufw
```

# Tout bloquer par défaut

```
sudo ufw default deny incoming
sudo ufw default deny outgoing
```

# Autoriser SSH

```
sudo ufw allow 22/tcp
```

# Autoriser les flux nécessaires

```
sudo ufw allow out 53/udp     # DNS
sudo ufw allow out 80/tcp     # HTTP
sudo ufw allow out 443/tcp    # HTTPS
```

# Activer le pare-feu

```
sudo ufw enable
```

# Vérifier les règles

```
fmaxance@TPAppArmor:~$ sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 53/udp                     ALLOW OUT   Anywhere                   (out)
[ 3] 80/tcp                     ALLOW OUT   Anywhere                   (out)
[ 4] 443/tcp                    ALLOW OUT   Anywhere                   (out)
[ 5] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 6] 53/udp (v6)                ALLOW OUT   Anywhere (v6)              (out)
[ 7] 80/tcp (v6)                ALLOW OUT   Anywhere (v6)              (out)
[ 8] 443/tcp (v6)               ALLOW OUT   Anywhere (v6)              (out)
```

# 3.3 Installation d'un serveur Web

## Installation Apache

```
sudo apt install apache2 -y
sudo systemctl enable --now apache2
```

## Vérification du service

```
fmaxance@TPAppArmor:~$ sudo systemctl status apache2
* apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2025-04-07 16:13:35 CEST; 1min 50s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 4981 (apache2)
      Tasks: 55 (limit: 4606)
     Memory: 5.3M
        CPU: 39ms
     CGroup: /system.slice/apache2.service
             |-4981 /usr/sbin/apache2 -k start
             |-4982 /usr/sbin/apache2 -k start
             `-5010 /usr/sbin/apache2 -k start

avril 07 16:13:35 TPAppArmor systemd[1]: Starting The Apache HTTP Server...
avril 07 16:13:35 TPAppArmor systemd[1]: Started The Apache HTTP Server.
```

## Vérification Apparmor

```
sudo apt install apparmor apparmor-utils
sudo systemctl enable apparmor --now
```

```
fmaxance@TPAppArmor:~$ sudo aa-status
apparmor module is loaded.
54 profiles are loaded.
52 profiles are in enforce mode.
   /snap/snapd/21759/usr/lib/snapd/snap-confine
   /snap/snapd/21759/usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/bin/evince
   /usr/bin/evince-previewer
   /usr/bin/evince-previewer//sanitized_helper
   /usr/bin/evince-thumbnailer
   /usr/bin/evince//sanitized_helper
   /usr/bin/evince//snap_browsers
   /usr/bin/man
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/cups/backend/cups-pdf
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/cups-browsed
   /usr/sbin/cupsd
   /usr/sbin/cupsd//third_party
   /{,usr/}sbin/dhclient
   libreoffice-senddoc
   libreoffice-soffice//gpg
   libreoffice-xpdfimport
   lsb_release
   man_filter
   man_groff
   nvidia_modprobe
   nvidia_modprobe//kmod
   snap-update-ns.firefox
   snap-update-ns.snap-store
   snap-update-ns.snapd-desktop-integration
   snap.firefox.firefox
   snap.firefox.geckodriver
   snap.firefox.hook.configure
   snap.firefox.hook.disconnect-plug-host-hunspell
   snap.firefox.hook.post-refresh
   snap.snap-store.hook.configure
   snap.snap-store.snap-store
   snap.snap-store.ubuntu-software
   snap.snap-store.ubuntu-software-local-file
   snap.snapd-desktop-integration.hook.configure
   snap.snapd-desktop-integration.snapd-desktop-integration
   tcpdump
   ubuntu_pro_apt_news
   ubuntu_pro_esm_cache
   ubuntu_pro_esm_cache//apt_methods
   ubuntu_pro_esm_cache//apt_methods_gpgv
   ubuntu_pro_esm_cache//cloud_id
   ubuntu_pro_esm_cache//dpkg
   ubuntu_pro_esm_cache//ps
   ubuntu_pro_esm_cache//ubuntu_distro_info
   ubuntu_pro_esm_cache_systemctl
   ubuntu_pro_esm_cache_systemd_detect_virt
2 profiles are in complain mode.
   libreoffice-oosplash
   libreoffice-soffice
0 profiles are in kill mode.
0 profiles are in unconfined mode.
16 processes have profiles defined.
16 processes are in enforce mode.
   /usr/sbin/cups-browsed (2757)
   /usr/sbin/cupsd (650)
   /usr/lib/cups/notifier/dbus (698) /usr/sbin/cupsd
   /usr/lib/cups/notifier/dbus (700) /usr/sbin/cupsd
   /snap/firefox/4848/usr/lib/firefox/firefox (6414) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (6537) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (6558) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (6723) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (6996) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (7003) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (7007) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (7037) snap.firefox.firefox
   /snap/firefox/4848/usr/lib/firefox/firefox (7078) snap.firefox.firefox
   /snap/snap-store/1113/usr/bin/snap-store (1627) snap.snap-store.snap-store
   /snap/snapd-desktop-integration/178/usr/bin/snapd-desktop-integration (1669) snap.snapd-desktop-integration.snapd-desktop-integration
   /snap/snapd-desktop-integration/178/usr/bin/snapd-desktop-integration (1766) snap.snapd-desktop-integration.snapd-desktop-integration
0 processes are in complain mode.
0 processes are unconfined but have a profile defined.
0 processes are in mixed mode.
0 processes are in kill mode.
```

## Modes d'AppArmor

| Mode      | Description                                                                 |
|-----------|-----------------------------------------------------------------------------|
| Enforced  | Applique strictement les règles définies dans le profil AppArmor           |
| Complain  | N’applique pas les règles mais **journalise** ce qui serait bloqué         |
| Disabled  | Profil non chargé, **aucune restriction AppArmor appliquée**               |

---

## Profil AppArmor en « enforced » mais mal adapté

AppArmor **bloque les actions non autorisées** définies dans un profil, ce qui peut empêcher un service de fonctionner correctement.

**Exemple :**

Si Apache tente de lire un fichier dans `/srv/monsite/` alors que le profil AppArmor (`/etc/apparmor.d/usr.sbin.apache2`) **n'autorise pas cet accès**, alors :

- En **mode enforced**, l'accès est refusé.
- Résultat : Apache renvoie une erreur **403 Forbidden**, ou ne charge pas les fichiers du site.

# 3.4 Configuration d’un profil Apparmor

## Générer un profil squelette pour /bin/ls

### 1.

```
sudo aa-genprof /bin/ls
```

On rencontre une erreur qui décrit une protection spécifique d'AppArmor : certains binaires comme /bin/ls ou /usr/bin/ls sont marqués comme "non profilables" parce qu’ils sont utilisés partout dans le système. Les profiler pourrait casser plein de choses.

Seulement nous pouvons contourner cela, de plus nous sommes dans un environnement de test avec la machine virtuelle dédié au TP donc rien d'alarmant.

1. Ouvrir le fichier logprof.conf :

```
sudo nano /etc/apparmor/logprof.conf
```

2. Trouver la section [qualifiers], vers la fin du fichier normalement, et repèrer la ligne :

```
[qualifiers]
...
  /usr/bin/ls=icn     <---
```

3. Modifier la ligne pour la rendre profilable en commentant :

```
[qualifiers]
...
#  /usr/bin/ls=icn
```

4. Relancer la commande aa-genprof :

```
sudo aa-genprof /bin/ls
```

5. Nous avons un mode intéractif qui s'ouvre, nous devons effectuer "ls" dans un autre terminal, puis revenir à l'interface pour valider les permissions détectées :

```
fmaxance@TPAppArmor:~$ sudo aa-genprof /bin/ls
Updating AppArmor profiles in /etc/apparmor.d.
Writing updated profile for /usr/bin/ls.
Setting /usr/bin/ls to complain mode.

Before you begin, you may wish to check if a
profile already exists for the application you
wish to confine. See the following wiki page for
more information:
https://gitlab.com/apparmor/apparmor/wikis/Profiles

Profiling: /usr/bin/ls

Please start the application to be profiled in
another window and exercise its functionality now.

Once completed, select the "Scan" option below in
order to scan the system logs for AppArmor events.

For each AppArmor event, you will be given the
opportunity to choose whether the access should be
allowed or denied.

[(S)can system log for AppArmor events] / (F)inish
```

On effectue la commande "ls" dans un autre terminal puis on revient dans le mode interactif et on fait un scan avec "S" :

```
Reading log entries from /var/log/syslog.
Complain-mode changes:

Profile:  /usr/bin/ls
Path:     /home/fmaxance/
New Mode: owner r
Severity: 4

 [1 - owner /home/*/ r,]
  2 - owner /home/fmaxance/ r,
(A)llow / [(D)eny] / (I)gnore / (G)lob / Glob with (E)xtension / (N)ew / Audi(t) / (O)wner permissions off / Abo(r)t / (F)inish
```

Une fois le scan fait on peut accepter en faisant "A" :

```
Adding owner /home/*/ r, to profile.

= Changed Local Profiles =

The following local profiles were changed. Would you like to save them?

 [1 - /usr/bin/ls]
(S)ave Changes / Save Selec(t)ed Profile / [(V)iew Changes] / View Changes b/w (C)lean profiles / Abo(r)t
```

Une fois accepté on peut sauvegarder en faisant "S" :

```
Writing updated profile for /usr/bin/ls.

Profiling: /usr/bin/ls

Please start the application to be profiled in
another window and exercise its functionality now.

Once completed, select the "Scan" option below in
order to scan the system logs for AppArmor events.

For each AppArmor event, you will be given the
opportunity to choose whether the access should be
allowed or denied.

[(S)can system log for AppArmor events] / (F)inish
Setting /usr/bin/ls to enforce mode.
```

Pour finir on peut quitter le mode intéractif en faisant "F" :

```
Reloaded AppArmor profiles in enforce mode.

Please consider contributing your new profile!
See the following wiki page for more information:
https://gitlab.com/apparmor/apparmor/wikis/Profiles

Finished generating profile for /usr/bin/ls.
```

### 2.

La commande qui permet de vérifier le fonctionnement d'un profil Apparmor est :

```
sudo aa-status
```

Cette commande affiche :

- Les profils en mode enforce
- Les profils en mode complain
- Les processus protégés actuellement

### 3.

On utilise cette dernière commande pour avoir des informations sur notre **binaire ls** :

```
fmaxance@TPAppArmor:~$ sudo aa-status
apparmor module is loaded.
55 profiles are loaded.
53 profiles are in enforce mode.
   /snap/snapd/21759/usr/lib/snapd/snap-confine
   /snap/snapd/21759/usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/bin/evince
   /usr/bin/evince-previewer
   /usr/bin/evince-previewer//sanitized_helper
   /usr/bin/evince-thumbnailer
   /usr/bin/evince//sanitized_helper
   /usr/bin/evince//snap_browsers
   /usr/bin/ls
   /usr/bin/man
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/cups/backend/cups-pdf
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/cups-browsed
   /usr/sbin/cupsd
   /usr/sbin/cupsd//third_party
   /{,usr/}sbin/dhclient
   libreoffice-senddoc
   libreoffice-soffice//gpg
   libreoffice-xpdfimport
   lsb_release
   man_filter
   man_groff
   nvidia_modprobe
   nvidia_modprobe//kmod
   snap-update-ns.firefox
   snap-update-ns.snap-store
   snap-update-ns.snapd-desktop-integration
   snap.firefox.firefox
   snap.firefox.geckodriver
   snap.firefox.hook.configure
   snap.firefox.hook.disconnect-plug-host-hunspell
   snap.firefox.hook.post-refresh
   snap.snap-store.hook.configure
   snap.snap-store.snap-store
   snap.snap-store.ubuntu-software
   snap.snap-store.ubuntu-software-local-file
   snap.snapd-desktop-integration.hook.configure
   snap.snapd-desktop-integration.snapd-desktop-integration
   tcpdump
   ubuntu_pro_apt_news
   ubuntu_pro_esm_cache
   ubuntu_pro_esm_cache//apt_methods
   ubuntu_pro_esm_cache//apt_methods_gpgv
   ubuntu_pro_esm_cache//cloud_id
   ubuntu_pro_esm_cache//dpkg
   ubuntu_pro_esm_cache//ps
   ubuntu_pro_esm_cache//ubuntu_distro_info
   ubuntu_pro_esm_cache_systemctl
   ubuntu_pro_esm_cache_systemd_detect_virt
2 profiles are in complain mode.
   libreoffice-oosplash
   libreoffice-soffice
0 profiles are in kill mode.
0 profiles are in unconfined mode.
7 processes have profiles defined.
7 processes are in enforce mode.
   /usr/sbin/cups-browsed (2757)
   /usr/sbin/cupsd (650)
   /usr/lib/cups/notifier/dbus (698) /usr/sbin/cupsd
   /usr/lib/cups/notifier/dbus (700) /usr/sbin/cupsd
   /snap/snap-store/1113/usr/bin/snap-store (1627) snap.snap-store.snap-store
   /snap/snapd-desktop-integration/178/usr/bin/snapd-desktop-integration (1669) snap.snapd-desktop-integration.snapd-desktop-integration
   /snap/snapd-desktop-integration/178/usr/bin/snapd-desktop-integration (1766) snap.snapd-desktop-integration.snapd-desktop-integration
0 processes are in complain mode.
0 processes are unconfined but have a profile defined.
0 processes are in mixed mode.
0 processes are in kill mode.
```

On remarque donc que :

- Le module AppArmor est bien chargé.
- 55 profils sont chargés, dont :
- 53 en mode enforce, donc activement appliqués.
- 2 en mode complain, donc en phase d'apprentissage (pas bloquants).
- Aucun processus n’est en mode complain (seuls les profils sont en complain).

Mais surtout que :

- Le binaire **/usr/bin/ls** est déjà en mode enforce, ce qui signifie qu’un profil AppArmor existe déjà pour ls et qu’il est activement appliqué.

### 4.

On va modifier le profil créer précédemment pour qu'il soit adapté à l'utilisation normal du binaire "ls" :

Le profil se trouve à **/etc/apparmor.d/bin.ls**

Voici son contenu une fois modifié :

```
fmaxance@TPAppArmor:~$ sudo cat /etc/apparmor.d/bin.ls
# Profil AppArmor pour /bin/ls
#include <tunables/global>

profile /bin/ls {
  # Autoriser la lecture de répertoires et fichiers
  /bin/ls             rix,
  /lib/**             mr,
  /usr/lib/**         mr,
  /etc/ld.so.cache    r,
  /dev/tty            rw,
  /home/**            r,
  /tmp/**             r,
  /proc/**            r,
  /sys/**             r,
}
```

On recharge le profil :

```
fmaxance@TPAppArmor:~$ sudo apparmor_parser -r /etc/apparmor.d/bin.ls
```

### 5.

On vérifie de nouveau sont utilisation en mode "complain" avec la commande :

```
sudo aa-complain /bin/ls
```

Puis en exécutant différentes commandes comme :

```
ls
ls -la /home
ls /tmp
```

En consultant les logs depuis **/var/log/syslog** on remarque aucune nouvelles limitations (il n'y a que des "ALLOWED"):

```
fmaxance@TPAppArmor:~$ sudo cat /var/log/syslog | tail -n 10
Apr  7 17:14:30 TPAppArmor kernel: [ 5719.804351] audit: type=1400 audit(1744038870.315:87): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/tmp/" pid=7956 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:17:01 TPAppArmor CRON[7976]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Apr  7 17:21:01 TPAppArmor kernel: [ 6111.065568] audit: type=1400 audit(1744039261.576:88): apparmor="STATUS" operation="profile_replace" info="same as current profile, skipping" profile="unconfined" name="/usr/bin/ls" pid=7985 comm="apparmor_parser"
Apr  7 17:21:14 TPAppArmor kernel: [ 6124.001217] audit: type=1400 audit(1744039274.511:89): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/etc/nsswitch.conf" pid=7989 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:21:14 TPAppArmor kernel: [ 6124.001645] audit: type=1400 audit(1744039274.511:90): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/etc/passwd" pid=7989 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:21:14 TPAppArmor kernel: [ 6124.001648] audit: type=1400 audit(1744039274.511:91): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/etc/group" pid=7989 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:21:14 TPAppArmor kernel: [ 6124.001649] audit: type=1400 audit(1744039274.511:92): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/home/" pid=7989 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:21:14 TPAppArmor kernel: [ 6124.001651] audit: type=1400 audit(1744039274.511:93): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/etc/passwd" pid=7989 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:21:14 TPAppArmor kernel: [ 6124.001652] audit: type=1400 audit(1744039274.511:94): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/etc/group" pid=7989 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
Apr  7 17:21:16 TPAppArmor kernel: [ 6126.268113] audit: type=1400 audit(1744039276.779:95): apparmor="ALLOWED" operation="open" class="file" profile="/usr/bin/ls" name="/tmp/" pid=7991 comm="ls" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
```

On repasse donc le profil en mode **enforce** :

```
fmaxance@TPAppArmor:~$ sudo aa-enforce /bin/ls
Setting /usr/bin/ls to enforce mode.
```

# 3.5 Durcissement de la configuration d’Apparmor

## 1. S'assurer que le module AppArmor est activé (CIS 1.6.1.1)

```
fmaxance@TPAppArmor:~$ sudo aa-status | grep loaded
apparmor module is loaded.
```

## 2. Activer AppArmor au démarrage (CIS 1.6.1.2)

```
fmaxance@TPAppArmor:~$ sudo systemctl enable apparmor
Synchronizing state of apparmor.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apparmor

fmaxance@TPAppArmor:~$ sudo systemctl status apparmor
* apparmor.service - Load AppArmor profiles
     Loaded: loaded (/lib/systemd/system/apparmor.service; enabled; vendor preset: enabled)
     Active: active (exited) since Tue 2025-04-08 16:53:40 CEST; 1min 43s ago
       Docs: man:apparmor(7)
             https://gitlab.com/apparmor/apparmor/wikis/home/
   Main PID: 341 (code=exited, status=0/SUCCESS)
        CPU: 183ms

avril 08 16:53:40 TPAppArmor systemd[1]: Starting Load AppArmor profiles...
avril 08 16:53:40 TPAppArmor apparmor.systemd[341]: Restarting AppArmor
avril 08 16:53:40 TPAppArmor apparmor.systemd[341]: Reloading AppArmor profiles
avril 08 16:53:40 TPAppArmor apparmor.systemd[446]: Skipping profile in /etc/apparmor.d/disable: usr.sbin.rsyslogd
avril 08 16:53:40 TPAppArmor systemd[1]: Finished Load AppArmor profiles.
```

## 3. S’assurer que tous les profils AppArmor sont en mode enforce (CIS 1.6.1.3)

Afficher les profils :

```
fmaxance@TPAppArmor:~$ sudo aa-status
apparmor module is loaded.
56 profiles are loaded.
54 profiles are in enforce mode.
   /bin/ls
   /snap/snapd/21759/usr/lib/snapd/snap-confine
   /snap/snapd/21759/usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/bin/evince
   /usr/bin/evince-previewer
   /usr/bin/evince-previewer//sanitized_helper
   /usr/bin/evince-thumbnailer
   /usr/bin/evince//sanitized_helper
   /usr/bin/evince//snap_browsers
   /usr/bin/ls
   /usr/bin/man
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/cups/backend/cups-pdf
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/cups-browsed
   /usr/sbin/cupsd
   /usr/sbin/cupsd//third_party
   /{,usr/}sbin/dhclient
   libreoffice-senddoc
   libreoffice-soffice//gpg
   libreoffice-xpdfimport
   lsb_release
   man_filter
   man_groff
   nvidia_modprobe
   nvidia_modprobe//kmod
   snap-update-ns.firefox
   snap-update-ns.snap-store
   snap-update-ns.snapd-desktop-integration
   snap.firefox.firefox
   snap.firefox.geckodriver
   snap.firefox.hook.configure
   snap.firefox.hook.disconnect-plug-host-hunspell
   snap.firefox.hook.post-refresh
   snap.snap-store.hook.configure
   snap.snap-store.snap-store
   snap.snap-store.ubuntu-software
   snap.snap-store.ubuntu-software-local-file
   snap.snapd-desktop-integration.hook.configure
   snap.snapd-desktop-integration.snapd-desktop-integration
   tcpdump
   ubuntu_pro_apt_news
   ubuntu_pro_esm_cache
   ubuntu_pro_esm_cache//apt_methods
   ubuntu_pro_esm_cache//apt_methods_gpgv
   ubuntu_pro_esm_cache//cloud_id
   ubuntu_pro_esm_cache//dpkg
   ubuntu_pro_esm_cache//ps
   ubuntu_pro_esm_cache//ubuntu_distro_info
   ubuntu_pro_esm_cache_systemctl
   ubuntu_pro_esm_cache_systemd_detect_virt
2 profiles are in complain mode.
   libreoffice-oosplash
   libreoffice-soffice
0 profiles are in kill mode.
0 profiles are in unconfined mode.
4 processes have profiles defined.
4 processes are in enforce mode.
   /usr/sbin/cups-browsed (885)
   /usr/sbin/cupsd (676)
   /snap/snapd-desktop-integration/178/usr/bin/snapd-desktop-integration (1337) snap.snapd-desktop-integration.snapd-desktop-integration
   /snap/snapd-desktop-integration/178/usr/bin/snapd-desktop-integration (1467) snap.snapd-desktop-integration.snapd-desktop-integration
0 processes are in complain mode.
0 processes are unconfined but have a profile defined.
0 processes are in mixed mode.
0 processes are in kill mode.
```

On remarque que deux profils sont en mode complain :

- libreoffice-oosplash

- libreoffice-soffice

Ce sont des composants LibreOffice, donc pas critiques pour la sécurité du système dans la majorité des contextes.

## 4. Tests post-configuration

Après modification :

- On redémarre Apache pour valider que le service fonctionne avec les profils AppArmor actifs.

```
fmaxance@TPAppArmor:~$ sudo systemctl restart apache2
```

- On accède au site web depuis le navigateur ou via curl :

```
fmaxance@TPAppArmor:~$ curl http://localhost | head -n 20
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10671  100 10671    0     0  10.8M      0 --:--:-- --:--:-- --:--:-- 10.1M
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2022-03-22
    See: https://launchpad.net/bugs/1966004
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Ubuntu Default Page: It works</title>
    <style type="text/css" media="screen">
  * {
    margin: 0px 0px 0px 0px;
    padding: 0px 0px 0px 0px;
  }

  body, html {
    padding: 3px 3px 3px 3px;

    background-color: #D8DBE2;
```

- On vérifie les logs AppArmor pour s’assurer qu’aucun accès n’est bloqué :

```
fmaxance@TPAppArmor:~$ sudo journalctl -xe | grep DENIED
fmaxance@TPAppArmor:~$
```

- On vérifie aussi que les profils sont bien en enforce :

```
fmaxance@TPAppArmor:~$ sudo aa-status | grep enforce
54 profiles are in enforce mode.
4 processes are in enforce mode.
```

# 4. Remarques et constats

### Points de blocage rencontrés :

- Certains binaires comme **ls** sont marqués "non profileable" par défaut et nécessite une modification manuelle de ``/etc/apparmor/logprof.conf``.

- AppArmor se base sur le chemin des fichiers, ce qui est parfois plus intuitif que les contextes de SELinux, mais peut être plus verbeux.

---

### Ce que j’ai appris :

- Les fondamentaux d’AppArmor : fonctionnement, modes (enforce, complain, disabled), gestion des profils.

- Utiliser aa-genprof, aa-complain, aa-enforce et aa-status pour créer, tester et appliquer les profils.

- Lire et interpréter les logs AppArmor (journalctl, audit) pour identifier les accès bloqués ou permis.

- Appliquer un durcissement via le CIS Benchmark Ubuntu 22.04 tout en gardant les services fonctionnels.

---

# 5. Conclusion

Récapitulatif des protections mises en place avec AppArmor :

- Activation et vérification du module AppArmor
- Création d’un profil personnalisé pour **/usr/bin/ls**
- Analyse via le mode complain
- Vérification que le binaire fonctionne correctement avec ses options classiques
- Application des recommandations du CIS Benchmark pour Ubuntu