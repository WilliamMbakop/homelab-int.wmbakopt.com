# 🏠 Homelab Personnel - Domaine `int.wmbakopt.com`

Ce dépôt documente l'installation, la configuration et l'organisation de mon homelab personnel dans le but d'approfondir mes compétences en administration système et réseau.

---

## 🎯 Objectifs du Homelab

- Mettre en œuvre un environnement réseau complet virtualisé
- Maîtriser les outils d'administration système (Linux/Windows)
- Configurer des services essentiels (DHCP, DNS, firewall, supervision)
- Préparer les certifications (CCNA, Network+)

---

## 🧱 Infrastructure

| Nom de la VM    | Composant  | Rôle                  | IP           |
| --------------- | ---------- | --------------------- | ------------ |
| `pve01`         | Proxmox VE | Hyperviseur principal | 10.10.10.1   |
| `renoir`        | pfSense    | Firewall & NAT        | 10.10.10.254 |
| `monet`         | Kea DHCP   | Attribution IP        | 10.10.10.2   |
| `picasso`       | CoreDNS    | DNS primaire          | 10.10.10.3   |
| `dali`          | CoreDNS    | DNS secondaire        | 10.10.10.4   |
| `matisse`       | Zabbix     | Supervision           | 10.10.10.5   |


---

## 🔧 Services Documentés

- [Architecture du réseau](docs/1-architecture.md)
- [Gestion des VMs (Proxmox)](docs/2-proxmox.md)
- [Serveur DHCP (Kea)](docs/3-dhcp.md)
- [Serveur DNS (CoreDNS)](docs/4-dns.md)
- [Firewall (pfSense)](docs/5-pfsense.md)
- [Supervision (Zabbix)](docs/6-zabbix.md)
- [Serveur VPN (OpenVPN)](docs/7-openvpn.md)

---

## 🔗 Liens utiles

[![Portfolio](https://wmbakopt.com)](https://wmbakopt.com)  
[![LinkedIn](https://www.linkedin.com/in/mbakopwilliam/)](https://www.linkedin.com/in/mbakopwilliam/)
