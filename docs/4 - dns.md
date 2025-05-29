# 🌐 Serveur DNS - CoreDNS (10.10.10.3 / 10.10.10.4)

## 🎯 Objectif

Le rôle du serveur DNS est d'assurer la résolution des noms internes au domaine `int.wmbakopt.com`. Deux instances CoreDNS sont utilisées : une primaire (10.10.10.3) et une secondaire (10.10.10.4).


## 🧱 Environnement

| Composant          | Rôle        | IP           |
|--------------------|-------------|--------------|
| CoreDNS primaire   | Maître DNS  | 10.10.10.3   |
| CoreDNS secondaire | Esclave DNS | 10.10.10.4 |


## 📦 Installation et configuration du serveur DNS primaire

### Configuration du réseau

```bash
su -
```

```bash
apt update
```

```bash
vi /etc/network/interfaces
```

```bash
# The loopback network interface
auto lo
iface lo inet loopback

auto ens18
iface ens18 inet static
address 10.10.10.3
netmask 255.255.255.0
gateway 10.10.10.254
dns-search int.wmbakopt.com
dns-nameservers 8.8.8.8
dns-nameservers 10.10.10.3 10.10.10.4
```

```bash
systemctl restart networking && systemctl status networking
```

```bash
vi /etc/apt/sources.list
```

```bash
deb https://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb-src https://deb.debian.org/debian bookworm main contrib non-free non-free-firmware

deb https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware

deb https://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
deb-src https://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
```

```bash
apt update
```

### Installation du serveur

```bash
adduser --system --group --shell "/usr/sbin/nologin" --comment "CoreDNS" --home "/etc/coredns" coredns
```

```bash
wget https://github.com/coredns/coredns/releases/download/v1.12.1/coredns_1.12.1_linux_amd64.tgz
```

```bash
tar -xvf coredns_1.12.1_linux_amd64.tgz
```

```bash
install coredns /usr/bin/
```

```bash
mkdir /var/lib/coredns
```

```bash
cd /etc/coredns
```

```bash
vi Corefile
```

```
int.wmbakopt.com {
    file /etc/coredns/db.int.wmbakopt.com
    transfer {
        to 10.10.10.4
    }
    log
    errors
}

10.10.10.in-addr.arpa {
    file /etc/coredns/db.10.10.10.in-addr.arpa
    transfer {
        to 10.10.10.4
    }
    log
    errors
}

. {
    forward . 1.1.1.1
    log
    errors
}
```

```bash
vi /etc/systemd/system/coredns.service
```

```
[Unit]
Description=CoreDNS DNS server
Documentation=https://coredns.io
After=network.target

[Service]
PermissionsStartOnly=true
LimitNOFILE=1048576
LimitNPROC=512
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
NoNewPrivileges=true
User=coredns
WorkingDirectory=/var/lib/coredns
ExecStart=/usr/bin/coredns -conf=/etc/coredns/Corefile
ExecReload=/bin/kill -SIGUSR1 $MAINPID
Restart=on-failure
StandardOutput=append:/var/log/coredns.log
StandardError=append:/var/log/coredns.err.log


[Install]
WantedBy=multi-user.target
```


```bash
vi db.int.wmbakopt.com
```


```bash
$TTL 86400
@   IN  SOA picasso.int.wmbakopt.com. admin.int.wmbakopt.com. (
        2025052702 ; Serial
        3600       ; Refresh
        1800       ; Retry
        1209600    ; Expire
        86400 )    ; Minimum TTL

          IN  NS      picasso.int.wmbakopt.com.
monet    IN  A       10.10.10.2
picasso   IN  A       10.10.10.3
dali      IN  A       10.10.10.4
renoir    IN  A       10.10.10.254
```

```bash
vi db.10.10.10.in-addr.arpa
```

```bash
$TTL 86400
@   IN  SOA picasso.int.wmbakopt.com. admin.int.wmbakopt.com. (
        2025052702 ; Serial
        3600       ; Refresh
        1800       ; Retry
        1209600    ; Expire
        86400 )    ; Minimum TTL

    IN  NS      picasso.int.wmbakopt.com.
2   IN  PTR     monet.int.wmbakopt.com.
3   IN  PTR     picasso.int.wmbakopt.com.
4   IN  PTR     dali.int.wmbakopt.com.
254 IN  PTR     renoir.int.wmbakopt.com.
```

```bash
systemctl daemon-reload
```

```bash
systemctl start coredns.service && systemctl status coredns.service
```

```bash
systemctl enable coredns.service
```



Tests :

```bash
apt install dnsutils -y
```

```bash
dig @127.0.0.1 nomDuServeur.int.wmbakopt.com
```

```bash
nslookup picasso.int.wmbakopt.com 10.10.10.3
```

```bash
dig -x 10.10.10.3 @10.10.10.3
```


**PS:** Pour toutes les machines qui ont une configuration statique de l'adresse ip ne pas oublier de rajouter :

- ```dns-search int.wmbakopt.com``` dans le fichier ```/etc/network/interfaces```

puis 

```bash
apt update
```


```bash
apt install resolvconf
```


```bash
systemctl enable resolvconf --now
```

```bash
systemctl restart networking
```

```bash
cat /etc/resolv.conf
```

On doit voir ``search int.wmbakopt.com`` .


### Réalisation de tests

1. Vérifier que le serveur  bien chargé la zone

```bash
dig @127.0.0.1 dali.wmbakopt.lan
```

2. Faire une requête DNS pour résoudre le nom monet en utilisant le serveur DNS par défaut configuré sur la machine (cf. /etc/resolv.conf sur Linux)

```bash
nslookup monet
```

3. Effectuer une résolution inverse DNS (reverse DNS lookup), c’est-à-dire qu’elle essaie de retrouver le nom d’hôte (hostname) associé à l’adresse IP 10.10.10.2

```bash
nslookup 10.10.10.2
```

4.  Tester une requête DNS depuis un client

```bash
dig dali.wmbakopt.lan
```


## 📦 Installation et configuration du serveur DNS secondaire

### Configuration du réseau

```bash
apt update
```

```bash
vi /etc/network/interfaces
```

```bash
# The loopback network interface
auto lo
iface lo inet loopback

auto ens18
iface ens18 inet static
address 10.10.10.3
netmask 255.255.255.0
gateway 10.10.10.254
dns-search int.wmbakopt.com
dns-nameservers 8.8.8.8
dns-nameservers 10.10.10.3 10.10.10.4
```

```bash
systemctl restart networking && systemctl status networking
```

```bash
vi /etc/apt/sources.list
```

```bash
deb https://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb-src https://deb.debian.org/debian bookworm main contrib non-free non-free-firmware

deb https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware

deb https://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
deb-src https://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
```

```bash
apt update
```

### Installation du serveur

```bash
adduser --system --group --shell "/usr/sbin/nologin" --comment "CoreDNS" --home "/etc/coredns" coredns
```

```bash
wget https://github.com/coredns/coredns/releases/download/v1.12.1/coredns_1.12.1_linux_amd64.tgz
```

```bash
tar -xvf coredns_1.12.1_linux_amd64.tgz
```

```bash
install coredns /usr/bin/
```

```bash
mkdir /var/lib/coredns
```

```bash
cd /etc/coredns
```

```bash
vi Corefile
```

```
int.wmbakopt.com  {
    secondary {
        transfer from 10.10.10.3
    }
}


10.10.10.in-addr.arpa {
    secondary {
        transfer from 10.10.10.3
    }
}


. {
    forward . 1.1.1.1
    log
    errors
}
```

```bash
vi /etc/systemd/system/coredns.service
```

```
[Unit]
Description=CoreDNS DNS server
Documentation=https://coredns.io
After=network.target

[Service]
PermissionsStartOnly=true
LimitNOFILE=1048576
LimitNPROC=512
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
NoNewPrivileges=true
User=coredns
WorkingDirectory=/var/lib/coredns
ExecStart=/usr/bin/coredns -conf=/etc/coredns/Corefile
ExecReload=/bin/kill -SIGUSR1 $MAINPID
Restart=on-failure
StandardOutput=append:/var/log/coredns.log
StandardError=append:/var/log/coredns.err.log


[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
```

```bash
systemctl start coredns.service && systemctl status coredns.service
```

```bash
systemctl enable coredns.service
```


### Réalisation de tests

1. Vérifier que le serveur secondaire a bien chargé la zone

```bash
dig @127.0.0.1 dali.wmbakopt.lan
```

2. Faire une requête DNS pour résoudre le nom monet en utilisant le serveur DNS secondaire

```bash
nslookup monet 10.10.10.4
```

3. Effectuer une résolution inverse DNS (reverse DNS lookup) en utilisant le serveur DNS secondaire

```bash
nslookup 10.10.10.2 10.10.10.4
```


4. Simuler la panne du serveur DNS primaire

Arrête CoreDNS sur le serveur primaire (10.10.10.3) :

```bash
systemctl stop coredns
```

5.  Tester une requête DNS depuis un client

```bash
dig dali.wmbakopt.lan
```

## Commandes utiles

| Élément                                      | Commande / Information                                                    |
|---------------------------------------------|----------------------------------------------------------------------------|
| **Nom du paquet**                           | coredns                                                                    |
| **Fonction**                                | Serveur DNS                                                                |
| **Installer le paquet**                     | cf. `https://coredns.io/manual/toc/#installation9`                         |
| **Lister les services installés par le paquet** | /                                                                      |
| **Nom du service**                          | `coredns`                                                                  |
| **Activer le service**                      | `systemctl enable coredns`                                                 |
| **Désactiver le service**                   | `systemctl disable coredns`                                                |
| **Démarrer le service**                     | `systemctl start coredns`                                                  |
| **Redémarrer le service**                   | `systemctl restart coredns`                                                |
| **Voir le statut du service**              | `systemctl status coredns`                                                  |
| **Stopper le service**                      | `systemctl stop coredns`                                                   |
| **Rechercher les fichiers de configuration**| `find / -name "Corefile"`                                                  |
| **Voir les fichiers de zone**               | Soit tout est configuré directement dans le Corefile, soit on utilise le plugin file, on crée les fichiers de zone manuellement et on indique leur chemin dans le Corefile.            |
| **Les fichiers de configuration**           |`/etc/coredns/Corefile`                                                     |
| **Fichiers de log**                         | `journalctl -u coredns`                                                    |
| **Voir les enregistrements DNS**            | CoreDNS ne permet pas d'afficher directement son cache (sauf avec plugins et logs) |
| **Tester la config**                        | `coredns -conf /etc/coredns/Corefile`                                      |
| **Afficher les logs**                       | `journalctl -u coredns`                                                    |
| **Afficher uniquement les logs d'erreur**   | `journalctl -u coredns -p err`                                             |
| **Suivre les logs en direct**               | `journalctl -u coredns -f`                                                 |