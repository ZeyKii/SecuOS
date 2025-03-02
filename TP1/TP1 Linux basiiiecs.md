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

