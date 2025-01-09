# HTaccess
live Coding fichier  htaccess


# Guide Complet de Configuration .htaccess

## 1. Introduction au Fichier .htaccess

### 1.1. Qu'est-ce que .htaccess ?
Le fichier .htaccess (Hypertext Access) est un fichier de configuration utilisé par le serveur web Apache. Il permet de modifier la configuration du serveur au niveau du répertoire, offrant ainsi un contrôle granulaire sur les fonctionnalités et les comportements du site web.

### 1.2. Rôle et utilité dans les projets web
Le fichier .htaccess permet de :
- Gérer les redirections d'URL
- Configurer la sécurité
- Optimiser les performances
- Personnaliser les pages d'erreur
- Gérer le cache et la compression
- Réécrire les URLs pour les rendre plus conviviales

### 1.3. Conditions préalables
```apache
# Vérifier que le module mod_rewrite est activé
<IfModule mod_rewrite.c>
    RewriteEngine On
</IfModule>

# Configuration dans le fichier httpd.conf ou apache2.conf
# AllowOverride All doit être défini pour le répertoire concerné
```

## 2. Structure et Localisation

### 2.1. Placement du fichier
Le fichier .htaccess doit être placé à la racine de votre site web ou dans le sous-répertoire où vous souhaitez appliquer les règles. Exemple d'arborescence :
```
www/
├── .htaccess           # Règles globales
├── public/
│   └── .htaccess      # Règles spécifiques au dossier public
└── assets/
    └── .htaccess      # Règles spécifiques aux assets
```

### 2.2. Héritage et priorités
Les règles sont héritées de manière hiérarchique. Les règles définies dans un sous-répertoire écrasent celles du répertoire parent.


## 3. Configurations de Base

### 3.1. Pages d'erreur personnalisées
```apache
# Redirection des erreurs vers des pages personnalisées
ErrorDocument 404 /errors/404.html
ErrorDocument 500 /errors/500.html
ErrorDocument 403 /errors/403.html
ErrorDocument 401 /errors/401.html
```

### 3.2. Redirections
```apache
# Redirection simple
Redirect 301 /ancien-chemin /nouveau-chemin

# Redirection avec expression régulière
RedirectMatch 301 ^/blog/([0-9]+)/([0-9]+)/(.*)$ /articles/$3
```

### 3.3. Indexation des répertoires
```apache
# Activer l'indexation
Options +Indexes

# Désactiver l'indexation
Options -Indexes

# Personnaliser l'affichage de l'index
IndexOptions FancyIndexing
```

## 4. Optimisation de la Performance

### 4.1. Compression GZIP
```apache
<IfModule mod_deflate.c>
    # Compression pour les types MIME spécifiques
    AddOutputFilterByType DEFLATE text/html text/plain text/xml
    AddOutputFilterByType DEFLATE text/css application/javascript
    AddOutputFilterByType DEFLATE application/json application/xml
    AddOutputFilterByType DEFLATE image/svg+xml application/font-woff
</IfModule>
```

### 4.2. Mise en cache
```apache
<IfModule mod_expires.c>
    ExpiresActive On
    
    # Images
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    
    # CSS, JavaScript
    ExpiresByType text/css "access plus 1 month"
    ExpiresByType application/javascript "access plus 1 month"
    
    # Par défaut
    ExpiresDefault "access plus 1 month"
</IfModule>
```

### 4.3. Keep-Alive
```apache
<IfModule mod_headers.c>
    Header set Connection keep-alive
    
    # Durée maximale de la connexion
    KeepAliveTimeout 5
    
    # Nombre maximal de requêtes par connexion
    MaxKeepAliveRequests 100
</IfModule>
```


## 5. Sécurité et Restrictions

### 5.1. Blocage d'IP
```apache
# Bloquer une IP spécifique
Deny from 192.168.1.1

# Bloquer une plage d'IP
Deny from 192.168.1.0/24

# Autoriser certaines IP uniquement
Order deny,allow
Deny from all
Allow from 192.168.1.100
```

### 5.2. Protection des fichiers sensibles
```apache
# Protéger le fichier .env
<Files .env>
    Order allow,deny
    Deny from all
</Files>

# Protéger plusieurs types de fichiers
<FilesMatch "^(\.env|\.git|\.htaccess)">
    Order allow,deny
    Deny from all
</FilesMatch>
```

### 5.3. Désactivation des scripts
```apache
# Désactiver l'exécution PHP dans un répertoire
<Directory "/uploads">
    php_flag engine off
</Directory>
```

### 5.4. Forcer HTTPS
```apache
# Redirection vers HTTPS
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

# Activer HSTS
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```
