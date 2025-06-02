# 📡 Serveur DHCP - Kea DHCP (10.10.10.2)

## 🎯 Objectif

Ce serveur a pour rôle d'attribuer automatiquement des adresses IP et de transmettre les paramètres réseau aux clients du LAN `10.10.10.0/24`.


## 🧱 Environnement

- **Nom d'hôte** : `monet`
- **Adresse IP** : `10.10.10.2`
- **Système** : `Debian 12`
- **Service principal** : `kea-dhcp4-server`
- **Domaine géré** : `int.wmbakopt.com`


## Configuration du réseau

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
address 10.10.10.2
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

## Installation et configuration du serveur DHCP

```bash
apt install kea -y
```

Effectuer les modifications sur le fichier kea-dhcp4.conf

```bash
cd /etc/kea
```

```bash
cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.original
```

```bash
vi kea-dhcp4.conf
```

```bash
{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": [ "ens18" ]
    },
    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/kea-leases4.csv"
    },
    "subnet4": [
      {
        "id": 1,
        "subnet": "10.10.10.0/24",
        "pools": [
          {
            "pool": "10.10.10.30 - 10.10.10.60"
          }
        ],
        "reservations": [
          {
            "hostname": "van-gogh", "hw-address": "bc:24:11:a5:85:d4", "ip-address": "10.10.10.30",
          }
        ],
        "option-data": [
          {
            "name": "routers",
            "code": 3,
            "space": "dhcp4",
           "data": "10.10.10.254"
          },
          {
            "name": "domain-name-servers",
            "code": 6,
            "space": "dhcp4",
            "data": "10.10.10.3, 10.10.10.4"
          },
          {
            "name": "domain-search",
            "data": "int.wmbakopt.com"
          }
        ]
      }
    ],
    "loggers": [
      {
        "name": "kea-dhcp4",
        "output_options": [
          {
            "output": "/var/log/kea/kea-dhcp4.log"
          }
        ],
        "severity": "INFO",
        "debuglevel": 0
      }
    ],
    "valid-lifetime": 3600,
    "renew-timer": 900,
    "rebind-timer": 1800
  }
}
```


```bash
kea-dhcp4 -t /etc/kea/kea-dhcp4.conf
```


```bash
apt install jq -y
```

```bash
cat /etc/kea/kea-dhcp4.conf | jq .
```


```bash
systemctl enable kea-dhcp4-server
```

```bash
systemctl start kea-dhcp4-server && systemctl status kea-dhcp4-server
```


## Commandes utiles : 

 | Élément                                         | Commande / Information                                                                               |
|-------------------------------------------------|------------------------------------------------------------------------------------------------------|
| **Nom du paquet**                               | kea-dhcp4-server                                                                                     |
| **Fonction**                                    | Serveur DHCP                                                                                         |
| **Installer le paquet**                         | `apt install kea-dhcp4-server`                                                                       |
| **Lister les services installés par le paquet** | `dpkg -L kea-dhcp4-server \| grep systemd`                                                           |
| **Nom du service**                              | `kea-dhcp4.service`                                                                                  |
| **Activer le service**                          | `systemctl enable kea-dhcp4.service`                                                                 |
| **Désactiver le service**                       | `systemctl disable kea-dhcp4.service`                                                                |
| **Démarrer le service**                         | `systemctl start kea-dhcp4.service`                                                                  |
| **Redémarrer le service**                       | `systemctl restart kea-dhcp4.service`                                                                |
| **Voir le statut du service**                   | `systemctl status kea-dhcp4.service`                                                                 |
| **Stopper le service**                          | `systemctl stop kea-dhcp4.service`                                                                   |
| **Rechercher les fichiers de configuration**    | `dpkg -L kea-dhcp4-server \| grep /etc/`                                                             |
| **Les fichiers de configuration**               | `/etc/kea/kea-dhcp4.conf`                                                                            |
| **Fichiers de log**                             | `/var/log/kea/kea-dhcp4.log`                                                                         |
| **Fichier de location leases**                  | `/var/lib/kea/kea-leases4.csv`                                                                       |
| **Tester la config**                            | `kea-dhcp4 -t -c /etc/kea/kea-dhcp4.conf`                                                            |
| **Afficher les logs**                           | `journalctl -u kea-dhcp4.service\ngrep kea /var/log/syslog`                                          |
| **Afficher uniquement les logs d'erreur**       | `journalctl -u kea-dhcp4.service -p err\ngrep kea /var/log/syslog \| grep -i error`                  |
| **Suivre les logs en direct**                   | `journalctl -u kea-dhcp4.service -f`                                                                 |
| **Voir le DORA**                                | ` sudo apt update && sudo apt install tcpdump -y` PUIS `tcpdump -i ens18 -n -vvv port 67 or port 68` |