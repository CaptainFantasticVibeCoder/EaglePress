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



**Nginx configuration for EaglePress**
```
# ════════════════════════════════════════════════════════════════════════════
#  EaglePress — Nginx Configuration
#  Python 3 CGI via fcgiwrap  ·  Clean URL routing via PATH_INFO
#
#  Replace every token before deploying:
#    YOURDOMAIN   →  your domain name (e.g. postwits.com)
#    YOURROOT     →  absolute path to pyEaglePress/ directory
#    YOUR_IP_*    →  allowed IP addresses for the login allowlist
# ════════════════════════════════════════════════════════════════════════════

server {

	server_name YOURDOMAIN.com www.YOURDOMAIN.com;

	root YOURROOT;
	index index.py;

	underscores_in_headers on;

	gzip off;


	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
	
	location ^~ /uploads/ {                                          
		alias /var/websites/postwits/www/html/pyEaglePress/uploads/;            
		add_header Content-Disposition "attachment";                              
	} 
  

	location ~ ^/ajax/[^/]+\.py$ {
	
		include /etc/nginx/fastcgi_params;
		fastcgi_pass unix:/var/run/fcgiwrap.socket;                               
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;                        
		fastcgi_param SCRIPT_NAME     $fastcgi_script_name;
		fastcgi_param QUERY_STRING    $query_string;                              
	}



	location ~* \.(css|js|png|jpg|jpeg|gif|ico|svg|svgz|images)$ {

		expires 1d; # 30d
		add_header Cache-Control "public, no-transform";

	}
	

	location ~ ^(/.*)$ {

		include /etc/nginx/fastcgi_params;
		fastcgi_pass unix:/var/run/fcgiwrap.socket;
		fastcgi_param SCRIPT_FILENAME $document_root/index.py;                      
		fastcgi_param PATH_INFO       $1;
		fastcgi_param SCRIPT_NAME     /index.py;                 
		fastcgi_param QUERY_STRING    $query_string;                          
	}	
	

	# ── Login page: IP allowlist  ─────────────────────────────────────────────────                             
	location = /login { 
	                                                          
        allow YOUR_IP_1;
        allow YOUR_IP_2;
        allow YOUR_IP_3;
        deny  all;                                                             

		include /etc/nginx/fastcgi_params;                                        
		fastcgi_pass unix:/var/run/fcgiwrap.socket;
		fastcgi_param SCRIPT_FILENAME $document_root/index.py;
		fastcgi_param PATH_INFO       /login;                                     
		fastcgi_param SCRIPT_NAME     /index.py;
		fastcgi_param QUERY_STRING    $query_string;                              
	}               
		                                                                
	# ── Login AJAX endpoint: same allowlist (prevents direct POST bypass) ─────────                                                                     
	location = /ajax/www_login.py {   
	                                            
        allow YOUR_IP_1;
        allow YOUR_IP_2;
        allow YOUR_IP_3;
        deny  all;      

		include /etc/nginx/fastcgi_params;                                        
		fastcgi_pass unix:/var/run/fcgiwrap.socket;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;         
		fastcgi_param SCRIPT_NAME     $fastcgi_script_name;
		fastcgi_param QUERY_STRING    $query_string;                              
	}


	client_max_body_size 256M;

    listen [::]:443 ssl; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/YOURDOMAIN/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/YOURDOMAIN/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}


```
