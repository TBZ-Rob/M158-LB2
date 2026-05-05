# Auftrag #7 – MySQL/MariaDB-Datenbankserver

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![MariaDB](https://img.shields.io/badge/MariaDB-11.4_LTS-blue)

## Installation

```bash
sudo apt install mariadb-server -y
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

Version prüfen:
```bash
mariadb --version
```
```
mariadb  Ver 15.1 Distrib 11.4.x-MariaDB, for debian-linux-gnu (x86_64)
```

---

## Sicherheitskonfiguration

```bash
sudo mysql_secure_installation
```

<details>
<summary>Durchgeführte Schritte anzeigen</summary>

| Schritt | Wert |
|---------|------|
| Unix socket authentication | beibehalten |
| Root-Passwort setzen | ✅ Ja |
| Anonyme Benutzer entfernen | ✅ Ja |
| Remote Root-Login deaktivieren | ✅ Ja |
| Test-Datenbank entfernen | ✅ Ja |
| Berechtigungen neu laden | ✅ Ja |

</details>

---

## Datenbank & Benutzer für Vtiger

```sql
-- Als root einloggen
sudo mariadb -u root -p

-- Datenbank erstellen
CREATE DATABASE vtigercrm CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Dedizierter Benutzer (nur lokaler Zugriff)
CREATE USER 'vtiger'@'localhost' IDENTIFIED BY 'sicheres_passwort';

-- Berechtigungen nur auf Vtiger-DB
GRANT ALL PRIVILEGES ON vtigercrm.* TO 'vtiger'@'localhost';
FLUSH PRIVILEGES;
```

> 🔒 Der DB-Benutzer `vtiger` hat ausschliesslich Zugriff auf die `vtigercrm`-Datenbank und nur von `localhost`.

---

## Konfiguration

<details>
<summary>/etc/mysql/mariadb.conf.d/50-server.cnf – Anpassungen anzeigen</summary>

```ini
[mysqld]
# Nur lokale Verbindungen erlauben
bind-address = 127.0.0.1

# Zeichensatz
character-set-server = utf8mb4
collation-server     = utf8mb4_unicode_ci

# Performance
innodb_buffer_pool_size = 256M
max_connections         = 100
```

</details>

```bash
sudo systemctl restart mariadb
```

---

## Checkliste

- [x] MariaDB 11.4 LTS installiert
- [x] `mysql_secure_installation` durchgeführt
- [x] Datenbank `vtigercrm` erstellt (UTF-8)
- [x] Dedizierter User `vtiger@localhost` angelegt
- [x] Zugriff auf `localhost` beschränkt
- [x] Remote Root-Login deaktiviert

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit <span style="color: #D4622A">Claude AI Pro</span> generiert.</sub>
