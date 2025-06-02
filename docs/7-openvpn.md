# Création d’une autorité de certification (CA)

- Aller dans System > Certificates > Authorithies

- Cliquer sur Add

- Remplir les champs :

    - Method: Create an internal Certificate Authority

    - Descriptif : "MyVPN-CA" (ou ce que tu veux)

    - Remplir les infos (Pays, Organisation, etc.)

- Sauvegarder


# Création d’un certificat serveur

- Aller dans System > Certificates > Certificates

- Cliquer sur Add/Sign

- Remplir les champs :

    - Method : Create an internal certificate

    - Descriptive name : Donner un nom (ex : OpenVPN-Server-Cert)

    - Certificate authority : Sélectionner le certificat créé précédemment

    - Remplir les infos demandées (Country Code, State or Province, City, Organization)

- Sauvegarder


# Configuration du serveur OpenVPN

- Aller dans VPN > OpenVPN > Servers

- Cliquer sur Add

- Remplir les champs :

    - Server Mode : Remote Access (SSL/TLS + User Auth)

    - Backend for authentication : Local Database

    - Device mode : tun (pour un routage classique IP)

    - Protocol : UDP

    - Interface : WAN

    - Local port : 1194

    - TLS Configuration : activer la case (génère une clé TLS-Auth)

    - Peer Certificate Authority : sélectionner le certificat authorité créé précédemment

    - Server Certificate : sélectionner le certificat serveur créé précédemment

    - DH Parameter Length : 2048 bits 

    - Data Encryption Algorithms : AES-256-GCM 

    - Tunnel Network : 10.10.50.0/24 (Il s'agit d'un réseau VPN virtuel)

    - Redirect IPv4 Gateway : cocher cette case pour que tout le trafic client passe par le VPN

    - IPv4 Local Network/s : ton réseau LAN, par exemple 192.168.1.0/24

- Enregistrer


# Ajouter des utilisateurs VPN

- Aller dans System > User Manager

- Cliquer sur Add

- Remplir les champs :

    - Username

    - Password

    - Descriptive name : vpnuser-cert

    - Certificate authority : choisir le certificat précédemment créé

    - Certificate Type : User Certificate

- Enregistrer


# : Exporter la configuration client OpenVPN

- Installer le package openvpn-client-export (System > Package Manager > Available Packages)

- Une fois installé, va dans VPN > OpenVPN > Client Export

- Remote Access Server: sélectionner le serveur VPN

- Host Name Resolution : sélectionner Other

- Host Name : Mettre L'adresse IP publique du serveur (cf. https://www.mon-ip.io pour connaitre son adresse ip publique)

- Dans OpenVPN Clients, télécharger le client correspondant (Windows, Mac, Android...) avec certificat et configuration inclus

- Envoyer ce fichier au client pour se connecter


# Configuration du firewall


- Aller dans Firewall > Rules > OpenVPN

- Ajouter qu’une règle autorise le trafic VPN vers le LAN


# Effectuer une redirection de port (si besoin)

Depuis l'interface web de la box, effectuer une redirection de port vers le port 1194 


# Test de connexion

- Installer le client OpenVPN sur un poste distant

- Importer la configuration exportée

- Se connecter

On pouvoir accéder à toutes les machines du LAN