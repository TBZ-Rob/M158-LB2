# Arbeitsjournal

## Tag 1 – Planung

### Netzwerk / SSH Zugang IST-System

Ziel war es, per PuTTY auf die IST-VM zuzugreifen.

Zuerst wurde versucht die VM über eine Netzwerkbrücke (Realtek USB GbE / Intel WiFi 6E) zu verbinden. Das Interface `eth0` zeigte dabei `NO-CARRIER` – die VM erhielt keine Verbindung. Auch ein Host-only Adapter brachte keine IP.

Lösung: NAT mit Port-Forwarding in VirtualBox eingerichtet.

```
Host: localhost
Port: 2222
User: root
SSH-Client: PuTTY
```

### IST-Analyse

Folgende Befehle wurden auf dem IST-System ausgeführt:

```bash
# OS Version
cat /etc/redhat-release
# Ausgabe: CentOS release 6.6 (Final)

# Webserver Version
httpd -v
# Ausgabe: Server version: Apache/2.2.15 (Unix)
#          Server built: Oct 16 2014 14:48:21

# PHP Version
php -v
# Ausgabe: PHP 5.3.3 (cli) (built: Oct 16 2014 10:57:05)

# MySQL Version
mysql --version
# Ausgabe: mysql Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64)

# Vtiger Version
mysql -u root -p -e "SELECT * FROM vtiger_version;" vtigercrm
# Ausgabe:
# +----+-------------+-----------------+
# | id | old_version | current_version |
# +----+-------------+-----------------+
# |  1 | 6.1.0       | 6.1.0           |
# +----+-------------+-----------------+
```

### Architekturdiagramm IST/SOLL

IST- und SOLL-Diagramm in Visio erstellt. Diagramme zeigen die Dienste auf dem Server sowie deren Abhängigkeiten (Apache → Vtiger, Apache → PHP → MySQL).

### Netzwerkplanung SOLL-System

Für die spätere Kommunikation zwischen IST- und SOLL-VM wird ein eigenes NAT-Netzwerk in VirtualBox eingerichtet:

```
Name:    crm-migration
Subnetz: 192.168.100.0/24
Gateway: 192.168.100.1
SOLL-IP: 192.168.100.10
```

### GitHub Repo

Repo-Struktur aufgesetzt und folgende Files erstellt:
- `README.md`
- `Arbeitsjournal.md`
- `01_Planung/Projektplan.md`
- `01_Planung/IST-Analyse.md`
- `01_Planung/Architekturdiagramm.md`