# SSL

Für SSL wird eine FQDN benötigt, um diese zu erstellen wird ein DNS server benötigt.

## DNS

Wir benötigen einen DNS-Server um FQDNs zu erstellen.
Als DNS-Lösung haben wir uns für PiHole entschieden
Der DNS wird mit auf dem Backup-Server mit installiert

### PiHole installation

```bash
git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
cd "Pi-hole/automated install/"
sudo bash basic-install.sh
```

Während der Installation wurden folgende angaben getätigt
```json
pihole-setup {
    Upstream-DNS: "Cloudflare (DNSSEC)",
    Blocklist use: True,
    Query Logging: False,
    Privacy Mode: "Anonymous Mode"
}
```

Generated PW is `x2c1isaH`

Für eine FQDN kann im Dashboard -> Settings -> Local DNS Records ein entsprechender Eintrag angelegt werden.

FQDN = `lab.xxxmc_69xxx.local`

## Lokale CA
Auf dem Pterodactyl server

```bash
sudo mkdir ~/ca
cd ~/ca
```

CA key & Zertifikat erstellen
```bash
openssl genrsa -out ca.key 4096

openssl req -x509 -new -nodes -key ca.key \
  -sha256 -days 3650 \
  -out ca.crt

# Country NAme: DE
# State: Saxony
# Locality Name: Local Area
# Organization: Aperture LAboratories
# Organizational Unit Name: Test Facility
# Common Name: lab.xxxmc_69xxx.local
# Email: 
```

## Panel Zertifikat (Signiert von lokaler CA)

```bash
mkdir panel
cd panel

# panel key
openssl genrsa -out panel.lan.key 4096
openssl req -new -key panel.lan.key -out panel.lan.csr
# Country NAme: DE
# State: Saxony
# Locality Name: Local Area
# Organization: Aperture LAboratories
# Organizational Unit Name: Test Facility
# Common Name: panel.xxxmc_69xxx.local
# Email: 
# Challenge password: pterodactyl-key
```

## SAN config (moderne Browser)
```bash
nano panel.ext
```

Folgendes einfügen
```ini
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1 = panel.xxxmc_69xxx.local
IP.1  = 192.168.65.161
```

## Zertifikat Signieren

```bash
openssl x509 -req -in pterodactyl-panel.csr \
  -CA ../ca.crt -CAkey ../ca.key -CAcreateserial \
  -out pterodactyl-panel.crt \
  -days 3560 -sha256 \
  -extfile panel.ext
```

## Zertifikat für Nginx installieren

```bash
sudo mkdir -p /etc/nginx/ssl
sudo cp pterodactyl-server.crt pterodactyl-server.key /etc/nginx/ssl
sudo chmod 600 /etc/nginx/ssl/panel.lan.key
```

## Nginx HTTPS Config (Pterodactyl)

```bash
sudo nano /etc/nginx/sites-available/pterodactyl.conf
```

```nginx
server {
    # Replace the example <domain> with your domain name or IP address
    listen 443;
    server_name lab.xxxmc_69xxx.local;

    root /var/www/pterodactyl/public;
    index index.html index.htm index.php;
    charset utf-8;

    ssl_certificate     /etc/nginx/ssl/pterodactyl-server.crt
    ssl_certificate_key /etc/nginx/ssl/pterodactyl-server.key

    ssl_protocols TLSv1.2 TLSv1.3

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/pterodactyl.app-error.log error;

    # allow larger file uploads and longer script runtimes
    client_max_body_size 100m;
    client_body_timeout 120s;

    sendfile off;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param PHP_VALUE "upload_max_filesize = 100M \n post_max_size=100M";
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTP_PROXY "";
        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Nginx neu laden
```bash
server {
    # Replace the example <domain> with your domain name or IP address
    listen 443;
    server_name lab.xxxmc_69xxx.local;

    root /var/www/pterodactyl/public;
    index index.html index.htm index.php;
    charset utf-8;

    ssl_certificate     /etc/nginx/ssl/pterodactyl-panel.crt;
    ssl_certificate_key /etc/nginx/ssl/pterodactyl-panel.key;

    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/pterodactyl.app-error.log error;

    # allow larger file uploads and longer script runtimes
    client_max_body_size 100m;
    client_body_timeout 120s;

    sendfile off;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param PHP_VALUE "upload_max_filesize = 100M \n post_max_size=100M";
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTP_PROXY "";
        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

# Firewall Setup

Als Firewall lösung wird ufw (uncomplicated firewall) verwendet.

~Genutzte Docs: https://wiki.ubuntuusers.de/ufw/~

## Install ufw

```bash
# Vorinstalliert bei UbuntuServer 24 LTS
sudo apt install ufw
```

## Status, Start, Stop

### Status abrufen

Standardmäßig ist ufw inaktiv. Um dies zu überprüfen `sudo ufw status` ausführen.
Ausgabe ist: `Status: Inaktiv`

### Firewall starten/stoppen

ufw kann durch `sudo ufw enable` gestartet werden.
Dadurch wird ufw auch als Dienst eingerichtet.

Um ufw zu stoppen und auch den Dienst aus dem autostart zu entfernen kann mit `sudo ufw disable` erreicht werden.

## Regeln

ufw verwendet ein 3-stufiges regelwerk, welches in 3 Konfigurationsdateien hinterlegt ist.

1. `/etc/ufw/before.rules`
2. `/var/lib/ufw/user.rules`
3. `/etc/ufw/after.rules`

Die Regeln in user.rules überschreibt before.rules und after.rules überschreibt user.rules

### Regel erstellen

Um regeln hinzuzufügen gillt eine recht einfache Syntax:
`sudo ufw allow|deny|reject SERVICE`

mit `allow` wird der jeweilige service erlaubt
mit `deny` wird der jeweilige service geblockt
mit `reject`wird der jeweilige service geblockt, aber der Absender des Pakets erhält eine Nachricht, dass das Paket abgelehnt wurde.

`SERVICE` kann dabei eine in *etc/services* genannten PRotokolle sein (POP3, HTTP, SSH, ...)

Bspw. um SSH zu erlauben: `sudo ufw allow ssh`

### Regelungen

Benutzte Ports unter Linux 
```bash
sudo apt install net-tools
sudo netstat -tunlp
```

#### Allowed
|App|Port|
|---|---|
|minecraft node|25565-25580|
|node_exporter|9100,39878|
|grafana|3000|
|prometheus|9090,39878,35304|
|redis|6379,6379,55532|
|DNS|53|
|ssh|22|
|HTTPS/nginx|443|
|php|33632,3306,6379,55532,33312,443|
|mariadb|3306,33632|
|wings|8080,2022,2194|

