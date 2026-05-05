# Auftrag #5 – Webserver

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Apache](https://img.shields.io/badge/Apache-2.4.62-orange)
![SSL](https://img.shields.io/badge/SSL-Self--Signed-yellow)

## Installation

```bash
sudo apt update
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

Installierte Version:
```
Server version: Apache/2.4.62 (Ubuntu)
Server built:   2024-xx-xx
```

---

## VirtualHost Konfiguration

Die Standardkonfiguration wurde deaktiviert und ein eigener VirtualHost für `crmserver.internal.ch` erstellt.

```bash
sudo nano /etc/apache2/sites-available/crmserver.conf
```

<details>
<summary>VirtualHost Config anzeigen</summary>

```apacheconf
<VirtualHost *:80>
    ServerName crmserver.internal.ch
    DocumentRoot /var/www/html/vtigercrm
    Redirect permanent / https://crmserver.internal.ch/
</VirtualHost>

<VirtualHost *:443>
    ServerName crmserver.internal.ch
    DocumentRoot /var/www/html/vtigercrm

    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/crmserver.crt
    SSLCertificateKeyFile /etc/ssl/private/crmserver.key

    <Directory /var/www/html/vtigercrm>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/crmserver_error.log
    CustomLog ${APACHE_LOG_DIR}/crmserver_access.log combined
</VirtualHost>
```

</details>

```bash
# Site aktivieren, Standard deaktivieren
sudo a2dissite 000-default.conf
sudo a2ensite crmserver.conf
sudo systemctl reload apache2
```

---

## SSL – Self-Signed Zertifikat

Da der Server intern betrieben wird (`crmserver.internal.ch`), ist kein öffentlich signiertes Zertifikat (z.B. Let's Encrypt) möglich. Es wurde ein selbst-signiertes Zertifikat erstellt.

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/crmserver.key \
  -out /etc/ssl/certs/crmserver.crt \
  -subj "/CN=crmserver.internal.ch/O=Sample/C=CH"
```

> ⚠️ Browser zeigen eine Zertifikatswarnung – für den internen Betrieb akzeptabel.

---

## Aktivierte Module

```bash
sudo a2enmod rewrite   # URL-Rewriting für Vtiger
sudo a2enmod ssl       # HTTPS-Unterstützung
sudo a2enmod headers   # Security-Header (X-Frame-Options, etc.)
sudo systemctl restart apache2
```

---

## Checkliste

- [x] Apache 2.4.62 installiert
- [x] VirtualHost für `crmserver.internal.ch` konfiguriert
- [x] HTTP → HTTPS Redirect eingerichtet
- [x] Self-Signed SSL-Zertifikat erstellt
- [x] Module `rewrite`, `ssl`, `headers` aktiviert
- [x] Standard-Site deaktiviert

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit KI-Unterstützung generiert.</sub>
