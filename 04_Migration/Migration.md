# Auftrag #10 – WebApp/DB-Migration

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Von](https://img.shields.io/badge/Von-Vtiger_6.1.0-red)
![Nach](https://img.shields.io/badge/Nach-Vtiger_8.0-brightgreen)

## Übersicht

Migration von Vtiger CRM 6.1.0 (CentOS 6.6) auf Vtiger 8.0 (Ubuntu 24.04 LTS).
Der Upgrade erfolgte in mehreren Schritten, da Vtiger nur sequenzielle
Versionssprünge unterstützt (kein Direktsprung über mehrere Versionen).

---

## Migrationspfad

```
6.1.0 → 6.4.0 → 6.5.0 → 7.0.0 → 7.1.0 → 7.2.0 → 7.3.0 → 7.4.0 → 7.5.0 → 8.0.0
```

> ⚠️ Vtiger erlaubt keine Direktsprünge. Jeder Schritt muss einzeln ausgeführt werden.

---

## Phase 1 – Datenbank exportieren (IST-System)

```bash
# Auf IST-System (CentOS 6.6)
mysqldump -u root -p vtigercrm > /tmp/vtigercrm_backup.sql

# Dump auf SOLL-System übertragen (via SFTP)
sftp sftpuser@192.168.10.x
put /tmp/vtigercrm_backup.sql
```

---

## Phase 2 – Vtiger 6.4 auf SOLL-System installieren

Da das Webinterface von Vtiger 6.1.0 auf dem neuen System nicht korrekt
dargestellt wurde (nur HTML-Quellcode sichtbar), wurde Vtiger komplett
neu installiert — startend mit Version 6.4.

```bash
# Vtiger 6.4 herunterladen und extrahieren
cd /var/www/html
sudo wget https://sourceforge.net/projects/vtigercrm/files/vtiger%20CRM%206.4.0/Core%20Product/vtigercrm-6.4.0.tar.gz
sudo tar -xzf vtigercrm-6.4.0.tar.gz -C /var/www/html/
sudo mv vtigercrm /var/www/html/vtigercrm
sudo chown -R www-data:www-data /var/www/html/vtigercrm
```

Datenbankdump importieren:
```bash
sudo mariadb -u root -p vtigercrm < /home/sftpuser/upload/vtigercrm_backup.sql
```

---

## Phase 3 – Schrittweiser Upgrade via Vtiger Migrations-Wizard

Vtiger beinhaltet einen eingebauten Migrations-Wizard, der unter `/migrate`
erreichbar ist. Für jeden Versionssprung wurde folgender Ablauf wiederholt:

<details>
<summary>Upgrade-Ablauf pro Version anzeigen</summary>

```bash
# 1. Patch herunterladen (Beispiel 6.4 → 6.5)
cd /var/www/html/vtigercrm
sudo wget https://sourceforge.net/projects/vtigercrm/files/.../vtigercrm-640-650-patch.zip
sudo unzip vtigercrm-640-650-patch.zip

# 2. Berechtigungen setzen
sudo chown -R www-data:www-data /var/www/html/vtigercrm

# 3. Migrations-Wizard aufrufen
# → https://crmserver.internal.ch/vtigercrm/migrate

# 4. Wizard durchlaufen:
#    - Admin-Zugangsdaten eingeben
#    - Voraussetzungen prüfen
#    - "Start Migration" klicken
#    - Post-Upgrade-Log prüfen

# 5. Snapshot erstellen (VirtualBox)
```

</details>

### Durchgeführte Upgrades

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

---

## Probleme & Lösungen

### Problem 1 – Webinterface zeigt nur HTML-Quellcode
**Ursache:** PHP war nicht korrekt mit Apache verknüpft oder Extensions fehlten.  
**Lösung:** Vtiger 6.1 komplett deinstalliert, Vtiger 6.4 neu installiert und PHP-Extensions nachinstalliert.

### Problem 2 – Migrations-Wizard nicht erreichbar (HTTP 500)
**Ursache:** Falsche PHP-Version aktiv beim Upgrade-Schritt 7.4 → 8.0.  
**Lösung:** Für den Upgrade-Schritt temporär PHP 7.4 aktiviert, danach zurück auf PHP 8.3.

```bash
# PHP-Version wechseln
sudo a2dismod php8.3
sudo a2enmod php7.4
sudo systemctl restart apache2
# ... Upgrade durchführen ...
sudo a2dismod php7.4
sudo a2enmod php8.3
sudo systemctl restart apache2
```

### Problem 3 – Datenbankdump Import Fehler
**Ursache:** Zeichensatz-Inkompatiblität zwischen MySQL 5.1 (IST) und MariaDB 11.4 (SOLL).  
**Lösung:** Dump mit korrektem Zeichensatz importiert:
```bash
sudo mariadb -u root -p --default-character-set=utf8 vtigercrm < vtigercrm_backup.sql
```

---

## Konfiguration anpassen

```bash
sudo nano /var/www/html/vtigercrm/config.inc.php
```

```php
$dbconfig['db_server']   = 'localhost';
$dbconfig['db_port']     = '3306';
$dbconfig['db_username'] = 'vtiger';
$dbconfig['db_password'] = 'sicheres_passwort';
$dbconfig['db_name']     = 'vtigercrm';

$site_URL = 'https://crmserver.internal.ch/vtigercrm';
```

---

## Checkliste

- [x] Datenbankdump von IST-System erstellt
- [x] Dump auf SOLL-System übertragen
- [x] Vtiger 6.4 neu installiert
- [x] DB-Import erfolgreich
- [x] Alle Upgrade-Schritte 6.4 → 8.0 durchgeführt
- [x] Vtiger Migrations-Wizard für jeden Schritt verwendet
- [x] `config.inc.php` angepasst
- [x] Login und Datenkonsistenz geprüft

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit $\textcolor{#D4622A}{\text{Claude AI Pro}}$ generiert.</sub>
