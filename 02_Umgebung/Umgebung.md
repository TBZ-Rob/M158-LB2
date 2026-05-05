# Auftrag #3 – Umgebung aufbauen/einrichten

## Übersicht

| Komponente | IST (alt) | SOLL (neu) |
|------------|-----------|------------|
| Hypervisor | Oracle VirtualBox | VMware Workstation Pro |
| OS | CentOS 6.6 | Ubuntu 24.04.2 LTS (Noble Numbat) |
| IP | DHCP | 192.168.10.x (statisch) |
| Firewall | keine | MikroTik (externes Gerät) |

## SOLL-VM – Ubuntu 24.04.2 LTS

### Installation
Ubuntu 24.04.2 LTS wurde als neue VM in VMware Workstation Pro installiert.
Die ISO wurde von der offiziellen Ubuntu-Webseite bezogen.

### Netzwerkkonfiguration

| Parameter | Wert |
|-----------|------|
| Netzwerk | 192.168.10.0/24 |
| IP (SOLL-VM) | 192.168.10.x |
| Gateway | 192.168.10.1 |
| DNS | 8.8.8.8 |
| Netzwerkadapter | Bridged (LAN-Port) |

### Firewall
Im Netzwerk ist eine MikroTik-Firewall integriert, welche sämtlichen
eingehenden Traffic von aussen blockiert. Die VMs kommunizieren
ausschliesslich intern im Subnetz 192.168.10.0/24.

## Snapshots

Während des gesamten Projekts wurden regelmässig Snapshots der VMs erstellt.
Ziel war es, jederzeit auf einen funktionierenden Zustand zurückgreifen zu
können – insbesondere vor grösseren Konfigurationsänderungen oder
Migrationsschritten. Dies erlaubte es, Fehler rückgängig zu machen, ohne
die gesamte Umgebung neu aufsetzen zu müssen.

## Probleme und Lösungen

### Problem 1 – SSH-Verbindung zur IST-VM (CentOS 6.6)
**Problem:** Da CentOS 6.6 sehr alt ist, unterstützt der SSH-Server nur
veraltete Key-Algorithmen, welche moderne SSH-Clients standardmässig
ablehnen.

**Lösung:** Verbindung mit explizit erlaubten Legacy-Algorithmen:
```bash
ssh -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    root@localhost -p 2222
```

### Problem 2 – Netzwerkadapter IST-VM
**Problem:** Der Netzwerkadapter der alten CentOS-VM zeigte `NO-CARRIER`
bei Verwendung einer Netzwerkbrücke. Die VM erhielt keine IP-Adresse.

**Lösung:** NAT mit Port-Forwarding in VirtualBox eingerichtet
(Host-Port 2222 → VM-Port 22).

### Problem 3 – Ubuntu-VM
Die Installation der Ubuntu-VM verlief ohne nennenswerte Probleme.
Dank bestehender Erfahrung mit Ubuntu konnte die Umgebung zügig
aufgesetzt werden.

<sub>Hinweis: Diagramme, Rechtschreibung und Repo-Struktur wurden mit $\textcolor{#D4622A}{\text{Claude AI Pro}}$ generiert.</sub>
