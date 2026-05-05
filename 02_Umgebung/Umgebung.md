# README.md

CRM Migration – crmserver.internal.ch

Migration des bestehenden CRM systems auf ein neues Betriebsystem mit aktuellem Web und Datenbankserver. Die CRM Applikation Vtiger wird auf die neueste Version aktualisiert und alle Daten vollständg migriert.

Die Ziele sind eine moderne Infrastruktur zu schaffen und die Sicherheit zu erhöhen. Dabei wird auch die Performance verbessert und das System stabiler gemacht.

Das Projekt ist in mehrere Phasen unterteilt welche nacheinander abgearbeitet werden. Und dabei wird darauf geachtet das keine Daten verloren gehen.

Die Migration soll möglichst ohne grosse ausfallzeit erfolgen damit die Benutzer kaum eingeschränkt sind.


# Projektplan.md

Das Projekt hat eine dauer von 5 Tagen und wird in einzelne Schritte unterteilt. Jeder Tag hat klare Aufgaben die erledigt werden müssen.

Am ersten Tag wird die Planung gemacht und die IST Analyse durchgeführt. Danach wird auch das Architekturdiagramm erstellt.

Am zweiten Tag wird die Umgebung aufgebaut und die VM installiert. Und das Netzwerk wird konfiguriert sowie die Firewall eingerichtet.

Am dritten Tag wird das Zielsystem installiert mit Apache PHP und MariaDB. Es werden auch zusätzliche Tools wie PhpMyAdmin und SFTP eingerichtet.

Am vierten Tag erfolgt die Migration der Daten und das Upgrade von Vtiger. Dabei müssen mehrere Schritte eingehalten werden.

Am fünften Tag werden Tests durchgeführt und das Deployment gemacht. Und danach wird das System produktiv geschaltet.


# IST-Analyse.md

Das aktuelle System läuft auf CentOS 6.6 und ist veraltet. Alle Komponenten sind bereits End of Life und erhalten keine Updates mehr.

Der Webserver ist Apache 2.2.15 und die PHP Version ist 5.3.3. Die Datenbank ist MySQL 5.1.73 und ebenfalls nicht mehr aktuell.

Die Analyse wurde mit verschiedenen Befehlen durchgeführt um die Versionen zu prüfen. Und diese wurden im System direkt ausgeführt.

```bash
cat /etc/redhat-release
httpd -v
php -v
mysql --version