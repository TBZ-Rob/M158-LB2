# Auftrag #9 – SFTP-Zugang

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Protokoll](https://img.shields.io/badge/Protokoll-SFTP-blue)
![Chroot](https://img.shields.io/badge/Chroot-aktiviert-green)

## Übersicht

Für den Dateitransfer wurde SFTP (SSH File Transfer Protocol) eingerichtet.
SFTP nutzt die bestehende SSH-Infrastruktur und ist deutlich sicherer als FTP/FTPS,
da alle Daten verschlüsselt übertragen werden.

---

## SFTP-Benutzer anlegen

```bash
# Dedizierter SFTP-User ohne Shell-Zugang
sudo useradd -m -s /sbin/nologin sftpuser
sudo passwd sftpuser

# Verzeichnis-Berechtigungen für Chroot
sudo chown root:root /home/sftpuser
sudo chmod 755 /home/sftpuser

# Upload-Verzeichnis erstellen (Schreibzugriff für User)
sudo mkdir /home/sftpuser/upload
sudo chown sftpuser:sftpuser /home/sftpuser/upload
```

---

## SSH-Konfiguration (Chroot)

<details>
<summary>/etc/ssh/sshd_config – SFTP-Chroot Konfiguration anzeigen</summary>

```sshd_config
# SFTP-Subsystem auf internen Handler umstellen
Subsystem sftp internal-sftp

# Chroot für SFTP-User
Match User sftpuser
    ChrootDirectory /home/sftpuser
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no
```

</details>

```bash
sudo systemctl restart ssh
```

> 🔒 **Chroot:** Der SFTP-User ist auf sein Heimverzeichnis beschränkt und kann nicht auf andere Teile des Dateisystems zugreifen. Shell-Zugang ist deaktiviert.

---

## Test

```bash
# Verbindungstest von Client aus
sftp sftpuser@crmserver.internal.ch
```

```
Connected to crmserver.internal.ch.
sftp> ls
upload
sftp> pwd
Remote working directory: /
```

---

## Verwendung während Migration

Der SFTP-Zugang wurde für folgende Aufgaben genutzt:
- Übertragung der Vtiger-Datendateien vom IST- auf das SOLL-System
- Upload von Patch-Dateien für Vtiger-Upgrades
- Transfer von Backup-Dateien

---

## Checkliste

- [x] SFTP-User `sftpuser` angelegt (ohne Shell)
- [x] Chroot-Jail konfiguriert
- [x] TCP-Forwarding und X11 deaktiviert
- [x] Upload-Verzeichnis mit korrekten Berechtigungen
- [x] Verbindung getestet
- [x] Für Datenmigration eingesetzt

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit Claude AI Pro generiert.</sub>
