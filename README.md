# Moodle Mairie – Plateforme de formation interne

## 📘 Description
Projet de déploiement sécurisé de Moodle pour la formation des agents de la mairie.

## 🛠️ Stack technique
- Debian 12.7
- Nginx + PHP 8.1 + MariaDB
- Moodle 4.3.x
- Accès interne uniquement (VPN si besoin)
- Durcissement selon les recommandations ANSSI

## 📂 Arborescence du dépôt
...

## 📦 Installation rapide
1. Cloner le dépôt
2. Lancer `scripts/install_moodle.sh`
3. Suivre les étapes dans `docs/03_installation_moodle.md`

## 🔒 Sécurité
- Firewall UFW
- Fail2ban
- SSL interne
- Mises à jour automatiques activées

## 🧪 Tests fonctionnels
Tests réalisés : accès utilisateur, quiz, fichiers, création de cours…

## 👤 Auteur
Ton prénom + nom (si tu veux), lien LinkedIn, école...
