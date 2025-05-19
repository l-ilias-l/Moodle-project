# 🛠️ Étape 0 – Préparation de l’environnement

---

## 1. Définition des besoins techniques et fonctionnels

### 🔧 Besoins fonctionnels
- Permettre à ~1000 employés d’accéder à des formations Moodle.
- Utilisation principalement non simultanée.
- Interface web simple, rapide et stable.
- Gestion des utilisateurs, cours, ressources, forums, quiz.
- Accès réseau sécurisé (interne uniquement, VPN envisagé plus tard).
- Mise en place d’un système de sauvegarde fiable.
- Accès administrateur et support technique documenté.

### 🖥️ Besoins techniques
- VM dédiée sous Debian 12.7 durcie.
- Serveur web : Nginx.
- Interpréteur PHP 8.1 ou plus.
- Base de données MariaDB.
- Système de fichiers séparé pour `moodledata`.
- Système de journalisation (logs) et surveillance de sécurité (Fail2ban, mises à jour auto).
- Accès SSH sécurisé.
- Adresse IP statique pour accès interne fiable.

---

## 2. Planification des ressources

| Ressource   | Estimation recommandée | Justification                                          |
|-------------|------------------------|--------------------------------------------------------|
| CPU         | 4 vCPU                 | Pour supporter Moodle, PHP, MariaDB, et connexions web |
| RAM         | 8 Go                   | Moodle + OS + MariaDB + PHP-FPM + cache OPCache        |
| Stockage    | 80 Go SSD              | Système + Moodle + `moodledata` + base + logs          |
| Réseau      | IP statique            | Nécessaire pour accès interne sans interruption         |

> ⚠️ Ces ressources peuvent être adaptées selon l’usage réel. Il est recommandé de monitorer la charge pendant les premières semaines.

---

## 3. Création d’un template Debian 12 durci

### 🔐 Objectif
Créer un template Debian 12.7 **fiable et sécurisé**, pour pouvoir facilement cloner une VM Moodle sans tout reconfigurer à chaque fois.

### 🧱 Étapes de durcissement

1. **Installation Debian 12 minimal**
   - Depuis l’ISO officielle netinst.
   - Sélection uniquement des composants nécessaires.

2. **Configuration réseau**
   - IP statique pour la VM Moodle.
   - (Template peut rester en DHCP initialement pour flexibilité).

3. **Sécurisation SSH**
   - Désactiver l’accès root par mot de passe (`PermitRootLogin no`).
   - Forcer l’authentification par clé (`PasswordAuthentication no`).
   - Optionnel : changer le port SSH (ex : `2222`).
   - Création d’un utilisateur dédié (ex : `sysadmin`).

4. **Installation de fail2ban**
   - Protection contre les attaques par brute-force sur SSH.
   - Surveillance des journaux d’authentification.

5. **Pare-feu (UFW ou nftables)**
   - Blocage par défaut de toutes les connexions entrantes.
   - Ouverture uniquement des ports nécessaires (SSH, HTTP, HTTPS).

6. **Mises à jour automatiques**
   - Installer et configurer `unattended-upgrades`.
   - Assurer la mise à jour régulière des paquets critiques de sécurité.

7. **Paramètres système**
   - Désactivation des services inutiles.
   - Limitation des droits root.
   - Vérification des permissions système critiques.

8. **Installation de cloud-init**
   - Pour automatiser les configurations (hostname, utilisateur, clé SSH…) lors du clonage.
   - Intégration avec Proxmox pour faciliter le déploiement.

---

📌 Prochaine étape : Installation propre et sécurisée de Debian 12.7 minimal avec les premières mesures de durcissement.  
Souhaites-tu commencer avec l’installation Debian ou directement la configuration du SSH et du durcissement ?
