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

- [Architecture du réseau](docs/architecture.md)
- [Serveur DHCP (Kea)](docs/dhcp.md)
- [Serveur DNS (CoreDNS)](docs/dns.md)
- [Firewall (pfSense)](docs/firewall.md)
- [Gestion des VMs (Proxmox)](docs/proxmox.md)
- [Supervision (Zabbix/Nagios)](docs/supervision.md)

---

## 🖼️ Schéma réseau

En cours de réalisation

---

## 🔗 Liens utiles

[![Portfolio](https://wmbakopt.com)](https://wmbakopt.com)  
[![LinkedIn](https://www.linkedin.com/in/mbakopwilliam/)](https://www.linkedin.com/in/mbakopwilliam/)
