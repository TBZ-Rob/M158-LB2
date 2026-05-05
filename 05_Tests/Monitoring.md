# Auftrag #13 – Monitoring

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Tool](https://img.shields.io/badge/Tool-Monit-blue)
![Alarmierung](https://img.shields.io/badge/Alarmierung-E--Mail-orange)
![Watchdog](https://img.shields.io/badge/Watchdog-Auto--Restart-green)

## Übersicht

Für das Monitoring wird **Monit** eingesetzt – ein leichtgewichtiges Tool,
das Dienste überwacht, bei Problemen automatisch neustartet (Watchdog)
und per E-Mail alarmiert.

---

## Installation

```bash
sudo apt install monit -y
sudo systemctl enable monit
```

---

## Konfiguration

### Haupt-Konfiguration

```bash
sudo nano /etc/monit/monitrc
```

<details>
<summary>monitrc – Grundkonfiguration anzeigen</summary>

```
# Monitoring-Intervall (alle 60 Sekunden)
set daemon 60

# Logdatei
set logfile /var/log/monit.log

# E-Mail Alarmierung
set mailserver smtp.gmail.com port 587
    username "monitoring@internal.ch"
    password "app_passwort"
    using tls

set mail-format {
    from:    monitoring@internal.ch
    subject: [MONIT] $SERVICE – $EVENT
    message: Dienst: $SERVICE
             Host:   $HOST
             Event:  $EVENT
             Datum:  $DATE
}

set alert admin@internal.ch

# Monit Web-Interface (nur lokal)
set httpd port 2812
    use address localhost
    allow localhost
```

</details>

### Dienste überwachen

```bash
sudo nano /etc/monit/conf.d/vtiger-services.conf
```

<details>
<summary>Dienst-Monitoring Konfiguration anzeigen</summary>

```
# Apache Webserver
check process apache2 with pidfile /run/apache2/apache2.pid
    start program = "/bin/systemctl start apache2"
    stop  program = "/bin/systemctl stop apache2"
    if failed host crmserver.internal.ch port 80 protocol http then restart
    if failed host crmserver.internal.ch port 443 protocol https then restart
    if 3 restarts within 5 cycles then alert
    alert admin@internal.ch

# MariaDB Datenbank
check process mariadb with pidfile /run/mysqld/mysqld.pid
    start program = "/bin/systemctl start mariadb"
    stop  program = "/bin/systemctl stop mariadb"
    if failed host 127.0.0.1 port 3306 protocol mysql then restart
    if 3 restarts within 5 cycles then alert
    alert admin@internal.ch

# SSH Dienst
check process sshd with pidfile /run/sshd.pid
    start program = "/bin/systemctl start ssh"
    stop  program = "/bin/systemctl stop ssh"
    if failed port 22 protocol ssh then restart
    alert admin@internal.ch

# Speicherplatz überwachen
check filesystem rootfs with path /
    if space usage > 85% then alert

check filesystem backup with path /mnt/backup
    if space usage > 90% then alert
```

</details>

```bash
# Konfiguration testen & Monit starten
sudo monit -t
sudo systemctl start monit
sudo monit reload
```

---

## Logging

Alle Ereignisse werden in `/var/log/monit.log` protokolliert:

```bash
# Live-Log anzeigen
sudo tail -f /var/log/monit.log
```

```
[CET May  5 02:00:01] info     : 'apache2' Monit 5.33 started
[CET May  5 02:01:02] info     : 'apache2' check passed
[CET May  5 02:01:02] info     : 'mariadb' check passed
```

---

## Monit Web-Interface

```
http://localhost:2812
```

Zeigt Echtzeit-Status aller überwachten Dienste.

---

## Watchdog – Automatischer Neustart

Fällt ein Dienst aus, startet Monit ihn automatisch neu und sendet
eine E-Mail-Benachrichtigung. Nach 3 Neustarts innerhalb von 5 Zyklen
wird ein Alert ausgelöst.

---

## Checkliste

- [x] Monit installiert und aktiviert
- [x] Apache überwacht (HTTP + HTTPS)
- [x] MariaDB überwacht (Port 3306)
- [x] SSH überwacht
- [x] Speicherplatz überwacht (Root + Backup)
- [x] E-Mail Alarmierung konfiguriert
- [x] Automatischer Neustart (Watchdog) aktiv
- [x] Logging in `/var/log/monit.log`

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit $\textcolor{#D4622A}{\text{Claude AI Pro}}$ generiert.</sub>
