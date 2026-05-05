# Auftrag #14 – Deployment

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Typ](https://img.shields.io/badge/Typ-Halbautomatisiert-yellow)
![Downtime](https://img.shields.io/badge/Downtime-Minimal-brightgreen)

## Übersicht

Das Deployment beschreibt den finalen Go-Live des SOLL-Systems und die
Ausserdienststellung des IST-Systems. Da das System Mo–Sa aktiv genutzt wird,
wurde das Deployment auf ein Wartungsfenster ausserhalb der Geschäftszeiten gelegt.

---

## Deployment-Checkliste (Pre-Go-Live)

- [x] Alle Tests aus Testkatalog bestanden (25/25)
- [x] Finales Backup des IST-Systems erstellt
- [x] Finales Backup des SOLL-Systems erstellt (Snapshot)
- [x] Benutzer über geplante Wartung informiert
- [x] Rollback-Plan definiert

---

## Deployment-Ablauf

### 1. Benutzer informieren

Vor dem Go-Live wurde eine E-Mail an alle CRM-Benutzer verschickt:

> **Betreff:** CRM-System – Wartung & Migration  
> Das CRM-System wird am [Datum] um 22:00 Uhr für kurze Zeit nicht erreichbar sein.  
> Nach der Wartung ist das System unter der gewohnten Adresse wieder verfügbar.  
> Bei Fragen wenden Sie sich an die IT.

### 2. IST-System einfrieren

```bash
# Auf IST-System (CentOS 6.6)
# Apache stoppen – keine neuen Schreibzugriffe mehr
service httpd stop
```

### 3. Finalen DB-Dump erstellen

```bash
# Auf IST-System
mysqldump -u root -p vtigercrm > /tmp/vtigercrm_final.sql

# Auf SOLL-System übertragen
sftp sftpuser@192.168.10.x
put /tmp/vtigercrm_final.sql
```

### 4. Finalen Dump importieren

```bash
# Auf SOLL-System
sudo mariadb -u root -p vtigercrm < /home/sftpuser/upload/vtigercrm_final.sql
```

### 5. DNS / hosts-Eintrag umschalten

Auf allen Client-Geräten wurde `/etc/hosts` (Windows: `C:\Windows\System32\drivers\etc\hosts`)
aktualisiert:

```
# Alt (IST-System)
# 192.168.100.x    crmserver.internal.ch

# Neu (SOLL-System)
192.168.10.x    crmserver.internal.ch
```

### 6. SOLL-System freigeben

```bash
# Apache auf SOLL-System starten (falls noch nicht aktiv)
sudo systemctl start apache2

# Finaler Verbindungstest
curl -k https://crmserver.internal.ch
```

### 7. IST-System ausschalten

Nach erfolgreicher Verifikation des SOLL-Systems wurde die IST-VM heruntergefahren
und als Archiv-Snapshot aufbewahrt.

```bash
# IST-VM in VirtualBox herunterfahren
sudo shutdown -h now
```

---

## Rollback-Plan

Sollte das SOLL-System beim Go-Live Probleme zeigen:

1. Apache auf SOLL-System stoppen
2. `hosts`-Einträge zurück auf IST-System IP setzen
3. Apache auf IST-System starten
4. Fehleranalyse und Behebung
5. Neues Deployment planen

---

## Go-Live Ergebnis

| Parameter | Wert |
|-----------|------|
| Downtime | ca. 15 Minuten |
| Rollback notwendig | Nein |
| Alle Benutzer erreichbar | ✅ |
| Datenverlust | Keiner |

---

## Checkliste

- [x] Benutzer informiert
- [x] IST-System gestoppt (kein weiterer Schreibzugriff)
- [x] Finaler DB-Dump erstellt und importiert
- [x] DNS/hosts-Eintrag umgeschaltet
- [x] SOLL-System freigegeben
- [x] Funktionstest nach Go-Live bestanden
- [x] IST-VM als Archiv-Snapshot gesichert
- [x] Downtime unter 30 Minuten

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit KI-Unterstützung generiert.</sub>
