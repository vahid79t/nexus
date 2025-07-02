
# Install Nexus 

we are running nexus by docker:

Os:
Ubuntu 22

cpu : 8
Ram: 16







## prerequisite

first : 

```bash
  apt update & apt upgrade
```

install docker

```bash
  apt install docker
```

Install docker compose

```bash
  apt install docker-compose
```

Start the server

```bash
  systemctl Start docker 
```
run:
```bash
  version: "3.9"
services:
  nexus:
    image: sonatype/nexus3:3.68.1-java11
    container_name: nexus3-3.68.1-java11
    volumes:
      - nexus3-data:/nexus-data
    expose:
      - 8081
      - 8443
    ports:
      - 8081:8081
      - 8443:8443
    logging:
      options:
        max-size: 10m
        max-file: "3"
    restart: unless-stopped
    networks:
      - edge

networks:
  edge:
    name: edge

volumes:
  nexus3-data:
```






## user and password
after first login we can see the given path for checking password:

like:

docker exec -it  2d96da43380f    cat /nexus-data/admin.password

## For Using Https Domain

for using https domain for our nexus in order to refuse the Tls and hand shake error we can use Nginx:



## prerequisite

install nginx 

```bash
  sudo apt install nginx
```
Configure Nginx:

```bash
  sudo nano /etc/nginx/sites-available/vptokelicloud.site
```
Edit the Configuration File:

```bash
  server {
    listen 80;
    server_name vptokelicloud.site www.vptokelicloud.site;

    location / {
        proxy_pass http://5.75.196.199:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```
Enable the Site:

```bash
sudo ln -s /etc/nginx/sites-available/vptokelicloud.site /etc/nginx/sites-enabled/


```

Test the Configuration:

```bash
sudo nginx -t

```
Reload Nginx:

```bash
sudo systemctl reload nginx

```
Set Up HTTPS (Optional but Recommended):

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d vptokelicloud.site -d www.vptokelicloud.site


```
# Create certificate file
cat kherad.pem | awk '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/' > certificate.pem

# Create private key file
cat kherad.pem | awk '/-----BEGIN PRIVATE KEY-----/,/-----END PRIVATE KEY-----/' > private_key.pem


Ensure Proper SSL Redirection (Optional): with .pem

```bash
server {
    listen 80;
    server_name vptokelicloud.site www.vptokelicloud.site;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name vptokelicloud.site www.vptokelicloud.site;

    ssl_certificate /etc/letsencrypt/live/vptokelicloud.site/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/vptokelicloud.site/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://5.75.196.199:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

or with cry and key:

server {
    listen 80;
    server_name vptokelicloud.site www.vptokelicloud.site;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name vptokelicloud.site www.vptokelicloud.site;

    ssl_certificate /etc/letsencrypt/live/vptokelicloud.site/fullchain.crt;  # Change to .crt
    ssl_certificate_key /etc/letsencrypt/live/vptokelicloud.site/privkey.key;  # Change to .key
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://5.75.196.199:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}


final and working now:

server {
    listen 80;
    server_name vtakban.ir www.vtakban.ir;

    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    server_name your_domain.com;

    ssl_certificate /etc/ssl/certificate.pem;
    ssl_certificate_key /etc/ssl/private_key.pem;

    # Optional: SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";

    # Other SSL settings like SSL session cache, timeouts, etc.
    # ssl_session_cache shared:SSL:10m;
    # ssl_session_timeout 10m;

    # Location block to define your web application or site
    location / {
        proxy_pass http://192.168.139.216:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Additional Nginx configurations can be added as needed
    # For example, error handling, logging, etc.
}


```

Reload Nginx Again:

```bash
sudo systemctl reload nginx

```




## In nexus Pannel

for havinf Apt package we can use apt proxy and use:

http://archive.ubuntu.com/ubuntu/

and then by given url we can add packages

and the sample package for ubuntu:







## Installation


```bash
deb https://vptokelicloud.site/repository/apt-proxy/ jammy main
deb https://vptokelicloud.site/repository/apt-proxy/ jammy restricted
deb https://vptokelicloud.site/repository/apt-proxy/ jammy universe
deb https://vptokelicloud.site/repository/apt-proxy/ jammy multiverse
deb https://vptokelicloud.site/repository/apt-proxy/ jammy-updates main restricted universe multiverse
deb https://vptokelicloud.site/repository/apt-proxy/ jammy-backports main restricted universe multiverse
deb https://vptokelicloud.site/repository/apt-proxy/ jammy-security main restricted universe multiverse

```
    
