 # Auftrag #4 – DNS

![Status](https://img.shields.io/badge/Status-Abgeschlossen-brightgreen)
![Methode](https://img.shields.io/badge/Methode-hosts--Datei-blue)
![Host](https://img.shields.io/badge/Host-crmserver.internal.ch-lightgrey)

## Übersicht

Für die lokale Namensauflösung wurde kein separater DNS-Server eingerichtet.
Stattdessen wurde die Datei `/etc/hosts` verwendet, um den Hostnamen
`crmserver.internal.ch` der IP-Adresse des SOLL-Systems zuzuweisen.

> **Warum `.internal` statt `.local`?**  
> `.local` ist für mDNS (Multicast DNS / Bonjour) reserviert und kann zu
> Konflikten führen. `.internal` ist die empfohlene Alternative für
> interne Hostnamen ohne eigenen DNS-Server.

---

## Konfiguration

### /etc/hosts

<details>
<summary>Eintrag in /etc/hosts anzeigen</summary>

```bash
# /etc/hosts – Eintrag für CRM-Server
192.168.10.x    crmserver.internal.ch
```

Befehl zum Bearbeiten:
```bash
sudo nano /etc/hosts
```

</details>

---

## Test

Die Namensauflösung wurde mit `ping` verifiziert:

```bash
ping crmserver.internal.ch
```

```
PING crmserver.internal.ch (192.168.10.x): 56 data bytes
64 bytes from 192.168.10.x: icmp_seq=0 ttl=64 time=0.x ms
```

✅ Hostname wird korrekt aufgelöst.

---

## Checkliste

- [x] Hostname definiert (`crmserver.internal.ch`)
- [x] `/etc/hosts` Eintrag gesetzt
- [x] Namensauflösung mit `ping` getestet
- [x] Keine Probleme aufgetreten

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit $\textcolor{#D4622A}{\text{Claude AI Pro}}$ generiert.</sub>
