# 🧱 Minecraft Paper Server – Installation & Einrichtung (Ubuntu 22.04)

**📅 Datum:** 08.06.2025  
**👨‍💻 Autor:** Marcel Ebel (aka ScriptRanger)  
**📂 Pfad:** `/opt/minecraft`  
**💡 Ziel:** Minecraft Server auf Ubuntu VM einrichten, updaten, sichern und kindersicher konfigurieren.

---

## 🔧 Voraussetzungen
- Ubuntu Server 22.04 LTS
- Benutzer `marcel` mit `sudo`-Rechten
- Java 21 (`openjdk-21-jdk`)
- Ordnerstruktur unter `/opt/minecraft` + `/opt/mc-backups`
- Dienststeuerung via `systemd`
- Netzwerk: intern via `192.168.178.90`, Port: `25565`

---

## 🛠️ Setup-Schritte

### 1. Java 21 installierens
```bash
sudo apt update && sudo apt install openjdk-21-jdk -y
```

### 2. Benutzer `minecraft` anlegen
```bash
sudo adduser --system --home /opt/minecraft --group minecraft
```

### 3. Verzeichnis vorbereiten
```bash
sudo mkdir -p /opt/minecraft
sudo chown -R minecraft:minecraft /opt/minecraft
```

### 4. Neueste PaperMC-Version downloaden
Aktuellste Build von: [https://papermc.io/downloads/paper](https://papermc.io/downloads/paper)

```bash
cd /opt/minecraft
sudo wget https://api.papermc.io/v2/projects/paper/versions/1.21.5/builds/25/downloads/paper-1.21.5-25.jar -O paperclip.jar
sudo chown minecraft:minecraft paperclip.jar
```

### 5. `eula.txt` akzeptieren
```bash
echo "eula=true" | sudo tee /opt/minecraft/eula.txt
sudo chown minecraft:minecraft /opt/minecraft/eula.txt
```

### 6. `systemd`-Service erstellen
```bash
sudo nano /etc/systemd/system/minecraft.service
```
**Inhalt:**
```ini
[Unit]
Description=Minecraft Server
After=network.target

[Service]
User=minecraft
WorkingDirectory=/opt/minecraft
ExecStart=/usr/bin/java -Xms2G -Xmx2G -jar paperclip.jar nogui
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable minecraft
sudo systemctl start minecraft
```

---

## 🛡️ Sicherheit & Kinderschutz

### 🔕 Mobs deaktivieren:
In `server.properties`:
```properties
spawn-monsters=false
difficulty=peaceful
```

### 👨‍👩‍👧‍👦 Spawn-Schutz einstellen:
```properties
spawn-protection=16
```

### 🔒 Nur bekannte Spieler zulassen (optional):
```properties
white-list=true
```

> ⚠️ Whitelist-Setup folgt noch.

---

## 💾 Backup-Konzept (geplant)

Backups mit `tar` via CronJob oder Script in `/opt/mc-backups`. Beispiel folgt.

---

## 🧪 Version prüfen
```bash
cat /opt/minecraft/version_history.json
```
**Aktueller Stand:**
```json
{
  "oldVersion": "1.20.6-99-535dca5 (MC: 1.20.6)",
  "currentVersion": "1.21.5-25-def0532 (MC: 1.21.5)"
}
```

---

## 🚀 Verbindung
Im lokalen Netzwerk erreichbar unter:
```
IP: 192.168.178.90
Port: 25565
```

---

## 📌 Nächste Schritte
- Whitelist aktivieren und pflegen
- Backup-Script und CronJob einrichten
- Bildschirmfreigabe via `screen` oder `tmux`
- Langzeitpflege + externe Zugriffe (VPN/NAT)

---

**✅ Status:**  
> Der Server läuft stabil, ist aktuell und kindersicher konfiguriert. Perfekte Spielwiese für Servertraining, Sicherheit, Admin-Prozesse und Spaß!
