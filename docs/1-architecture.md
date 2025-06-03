# 🧱 Architecture du Homelab - Domaine `int.wmbakopt.com`

## 🎯 Objectif

Ce document présente l'architecture globale de mon homelab personnel. Cet environnement me permet de :
- simuler un réseau d'entreprise complet,
- pratiquer les compétences nécessaires à l'obtention du BTS SISR et à l'administration réseau/système,
- tester et documenter différents services et outils d'infrastructure.


## 🗺️ Topologie réseau

```
              +--------------------------+
              |        Internet          |
              +-----------+--------------+
                          |
              [Passerelle: 192.168.1.254]
                          |
               [WAN: 192.168.1.205]
                        pfSense
               [LAN: 10.10.10.254]
                          |
       +-----------------+------------------+
       |                 |                  |
+------+-----+   +-------+-----+    +-------+------+
| Proxmox VE |   | DNS Primaire |    | DNS Secondaire |
| 10.10.10.1 |   | 10.10.10.3   |    | 10.10.10.4     |
+------------+   +-------------+    +----------------+
       |
+--------------------------+
| VMs :                    |
| - Kea DHCP : 10.10.10.2  |
| - Zabbix : 10.10.10.5    |
| - Clients Linux/Windows  |
+--------------------------+
```

## 🧩 Détails des composants

| Composant      | Rôle                                      | Système            |
|----------------|-------------------------------------------|--------------------|
| **Proxmox VE** | Hyperviseur de toutes les VM              | Debian 12 + PVE    |
| **pfSense**    | Firewall, NAT, DHCP fallback              | FreeBSD/pfSense    |
| **Kea DHCP**   | Attribution dynamique d'adresses IP       | Debian 12          |
| **CoreDNS**    | Résolution DNS locale                     | Debian 12          |
| **Zabbix**     | Supervision réseau                        | Debian 12          |
| **Clients**    | Clients Linux et Windows simulés          | Debian, Windows 10 |


## 🌐 Configuration réseau

- **Sous-réseau LAN** : `10.10.10.0/24`
- **Passerelle par défaut** : `10.10.10.254` (pfSense)
- **DNS primaire** : `10.10.10.3`
- **DNS secondaire** : `10.10.10.4`
- **Nom de domaine interne** : `int.wmbakopt.com`
- nom de domaine public : wmbakopt.com
- portfolio : htttp://www.wmbakopt.com (en cours de mise en production)


