# EaglePress
Python3 Powerful Content Management System




**Apache2 configuration for EaglePress**
```
AllowOverride None
Require all granted

Options +ExecCGI
AddHandler cgi-script .py
AddHandler default-handler .css .js .jpg .jpeg .png .gif .ico .svg .woff .woff2 .ttf .eot .webp .mp4 .mp3 .pdf .zip
DirectoryIndex index.py

RewriteEngine On

# Never rewrite requests to the ajax/ directory
RewriteCond %{REQUEST_URI} !^/ajax/

# Never rewrite static asset URLs (matched by extension)
RewriteCond %{REQUEST_URI} !\.(css|js|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot|webp|mp4|mp3|pdf|zip)$

# Route everything else through index.py with the path as the ?r= param
RewriteRule ^(.*)$ index.py?r=$1 [L,QSA]
```

**Postgresql database configuration and Installation**
1. create a database with the name **eaglepress**
2. run python3 sql/install.py
