# 📘 Plan détaillé du projet Moodle – Formation interne Mairie

---

## 🧩 1. Contexte du projet

- **But** : Permettre aux employés de la mairie (~1000 personnes) d'accéder à des formations internes via Moodle.  
- **Accès** : Interne uniquement (VPN si besoin ultérieur).  
- **Infrastructure** : VM dédiée sur un hyperviseur Proxmox, système Debian 12.7, IP statique.  
- **Objectif** : Déployer une solution Moodle stable, sécurisée, maintenable et documentée.

---

## 🏗️ 2. Étapes du projet

### Étape 0 : Préparation de l’environnement
- Définition des besoins techniques et fonctionnels.  
- Planification des ressources (RAM, CPU, stockage, réseau).  
- Création d’un template Debian 12 durci (cloud-init, SSH, fail2ban, firewall, mises à jour automatiques, etc.).

### Étape 1 : Création de la VM Moodle
- Clonage à partir du template durci.  
- Attribution IP statique.  
- Nom d’hôte propre (ex. `moodle.mairie.local`).  
- Configuration initiale (hostname, timezone, NTP, locales…).

### Étape 2 : Installation de la stack logicielle
- Serveur Web : Nginx.  
- PHP 8.1 ou supérieur.  
- Base de données : MariaDB.  
- Configuration SSL (auto-signé ou Let's Encrypt interne).  
- Sécurisation du stack (droits, pare-feu, tuning PHP/MariaDB…).

### Étape 3 : Déploiement de Moodle
- Téléchargement de Moodle.  
- Configuration du fichier `config.php`.  
- Initialisation via l’interface web.  
- Création des premiers utilisateurs/admins.

### Étape 4 : Sécurisation et durcissement
- Droits système (www-data, `moodledata` en dehors du root web).  
- Suppression des fichiers d’installation inutiles.  
- Configuration UFW / nftables.  
- Mise en place de Fail2ban, gestion des logs, journalctl.  
- Surveillance des mises à jour Moodle et paquets système.

### Étape 5 : Tests fonctionnels
- Création d’un cours test.  
- Inscription d’utilisateurs.  
- Téléversement de fichiers, forums, quiz…  
- Vérification des logs et du comportement système.

### Étape 6 : Mise en service / documentation
- Documentation technique complète (voir GitHub plus bas).  
- Plan de sauvegarde (base de données + `moodledata`).  
- Support utilisateurs et gestion des comptes administrateurs secondaires.

---
