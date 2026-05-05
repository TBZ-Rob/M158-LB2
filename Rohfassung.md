# Rohfassung – Alle Dokumente (vor Korrektur)

> Ursprüngliche Rohfassung aller Projektdokumente, bevor Grafiken hinzugefügt wurden und Rechtschreib- und Formulierungs Fehler korrigiert wurde.

Folgender Prompt wurde zur Textverbesserung angewendet:
```
"Verbessere alle folgenden Sätze in Bezug auf Gross-/Kleinschreibung und korrigiere grössere Formulierungsfehler:"
```
mit folgendem Text:
---

## README – CRM Migration

migration des bestehenden CRM-Systems auf ein neues Betriebssystem mit aktuellem Web- und datenbankserver. Die CRM-Applikation Vtiger wird auf die neueste Version aktualisiert und alle Daten vollständig migriert.

### Ziele
- Migration auf ubuntu 24.04 LTS mit neuem Web/DB Server
- Vtiger auf neueste Version aktualisieren
- vollständige Datenmigration
- Sicherheit erhöhen durch Firewall und aktuelle Softwareversionen

---

## Auftrag #1 – Projektplan

### Rahmenbedingungen
- 5 Projekttage (je 4 x 45 min = 3h)
- 1 Tag pro Woche
- System wird Mo–Sa aktiv genutzt, Ausfallzeit so kurz wie möglich

### Zeitplan

#### Tag 1 – Planung
- Projektplan erstellen
- IST-analyse durchführen und dokumentieren
- Architekturdiagramm IST/SOLL erstellen
- GitHub Repo aufsetzen

#### Tag 2 – Umgebung
- Neue VM aufsetzen (ubuntu 24.04 LTS)
- Netzwerk konfigurieren
- MikroTik Firewall einrichten

#### Tag 3 – zielsystem
- DNS konfigurieren
- Apache 2.4 installieren und konfigurieren
- PHP 8.3 installieren und konfigurieren
- MariaDB 11.4 installieren und konfigurieren
- PhpMyAdmin einrichten
- SFTP-Zugang einrichten

#### Tag 4 – Migration
- Backup des IST-systems erstellen (CentOS 6.6 / Vtiger 6.1.0)
- Vtiger-Datenbank exportieren (MySQL Dump)
- Daten auf neues System übertragen
- Datenbankdump in MariaDB importieren
- Vtiger schrittweise auf 8.0 upgraden
- Konfiguration anpassen und testen

#### Tag 5 – Tests
- Funktionstest gemäss Testkatalog durchführen (25 Testfälle)
- Monitoring einrichten
- Deployment / Abnahme

### Kostenabschätzung
| Tätigkeit | Zeitaufwand |
|-----------|-------------|
| Planung | 3h |
| Umgebung aufbauen | 3h |
| Zielsystem einrichten | 3h |
| Migration | 3h |
| Tests & Deployment | 3h |
| **Total** | **15h** |

---

## Auftrag #2 (Teil) – IST-Analyse

### Zugang
- Host: localhost
- Port: 2222 (NAT Port-Forwarding via VirtualBox)
- User: root
- SSH-Client: PuTTY

### IST zustand

```
OS: CentOS release 6.6 (Final)
WebServer:
    Server version: Apache/2.2.15 (Unix)
    Server built:   Oct 16 2014 14:48:21
PHP Version: PHP 5.3.3 (cli) (built: Oct 16 2014 10:57:05)
MySQL Version: mysql  Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64)
Vtiger Version: 6.1.0
```

### Verwendete Befehle

```bash
cat /etc/redhat-release
httpd -v
php -v
mysql --version
mysql -u root -p -e "SELECT * FROM vtiger_version;" vtigercrm
```

### Mengengerüst Datenbank

```sql
SELECT table_schema AS 'Datenbank',
  ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Grösse (MB)'
FROM information_schema.tables
WHERE table_schema = 'vtigercrm'
GROUP BY table_schema;
```

### Bewertung

| Komponente | Version | Status |
|------------|---------|--------|
| OS | CentOS 6.6 | ⚠️ EOL seit 2020 |
| Webserver | Apache 2.2.15 | ⚠️ EOL |
| PHP | 5.3.3 | ⚠️ EOL |
| Datenbank | MySQL 5.1.73 | ⚠️ EOL |
| CRM | Vtiger 6.1.0 | ⚠️ EOL |

alle Komponenten sind End-of-Life. Eine Migration ist dringend notwendig.

---

## Auftrag #2 – Architekturdiagramm IST/SOLL

### IST-Zustand

| Komponente | Details |
|------------|---------|
| Server | crmserver.internal.ch |
| OS | CentOS 6.6 |
| Webserver | Apache 2.2.15 |
| PHP | 5.3.3 |
| Datenbank | MySQL 5.1.73 (Port 3306) |
| CRM | Vtiger 6.1.0 |
| Firewall | keine |

### SOLL-Zustand

| Komponente | Details |
|------------|---------|
| Server | crmserver.internal.ch |
| IP | 192.168.10.x |
| OS | Ubuntu 24.04.2 LTS |
| Webserver | Apache 2.4.62 |
| PHP | 8.3 |
| Datenbank | MariaDB 11.4 LTS (Port 3306) |
| CRM | Vtiger 8.0 |
| Firewall | MikroTik (kein eingehender Traffic) |

### Vergleich IST / SOLL

| Komponente | IST | SOLL | Grund |
|------------|-----|------|-------|
| OS | CentOS 6.6 | Ubuntu 24.04 LTS | CentOS EOL, Ubuntu LTS bis 2029 |
| Webserver | Apache 2.2.15 | Apache 2.4.62 | EOL, Sicherheit und performance |
| PHP | 5.3.3 | 8.3 | EOL, Vtiger 8 benötigt PHP 8.x |
| Datenbank | MySQL 5.1.73 | MariaDB 11.4 LTS | EOL, MariaDB aktiv weiterentwickelt |
| CRM | Vtiger 6.1.0 | Vtiger 8.0 | EOL, aktuelle Version |
| Firewall | keine | MikroTik | Sicherheit erhöhen |

### Netzwerk

```
Subnetz:  192.168.10.0/24
Gateway:  192.168.10.1
DNS:      8.8.8.8
SOLL-IP:  192.168.10.x
```

---

## Auftrag #3 – Umgebung aufbauen/einrichten

### Übersicht

| Komponente | IST (alt) | SOLL (neu) |
|------------|-----------|------------|
| Hypervisor | Oracle VirtualBox | VMware Workstation Pro |
| OS | CentOS 6.6 | Ubuntu 24.04.2 LTS (Noble Numbat) |
| IP | DHCP | 192.168.10.x (statisch) |
| Firewall | keine | MikroTik (externes Gerät) |

### SOLL-VM – Ubuntu 24.04.2 LTS

ubuntu 24.04.2 LTS wurde als neue VM in VMware Workstation Pro installiert. Die ISO wurde von der offiziellen Ubuntu-Webseite bezogen.

#### Netzwerkkonfiguration

| Parameter | Wert |
|-----------|------|
| Netzwerk | 192.168.10.0/24 |
| IP (SOLL-VM) | 192.168.10.x |
| Gateway | 192.168.10.1 |
| DNS | 8.8.8.8 |
| Netzwerkadapter | Bridged (LAN-Port) |

#### Firewall
Im Netzwerk ist eine MikroTik-Firewall integriert, welche sämtlichen eingehenden traffic von aussen blockiert. Die VMs kommunizieren ausschliesslich intern im Subnetz 192.168.10.0/24.

### Snapshots

Während des gesamten Projekts wurden regelmässig Snapshots der VMs erstellt. Ziel war es, jederzeit auf einen funktionierenden Zustand zurückgreifen zu können – insbesondere vor grösseren Konfigurationsänderungen oder Migrationsschritten. Dies erlaubte es Fehler rückgängig zu machen ohne die gesamte Umgebung neu aufsetzen zu müssen.

### Probleme und Lösungen

#### Problem 1 – SSH-Verbindung zur IST-VM (CentOS 6.6)
**Problem:** Da CentOS 6.6 sehr alt ist, unterstützt der ssh-Server nur veraltete Key-Algorithmen, welche moderne SSH-Clients standardmässig ablehnen.

**Lösung:** Verbindung mit explizit erlaubten Legacy-Algorithmen:
```bash
ssh -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    root@localhost -p 2222
```

#### Problem 2 – Netzwerkadapter IST-VM
**Problem:** Der Netzwerkadapter der alten CentOS-VM zeigte `NO-CARRIER` bei Verwendung einer Netzwerkbrücke. Die VM erhielt keine IP-Adresse.

**Lösung:** NAT mit Port-Forwarding in VirtualBox wurde eingerichtet (Host-Port 2222 → VM-Port 22).

#### Problem 3 – Ubuntu-VM
Die Installation der Ubuntu-VM verlief ohne nennenswerte Probleme. Dank bestehender Erfahrung mit ubuntu konnte die Umgebung zügig aufgesetzt werden.

---

## Auftrag #4 – DNS

Für die lokale Namensauflösung wurde kein separater DNS-Server eingerichtet. Stattdessen wurde die Datei `/etc/hosts` verwendet, um den Hostnamen `crmserver.internal.ch` der IP-Adresse des SOLL-Systems zuzuweisen.

> **Warum `.internal` statt `.local`?**
> `.local` ist für mDNS (Multicast DNS / Bonjour) reserviert und kann zu konflikten führen. `.internal` ist die empfohlene Alternative für interne Hostnamen ohne eigenen DNS-Server.

### Konfiguration

```bash
# /etc/hosts – Eintrag für CRM-Server
192.168.10.x    crmserver.internal.ch
```

```bash
sudo nano /etc/hosts
```

### Test

Die Namensauflösung wurde mit `ping` verifiziert:

```bash
ping crmserver.internal.ch
```

✅ Hostname wird korrekt aufgelöst.

### Checkliste

- [x] Hostname definiert (`crmserver.internal.ch`)
- [x] `/etc/hosts` Eintrag gesetzt
- [x] Namensauflösung mit `ping` getestet
- [x] Keine Probleme aufgetreten

---

## Auftrag #5 – Webserver

### Installation

```bash
sudo apt update
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

### VirtualHost Konfiguration

Die Standardkonfiguration wurde deaktiviert und ein eigener VirtualHost für `crmserver.internal.ch` erstellt.

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

```bash
sudo a2dissite 000-default.conf
sudo a2ensite crmserver.conf
sudo systemctl reload apache2
```

### SSL – Self-Signed Zertifikat

Da der server intern betrieben wird (`crmserver.internal.ch`), ist kein öffentlich signiertes Zertifikat möglich. Es wurde ein selbst-signiertes Zertifikat erstellt, welches für den internen betrieb ausreichend ist.

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/crmserver.key \
  -out /etc/ssl/certs/crmserver.crt \
  -subj "/CN=crmserver.internal.ch/O=Sample/C=CH"
```

> ⚠️ Browser zeigen eine Zertifikatswarnung – für den internen Betrieb akzeptabel.

### Aktivierte Module

```bash
sudo a2enmod rewrite
sudo a2enmod ssl
sudo a2enmod headers
sudo systemctl restart apache2
```

### Checkliste

- [x] Apache 2.4.62 installiert
- [x] VirtualHost für `crmserver.internal.ch` konfiguriert
- [x] HTTP → HTTPS Redirect eingerichtet
- [x] Self-Signed SSL-Zertifikat erstellt
- [x] Module `rewrite`, `ssl`, `headers` aktiviert
- [x] Standard-Site deaktiviert

---

## Auftrag #6 – PHP

### Installation

```bash
sudo apt install php8.3 php8.3-fpm -y
```

#### Benötigte Extensions für Vtiger

```bash
sudo apt install -y \
  php8.3-mysql \
  php8.3-xml \
  php8.3-curl \
  php8.3-zip \
  php8.3-gd \
  php8.3-mbstring \
  php8.3-imap \
  php8.3-soap \
  php8.3-bcmath \
  php8.3-intl
```

Installierte version prüfen:
```bash
php -v
```

### Konfiguration (php.ini)

```ini
memory_limit = 512M
max_execution_time = 300
max_input_time = 300
upload_max_filesize = 64M
post_max_size = 64M
display_errors = Off
log_errors = On
error_log = /var/log/php_errors.log
date.timezone = Europe/Zurich
```

```bash
sudo systemctl restart apache2
```

### Checkliste

- [x] PHP 8.3 installiert
- [x] Alle Vtiger-Extensions installiert
- [x] `php.ini` angepasst (Limits, Zeitzone, Fehlerlogging)
- [x] Apache neu gestartet
- [x] `info.php` nach Test wieder gelöscht

---

## Auftrag #7 – MySQL/MariaDB-Datenbankserver

### Installation

```bash
sudo apt install mariadb-server -y
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

### Sicherheitskonfiguration

```bash
sudo mysql_secure_installation
```

| Schritt | Wert |
|---------|------|
| Root-Passwort setzen | ✅ Ja |
| Anonyme Benutzer entfernen | ✅ Ja |
| Remote Root-Login deaktivieren | ✅ Ja |
| Test-Datenbank entfernen | ✅ Ja |

### Datenbank & Benutzer für Vtiger

```sql
CREATE DATABASE vtigercrm CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'vtiger'@'localhost' IDENTIFIED BY 'sicheres_passwort';
GRANT ALL PRIVILEGES ON vtigercrm.* TO 'vtiger'@'localhost';
FLUSH PRIVILEGES;
```

> 🔒 Der DB-Benutzer `vtiger` hat ausschliesslich Zugriff auf die `vtigercrm`-Datenbank und nur von `localhost`.

### Konfiguration

```ini
[mysqld]
bind-address = 127.0.0.1
character-set-server = utf8mb4
collation-server     = utf8mb4_unicode_ci
innodb_buffer_pool_size = 256M
max_connections         = 100
```

### Checkliste

- [x] MariaDB 11.4 LTS installiert
- [x] `mysql_secure_installation` durchgeführt
- [x] Datenbank `vtigercrm` erstellt (UTF-8)
- [x] Dedizierter User `vtiger@localhost` angelegt
- [x] Zugriff auf `localhost` beschränkt
- [x] Remote Root-Login deaktiviert

---

## Auftrag #8 – PhpMyAdmin

### Installation

```bash
sudo apt install phpmyadmin -y
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

### Zugriff

phpmyadmin ist erreichbar unter:

```
https://crmserver.internal.ch/phpmyadmin
```

Login mit dem MariaDB-Root-Benutzer oder dem vtiger-Benutzer.

### Sicherheit

Da phpmyadmin ein bekanntes Angriffsziel ist, wurde der Zugriff auf localhost beschränkt:

```apacheconf
<Directory /usr/share/phpmyadmin>
    <RequireAny>
        Require ip 127.0.0.1
        Require ip 192.168.10.0/24
    </RequireAny>
</Directory>
```

### Verwendung während Migration

PhpMyAdmin wurde während der Migration genutzt für:
- Überprüfung des importierten Datenbankdumps
- Kontrolle der Tabellenstruktur nach jedem Upgrade-schritt
- Manuelle SQL-Queries zur Fehlerdiagnose

### Checkliste

- [x] PhpMyAdmin installiert
- [x] Apache-Integration aktiviert
- [x] Erreichbar unter `/phpmyadmin`
- [x] Zugriff auf lokales Netz (192.168.10.0/24) beschränkt
- [x] Für Migrationskontrolle eingesetzt

---

## Auftrag #9 – SFTP-Zugang

Für den Dateitransfer wurde SFTP (SSH File Transfer Protocol) eingerichtet. SFTP nutzt die bestehende SSH-Infrastruktur und ist deutlich sicherer als FTP/FTPS, da alle Daten werden verschlüsselt übertragen.

### SFTP-Benutzer anlegen

```bash
sudo useradd -m -s /sbin/nologin sftpuser
sudo passwd sftpuser
sudo chown root:root /home/sftpuser
sudo chmod 755 /home/sftpuser
sudo mkdir /home/sftpuser/upload
sudo chown sftpuser:sftpuser /home/sftpuser/upload
```

### SSH-Konfiguration (Chroot)

```sshd_config
Subsystem sftp internal-sftp

Match User sftpuser
    ChrootDirectory /home/sftpuser
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no
```

```bash
sudo systemctl restart ssh
```

### Test

```bash
sftp sftpuser@crmserver.internal.ch
```

### Verwendung während Migration

Der SFTP-zugang wurde für folgende Aufgaben genutzt:
- Übertragung der Vtiger-Datendateien vom IST- auf das SOLL-System
- Upload von Patch-Dateien für Vtiger-Upgrades
- Transfer von Backup-Dateien

### Checkliste

- [x] SFTP-User `sftpuser` angelegt (ohne Shell)
- [x] Chroot-Jail konfiguriert
- [x] TCP-Forwarding und X11 deaktiviert
- [x] Upload-Verzeichnis mit korrekten Berechtigungen
- [x] Verbindung getestet
- [x] Für Datenmigration eingesetzt

---

## Auftrag #10 – WebApp/DB-Migration

### Übersicht

Migration von Vtiger CRM 6.1.0 (CentOS 6.6) auf Vtiger 8.0 (Ubuntu 24.04 LTS). Der Upgrade erfolgte in mehreren Schritten, da Vtiger nur sequenzielle Versionssprünge unterstützt (kein Direktsprung über mehrere Versionen).

### Migrationspfad

```
6.1.0 → 6.4.0 → 6.5.0 → 7.0.0 → 7.1.0 → 7.2.0 → 7.3.0 → 7.4.0 → 7.5.0 → 8.0.0
```

> ⚠️ Vtiger erlaubt keine Direktsprünge. Jeder Schritt muss einzeln ausgeführt werden.

### Phase 1 – Datenbank exportieren (IST-System)

```bash
mysqldump -u root -p vtigercrm > /tmp/vtigercrm_backup.sql
sftp sftpuser@192.168.10.x
put /tmp/vtigercrm_backup.sql
```

### Phase 2 – Vtiger 6.4 auf SOLL-System installieren

Da das Webinterface von Vtiger 6.1.0 auf dem neuen System nicht korrekt dargestellt wurde (nur HTML-Quellcode sichtbar), wurde Vtiger komplett neu installiert — startend mit Version 6.4.

```bash
cd /var/www/html
sudo tar -xzf vtigercrm-6.4.0.tar.gz -C /var/www/html/
sudo mv vtigercrm /var/www/html/vtigercrm
sudo chown -R www-data:www-data /var/www/html/vtigercrm
```

Datenbankdump importieren:
```bash
sudo mariadb -u root -p vtigercrm < /home/sftpuser/upload/vtigercrm_backup.sql
```

### Phase 3 – Schrittweiser Upgrade via Vtiger Migrations-Wizard

Vtiger beinhaltet einen eingebauten Migrations-Wizard, der unter `/migrate` erreichbar ist. Für jeden Versionssprung folgender Ablauf wurde wiederholt:

```bash
# 1. Patch herunterladen
# 2. Berechtigungen setzen
sudo chown -R www-data:www-data /var/www/html/vtigercrm
# 3. Migrations-Wizard aufrufen
# 4. Wizard durchlaufen und Log prüfen
# 5. Snapshot erstellen
```

#### Durchgeführte Upgrades

| Schritt | Von | Nach | Methode |
|---------|-----|------|---------|
| 1 | 6.1.0 | 6.4.0 | Neuinstallation + DB-Import |
| 2 | 6.4.0 | 6.5.0 | Vtiger Migrations-Wizard |
| 3 | 6.5.0 | 7.0.0 | Vtiger Migrations-Wizard |
| 4 | 7.0.0 | 7.1.0 | Vtiger Migrations-Wizard |
| 5 | 7.1.0 | 7.2.0 | Vtiger Migrations-Wizard |
| 6 | 7.2.0 | 7.3.0 | Vtiger Migrations-Wizard |
| 7 | 7.3.0 | 7.4.0 | Vtiger Migrations-Wizard |
| 8 | 7.4.0 | 7.5.0 | Vtiger Migrations-Wizard |
| 9 | 7.5.0 | 8.0.0 | Vtiger Migrations-Wizard |

### Probleme & Lösungen

#### Problem 1 – Webinterface zeigt nur HTML-Quellcode
**Ursache:** PHP war nicht korrekt mit Apache verknüpft oder Extensions fehlten.
**Lösung:** Vtiger 6.1 komplett deinstalliert, Vtiger 6.4 neu installiert und PHP-Extensions nachinstalliert.

#### Problem 2 – Migrations-Wizard nicht erreichbar (HTTP 500)
**Ursache:** Falsche PHP-Version aktiv beim Upgrade-Schritt 7.4 → 8.0.
**Lösung:** Für den Upgrade-Schritt temporär PHP 7.4 aktiviert, danach zurück auf PHP 8.3.

```bash
sudo a2dismod php8.3
sudo a2enmod php7.4
sudo systemctl restart apache2
# ... Upgrade durchführen ...
sudo a2dismod php7.4
sudo a2enmod php8.3
sudo systemctl restart apache2
```

#### Problem 3 – Datenbankdump Import Fehler
**Ursache:** Zeichensatz-Inkompatiblität zwischen MySQL 5.1 (IST) und MariaDB 11.4 (SOLL).
**Lösung:**
```bash
sudo mariadb -u root -p --default-character-set=utf8 vtigercrm < vtigercrm_backup.sql
```

### Konfiguration anpassen

```php
$dbconfig['db_server']   = 'localhost';
$dbconfig['db_port']     = '3306';
$dbconfig['db_username'] = 'vtiger';
$dbconfig['db_password'] = 'sicheres_passwort';
$dbconfig['db_name']     = 'vtigercrm';
$site_URL = 'https://crmserver.internal.ch/vtigercrm';
```

### Checkliste

- [x] Datenbankdump von IST-System erstellt
- [x] Dump auf SOLL-System übertragen
- [x] Vtiger 6.4 neu installiert
- [x] DB-Import erfolgreich
- [x] Alle Upgrade-Schritte 6.4 → 8.0 durchgeführt
- [x] `config.inc.php` angepasst
- [x] Login und Datenkonsistenz geprüft

---

## Auftrag #11 – Backup

### Backup-Konzept

| Parameter | Wert |
|-----------|------|
| Datenbank | Täglich, vollständiger `mysqldump` |
| Dateien | Täglich, komprimiertes Archiv |
| Speicherort | Externes Laufwerk (`/mnt/backup`) |
| Aufbewahrung | 7 Tage (danach automatisch gelöscht) |
| Zeitplan | Täglich 02:00 Uhr |

### backup-Skript

```bash
#!/bin/bash
BACKUP_DIR="/mnt/backup/vtiger"
DATE=$(date +%Y-%m-%d)
DB_USER="vtiger"
DB_PASS="sicheres_passwort"
DB_NAME="vtigercrm"
WEB_DIR="/var/www/html/vtigercrm"
RETENTION_DAYS=7

mkdir -p "$BACKUP_DIR"
mysqldump -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" | gzip > "$BACKUP_DIR/db_$DATE.sql.gz"
tar -czf "$BACKUP_DIR/files_$DATE.tar.gz" "$WEB_DIR"
find "$BACKUP_DIR" -name "*.gz" -mtime +$RETENTION_DAYS -delete
```

```bash
sudo chmod +x /usr/local/bin/vtiger-backup.sh
sudo /usr/local/bin/vtiger-backup.sh
```

### Cron Job

```cron
0 2 * * * /usr/local/bin/vtiger-backup.sh >> /var/log/vtiger-backup.log 2>&1
```

### Checkliste

- [x] backup-Skript erstellt (`/usr/local/bin/vtiger-backup.sh`)
- [x] DB-Backup via `mysqldump`
- [x] Datei-Backup via `tar`
- [x] Aufräumfunktion (7-Tage-Retention)
- [x] Cron Job täglich 02:00 Uhr
- [x] Manueller Test erfolgreich

---

## Auftrag #12 – Testing

### Testkonzept

Ziel der tests ist der Nachweis, dass die Migration erfolgreich war und das System vollständig funktionsfähig ist. Die Tests decken Netzwerk, Dienste, Datenbank, Webserver und Vtiger-Funktionen ab.

### Testkatalog

#### 🌐 Netzwerk & Erreichbarkeit

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T01 | `ping crmserver.internal.ch` | Antwort von 192.168.10.x | ✅ |
| T02 | HTTP-Aufruf | Redirect auf HTTPS | ✅ |
| T03 | HTTPS-Aufruf | Vtiger Loginseite erscheint | ✅ |
| T04 | SSH-Verbindung | Verbindung erfolgreich | ✅ |
| T05 | SFTP-Verbindung | Verbindung erfolgreich | ✅ |

#### 🔧 Dienste

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T06 | `systemctl status apache2` | active (running) | ✅ |
| T07 | `systemctl status mariadb` | active (running) | ✅ |
| T08 | `systemctl status ssh` | active (running) | ✅ |
| T09 | Apache nach Neustart | Dienst startet automatisch | ✅ |
| T10 | MariaDB nach Neustart | Dienst startet automatisch | ✅ |

#### 🗄️ Datenbank

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T11 | Login MariaDB als `vtiger@localhost` | Erfolgreich | ✅ |
| T12 | Datenbank `vtigercrm` vorhanden | Tabellen sichtbar | ✅ |
| T13 | Datensätze in `vtiger_accounts` | Kundendaten vorhanden | ✅ |
| T14 | PhpMyAdmin erreichbar | Login möglich | ✅ |

#### 🖥️ Vtiger CRM – Funktionen

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T15 | Login Admin-Benutzer | Dashboard erscheint | ✅ |
| T16 | Login normaler Benutzer | Erfolgreich | ✅ |
| T17 | Kontakt anzeigen | Bestehende Kontakte vorhanden | ✅ |
| T18 | Neuen Kontakt erstellen | Kontakt wird gespeichert | ✅ |
| T19 | Kontakt bearbeiten | Änderungen werden gespeichert | ✅ |
| T20 | Kontakt löschen | Kontakt wird entfernt | ✅ |
| T21 | Angebot erstellen | Formular funktioniert | ✅ |
| T22 | Kalender / Aufgaben | Einträge bearbeitbar | ✅ |
| T23 | Dateiupload in Vtiger | Upload erfolgreich | ✅ |

#### 💾 Backup

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T24 | Manueller Backup-Lauf | Dateien vorhanden | ✅ |
| T25 | DB-Dump entpacken & prüfen | SQL-Inhalt lesbar | ✅ |

### Testergebnis

```
Testfälle gesamt:    25
Bestanden:           25 ✅
Fehlgeschlagen:       0 ❌
```

> alle Verbindungen, Dienste und Vtiger-Funktionen wurden erfolgreich getestet. Die migration gilt als vollständig und korrekt abgeschlossen.

---

## Auftrag #13 – Monitoring

### Übersicht

Für das Monitoring wird **monit** eingesetzt – ein leichtgewichtiges Tool, das Dienste überwacht, bei Problemen automatisch neustartet (Watchdog) und per E-Mail alarmiert.

### Installation

```bash
sudo apt install monit -y
sudo systemctl enable monit
```

### Konfiguration

```
set daemon 60
set logfile /var/log/monit.log

set mailserver smtp.gmail.com port 587
    username "monitoring@internal.ch"
    password "app_passwort"
    using tls

set alert admin@internal.ch

set httpd port 2812
    use address localhost
    allow localhost
```

#### Dienste überwachen

```
check process apache2 with pidfile /run/apache2/apache2.pid
    start program = "/bin/systemctl start apache2"
    stop  program = "/bin/systemctl stop apache2"
    if failed host crmserver.internal.ch port 80 protocol http then restart
    if 3 restarts within 5 cycles then alert

check process mariadb with pidfile /run/mysqld/mysqld.pid
    start program = "/bin/systemctl start mariadb"
    stop  program = "/bin/systemctl stop mariadb"
    if failed host 127.0.0.1 port 3306 protocol mysql then restart
    if 3 restarts within 5 cycles then alert

check filesystem rootfs with path /
    if space usage > 85% then alert
```

```bash
sudo monit -t
sudo systemctl start monit
sudo monit reload
```

### Watchdog – Automatischer neustart

Fällt ein Dienst aus, startet Monit ihn automatisch neu und sendet eine E-Mail-Benachrichtigung. Nach 3 Neustarts innerhalb von 5 Zyklen wird ein alert ausgelöst.

### Checkliste

- [x] Monit installiert und aktiviert
- [x] Apache überwacht (HTTP + HTTPS)
- [x] MariaDB überwacht (Port 3306)
- [x] SSH überwacht
- [x] Speicherplatz überwacht (Root + Backup)
- [x] E-Mail Alarmierung konfiguriert
- [x] Automatischer Neustart (Watchdog) aktiv
- [x] Logging in `/var/log/monit.log`

---

## Auftrag #14 – Deployment

### Übersicht

Das Deployment beschreibt den finalen Go-Live des SOLL-Systems und die Ausserdienststellung des IST-Systems. Da das System Mo–Sa aktiv genutzt wird, wurde das Deployment auf ausserhalb der Geschäftszeiten ein Wartungsfenster gelegt.

### Deployment-Ablauf

#### 1. Benutzer informieren

Vor dem Go-Live wurde eine E-Mail an alle CRM-Benutzer verschickt:

> Das CRM-System wird am [Datum] um 22:00 Uhr für kurze Zeit nicht erreichbar sein. Nach der Wartung ist das System unter der gewohnten Adresse wieder verfügbar.

#### 2. IST-System einfrieren

```bash
service httpd stop
```

#### 3. Finalen DB-Dump erstellen und importieren

```bash
mysqldump -u root -p vtigercrm > /tmp/vtigercrm_final.sql
# Transfer auf SOLL-System
sudo mariadb -u root -p vtigercrm < /home/sftpuser/upload/vtigercrm_final.sql
```

#### 4. DNS / hosts-Eintrag umschalten

```
192.168.10.x    crmserver.internal.ch
```

#### 5. SOLL-System freigeben

```bash
sudo systemctl start apache2
curl -k https://crmserver.internal.ch
```

#### 6. IST-System ausschalten

Nach erfolgreicher Verifikation des SOLL-Systems wurde die IST-VM heruntergefahren und als Archiv-Snapshot aufbewahrt.

### Rollback-Plan

Sollte das SOLL-System beim Go-Live Probleme zeigen:

1. Apache auf SOLL-System stoppen
2. `hosts`-Einträge zurück auf IST-System IP setzen
3. Apache auf IST-System starten
4. Fehleranalyse und Behebung
5. Neues Deployment planen

### Go-Live Ergebnis

| Parameter | Wert |
|-----------|------|
| Downtime | ca. 15 Minuten |
| Rollback notwendig | Nein |
| Alle Benutzer erreichbar | ✅ |
| Datenverlust | Keiner |

### Checkliste

- [x] Benutzer informiert
- [x] IST-System gestoppt
- [x] Finaler DB-Dump erstellt und importiert
- [x] DNS/hosts-Eintrag umgeschaltet
- [x] SOLL-System freigegeben
- [x] Funktionstest nach Go-Live bestanden
- [x] IST-VM als Archiv-Snapshot gesichert
- [x] Downtime unter 30 Minuten

---

## Arbeitsjournal – CRM Migration

### Tag 1 – Planung

#### Ziel
Projektplanung abschliessen, IST-System analysieren und dokumentieren, GitHub Repo aufsetzen.

#### SSH-Zugang zum IST-System

Der erste Versuch, per PuTTY auf die IST-VM (CentOS 6.6) zuzugreifen, schlug fehl. Verschiedene Netzwerkadapter wurden getestet:

| Adapter | Ergebnis |
|---------|----------|
| Realtek USB GbE (Netzwerkbrücke) | `NO-CARRIER` – keine Verbindung |
| Intel WiFi 6E (Netzwerkbrücke) | Keine IP erhalten |
| Host-only Adapter | Keine IP erhalten |
| **NAT mit Port-Forwarding** | ✅ Verbindung erfolgreich |

Da CentOS 6.6 sehr alt ist, lehnt ein moderner SSH-Client die veralteten Key-Algorithmen standardmässig ab. Lösung:

```bash
ssh -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    root@localhost -p 2222
```

#### IST-Analyse

Nach erfolgreichem SSH-Zugang das System wurde analysiert:

```bash
cat /etc/redhat-release   # CentOS release 6.6 (Final)
httpd -v                  # Apache/2.2.15
php -v                    # PHP 5.3.3
mysql --version           # MySQL 5.1.73
```

**Erkenntnis:** Alle Komponenten sind End-of-Life. Eine migration ist dringend notwendig.

#### Architekturdiagramm & Netzwerkplanung

IST/SOLL-Diagramm erstellt. Für die spätere Kommunikation zwischen den VMs folgendes Netzwerk geplant wurde:

```
Subnetz: 192.168.10.0/24 | Gateway: 192.168.10.1 | SOLL-IP: 192.168.10.x
```

#### GitHub Repo

Repo-Struktur mit allen Ordnern und Platzhalter-Files aufgesetzt und gepusht.

---

### Tag 2 – Umgebung

#### Ziel
Neue SOLL-VM aufsetzen, Netzwerk konfigurieren, Umgebung für Migration vorbereiten.

#### VM-Installation

In VMware Workstation Pro wurde eine neue VM mit **ubuntu 24.04.2 LTS** installiert. Der Wechsel von Oracle VirtualBox (IST) zu VMware Workstation Pro (SOLL) war bewusst gewählt, da VMware eine bessere Performance und stabilere Netzwerkkonfiguration bietet.

| Parameter | Wert |
|-----------|------|
| OS | Ubuntu 24.04.2 LTS (Noble Numbat) |
| Hypervisor | VMware Workstation Pro |
| Netzwerkadapter | Bridged (LAN-Port) |
| IP | 192.168.10.x (statisch) |
| Gateway | 192.168.10.1 |
| DNS | 8.8.8.8 |

#### MikroTik Firewall

Im Netzwerk wurde eine MikroTik-Firewall eingebunden, welche sämtlichen eingehenden traffic von aussen blockiert. Die interne Kommunikation im Subnetz 192.168.10.0/24 bleibt uneingeschränkt.

#### Verlauf

Die ubuntu-Installation verlief dank bestehender Erfahrung problemlos. Netzwerk, SSH und Grundkonfiguration wurden ohne Komplikationen abgeschlossen.

---

### Tag 3 – Zielsystem

#### Ziel
Alle benötigten Dienste installieren und konfigurieren: DNS, Apache, PHP, MariaDB, PhpMyAdmin, SFTP.

#### DNS

Lokale Namensauflösung via `/etc/hosts` eingerichtet. `.internal` als Domain-Suffix gewählt (empfohlene Alternative zu `.local`):

```
192.168.10.x    crmserver.internal.ch
```

#### Apache 2.4.62

Apache installiert, VirtualHost für `crmserver.internal.ch` konfiguriert. HTTP → HTTPS Redirect eingerichtet. Self-Signed SSL-Zertifikat erstellt. Module `rewrite`, `ssl` und `headers` aktiviert.

#### PHP 8.3

PHP 8.3 mit allen für Vtiger benötigten Extensions installiert. `php.ini` angepasst: `memory_limit`, Upload-Limits, Zeitzone auf `Europe/Zurich`.

#### MariaDB 11.4 LTS

MariaDB installiert, `mysql_secure_installation` durchgeführt. Datenbank `vtigercrm` mit UTF-8mb4 erstellt. Dedizierter User `vtiger@localhost` angelegt. `bind-address = 127.0.0.1` – kein externer DB-Zugriff möglich.

#### PhpMyAdmin & SFTP

phpmyadmin installiert, Zugriff auf `192.168.10.0/24` beschränkt. SFTP-User mit Chroot-Jail eingerichtet – kein Shell-Zugang, kein Zugriff ausserhalb des Home-Verzeichnisses.

---

### Tag 4 – Migration

#### Ziel
Daten vom IST-System übertragen, Vtiger schrittweise von 6.1.0 auf 8.0 upgraden.

#### Problem: Webinterface zeigt nur HTML-Quellcode

Bei der ersten Installation von Vtiger 6.1.0 auf dem neuen System wurde im Browser nur der rohe HTML-Quellcode angezeigt statt der Applikation. Ursache war eine fehlerhafte PHP-apache-Integration.

**Lösung:** Vtiger komplett deinstalliert, mit Version 6.4 neu begonnen und alle PHP-Extensions nachinstalliert. Danach funktionierte das Webinterface korrekt.

#### Datenbankdump

```bash
mysqldump -u root -p vtigercrm > /tmp/vtigercrm_backup.sql
sudo mariadb -u root -p --default-character-set=utf8 vtigercrm < vtigercrm_backup.sql
```

#### Vtiger Upgrade 6.4 → 8.0

Der Upgrade erfolgte in 8 Einzelschritten via dem eingebauten Vtiger Migrations-Wizard. Für jeden Schritt: Patch herunterladen → entpacken → Wizard aufrufen → Log prüfen → Snapshot.

**Problem beim Schritt 7.4 → 8.0:** Der Migrations-Wizard war nicht erreichbar (HTTP 500). Ursache: PHP 8.3 inkompatibel für diesen Upgrade-Schritt. Lösung: Temporär auf PHP 7.4 gewechselt, Upgrade durchgeführt, danach zurück auf PHP 8.3.

---

### Tag 5 – Tests & Deployment

#### Testing

25 Testfälle durchgeführt, gegliedert in 5 Kategorien. **Ergebnis: 25/25 bestanden ✅**

#### Monitoring

**Monit** als leichtgewichtiges Monitoring-Tool installiert. Überwacht Apache, MariaDB, SSH und Speicherplatz. E-Mail-Alarmierung bei Ausfall konfiguriert. Automatischer Neustart (Watchdog) aktiv.

#### Go-Live

Deployment ausserhalb der Geschäftszeiten durchgeführt:

1. Benutzer per E-Mail informiert
2. IST-System gestoppt (kein weiterer Schreibzugriff)
3. Finalen DB-Dump erstellt und importiert
4. `hosts`-Einträge auf SOLL-System umgestellt
5. Funktionstest bestanden
6. IST-VM als Archiv-Snapshot gesichert

| Kennzahl | Wert |
|----------|------|
| Downtime | ca. 15 Minuten |
| Datenverlust | Keiner |
| Rollback notwendig | Nein |
"