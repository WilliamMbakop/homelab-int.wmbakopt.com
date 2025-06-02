🔰 Routeur / Pare-feu pfSense

# Présentation

pfSense est une distribution libre basée sur FreeBSD, dédiée aux fonctions de pare-feu, routeur et autres services réseau comme le NAT, VPN, DHCP, DNS, etc. Elle est utilisée ici comme routeur principal dans le homelab.

#  Architecture Réseau

WAN : 192.168.1.205 (passerelle : 192.168.1.254)

LAN : 10.10.10.254

Domaine : int.wmbakopt.com

# Installation de pfSense

- Télécharger l’ISO sur : https://www.pfsense.org/download/

- Installer sur une VM avec 2 interfaces réseau :

    - WAN connecté à une interface bridge vers le réseau réel

    - LAN connecté au réseau interne Proxmox

- Attribuer les IP manuellement


# Configuration initiale

Accès via WebGUI : http://10.10.10.254

Identifiants par défaut :

Utilisateur : admin

Mot de passe : pfsense

Changer le mot de passe immédiatement

🔥 Règles de pare-feu

Mise en place de règles de pare-feu permettant par exemple :

- l'accès à un seul équipement (par son adresse ip) au réseau LAN concernant les protocoles TCP et ICMP

- la possibilité pour les équipements du LAN d'accéder à Internet












