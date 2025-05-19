# 00 - Contexte du projet Moodle Mairie

## Objectif général
Permettre aux employés de la mairie (~1000 utilisateurs) d'accéder à des formations internes via une plateforme Moodle stable, sécurisée et maintenable.

## Enjeux
- Accessibilité interne uniquement (accès VPN si nécessaire).  
- Gestion d’une charge utilisateur importante mais pas simultanée.  
- Sécurisation de la VM et de la plateforme selon les recommandations de l’ANSSI.

## Infrastructure cible
- VM dédiée sous Debian 12.7 durci, hébergée sur un hyperviseur Proxmox.  
- Adresse IP statique réservée.  
- Stack logicielle composée de Nginx, PHP 8.1+, MariaDB.

## Contraintes
- Pas d’accès direct depuis l’extérieur sans VPN.  
- Sauvegardes régulières nécessaires.  
- Maintenance simple et évolutive.

---
