# Homey MCP Server – Konzept & Zusammenfassung

## Ziel
Einen MCP Server erstellen, der die **Homey Local REST API** (Homey Pro) anbindet,
damit Claude direkt mit Homey-Geräten und Flows interagieren kann.

## Tech Stack

| | |
|---|---|
| **Sprache** | TypeScript |
| **Runtime** | Node.js 18+ |
| **MCP SDK** | `@modelcontextprotocol/sdk` |
| **Transport** | stdio |

## Projektstruktur

```
homey-mcp/
├── src/
│   ├── index.ts              # Einstiegspunkt, MCP Server Setup
│   ├── homey-client.ts       # Zentraler HTTP-Client für Homey API
│   └── tools/
│       ├── index.ts          # Tool-Registry (erweiterbar)
│       ├── devices.ts        # Geräte-Tools
│       └── flows.ts          # Flow-Tools
├── package.json
├── tsconfig.json
└── README.md
```

## Konfiguration

Via Umgebungsvariablen (`.env` oder direkt in Claude Desktop Config):

```
HOMEY_IP=192.168.x.x
HOMEY_TOKEN=<bearer-token>
```

## Phase 1: Tools

### Devices (`/api/manager/devices/device`)

| Tool | Beschreibung |
|------|-------------|
| `list_devices` | Alle Geräte auflisten, filterbar nach Zone/Klasse |
| `get_device` | Ein Gerät mit allen Capabilities |
| `set_device_capability` | Capability-Wert setzen (z.B. `onoff`, `dim`) |

### Flows

| Endpunkt | Beschreibung |
|---------|-------------|
| `GET /api/manager/flow/flow` | Standard Flows |
| `GET /api/manager/flowadvanced/flow` | Advanced Flows |

| Tool | Beschreibung |
|------|-------------|
| `list_flows` | Alle Flows auflisten (Standard + Advanced) |
| `get_flow` | Flow-Details abrufen |
| `trigger_flow` | Flow auslösen (nur mit manuellem Trigger) |

## API-Beschränkungen

- Nur Flows mit **"Dieser Flow wird gestartet"**-Trigger können manuell ausgelöst werden
- Lokale API nur erreichbar wenn PC und Homey im **gleichen Netzwerk**
- Nur **Homey Pro** unterstützt (nicht Bridge/Cloud)
- Bearer Token aus Homey Entwickler-Einstellungen erforderlich

## Erweiterbarkeit

Neue Bereiche (Zones, Apps, Insights, System) als eigene Module in `tools/` hinzufügen
und in `tools/index.ts` registrieren – keine Änderungen am Server-Core nötig.

## Einrichtung in Claude Desktop

Datei öffnen:
- **Mac**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "homey": {
      "command": "node",
      "args": ["/absoluter/pfad/zu/homey-mcp/dist/index.js"],
      "env": {
        "HOMEY_IP": "192.168.x.x",
        "HOMEY_TOKEN": "dein-token-hier"
      }
    }
  }
}
```

Claude Desktop neu starten – fertig.

## Voraussetzungen

| | |
|---|---|
| **Hardware** | Kein Extra-Hardware – normaler PC/Mac reicht |
| **Node.js** | Version 18+ (`node --version` zum Prüfen) |
| **Claude Desktop** | Für die einfachste Einrichtung |
| **Homey Pro** | Im gleichen lokalen Netzwerk wie der PC |
| **Bearer Token** | Aus den Homey Entwickler-Einstellungen |
