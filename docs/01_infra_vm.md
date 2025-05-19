# 01 - Infrastructure VM Moodle

## Description de la VM
- Hyperviseur : Proxmox VE.  
- OS : Debian 12.7 minimal, template durci (cloud-init, fail2ban, firewall, mises à jour automatisées).  
- Ressources recommandées :  
  - CPU : 4 vCPU (peut évoluer selon charge)  
  - RAM : 8 Go minimum  
  - Stockage : 80 Go SSD (incluant système + moodledata + base de données)

## Réseau
- IP statique dédiée sur le réseau interne de la mairie.  
- Nom d’hôte : `moodle.mairie.local`.  
- Configuration réseau statique via netplan ou fichier `/etc/network/interfaces`.

## Sécurité de la VM
- Firewall local (UFW ou nftables).  
- SSH configuré avec clé uniquement.  
- Surveillance avec Fail2ban.  
- Mise à jour automatique des paquets critiques.

---
