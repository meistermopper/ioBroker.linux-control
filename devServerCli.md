# ioBroker dev-server Befehlsreferenz

| Befehl | Beschreibung |
| :--- | :--- |
| **`npm i -D @iobroker/dev-server`** | Installiert den `dev-server` als Entwicklungsabhängigkeit (*DevDependency*) in deinem Projekt. |
| **`npx dev-server setup`** | Initialisiert die Testumgebung, lädt den `js-controller` sowie den `admin`-Adapter herunter und bereitet den Ordner `.dev-server/` vor. |
| **`npx dev-server watch`** | **(Empfohlen)** Startet das Testsystem im Watch-Modus. Code-Änderungen werden live kompiliert und der Adapter automatisch im Terminal neu gestartet. |
| **`npx dev-server run`** | Fährt die ioBroker-Testumgebung ganz normal hoch, reagiert jedoch nicht automatisch auf Code-Änderungen. |
| **`npx dev-server clean`** | Setzt alle lokalen Test-Zustände (States) und Objekte in den temporären Redis-Datenbanken zurück, ohne die Adapter zu löschen. |
| **`npx dev-server upload`** | Zwingt das System, die HTML/CSS-Dateien deines Adapters (aus dem `admin/`-Ordner) manuell in die Testumgebung hochzubauen. |
| **`Remove-Item -Recurse -Force .dev-server`** | PowerShell-Befehl: Löscht das komplette temporäre ioBroker-Verzeichnis restlos von deiner Festplatte. |
| **`Remove-Item -Force .dev-server.json, .dev-server.rc`** | PowerShell-Befehl: Entfernt die lokalen Konfigurationsdateien des dev-servers aus dem Hauptverzeichnis. |
| **`npm uninstall @iobroker/dev-server`** | Deinstalliert das Paket komplett und entfernt den Eintrag aus deiner `package.json`. |

---
**Standard-URL nach dem Start:** [http://localhost:8081](http://localhost:8081)
