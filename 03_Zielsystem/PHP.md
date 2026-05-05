# Auftrag #6 – PHP

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![PHP](https://img.shields.io/badge/PHP-8.3-blueviolet)

## Installation

```bash
sudo apt install php8.3 php8.3-fpm -y
```

### Benötigte Extensions für Vtiger

```bash
sudo apt install -y \
  php8.3-mysql \
  php8.3-xml \
  php8.3-curl \
  php8.3-zip \
  php8.3-gd \
  php8.3-mbstring \
  php8.3-imap \
  php8.3-soap \
  php8.3-bcmath \
  php8.3-intl
```

Installierte Version prüfen:
```bash
php -v
```
```
PHP 8.3.x (cli) (built: ...)
Copyright (c) The PHP Group
```

---

## Konfiguration (php.ini)

<details>
<summary>/etc/php/8.3/apache2/php.ini – Anpassungen anzeigen</summary>

```ini
; Speicher & Limits
memory_limit = 512M
max_execution_time = 300
max_input_time = 300

; Upload
upload_max_filesize = 64M
post_max_size = 64M

; Fehlerbehandlung (Produktion)
display_errors = Off
log_errors = On
error_log = /var/log/php_errors.log

; Zeitzone
date.timezone = Europe/Zurich
```

</details>

```bash
# Apache nach Änderungen neu starten
sudo systemctl restart apache2
```

---

## Überprüfung

```bash
# Alle geladenen Extensions anzeigen
php -m

# PHP-Info im Browser (nach Test entfernen!)
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```

---

## Checkliste

- [x] PHP 8.3 installiert
- [x] Alle Vtiger-Extensions installiert
- [x] `php.ini` angepasst (Limits, Zeitzone, Fehlerlogging)
- [x] Apache neu gestartet
- [x] `info.php` nach Test wieder gelöscht

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit KI-Unterstützung generiert.</sub>
