# Auftrag #2 (Teil) – IST-Analyse

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![System](https://img.shields.io/badge/System-CentOS_6.6-red)

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
MySQL Version: mysql  Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64)
Vtiger Version: 6.1.0
```

## Verwendete Befehle

```bash
cat /etc/redhat-release
httpd -v
php -v
mysql --version
mysql -u root -p -e "SELECT * FROM vtiger_version;" vtigercrm
```

## Mengengerüst Datenbank

```sql
SELECT table_schema AS 'Datenbank',
  ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Grösse (MB)'
FROM information_schema.tables
WHERE table_schema = 'vtigercrm'
GROUP BY table_schema;
```

## Bewertung

| Komponente | Version | Status |
|------------|---------|--------|
| OS | CentOS 6.6 | ⚠️ EOL seit 2020 |
| Webserver | Apache 2.2.15 | ⚠️ EOL |
| PHP | 5.3.3 | ⚠️ EOL |
| Datenbank | MySQL 5.1.73 | ⚠️ EOL |
| CRM | Vtiger 6.1.0 | ⚠️ EOL |

Alle Komponenten sind End-of-Life. Eine Migration ist dringend notwendig.

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit <img src="https://img.shields.io/badge/Claude_AI_Pro-D4622A?style=flat-square" alt="Claude AI Pro"> generiert.</sub>
