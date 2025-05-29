# 🖥️ Hyperviseur - Proxmox VE

## 🎯 Objectif

Proxmox VE est l’hyperviseur principal de mon homelab. Il me permet de gérer efficacement un environnement virtualisé complet, utilisé à la fois pour mes projets personnels, mes expérimentations techniques, et ma montée en compétences pour le BTS SISR.


## 🧱 Infrastructure Proxmox

| Élément             | Détail                             |
|---------------------|------------------------------------|
| Hyperviseur         | Proxmox VE 8.4.1                   |
| Type de serveur     | Machine physique dédiée            |
| Stockage principal  | LVM Thin                           |
| Sauvegardes         | Prochainement                      |
| Accès réseau        | Interfaces `vmbr0` et `vmbr0`      |
| Domaine             | `int.wmbakopt.com`                 |
| IP de gestion       | `10.10.10.1`                       |



## ⚙️ Configuration réseau (Proxmox)


```bash
auto lo
iface lo inet loopback

auto vmbr0
iface vmbr0 inet static
        address 192.168.1.201/24
        gateway 192.168.1.254
        bridge-ports enp6s0
        bridge-stp off
        bridge-fd 0

auto vmbr1
iface vmbr1 inet static
        address 10.10.10.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0
```