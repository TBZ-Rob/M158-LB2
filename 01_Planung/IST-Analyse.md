# IST-Analyse – crmserver.sample.ch

## Zugang
- Host: localhost
- Port: 2222 (NAT Port-Forwarding via VirtualBox)
- User: root
- SSH-Client: PuTTY

## IST Zustand

```
OS: CentOS release 6.6 (Final)
WebServer:
    Server version: Apache/2.2.15 (Unix)
    Server built:   Oct 16 2014 14:48:21
PHP Version: PHP 5.3.3 (cli) (built: Oct 16 2014 10:57:05)
MySQL Version: mysql  Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64) using readline 5.1
Vtiger Version: 6.1.0
```

## Verwendete Befehle

```bash
# OS Version
cat /etc/redhat-release

# Webserver Version
httpd -v

# PHP Version
php -v

# MySQL Version
mysql --version

# Vtiger Version
mysql -u root -p -e "SELECT * FROM vtiger_version;" vtigercrm
```

## Mengengerüst Datenbank

```sql
-- Datenbankgrösse IST-System
SELECT table_schema AS 'Datenbank',
  ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Grösse (MB)'
FROM information_schema.tables
WHERE table_schema = 'vtigercrm'
GROUP BY table_schema;
```

*Ergebnis hier einfügen nach Ausführung auf IST-System.*

## Bewertung

| Komponente | Version | Status |
|------------|---------|--------|
| OS | CentOS 6.6 | ⚠️ EOL seit 2020 |
| Webserver | Apache 2.2.15 | ⚠️ EOL |
| PHP | 5.3.3 | ⚠️ EOL |
| Datenbank | MySQL 5.1.73 | ⚠️ EOL |
| CRM | Vtiger 6.1.0 | ⚠️ EOL |

Alle Komponenten sind End-of-Life. Es gibt keine Sicherheitsupdates mehr. Eine Migration ist dringend notwendig.