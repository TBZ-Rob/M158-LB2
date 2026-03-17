# CRM Migration – crmserver.sample.ch

## Projektbeschreibung
Migration des bestehenden CRM-Systems auf ein neues Betriebssystem mit aktuellem Web- und Datenbankserver. Die CRM-Applikation Vtiger wird auf die neueste Version aktualisiert und alle Daten vollständig migriert.

## Ziele
- Migration auf Ubuntu 22.04 mit neuem Web/DB Server
- Vtiger auf neueste Version aktualisieren
- Vollständige Datenmigration
- Sicherheit erhöhen durch Firewall und aktuelle Softwareversionen

## Projektphasen
| Phase | Inhalt | Aufträge |
|-------|--------|----------|
| Tag 1 | Planung | #1, #2 |
| Tag 2 | Umgebung | #3 |
| Tag 3 | Zielsystem | #4, #5, #6, #7, #8, #9 |
| Tag 4 | Migration | #10, #11 |
| Tag 5 | Tests | #12, #13, #14 |

## Aufträge
| Nr | Auftrag |
|----|---------|
| #1 | Projektplan |
| #2 | Architekturdiagramm IST/SOLL |
| #3 | Umgebung aufbauen/einrichten |
| #4 | DNS |
| #5 | Webserver |
| #6 | PHP |
| #7 | MySQL/MariaDB-Datenbankserver |
| #8 | PhpMyAdmin/Adminer |
| #9 | SFTP oder FTPS-Zugang |
| #10 | CRM-Migration |
| #11 | Backup |
| #12 | Testing |
| #13 | Monitoring |
| #14 | Deployment |

## Repo-Struktur
```
crm-migration/
├── README.md
├── 01_Planung/
│   ├── Projektplan.md
│   ├── IST-Analyse.md
│   ├── Architekturdiagramm.md
│   └── Diagramme/
├── 02_Umgebung/
│   └── Umgebung.md
├── 03_Zielsystem/
│   ├── DNS.md
│   ├── Webserver.md
│   ├── PHP.md
│   ├── MariaDB.md
│   ├── PhpMyAdmin.md
│   └── SFTP.md
├── 04_Migration/
│   ├── Migration.md
│   └── Backup.md
└── 05_Tests/
    ├── Testkatalog.md
    ├── Monitoring.md
    └── Deployment.md
```