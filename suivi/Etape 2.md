# 📦 Étape 2 – Installation de la stack logicielle (Moodle)

---

## ✅ Objectif

Installer et configurer la stack nécessaire au fonctionnement de Moodle sur Debian 12.7 :

* Serveur web (Nginx)
* Moteur PHP 8.2
* Base de données (MariaDB)
* SSL via certificat auto-signé (provisoire)
* Sécurisation des composants

---

## 🧱 1. Mise à jour du système

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🌐 2. Installation de Nginx

```bash
sudo apt install nginx -y
sudo systemctl enable nginx --now
```

Vérifier le service :

```bash
curl http://localhost
```

---

## 💘 3. Installation de PHP 8.2

```bash
sudo apt install php php-fpm php-mysql php-curl php-gd php-intl php-xmlrpc php-mbstring php-xml php-zip php-soap php-cli php-bcmath php-readline php-opcache php-common -y
```

Vérification :

```bash
php -v
```

---

## 💄️ 4. Installation de MariaDB

```bash
sudo apt install mariadb-server -y
sudo systemctl enable mariadb --now
```

Sécurisation de l’installation :

```bash
sudo mysql_secure_installation
```

---

## 💪 5. Création de la base Moodle

```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'moodleuser'@'localhost' IDENTIFIED BY 'MotDePasseSolide!';
GRANT ALL PRIVILEGES ON moodle.* TO 'moodleuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 🔐 6. Génération d’un certificat auto-signé (temporaire)

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/moodle-selfsigned.key \
-out /etc/ssl/certs/moodle-selfsigned.crt
```

---

## ⚙️ 7. Configuration Nginx pour Moodle

Créer le fichier :

```bash
sudo nano /etc/nginx/sites-available/moodle
```

Contenu recommandé :

```nginx
server {
    listen 80;
    server_name moodle.mairie.local;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name moodle.mairie.local;

    ssl_certificate /etc/ssl/certs/moodle-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/moodle-selfsigned.key;

    root /var/www/moodle;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ [^/]\.php(/|$) {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Activer la configuration :

```bash
sudo ln -s /etc/nginx/sites-available/moodle /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🧺 8. Tuning PHP

Fichier : `/etc/php/8.2/fpm/php.ini`

Recommandations :

```ini
upload_max_filesize = 100M
post_max_size = 100M
max_execution_time = 300
memory_limit = 256M
```

Redémarrer PHP-FPM :

```bash
sudo systemctl restart php8.2-fpm
```

---

## 📄 Mémo technique : Transition certificat auto-signé → Let's Encrypt (Moodle)

### 🔹 1. Situation actuelle

Le serveur Moodle est en accès interne (pas de nom de domaine public).
Impossible pour le moment d’utiliser Let’s Encrypt (besoin d’un DNS + accès HTTP).

### ✅ Solution temporaire : certificat auto-signé

Tu peux générer un certificat auto-signé :

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/moodle-selfsigned.key \
-out /etc/ssl/certs/moodle-selfsigned.crt
```

> 🧐 Common Name (CN) = ton IP locale ou FQDN interne

#### Exemple config Nginx :

```nginx
server {
    listen 443 ssl;
    server_name 10.10.x.x;

    ssl_certificate /etc/ssl/certs/moodle-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/moodle-selfsigned.key;

    # ... configuration Moodle ...
}
```

### ⭯️ Passage à Let's Encrypt (plus tard)

Quand le DNS sera disponible :

1. Ouvre temporairement le port 80.
2. Installe Certbot :

```bash
sudo apt install certbot python3-certbot-nginx -y
```

3. Lance :

```bash
sudo certbot --nginx
```

### 🔒 Bonnes pratiques :

* Ferme le port 80 après l’émission du certificat.
* Si usage long d’un auto-signé : tu peux importer le certificat manuellement dans les navigateurs internes.

---

## 📌 Résumé des composants

| Composant | Version    | Statut |
| --------- | ---------- | ------ |
| Nginx     | stable     | ✅      |
| PHP       | 8.2        | ✅      |
| MariaDB   | 10.x       | ✅      |
| SSL       | Auto-signé | ✅      |

---
