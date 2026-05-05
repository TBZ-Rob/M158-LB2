# Auftrag #11 – Backup

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Typ](https://img.shields.io/badge/Typ-Automatisiert-green)
![Speicher](https://img.shields.io/badge/Speicher-Externes_Laufwerk-blue)

## Backup-Konzept

| Parameter | Wert |
|-----------|------|
| Datenbank | Täglich, vollständiger `mysqldump` |
| Dateien | Täglich, komprimiertes Archiv |
| Speicherort | Externes Laufwerk (`/mnt/backup`) |
| Aufbewahrung | 7 Tage (danach automatisch gelöscht) |
| Zeitplan | Täglich 02:00 Uhr |

---

## Backup-Skript

```bash
sudo nano /usr/local/bin/vtiger-backup.sh
```

<details>
<summary>Backup-Skript anzeigen</summary>

```bash
#!/bin/bash
# ============================================
# Vtiger CRM – Backup Skript
# Speicherort: /usr/local/bin/vtiger-backup.sh
# ============================================

BACKUP_DIR="/mnt/backup/vtiger"
DATE=$(date +%Y-%m-%d)
DB_USER="vtiger"
DB_PASS="sicheres_passwort"
DB_NAME="vtigercrm"
WEB_DIR="/var/www/html/vtigercrm"
RETENTION_DAYS=7

# Verzeichnis erstellen falls nicht vorhanden
mkdir -p "$BACKUP_DIR"

echo "[$(date)] Backup gestartet..."

# --- Datenbank-Backup ---
echo "[$(date)] Datenbank wird gesichert..."
mysqldump -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" | gzip > "$BACKUP_DIR/db_$DATE.sql.gz"

# --- Datei-Backup ---
echo "[$(date)] Dateien werden gesichert..."
tar -czf "$BACKUP_DIR/files_$DATE.tar.gz" "$WEB_DIR"

# --- Alte Backups löschen ---
echo "[$(date)] Backups älter als $RETENTION_DAYS Tage werden gelöscht..."
find "$BACKUP_DIR" -name "*.gz" -mtime +$RETENTION_DAYS -delete

echo "[$(date)] Backup abgeschlossen."
```

</details>

```bash
# Skript ausführbar machen
sudo chmod +x /usr/local/bin/vtiger-backup.sh

# Manueller Test
sudo /usr/local/bin/vtiger-backup.sh
```

---

## Cron Job

```bash
sudo crontab -e
```

```cron
# Vtiger Backup – täglich um 02:00 Uhr
0 2 * * * /usr/local/bin/vtiger-backup.sh >> /var/log/vtiger-backup.log 2>&1
```

---

## Externes Laufwerk einbinden

```bash
# UUID des Laufwerks ermitteln
sudo blkid

# Eintrag in /etc/fstab
UUID=xxxx-xxxx  /mnt/backup  ext4  defaults,nofail  0  2

# Mounten
sudo mkdir -p /mnt/backup
sudo mount -a
```

---

## Backup verifizieren

```bash
# Vorhandene Backups anzeigen
ls -lh /mnt/backup/vtiger/

# DB-Backup testen (Entpacken & prüfen)
gunzip -c /mnt/backup/vtiger/db_$(date +%Y-%m-%d).sql.gz | head -20
```

---

## Checkliste

- [x] Backup-Skript erstellt (`/usr/local/bin/vtiger-backup.sh`)
- [x] DB-Backup via `mysqldump`
- [x] Datei-Backup via `tar`
- [x] Aufräumfunktion (7-Tage-Retention)
- [x] Cron Job täglich 02:00 Uhr
- [x] Externes Laufwerk als Speicherort
- [x] Manueller Test erfolgreich

$\textcolor{#8b949e}{\text{Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit }} \textcolor{#D4622A}{\text{Claude AI Pro}} \textcolor{#8b949e}{\text{ generiert.}}$
