# 02 - Stack logicielle Moodle

## Composants principaux
- **Serveur web** : Nginx (performant et léger).  
- **PHP** : Version 8.1 ou supérieure, avec extensions nécessaires pour Moodle (gd, curl, intl, mbstring, xml, zip, etc.).  
- **Base de données** : MariaDB (compatibilité et performance).

## Configuration recommandée
- PHP-FPM configuré pour performance et sécurité (limitation mémoire, max execution time, etc.).  
- MariaDB sécurisé avec utilisateur dédié Moodle, mot de passe fort, accès restreint à la VM Moodle.  
- SSL configuré (certificat auto-signé ou interne Let's Encrypt).  
- Permissions strictes sur les fichiers Moodle et le dossier `moodledata`.

## Optimisations possibles
- Caching OPCache activé sur PHP.  
- Tuning MariaDB pour améliorer les requêtes fréquentes Moodle.  
- Compression gzip activée sur Nginx.

---
