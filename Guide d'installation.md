# 📘 Guide complet d’installation de Moodle sur Debian 12 avec NGINX, MariaDB et PHP 8.2

## 🧰 Prérequis

* **VM Debian 12** à jour
* Accès root ou `sudo`
* Services installés :

  * `nginx`, `mariadb-server`, `php8.2`, `php8.2-fpm` + modules requis
  * Exemple : `apt install php8.2-{fpm,cli,common,gd,mbstring,intl,mysql,xml,zip,curl}`
* Nom de domaine ou IP locale
* Accès web à Moodle via navigateur

---

## 📦 Étape 1 – Téléchargement de Moodle

```bash
cd /var/www/html
wget https://download.moodle.org/latest.zip
unzip latest.zip
mv moodle/ /var/www/html/moodle
mkdir /var/www/moodledata
chown -R www-data:www-data /var/www/html/moodle /var/www/moodledata
chmod -R 755 /var/www/html/moodle /var/www/moodledata
```

---

## 🛠 Étape 2 – Configuration NGINX

Fichier `/etc/nginx/sites-available/moodle` :

```nginx
server {
    listen 80;
    server_name moodle.local;  # ou votre IP ou nom DNS

    root /var/www/html/moodle;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires max;
        log_not_found off;
    }
}
```

Activation :

```bash
ln -s /etc/nginx/sites-available/moodle /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

---

## 💃 Étape 3 – Création de la base de données

```bash
mysql -u root -p

CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'sysadmin'@'localhost' IDENTIFIED BY 'SuperMotDePasse!';
GRANT ALL PRIVILEGES ON moodle.* TO 'sysadmin'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## ⚠️ Problèmes rencontrés et comment les éviter

### ❌ 1. *Erreur max\_input\_vars*

> **Message** : *PHP setting max\_input\_vars must be at least 5000*

**Correction** :

```bash
nano /etc/php/8.2/fpm/php.ini
```

Modifier :

```ini
max_input_vars = 5000
```

Puis :

```bash
systemctl restart php8.2-fpm
```

---

### ❌ 2. *Erreur de droits DELETE sur une table pendant l’installation*

> `DELETE command denied to user 'moodleroot'`

**Cause** : installation lancée avec `moodleroot`, puis changement d’utilisateur `sysadmin`, mais des objets ont été créés avec les droits de l’ancien.

**Solution propre** :

```bash
mysql -u root -p -e "DROP DATABASE moodle; CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
rm /var/www/html/moodle/config.php
```

---

### ❌ 3. *Erreur "Config table does not contain the version"*

**Cause** : base mal initialisée ou table `config` vide ou absente.

**Solution** : supprimer la base + `config.php`, puis **recommencer** l’installation dans le navigateur.

---

### ❌ 4. *Erreur de lecture de la base de données (generic DB read error)*

**Causes possibles** :

* Mauvais utilisateur/MDP en base
* `config.php` contient encore les anciennes infos
* La base est vide, Moodle n’a pas été installé

**Solution** :

* Supprimer `config.php` à la racine de Moodle
* S’assurer que l’utilisateur a les bons droits (`GRANT ALL ON moodle.* TO 'sysadmin'@'localhost';`)
* Vérifier que la base n’est pas vide :

```bash
mariadb -u sysadmin -p -D moodle -e "SHOW TABLES;"
```

---

### ❌ 5. *CSS/Mise en page Moodle très moche (HTML brut)*

**Cause** : Moodle n’arrive pas à charger les fichiers JS/CSS → erreurs `404`.

**Solution** :

* Vérifier que tous les chemins sont bons dans NGINX
* Assurez-vous que `$CFG->wwwroot` dans `config.php` correspond à votre IP ou domaine :

```php
$CFG->wwwroot = 'http://10.10.153.101'; // par exemple
```

* Vérifier les logs :

```bash
tail -f /var/log/nginx/access.log /var/log/nginx/error.log
```

---

## ✅ Étape finale – Lancement de l’installation

Une fois la base et les permissions prêtes, relancer le navigateur sur :
**http\://<IP>/moodle/**

Suivre les étapes :

* Choix de la langue
* Chemins :

  * Moodle : `/var/www/html/moodle`
  * Moodledata : `/var/www/moodledata`
* Connexion base : `sysadmin`, `moodle`, `localhost`

---

## 🔒 Sécurisation post-installation

* Supprimer les fichiers `.zip` inutiles
* Configurer HTTPS avec Let's Encrypt ou certificat interne
* Restreindre l'accès à `/admin` (via IP ou auth)
* Sécuriser MariaDB (supprimer utilisateurs anonymes, test db, etc.)

---

## 🧺 Script de test MySQL depuis PHP

```php
<?php
$link = mysqli_connect('localhost', 'sysadmin', 'motdepasse', 'moodle');
if (!$link) {
    die('Connexion échouée : ' . mysqli_connect_error());
}
echo 'Connexion réussie !';
mysqli_close($link);
?>
```

---
