# Auftrag #2 – Architekturdiagramm IST/SOLL

## IST-Diagramm

![IST-Diagramm](Diagramme/IST-Diagramm.png)

### IST-Zustand
| Komponente | Details |
|------------|---------|
| Server | crmserver.sample.ch |
| IP | 192.168.100.x (DHCP) |
| OS | CentOS 6.6 |
| Webserver | Apache 2.2.15 |
| PHP | 5.3.3 |
| Datenbank | MySQL 5.1.73 (Port 3306) |
| CRM | Vtiger 6.1.0 |
| Firewall | keine |

## SOLL-Diagramm

![alt text](../00_Files/Screenshots/IST-Grafik.png)

### SOLL-Zustand
| Komponente | Details |
|------------|---------|
| Server | crmserver.sample.ch |
| IP | 192.168.100.10 |
| OS | Ubuntu 22.04 LTS |
| Webserver | Apache 2.4.x |
| PHP | 8.2.x |
| Datenbank | MariaDB 10.x (Port 3306) |
| CRM | Vtiger 7.x |
| Firewall | UFW (Port 22, 80, 443) |

## Netzwerk
- NAT-Netzwerk: `crm-migration`
- Subnetz: 192.168.100.0/24
- Gateway: 192.168.100.1

## Vergleich IST / SOLL

| Komponente | IST | SOLL | Grund |
|------------|-----|------|-------|
| OS | CentOS 6.6 | Ubuntu 22.04 | CentOS EOL, Ubuntu LTS bis 2027 |
| Webserver | Apache 2.2.15 | Apache 2.4.x | EOL, Sicherheit und Performance |
| PHP | 5.3.3 | 8.2.x | EOL, Vtiger 7 benötigt PHP 7.4+ |
| Datenbank | MySQL 5.1.73 | MariaDB 10.x | EOL, MariaDB aktiv weiterentwickelt |
| CRM | Vtiger 6.1.0 | Vtiger 7.x | EOL, aktuelle Version |
| Firewall | keine | UFW | Sicherheit erhöhen |