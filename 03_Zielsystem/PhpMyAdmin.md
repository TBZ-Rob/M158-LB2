# Auftrag #8 – PhpMyAdmin

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Tool](https://img.shields.io/badge/Tool-phpMyAdmin-f89820)
![Zugriff](https://img.shields.io/badge/Zugriff-Lokal-lightgrey)

## Installation

```bash
sudo apt install phpmyadmin -y
```

Während der Installation:
- Webserver: `apache2` gewählt
- Datenbank konfigurieren mit `dbconfig-common`: ✅ Ja

```bash
# Apache-Konfiguration einbinden
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin
sudo systemctl reload apache2
```

---

## Zugriff

PhpMyAdmin ist erreichbar unter:

```
https://crmserver.internal.ch/phpmyadmin
```

Login mit dem MariaDB-Root-Benutzer oder dem Vtiger-Benutzer.

---

## Sicherheit

Da PhpMyAdmin ein bekanntes Angriffsziel ist, wurde der Zugriff auf localhost beschränkt:

<details>
<summary>Zugriffsbeschränkung in Apache-Config anzeigen</summary>

```apacheconf
# /etc/apache2/conf-available/phpmyadmin.conf (Ergänzung)
<Directory /usr/share/phpmyadmin>
    <RequireAny>
        Require ip 127.0.0.1
        Require ip 192.168.10.0/24
    </RequireAny>
</Directory>
```

</details>

```bash
sudo systemctl reload apache2
```

---

## Verwendung während Migration

PhpMyAdmin wurde während der Migration genutzt für:
- Überprüfung des importierten Datenbankdumps
- Kontrolle der Tabellenstruktur nach jedem Upgrade-Schritt
- Manuelle SQL-Queries zur Fehlerdiagnose

---

## Checkliste

- [x] PhpMyAdmin installiert
- [x] Apache-Integration aktiviert
- [x] Erreichbar unter `/phpmyadmin`
- [x] Zugriff auf lokales Netz (192.168.10.0/24) beschränkt
- [x] Für Migrationskontrolle eingesetzt

$\textcolor{#8b949e}{\text{Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit }} \textcolor{#D4622A}{\text{Claude AI Pro}} \textcolor{#8b949e}{\text{ generiert.}}$
