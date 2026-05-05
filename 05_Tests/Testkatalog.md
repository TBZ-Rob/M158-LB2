# Auftrag #12 – Testing

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Tests](https://img.shields.io/badge/Tests-25_Testfälle-blue)
![Ergebnis](https://img.shields.io/badge/Ergebnis-Alle_bestanden-brightgreen)

## Testkonzept

Ziel der Tests ist der Nachweis, dass die Migration erfolgreich war und das System vollständig funktionsfähig ist. Die Tests decken Netzwerk, Dienste, Datenbank, Webserver und Vtiger-Funktionen ab.

---

## Testkatalog

### 🌐 Netzwerk & Erreichbarkeit

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T01 | `ping crmserver.internal.ch` | Antwort von 192.168.10.x | ✅ |
| T02 | HTTP-Aufruf `http://crmserver.internal.ch` | Redirect auf HTTPS | ✅ |
| T03 | HTTPS-Aufruf `https://crmserver.internal.ch` | Vtiger Loginseite erscheint | ✅ |
| T04 | SSH-Verbindung `ssh user@crmserver.internal.ch` | Verbindung erfolgreich | ✅ |
| T05 | SFTP-Verbindung `sftp sftpuser@crmserver.internal.ch` | Verbindung erfolgreich | ✅ |

### 🔧 Dienste

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T06 | `systemctl status apache2` | active (running) | ✅ |
| T07 | `systemctl status mariadb` | active (running) | ✅ |
| T08 | `systemctl status ssh` | active (running) | ✅ |
| T09 | Apache nach Neustart verfügbar | Dienst startet automatisch | ✅ |
| T10 | MariaDB nach Neustart verfügbar | Dienst startet automatisch | ✅ |

### 🗄️ Datenbank

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T11 | Login MariaDB als `vtiger@localhost` | Erfolgreich | ✅ |
| T12 | Datenbank `vtigercrm` vorhanden | Tabellen sichtbar | ✅ |
| T13 | Datensätze in `vtiger_accounts` | Kundendaten aus Migration vorhanden | ✅ |
| T14 | PhpMyAdmin erreichbar | Login möglich | ✅ |

### 🖥️ Vtiger CRM – Funktionen

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T15 | Login mit Admin-Benutzer | Erfolgreich, Dashboard erscheint | ✅ |
| T16 | Login mit normalem Benutzer | Erfolgreich | ✅ |
| T17 | Kontakt anzeigen | Bestehende Kontakte aus IST-System vorhanden | ✅ |
| T18 | Neuen Kontakt erstellen | Kontakt wird gespeichert | ✅ |
| T19 | Kontakt bearbeiten | Änderungen werden gespeichert | ✅ |
| T20 | Kontakt löschen | Kontakt wird entfernt | ✅ |
| T21 | Angebot erstellen | Formular funktioniert, Speicherung OK | ✅ |
| T22 | Kalender / Aufgaben | Einträge sichtbar und bearbeitbar | ✅ |
| T23 | Dateiupload in Vtiger | Upload erfolgreich | ✅ |

### 💾 Backup

| # | Testfall | Erwartetes Ergebnis | Status |
|---|----------|-------------------|--------|
| T24 | Manueller Backup-Lauf | Dateien in `/mnt/backup/vtiger/` vorhanden | ✅ |
| T25 | DB-Dump entpacken & prüfen | SQL-Inhalt lesbar und vollständig | ✅ |

---

## Testergebnis

```
Testfälle gesamt:    25
Bestanden:           25 ✅
Fehlgeschlagen:       0 ❌
```

> Alle Verbindungen, Dienste und Vtiger-Funktionen wurden erfolgreich getestet.
> Die Migration gilt als vollständig und korrekt abgeschlossen.

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit KI-Unterstützung generiert.</sub>
