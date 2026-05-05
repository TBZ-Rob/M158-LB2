# Auftrag #1 – Projektplan

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)

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
- Neue VM aufsetzen (Ubuntu 24.04 LTS)
- Netzwerk konfigurieren
- MikroTik Firewall einrichten

### Tag 3 – Zielsystem
- DNS konfigurieren
- Apache 2.4 installieren und konfigurieren
- PHP 8.3 installieren und konfigurieren
- MariaDB 11.4 installieren und konfigurieren
- PhpMyAdmin einrichten
- SFTP-Zugang einrichten

### Tag 4 – Migration
- Backup des IST-Systems erstellen (CentOS 6.6 / Vtiger 6.1.0)
- Vtiger-Datenbank exportieren (MySQL Dump)
- Daten auf neues System übertragen
- Datenbankdump in MariaDB importieren
- Vtiger schrittweise auf 8.0 upgraden
- Konfiguration anpassen und testen

### Tag 5 – Tests
- Funktionstest gemäss Testkatalog durchführen (25 Testfälle)
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

$\textcolor{#8b949e}{\text{Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit }} \textcolor{#D4622A}{\text{Claude AI Pro}} \textcolor{#8b949e}{\text{ generiert.}}$
