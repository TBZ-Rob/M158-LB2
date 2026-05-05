# Auftrag #2 – Architekturdiagramm IST/SOLL

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)

## IST-Zustand

| Komponente | Details |
|------------|---------|
| Server | crmserver.internal.ch |
| OS | CentOS 6.6 |
| Webserver | Apache 2.2.15 |
| PHP | 5.3.3 |
| Datenbank | MySQL 5.1.73 (Port 3306) |
| CRM | Vtiger 6.1.0 |
| Firewall | keine |

## SOLL-Zustand

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

## Vergleich IST / SOLL

| Komponente | IST | SOLL | Grund |
|------------|-----|------|-------|
| OS | CentOS 6.6 | Ubuntu 24.04 LTS | CentOS EOL, Ubuntu LTS bis 2029 |
| Webserver | Apache 2.2.15 | Apache 2.4.62 | EOL, Sicherheit und Performance |
| PHP | 5.3.3 | 8.3 | EOL, Vtiger 8 benötigt PHP 8.x |
| Datenbank | MySQL 5.1.73 | MariaDB 11.4 LTS | EOL, MariaDB aktiv weiterentwickelt |
| CRM | Vtiger 6.1.0 | Vtiger 8.0 | EOL, aktuelle Version |
| Firewall | keine | MikroTik | Sicherheit erhöhen |

## Netzwerk

```
Subnetz:  192.168.10.0/24
Gateway:  192.168.10.1
DNS:      8.8.8.8
SOLL-IP:  192.168.10.x
```

$\textcolor{#8b949e}{\text{Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit }} \textcolor{#D4622A}{\text{Claude AI Pro}} \textcolor{#8b949e}{\text{ generiert.}}$
