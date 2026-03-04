# Agent 2 – Wetter-Agent mit MCP Server (Erweiterter Agent)

> **Ziel:** Einen erweiterten Copilot Agent erstellen, der über einen eigenen **MCP Server (Model Context Protocol)** Live-Wetterdaten von einer externen API abruft und dem Benutzer orts- und zeitbasierte Wetterauskünfte liefert.

---

## Lernziele

- Verstehen, was das **Model Context Protocol (MCP)** ist und wie es funktioniert
- Einen **MCP Server** mit Node.js/TypeScript erstellen
- Eine **externe Wetter-API** (OpenWeatherMap) anbinden
- Den MCP Server in **Microsoft Copilot Studio** als Aktion verbinden
- Einen **erweiterten Agenten** aufbauen, der Live-Daten verwendet
- Den Agenten **testen und vorführen**

---

## Schwierigkeitsgrad

🟡 **Fortgeschritten** – Grundkenntnisse aus Agent 1 werden vorausgesetzt. Basiswissen in Node.js/TypeScript ist hilfreich.

---

## Architekturübersicht

```
┌──────────────────────────────────────────────────────────────┐
│                    Copilot Studio Agent                       │
│                  "Wetter-Auskunft Agent"                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Wissensquelle: SharePoint (Reiserichtlinien, optional)      │
│                                                              │
│  Aktionen:                                                   │
│  └── MCP Server Verbindung                                   │
│       ├── Tool: get_current_weather                          │
│       │   → Aktuelles Wetter für einen Ort                   │
│       ├── Tool: get_weather_forecast                         │
│       │   → 5-Tage-Vorhersage für einen Ort                 │
│       └── Tool: get_weather_alert                            │
│           → Wetterwarnungen für eine Region                  │
│                                                              │
│  Topics:                                                     │
│  ├── Begrüßung (angepasst)                                  │
│  ├── Aktuelles Wetter abfragen                              │
│  ├── Wettervorhersage abfragen                              │
│  └── Verabschiedung                                          │
│                                                              │
└────────────────┬─────────────────────────────────────────────┘
                 │ MCP Protocol (JSON-RPC / Streamable HTTP)
                 ▼
┌──────────────────────────────────────────────────────────────┐
│              MCP Server (Node.js / TypeScript)                │
│         Gehostet auf Azure / lokal via Dev Tunnel             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Tools:                                                      │
│  ├── get_current_weather(city)                               │
│  ├── get_weather_forecast(city, days)                        │
│  └── get_weather_alert(city)                                 │
│                                                              │
│  Prompt: weather_advisor                                     │
│  Resource: weather://config                                  │
│                                                              │
└────────────────┬─────────────────────────────────────────────┘
                 │ HTTPS REST API
                 ▼
┌──────────────────────────────────────────────────────────────┐
│            OpenWeatherMap API (extern)                        │
│            https://api.openweathermap.org                     │
└──────────────────────────────────────────────────────────────┘
```

---

## Voraussetzungen

> **Hinweis:** Euer Account ist bereits eingerichtet. Ihr habt alle nötigen Lizenzen und Berechtigungen.

| Was | Details |
|---|---|
| **M365 Account** | Wurde für euch bereitgestellt (inkl. Copilot Studio) |
| **Copilot Studio Pro-Lizenz** | Bereits zugewiesen |
| **Browser** | HTML5-fähiger Browser (Edge, Chrome) |
| **Node.js** | Version 18 oder höher ([Download](https://nodejs.org/)) |
| **Visual Studio Code** | Empfohlen als Code-Editor ([Download](https://code.visualstudio.com/)) |
| **OpenWeatherMap API-Key** | Kostenlos registrieren unter https://openweathermap.org/api |

---

## Wichtige Links

| Portal / Tool | URL |
|---|---|
| **Copilot Studio** | https://copilotstudio.microsoft.com/ |
| **Power Platform Admin Center** | https://admin.powerplatform.microsoft.com/ |
| **OpenWeatherMap API** | https://openweathermap.org/api |
| **MCP TypeScript SDK (GitHub)** | https://github.com/modelcontextprotocol/typescript-sdk |
| **MCP Spezifikation** | https://spec.modelcontextprotocol.io/ |
| **Azure Portal** | https://portal.azure.com/ |

---

## Dokumentation & Hilfe

| Thema | Link |
|---|---|
| Was ist MCP? (Übersicht) | https://modelcontextprotocol.io/introduction |
| MCP Spezifikation (vollständig) | https://spec.modelcontextprotocol.io/ |
| MCP TypeScript SDK Docs | https://github.com/modelcontextprotocol/typescript-sdk#readme |
| MCP Server in Copilot Studio | https://learn.microsoft.com/de-de/microsoft-copilot-studio/agent-extend-action-mcp |
| Copilot Studio – Aktionen erstellen | https://learn.microsoft.com/de-de/microsoft-copilot-studio/advanced-plugin-actions |
| Copilot Studio – Benutzerdefinierte Connectors | https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-connectors |
| OpenWeatherMap API Docs | https://openweathermap.org/current |
| OpenWeatherMap 5-Tage-Vorhersage | https://openweathermap.org/forecast5 |
| Dev Tunnels (lokales Testen) | https://learn.microsoft.com/de-de/azure/developer/dev-tunnels/overview |
| Azure App Service Deployment | https://learn.microsoft.com/de-de/azure/app-service/quickstart-nodejs |
| Copilot Studio Agent erstellen | https://learn.microsoft.com/de-de/microsoft-copilot-studio/fundamentals-get-started |
| Topics erstellen & verwalten | https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-create-edit-topics |
| Agent veröffentlichen | https://learn.microsoft.com/de-de/microsoft-copilot-studio/publication-fundamentals-publish-channels |

---

## Vorführungsanleitung (Step-by-Step)

Die folgenden Schritte sind als **Trainer-Leitfaden** für die Live-Vorführung gedacht. Für jeden Schritt gibt es Hinweise, was demonstriert werden soll und worauf besonders hinzuweisen ist.

---

### Schritt 1 – Einführung: Was ist MCP?

**Dauer:** ca. 10 Minuten

**Erklärung für Teilnehmer:**

> Das **Model Context Protocol (MCP)** ist ein offener Standard, der es KI-Modellen (wie Copilot) ermöglicht, auf externe Werkzeuge und Datenquellen zuzugreifen. Stellt euch MCP wie einen **USB-Anschluss für KI** vor – eine standardisierte Schnittstelle, über die Agenten mit der Außenwelt kommunizieren.

**Zeigen Sie:**
- Die [MCP Spezifikation](https://spec.modelcontextprotocol.io/) im Browser
- Das Konzept: **Host** (Copilot Studio) → **Client** → **Server** (unser Wetter-Server)
- Die drei Hauptkonzepte von MCP:
  - **Tools:** Funktionen, die der Agent aufrufen kann (z. B. `get_current_weather`)
  - **Resources:** Datenquellen, die der Agent lesen kann
  - **Prompts:** Vordefinierte Prompt-Templates

**Architekturdiagramm erklären** (siehe oben)

📖 **Doku-Verweis:** [MCP Introduction](https://modelcontextprotocol.io/introduction) | [MCP Spezifikation](https://spec.modelcontextprotocol.io/)

---

### Schritt 2 – OpenWeatherMap API-Key erstellen

**Dauer:** ca. 5 Minuten

1. Öffnen Sie https://openweathermap.org/api
2. Erstellen Sie einen kostenlosen Account (oder zeigen Sie Ihren vorbereiteten)
3. Navigieren Sie zu **API keys** im Profil
4. Kopieren Sie den API-Key

**Zeigen Sie:**
- Die API-Dokumentation: https://openweathermap.org/current
- Einen Beispiel-API-Aufruf im Browser:
  ```
  https://api.openweathermap.org/data/2.5/weather?q=Berlin&appid=IHR_API_KEY&units=metric&lang=de
  ```
- Die JSON-Antwort erklären (Temperatur, Beschreibung, Luftfeuchtigkeit, etc.)

> **Hinweis an Teilnehmer:** Der kostenlose „Free"-Plan reicht für diese Übung aus (60 Aufrufe/Minute).

📖 **Doku-Verweis:** [OpenWeatherMap Current Weather](https://openweathermap.org/current) | [API Keys](https://openweathermap.org/appid)

---

### Schritt 3 – MCP Server Projekt erstellen

**Dauer:** ca. 15 Minuten

1. Öffnen Sie ein Terminal und erstellen Sie das Projekt:

```bash
mkdir weather-mcp-server
cd weather-mcp-server
npm init -y
```

2. Installieren Sie die Abhängigkeiten:

```bash
npm install @modelcontextprotocol/sdk express dotenv
npm install -D typescript @types/node @types/express ts-node
```

3. Erstellen Sie die `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"]
}
```

4. Erstellen Sie die `.env`-Datei:

```
OPENWEATHER_API_KEY=ihr_api_key_hier
PORT=3001
```

📖 **Doku-Verweis:** [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk) | [Node.js TypeScript Setup](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

---

### Schritt 4 – MCP Server implementieren

**Dauer:** ca. 20 Minuten

Erstellen Sie die Datei `src/index.ts`:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import express, { Request, Response } from "express";
import { z } from "zod";
import dotenv from "dotenv";
import { randomUUID } from "crypto";

dotenv.config();

const app = express();
app.use(express.json());

const API_KEY = process.env.OPENWEATHER_API_KEY;
const PORT = process.env.PORT || 3001;

// --- Hilfsfunktionen ---

async function fetchWeather(city: string): Promise<any> {
  const url = `https://api.openweathermap.org/data/2.5/weather?q=${encodeURIComponent(city)}&appid=${API_KEY}&units=metric&lang=de`;
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error(`Wetterdaten für "${city}" konnten nicht abgerufen werden (HTTP ${response.status})`);
  }
  return response.json();
}

async function fetchForecast(city: string): Promise<any> {
  const url = `https://api.openweathermap.org/data/2.5/forecast?q=${encodeURIComponent(city)}&appid=${API_KEY}&units=metric&lang=de&cnt=40`;
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error(`Vorhersage für "${city}" konnte nicht abgerufen werden (HTTP ${response.status})`);
  }
  return response.json();
}

// --- MCP Server erstellen ---

const mcpServer = new McpServer({
  name: "weather-mcp-server",
  version: "1.0.1",
  description: "MCP Server für Wetterdaten über OpenWeatherMap",
});

// Session-Management
const transports = new Map<string, StreamableHTTPServerTransport>();

// --- Tools registrieren ---

function registerTools(server: McpServer) {
// Tool 1: Aktuelles Wetter
server.tool(
  "get_current_weather",
  "Ruft das aktuelle Wetter für eine Stadt ab. Liefert Temperatur, Beschreibung, Luftfeuchtigkeit und Wind.",
  {
    city: z.string().describe("Name der Stadt (z.B. 'Berlin', 'München', 'Wien')"),
  },
  async ({ city }) => {
    try {
      const data = await fetchWeather(city);
      const weather = {
        stadt: data.name,
        land: data.sys.country,
        temperatur: `${Math.round(data.main.temp)}°C`,
        gefuehlt: `${Math.round(data.main.feels_like)}°C`,
        beschreibung: data.weather[0].description,
        luftfeuchtigkeit: `${data.main.humidity}%`,
        wind: `${data.wind.speed} m/s`,
        wolken: `${data.clouds.all}%`,
      };
      return {
        content: [
          {
            type: "text" as const,
            text: JSON.stringify(weather, null, 2),
          },
        ],
      };
    } catch (error: any) {
      return {
        content: [{ type: "text" as const, text: `Fehler: ${error.message}` }],
        isError: true,
      };
    }
  }
);

// Tool 2: 5-Tage-Vorhersage
server.tool(
  "get_weather_forecast",
  "Ruft die 5-Tage-Wettervorhersage für eine Stadt ab, gruppiert nach Tagen.",
  {
    city: z.string().describe("Name der Stadt (z.B. 'Berlin', 'Hamburg')"),
    days: z.number().min(1).max(5).default(3).describe("Anzahl der Vorhersagetage (1-5, Standard: 3)"),
  },
  async ({ city, days }) => {
    try {
      const data = await fetchForecast(city);
      const dailyForecasts: Record<string, any[]> = {};

      for (const entry of data.list) {
        const date = entry.dt_txt.split(" ")[0];
        if (!dailyForecasts[date]) {
          dailyForecasts[date] = [];
        }
        dailyForecasts[date].push({
          zeit: entry.dt_txt.split(" ")[1],
          temp: `${Math.round(entry.main.temp)}°C`,
          beschreibung: entry.weather[0].description,
          wind: `${entry.wind.speed} m/s`,
        });
      }

      const result = Object.entries(dailyForecasts)
        .slice(0, days)
        .map(([date, entries]) => ({
          datum: date,
          vorhersagen: entries,
        }));

      return {
        content: [
          {
            type: "text" as const,
            text: JSON.stringify({ stadt: data.city.name, vorhersage: result }, null, 2),
          },
        ],
      };
    } catch (error: any) {
      return {
        content: [{ type: "text" as const, text: `Fehler: ${error.message}` }],
        isError: true,
      };
    }
  }
);

// Tool 3: Wetterwarnung (vereinfacht – basiert auf Extremwerten)
server.tool(
  "get_weather_alert",
  "Prüft das aktuelle Wetter und gibt Warnungen bei extremen Bedingungen aus (Hitze, Kälte, Sturm, etc.).",
  {
    city: z.string().describe("Name der Stadt"),
  },
  async ({ city }) => {
    try {
      const data = await fetchWeather(city);
      const alerts: string[] = [];

      if (data.main.temp > 35) alerts.push("🔴 HITZEWARNUNG: Temperatur über 35°C!");
      if (data.main.temp < -10) alerts.push("🔵 KÄLTEWARNUNG: Temperatur unter -10°C!");
      if (data.wind.speed > 20) alerts.push("🟡 STURMWARNUNG: Windgeschwindigkeit über 20 m/s!");
      if (data.main.humidity > 90) alerts.push("🟡 HOHE LUFTFEUCHTIGKEIT: Über 90%!");
      if (data.clouds.all > 90 && data.main.temp < 2) alerts.push("🟡 GLÄTTEGEFAHR: Bewölkt und Temperaturen nahe 0°C!");

      const result = {
        stadt: data.name,
        aktuelle_temperatur: `${Math.round(data.main.temp)}°C`,
        wind: `${data.wind.speed} m/s`,
        warnungen: alerts.length > 0 ? alerts : ["✅ Keine Wetterwarnungen für diese Region."],
      };

      return {
        content: [
          {
            type: "text" as const,
            text: JSON.stringify(result, null, 2),
          },
        ],
      };
    } catch (error: any) {
      return {
        content: [{ type: "text" as const, text: `Fehler: ${error.message}` }],
        isError: true,
      };
    }
  }
);

} // registerTools Ende

registerTools(mcpServer);

// --- Accept Header Middleware ---

app.use("/mcp", (req: Request, res: Response, next) => {
  if (!req.headers.accept) {
    req.headers.accept = "application/json, text/event-stream";
  }
  next();
});

// --- Streamable HTTP Transport für Copilot Studio ---

app.post("/mcp", async (req: Request, res: Response) => {
  const sessionId = req.headers["mcp-session-id"] as string | undefined;

  if (sessionId && transports.has(sessionId)) {
    const transport = transports.get(sessionId)!;
    await transport.handleRequest(req, res);
    return;
  }

  const transport = new StreamableHTTPServerTransport({
    sessionId: randomUUID(),
  });

  transports.set(transport.sessionId, transport);
  console.log(`🔗 Neue MCP Session: ${transport.sessionId}`);

  res.on("close", () => {
    transports.delete(transport.sessionId);
    console.log(`🔌 Session beendet: ${transport.sessionId}`);
  });

  await mcpServer.connect(transport);
  await transport.handleRequest(req, res);
});

app.get("/mcp", async (req: Request, res: Response) => {
  const sessionId = req.headers["mcp-session-id"] as string | undefined;
  if (sessionId && transports.has(sessionId)) {
    const transport = transports.get(sessionId)!;
    await transport.handleRequest(req, res);
  } else {
    res.status(400).json({ error: "Keine aktive MCP Session" });
  }
});

app.delete("/mcp", async (req: Request, res: Response) => {
  const sessionId = req.headers["mcp-session-id"] as string | undefined;
  if (sessionId && transports.has(sessionId)) {
    const transport = transports.get(sessionId)!;
    await transport.handleRequest(req, res);
    transports.delete(sessionId);
  } else {
    res.status(400).json({ error: "Keine aktive MCP Session" });
  }
});

// Health-Check Endpoint
app.get("/health", (req, res) => {
  res.json({ status: "ok", server: "weather-mcp-server", version: "1.0.1" });
});

app.listen(PORT, () => {
  console.log(`🌤️  Wetter MCP Server läuft auf http://localhost:${PORT}`);
  console.log(`📡 MCP Endpoint: http://localhost:${PORT}/mcp`);
  console.log(`❤️  Health Check: http://localhost:${PORT}/health`);
});
```

**Zeigen und erklären Sie dabei:**
- Die drei `server.tool()`-Definitionen in der `registerTools()`-Funktion und deren Parameter-Schemas (Zod)
- Den Streamable HTTP Transport als Verbindung zwischen Copilot Studio und dem Server
- Wie die OpenWeatherMap-API angesprochen wird

📖 **Doku-Verweis:** [MCP TypeScript SDK – Server](https://github.com/modelcontextprotocol/typescript-sdk#server) | [MCP Tools Konzept](https://spec.modelcontextprotocol.io/specification/2025-03-26/server/tools/)

---

### Schritt 5 – MCP Server testen

**Dauer:** ca. 10 Minuten

1. Starten Sie den Server:

```bash
npx ts-node src/index.ts
```

2. Prüfen Sie den Health-Check:

```bash
curl https://mthwaetherapp-mthcloud.msappproxy.net/health
```

Erwartete Antwort:
```json
{"status":"ok","server":"weather-mcp-server","version":"1.0.1"}
```

3. **(Optional)** Testen Sie den MCP-Endpoint mit einem MCP-Client oder dem [MCP Inspector](https://github.com/modelcontextprotocol/inspector):

```bash
npx @modelcontextprotocol/inspector
```

Der Inspector zeigt die verfügbaren Tools und erlaubt Testaufrufe.

**Vorführ-Tipp:** Zeigen Sie im Inspector die Tool-Liste und rufen Sie `get_current_weather` mit „Berlin" als Parameter auf – die Live-Daten sorgen für einen Wow-Effekt.

📖 **Doku-Verweis:** [MCP Inspector](https://github.com/modelcontextprotocol/inspector) | [MCP Debugging Guide](https://modelcontextprotocol.io/docs/tools/inspector)

---

### Schritt 6 – MCP Server erreichbar machen (Dev Tunnel)

**Dauer:** ca. 10 Minuten

Copilot Studio benötigt eine öffentlich erreichbare URL. Für die Vorführung verwenden wir **VS Code Dev Tunnels**:

**Option A – VS Code Dev Tunnel (empfohlen für Vorführung):**

1. Öffnen Sie VS Code mit dem laufenden Server
2. Drücken Sie `Ctrl+Shift+P` → „Port weiterleiten" (Forward a Port)
3. Geben Sie Port `3001` ein
4. Ändern Sie die Sichtbarkeit auf **Public**
5. Kopieren Sie die generierte URL (z. B. `https://abc123.devtunnels.ms`)

**Option B – Azure App Service (für dauerhafte Bereitstellung):**

1. Erstellen Sie einen Azure App Service (Node.js 18+)
2. Deployen Sie den Server mit `az webapp up`
3. Setzen Sie die Umgebungsvariable `OPENWEATHER_API_KEY`

**Hinweis an Teilnehmer:** Für die Schulung reicht Option A (Dev Tunnel). Für den Produktiveinsatz empfiehlt sich Option B.

📖 **Doku-Verweis:** [Dev Tunnels Übersicht](https://learn.microsoft.com/de-de/azure/developer/dev-tunnels/overview) | [Azure App Service Node.js](https://learn.microsoft.com/de-de/azure/app-service/quickstart-nodejs)

---

### Schritt 7 – Agent in Copilot Studio erstellen

**Dauer:** ca. 10 Minuten

1. Öffnen Sie **Copilot Studio**: https://copilotstudio.microsoft.com/
2. Stellen Sie sicher, dass die richtige **Umgebung** ausgewählt ist
3. Klicken Sie auf **+ Erstellen** → **Neuer Agent**
4. Konfigurieren Sie den Agenten:

   | Eigenschaft | Wert |
   |---|---|
   | Name | `Wetter-Auskunft Agent` |
   | Beschreibung | `Liefert aktuelle Wetterdaten und Vorhersagen über einen MCP Server` |
   | Anweisungen | Siehe unten |

5. Geben Sie folgende **Anweisungen** ein:

   ```
   Du bist ein freundlicher Wetter-Assistent.
   Deine Aufgabe ist es, Benutzern aktuelle Wetterdaten und 
   Vorhersagen für beliebige Städte zu liefern.
   
   Verwende die verfügbaren Werkzeuge (Tools), um Wetterdaten 
   abzurufen. Antworte immer auf Deutsch.
   
   Strukturiere deine Antworten übersichtlich mit:
   - Aktuelle Temperatur und Wetterlage
   - Empfehlungen (z.B. Regenschirm mitnehmen, Sonnencreme)
   - Bei Vorhersagen: Übersicht nach Tagen
   
   Wenn der Benutzer keinen Ort nennt, frage freundlich nach.
   ```

6. Klicken Sie auf **Erstellen**

📖 **Doku-Verweis:** [Agent erstellen (Quickstart)](https://learn.microsoft.com/de-de/microsoft-copilot-studio/fundamentals-get-started)

---

### Schritt 8 – MCP Server in Copilot Studio verbinden

**Dauer:** ca. 15 Minuten

> **Das ist der zentrale Schritt der Vorführung!**

1. Öffnen Sie den erstellten **Wetter-Auskunft Agent** in Copilot Studio
2. Navigieren Sie zu **Aktionen** (Actions) im linken Menü
3. Klicken Sie auf **+ Aktion hinzufügen**
4. Wählen Sie **MCP Server** (Model Context Protocol)
5. Geben Sie die Server-URL ein:

   | Eigenschaft | Wert |
   |---|---|
   | Servername | `Wetter MCP Server` |
   | Server-URL | `https://mthwaetherapp-mthcloud.msappproxy.net/mcp` |

6. Klicken Sie auf **Verbinden** – Copilot Studio erkennt automatisch die verfügbaren Tools:
   - ✅ `get_current_weather`
   - ✅ `get_weather_forecast`
   - ✅ `get_weather_alert`

7. Aktivieren Sie alle drei Tools
8. Klicken Sie auf **Speichern**

**Vorführ-Tipp:** Erklären Sie, wie Copilot Studio über das MCP-Protokoll die Tool-Definitionen (Name, Beschreibung, Parameter-Schema) automatisch erkennt – genau so, wie wir sie im TypeScript-Code definiert haben.

📖 **Doku-Verweis:** [MCP Server in Copilot Studio](https://learn.microsoft.com/de-de/microsoft-copilot-studio/agent-extend-action-mcp) | [Aktionen erstellen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/advanced-plugin-actions)

---

### Schritt 9 – Topics erstellen

**Dauer:** ca. 15 Minuten

#### 9.1 Begrüßung anpassen

1. Navigieren Sie zu **Topics** → System-Topic **Begrüßung**
2. Passen Sie die Nachricht an:

   ```
   Willkommen beim Wetter-Auskunft Service! ☀️🌧️
   
   Ich kann Ihnen helfen mit:
   • Aktuellem Wetter für jede Stadt weltweit
   • 5-Tage-Wettervorhersagen
   • Wetterwarnungen (Hitze, Sturm, Kälte)
   
   Fragen Sie mich einfach, z.B.:
   "Wie ist das Wetter in Berlin?"
   "Wettervorhersage für München"
   
   Wie kann ich Ihnen helfen?
   ```

#### 9.2 Topic: Aktuelles Wetter (optional – falls generative Orchestrierung nicht reicht)

1. Erstellen Sie ein neues Topic: **Aktuelles Wetter abfragen**
2. Trigger-Phrasen:
   - `Wie ist das Wetter in ...`
   - `Aktuelles Wetter`
   - `Wetter heute`
   - `Was für Wetter ist gerade in ...`
   - `Temperatur in ...`
3. Fügen Sie einen **Frage-Knoten** hinzu:
   - Frage: `Für welche Stadt möchten Sie das Wetter wissen?`
   - Variable: `varStadt`
4. Fügen Sie einen **Generative Antworten**-Knoten hinzu (**+** → **Erweitert** → **Generative Antworten**)
   - Eingabe: `Wie ist das aktuelle Wetter in {Topic.varStadt}? Nutze das verfügbare Wetter-Tool.`

> **Hinweis für die Vorführung:** Die MCP-Tools stehen **nicht** als einzelne Plugin-Aktionen zur Auswahl. Stattdessen nutzt man den **Generative Antworten**-Knoten – die KI-Engine erkennt automatisch, welches Tool aufgerufen werden muss. In vielen Fällen reicht die **generative Orchestrierung** sogar ohne manuelles Topic – der Agent ruft die Tools selbstständig auf!

📖 **Doku-Verweis:** [Topics erstellen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-create-edit-topics) | [Generative Orchestrierung](https://learn.microsoft.com/de-de/microsoft-copilot-studio/advanced-generative-actions)

---

### Schritt 10 – Live-Demo & Test

**Dauer:** ca. 15 Minuten

Öffnen Sie das **Testfenster** in Copilot Studio und führen Sie folgende Szenarien vor:

| # | Eingabe | Erwartetes Ergebnis |
|---|---|---|
| 1 | `Hallo` | Begrüßungsnachricht mit Funktionsübersicht |
| 2 | `Wie ist das Wetter in Berlin?` | Aktuelles Wetter: Temperatur, Beschreibung, Luftfeuchtigkeit |
| 3 | `Wettervorhersage für München, 3 Tage` | 3-Tage-Vorhersage mit Temperaturen und Beschreibungen |
| 4 | `Gibt es Wetterwarnungen für Hamburg?` | Wetterwarnungen oder "Keine Warnungen" |
| 5 | `Wie warm ist es in Wien?` | Agent erkennt die Frage und ruft `get_current_weather` auf |
| 6 | `Brauche ich morgen in Köln einen Regenschirm?` | Agent nutzt Vorhersage-Tool und gibt Empfehlung |

**Vorführ-Highlights:**
- Zeigen Sie im Testfenster, wie der Agent den **Tool-Aufruf** auswählt
- Öffnen Sie parallel das Terminal mit dem laufenden MCP Server und zeigen Sie die **Log-Ausgaben** bei jedem Aufruf
- Wechseln Sie zwischen verschiedenen Städten, um Live-Daten zu demonstrieren

---

### Schritt 11 – Agent veröffentlichen

**Dauer:** ca. 5 Minuten

1. Klicken Sie auf **Veröffentlichen** (Publish)
2. Bestätigen Sie die Veröffentlichung
3. **(Optional)** Zeigen Sie die Veröffentlichung in **Microsoft Teams**

📖 **Doku-Verweis:** [Agent veröffentlichen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/publication-fundamentals-publish-channels)

---

## Tipps für die Vorführung

- **Server vorab starten:** Stellen Sie sicher, dass der MCP Server läuft und der Dev Tunnel aktiv ist, bevor die Demo beginnt
- **API-Key testen:** Prüfen Sie vorher, dass der OpenWeatherMap API-Key funktioniert
- **Fallback vorbereiten:** Haben Sie Screenshots der erwarteten Ergebnisse für den Fall, dass die Live-Demo ausfällt
- **Parallele Ansicht:** Zeigen Sie Copilot Studio und das Terminal mit MCP Server-Logs nebeneinander
- **Städte variieren:** Nutzen Sie verschiedene Städte für einen beeindruckenderen Live-Effekt
- **Vergleich zu Agent 1:** Betonen Sie den Unterschied: Agent 1 nutzt nur statisches Wissen (SharePoint), Agent 2 greift auf Live-Daten zu

---

## Aufgabenübersicht für Teilnehmer

Die vollständige Schritt-für-Schritt-Aufgabe für die Teilnehmer befindet sich in der Datei:
📄 **[Aufgabe-Wetter-Agent-MCP.md](Aufgabe-Wetter-Agent-MCP.md)**

| Aufgabe | Beschreibung |
|---|---|
| **1** | Power Apps Entwicklungsumgebung erstellen |
| **2** | Lösung erstellen |
| **3** | Copilot Agent erstellen und konfigurieren |
| **4** | MCP Server als Aktion verbinden |
| **5** | Topics verwalten (Begrüßung & Verabschiedung) |
| **6** | Topic: Wettervorhersage mit Stadtabfrage + Generative Antworten |
| **7** | Topic: Wetterwarnungen mit Adaptive Card |
| **8** | Topic: Wettervergleich zweier Städte |
| **9** | Agent zur Lösung hinzufügen |
| **10** | Agent veröffentlichen und testen |
| **Bonus** | Erweiterte Adaptive Card, Teams-Veröffentlichung, Reiseplanungs-Topic |

---

## Ergebnis

Nach Abschluss der Vorführung haben die Teilnehmer gesehen:

✅ Einen lauffähigen **MCP Server** mit drei Wetter-Tools  
✅ Die Verbindung zwischen **MCP Server und Copilot Studio**  
✅ Einen **erweiterten Agenten**, der Live-Wetterdaten liefert  
✅ Das Zusammenspiel von **MCP-Protokoll, API-Anbindung und Agent**  
✅ Den Unterschied zwischen **statischen Wissensquellen** (Agent 1) und **dynamischen Tool-Aufrufen** (Agent 2)
