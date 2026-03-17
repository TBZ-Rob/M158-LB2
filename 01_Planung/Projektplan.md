# Auftrag #1 – Projektplan

## Rahmenbedingungen
- 5 Projekttage (je 4 x 45 min = 3h)
- 1 Tag pro Woche
- System wird Mo–Sa aktiv genutzt, Ausfallzeit so kurz wie möglich

## Zeitplan

### Tag 1 – Planung
- Projektplan erstellen
- IST-Analyse durchführen und dokumentieren
- Architekturdiagramm IST/SOLL erstellen
- GitHub Repo aufsetzen

### Tag 2 – Umgebung
- Neue VM aufsetzen (Ubuntu 22.04)
- Netzwerk konfigurieren
- Firewall (UFW) einrichten

### Tag 3 – Zielsystem
- DNS konfigurieren
- Apache 2.4 installieren und konfigurieren
- PHP 8.2 installieren und konfigurieren
- MariaDB 10.x installieren und konfigurieren
- PhpMyAdmin einrichten
- SFTP-Zugang einrichten

### Tag 4 – Migration
- Backup des IST-Systems erstellen (CentOS 6.6 / Vtiger 6.1.0)
- Vtiger-Datenbank exportieren (MySQL Dump)
- Daten auf neues System übertragen
- Datenbankdump in MariaDB importieren
- Vtiger 7.x auf Ubuntu 22.04 konfigurieren
- Konfiguration anpassen und testen

### Tag 5 – Tests
- Funktionstest gemäss Testkatalog durchführen
- Monitoring einrichten
- Deployment / Abnahme

## Kostenabschätzung
| Tätigkeit | Zeitaufwand |
|-----------|-------------|
| Planung | 3h |
| Umgebung aufbauen | 3h |
| Zielsystem einrichten | 3h |
| Migration | 3h |
| Tests & Deployment | 3h |
| **Total** | **15h** |