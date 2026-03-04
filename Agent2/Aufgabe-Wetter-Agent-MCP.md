# Aufgabe: Wetter-Agent mit MCP Server in Microsoft Copilot Studio

## Übersicht

In dieser Übung erstellen Sie einen **erweiterten Copilot Agent**, der über einen bereits bereitgestellten **MCP Server** (Model Context Protocol) Live-Wetterdaten abruft. Der Agent kann aktuelles Wetter, Vorhersagen und Wetterwarnungen für beliebige Städte weltweit liefern.

> **Hinweis:** Der MCP Server wird vom Trainer bereitgestellt und läuft bereits. Sie verbinden ihn nur mit Ihrem Agenten – **es ist keine Programmierung erforderlich!** Alles wird direkt in Copilot Studio konfiguriert.

---

## Ziele

- Verstehen, was das **Model Context Protocol (MCP)** ist
- Eine **Entwicklungsumgebung** und **Lösung** in Power Platform erstellen
- Einen **Copilot Agent** in Copilot Studio erstellen
- Den bereitgestellten **MCP Server als Aktion** im Agenten verbinden
- **Topics** erstellen, die die Wetter-Tools nutzen
- Eine **Adaptive Card** für die Wetteranzeige einsetzen
- Den Agenten **veröffentlichen und testen**

---

## Voraussetzungen

> **Euer Account ist bereits eingerichtet!** Alle Lizenzen und Berechtigungen sind zugewiesen. Meldet euch einfach mit euren Zugangsdaten an.

- HTML5-fähiger Browser (Edge, Chrome)
- Eure M365-Zugangsdaten (E-Mail + Passwort)

**Portale für diese Aufgabe:**

| Portal | URL |
|---|---|
| Power Platform Admin Center | https://admin.powerplatform.microsoft.com/ |
| Copilot Studio | https://copilotstudio.microsoft.com/ |
| Power Apps | https://make.powerapps.com/ |
| Adaptive Cards Designer | https://adaptivecards.io/designer/ |

**Vom Trainer bereitgestellt:**

| Was | Wert |
|---|---|
| MCP Server URL (SSE) | `https://mthwaetherapp-mthcloud.msappproxy.net/sse` |

---

## Hintergrund: Was ist MCP?

Das **Model Context Protocol (MCP)** ist ein offener Standard, der es KI-Agenten ermöglicht, auf externe Werkzeuge und Datenquellen zuzugreifen. Stellt euch MCP wie einen **USB-Anschluss für KI** vor – eine standardisierte Schnittstelle, über die Agenten mit der Außenwelt kommunizieren.

```
┌─────────────────┐       MCP Protokoll       ┌─────────────────┐
│  Copilot Studio │  ◄──── JSON-RPC/SSE ────►  │   MCP Server    │
│  (euer Agent)   │                            │ (vom Trainer)   │
└─────────────────┘                            └────────┬────────┘
                                                        │ HTTPS
                                                        ▼
                                               ┌─────────────────┐
                                               │ OpenWeatherMap  │
                                               │     API         │
                                               └─────────────────┘
```

**MCP bietet drei Hauptkonzepte:**
- **Tools:** Funktionen, die der Agent aufrufen kann (z. B. „hole aktuelles Wetter")
- **Resources:** Datenquellen, die der Agent lesen kann
- **Prompts:** Vordefinierte Prompt-Templates

**Der bereitgestellte MCP Server bietet drei Tools:**

| Tool | Beschreibung | Parameter |
|---|---|---|
| `get_current_weather` | Aktuelles Wetter für eine Stadt | `city` (Stadtname) |
| `get_weather_forecast` | 5-Tage-Wettervorhersage | `city` (Stadtname), `days` (1-5) |
| `get_weather_alert` | Wetterwarnungen (Hitze, Sturm, Kälte) | `city` (Stadtname) |

📖 **Weiterführend:** [MCP Introduction](https://modelcontextprotocol.io/introduction) | [MCP Spezifikation](https://spec.modelcontextprotocol.io/)

---

## Aufgabe 1 – Power Apps Entwicklungsumgebung erstellen

1. Öffnen Sie das **Power Platform Admin Center**: https://admin.powerplatform.microsoft.com/
2. Klicken Sie im linken Menü auf **Umgebungen** → **+ Neue Umgebung**
3. Vergeben Sie folgende Einstellungen:

   | Eigenschaft | Wert |
   |---|---|
   | Name | `Wetter-Agent Entwicklung` |
   | Typ | **Entwicklung** |
   | Region | Europa |
   | Dataverse-Datenbank | **Ja** |

4. Klicken Sie auf **Speichern** und warten Sie, bis die Umgebung bereitgestellt ist

> **Ergebnis:** Sie haben eine Entwicklungsumgebung mit Dataverse-Datenbank erstellt.

📖 **Doku:** [Power Platform Umgebungen](https://learn.microsoft.com/de-de/power-platform/admin/environments-overview)

---

## Aufgabe 2 – Lösung erstellen

1. Öffnen Sie **Power Apps**: https://make.powerapps.com/
2. Stellen Sie oben rechts sicher, dass die Umgebung **„Wetter-Agent Entwicklung"** ausgewählt ist
3. Navigieren Sie im linken Menü zu **Lösungen**
4. Klicken Sie auf **+ Neue Lösung**
5. Vergeben Sie folgende Einstellungen:

   | Eigenschaft | Wert |
   |---|---|
   | Anzeigename | `Wetter-Agent Lösung` |
   | Name | `WetterAgentLoesung` |
   | Herausgeber | Verwenden Sie den Standardherausgeber oder erstellen Sie einen neuen |

6. Klicken Sie auf **Erstellen**

> **Ergebnis:** Sie haben eine Lösung erstellt, in der alle Komponenten des Agenten verwaltet werden.

📖 **Doku:** [Lösungen in Power Apps](https://learn.microsoft.com/de-de/power-apps/maker/data-platform/solutions-overview)

---

## Aufgabe 3 – Copilot Agent erstellen

### 3.1 Agent anlegen

1. Öffnen Sie **Copilot Studio**: https://copilotstudio.microsoft.com/
2. Stellen Sie sicher, dass die Umgebung **„Wetter-Agent Entwicklung"** ausgewählt ist (oben rechts)
3. Klicken Sie auf **+ Erstellen** → **Neuer Agent**
4. Konfigurieren Sie den Agenten:

   | Eigenschaft | Wert |
   |---|---|
   | Name | `Wetter-Auskunft Agent` |
   | Beschreibung | `Liefert aktuelle Wetterdaten, Vorhersagen und Warnungen über einen MCP Server` |
   | Anweisungen | Siehe unten |

5. Geben Sie folgende **Anweisungen** (Instructions) ein:

   ```
   Du bist ein freundlicher Wetter-Assistent.
   Deine Aufgabe ist es, Benutzern aktuelle Wetterdaten und 
   Vorhersagen für beliebige Städte weltweit zu liefern.
   
   Verwende die verfügbaren Werkzeuge (Tools), um Wetterdaten 
   abzurufen. Antworte immer auf Deutsch.
   
   Strukturiere deine Antworten übersichtlich mit:
   - Aktuelle Temperatur und Wetterlage
   - Empfehlungen (z.B. Regenschirm mitnehmen, Sonnencreme)
   - Bei Vorhersagen: Übersicht nach Tagen
   
   Wenn der Benutzer keinen Ort nennt, frage freundlich nach.
   ```

6. Klicken Sie auf **Erstellen**

> **Ergebnis:** Der Basis-Agent ist erstellt.

📖 **Doku:** [Agent erstellen (Quickstart)](https://learn.microsoft.com/de-de/microsoft-copilot-studio/fundamentals-get-started) | [Copilot Studio Übersicht](https://learn.microsoft.com/de-de/microsoft-copilot-studio/fundamentals-what-is-copilot-studio)

---

## Aufgabe 4 – MCP Server als Aktion verbinden

> **Dies ist der wichtigste Schritt!** Hier verbinden Sie den vom Trainer bereitgestellten MCP Server mit Ihrem Agent.

### 4.1 MCP Server URL notieren

Die **MCP Server URL** lautet:
```
https://mthwaetherapp-mthcloud.msappproxy.net/sse
```

> ⚠️ **Wichtig:** Die URL muss auf `/sse` enden!

### 4.2 MCP-Aktion hinzufügen

1. Öffnen Sie den **Wetter-Auskunft Agent** in Copilot Studio
2. Navigieren Sie im linken Menü zu **Aktionen** (Actions)
3. Klicken Sie auf **+ Aktion hinzufügen**
4. Wählen Sie **MCP Server** (Model Context Protocol) aus der Liste
5. Konfigurieren Sie die Verbindung:

   | Eigenschaft | Wert |
   |---|---|
   | Servername | `Wetter MCP Server` |
   | Server-URL | `https://mthwaetherapp-mthcloud.msappproxy.net/sse` |

6. Klicken Sie auf **Verbinden**

### 4.3 Tools überprüfen und aktivieren

Nach der Verbindung erkennt Copilot Studio **automatisch** die verfügbaren Tools des MCP Servers:

| Tool | Beschreibung | Status |
|---|---|---|
| `get_current_weather` | Aktuelles Wetter für eine Stadt abrufen | ✅ Erkannt |
| `get_weather_forecast` | 5-Tage-Vorhersage abrufen | ✅ Erkannt |
| `get_weather_alert` | Wetterwarnungen prüfen | ✅ Erkannt |

7. Stellen Sie sicher, dass **alle drei Tools aktiviert** sind (Häkchen gesetzt)
8. Klicken Sie auf **Speichern**

### 4.4 Schnelltest

1. Öffnen Sie das **Testfenster** (rechts in Copilot Studio)
2. Schreiben Sie: `Wie ist das Wetter in Berlin?`
3. Der Agent sollte das Tool `get_current_weather` aufrufen und die aktuellen Wetterdaten anzeigen

> **Troubleshooting:**
> - Keine Verbindung? → Prüfen Sie, ob die URL korrekt ist (mit `/sse` am Ende)
> - Timeout? → Fragen Sie den Trainer, ob der MCP Server läuft
> - Keine Tools erkannt? → Versuchen Sie die Verbindung zu trennen und erneut herzustellen

> **Ergebnis:** Der MCP Server ist erfolgreich mit Ihrem Agent verbunden. Copilot Studio hat die Tools automatisch erkannt.

📖 **Doku:** [MCP Server in Copilot Studio](https://learn.microsoft.com/de-de/microsoft-copilot-studio/agent-extend-action-mcp) | [Aktionen verwalten](https://learn.microsoft.com/de-de/microsoft-copilot-studio/advanced-plugin-actions)

---

## Aufgabe 5 – Topics verwalten

### 5.1 Begrüßung anpassen

1. Navigieren Sie im Agenten zu **Topics** (Themen)
2. Öffnen Sie das System-Topic **Begrüßung** (Greeting)
3. Passen Sie die Begrüßungsnachricht an:

   ```
   Willkommen beim Wetter-Auskunft Service! ☀️🌧️
   
   Ich kann Ihnen helfen mit:
   • Aktuellem Wetter für jede Stadt weltweit
   • 5-Tage-Wettervorhersagen
   • Wetterwarnungen (Hitze, Sturm, Kälte)
   
   Fragen Sie mich einfach, z.B.:
   "Wie ist das Wetter in Berlin?"
   "Wettervorhersage für München, 5 Tage"
   "Gibt es Wetterwarnungen für Hamburg?"
   
   Wie kann ich Ihnen helfen?
   ```

4. **Speichern** Sie das Topic

### 5.2 Verabschiedung anpassen

1. Öffnen Sie das System-Topic **Ende der Unterhaltung** (End of Conversation)
2. Passen Sie die Nachricht an:

   ```
   Vielen Dank für Ihre Wetteranfrage! 
   Ich hoffe, ich konnte Ihnen weiterhelfen.
   
   Tipp: Sie können mich jederzeit nach dem Wetter in einer 
   beliebigen Stadt fragen – weltweit!
   
   Einen schönen Tag noch! 🌤️
   ```

3. **Speichern** Sie das Topic

> **Ergebnis:** Begrüßung und Verabschiedung sind konfiguriert.

📖 **Doku:** [Topics erstellen & verwalten](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-create-edit-topics)

---

## Aufgabe 6 – Topic: Wettervorhersage mit Stadtabfrage erstellen

In dieser Aufgabe erstellen Sie ein eigenes Topic, das gezielt eine Stadt abfragt und die Vorhersage abruft.

### 6.1 Neues Topic erstellen

1. Navigieren Sie zu **Topics** → **+ Hinzufügen** → **Topic** → **Von Grund auf neu**
2. Benennen Sie das Topic: **Wettervorhersage abfragen**

### 6.2 Trigger-Phrasen definieren

Fügen Sie folgende **Trigger-Phrasen** hinzu:

- `Wettervorhersage`
- `Wie wird das Wetter?`
- `Wetter für die nächsten Tage`
- `Vorhersage für morgen`
- `Wie wird das Wetter diese Woche?`
- `Brauche ich morgen einen Regenschirm?`

### 6.3 Stadtabfrage einbauen

1. Fügen Sie einen **Frage-Knoten** (Ask a question) hinzu:
   - **Frage:** `Für welche Stadt möchten Sie die Wettervorhersage?`
   - **Identifizieren:** Wählen Sie **Stadt** (City) als Entität – falls nicht verfügbar, verwenden Sie **Eingabe des Benutzers als Text**
   - **Speichern als Variable:** `varStadt`

### 6.4 Anzahl der Tage abfragen

1. Fügen Sie einen weiteren **Frage-Knoten** hinzu:
   - **Frage:** `Für wie viele Tage möchten Sie die Vorhersage? (1-5 Tage)`
   - **Identifizieren:** **Zahl** (Number)
   - **Speichern als Variable:** `varTage`

### 6.5 Plugin-Aktion aufrufen

1. Fügen Sie einen **Aktion aufrufen**-Knoten (Call an action) hinzu
2. Wählen Sie die MCP-Aktion **`get_weather_forecast`**
3. Ordnen Sie die Parameter zu:
   - `city` → `varStadt`
   - `days` → `varTage`
4. Speichern Sie das Ergebnis in einer Variable: `varVorhersage`

### 6.6 Antwort formatieren

1. Fügen Sie einen **Nachricht-Knoten** (Send a message) hinzu:

   ```
   🌤️ Wettervorhersage für {varStadt}:
   
   {varVorhersage}
   
   Möchten Sie noch etwas wissen?
   ```

2. **Speichern** Sie das Topic

> **Ergebnis:** Sie haben ein Topic erstellt, das gezielt Stadt und Tage abfragt und die Vorhersage über den MCP Server abruft.

📖 **Doku:** [Topics erstellen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-create-edit-topics) | [Aktionen in Topics verwenden](https://learn.microsoft.com/de-de/microsoft-copilot-studio/advanced-plugin-actions)

---

## Aufgabe 7 – Topic: Wetterwarnungen mit Adaptive Card erstellen

In dieser Aufgabe erstellen Sie ein Topic, das Wetterwarnungen abfragt und das Ergebnis als ansprechende **Adaptive Card** anzeigt.

### 7.1 Neues Topic erstellen

1. Erstellen Sie ein neues Topic: **Wetterwarnungen prüfen**
2. Trigger-Phrasen:
   - `Gibt es Wetterwarnungen?`
   - `Wetterwarnung`
   - `Unwetterwarnung`
   - `Ist das Wetter gefährlich?`
   - `Sturm- oder Hitzewarnung`
   - `Wetterwarnungen für...`

### 7.2 Stadtabfrage

1. Fügen Sie einen **Frage-Knoten** hinzu:
   - **Frage:** `Für welche Stadt möchten Sie Wetterwarnungen prüfen?`
   - **Speichern als Variable:** `varWarnStadt`

### 7.3 Wetterwarnungs-Tool aufrufen

1. Fügen Sie einen **Aktion aufrufen**-Knoten hinzu
2. Wählen Sie **`get_weather_alert`**
3. Ordnen Sie zu: `city` → `varWarnStadt`
4. Speichern Sie das Ergebnis in: `varWarnungen`

### 7.4 Adaptive Card für die Wetterwarnung erstellen

1. Fügen Sie einen **Nachricht-Knoten** hinzu
2. Klicken Sie auf **+ Hinzufügen** → **Adaptive Card**
3. Wechseln Sie in den **JSON-Editor** und fügen Sie folgendes JSON ein:

```json
{
  "type": "AdaptiveCard",
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "version": "1.5",
  "body": [
    {
      "type": "TextBlock",
      "text": "⚠️ Wetterbericht & Warnungen",
      "weight": "Bolder",
      "size": "Large",
      "wrap": true
    },
    {
      "type": "TextBlock",
      "text": "Stadt: {varWarnStadt}",
      "weight": "Bolder",
      "spacing": "Medium",
      "wrap": true
    },
    {
      "type": "TextBlock",
      "text": "{varWarnungen}",
      "wrap": true,
      "spacing": "Medium"
    },
    {
      "type": "TextBlock",
      "text": "💡 Tipp: Nutzen Sie die Wettervorhersage, um sich auf die kommenden Tage vorzubereiten.",
      "wrap": true,
      "spacing": "Large",
      "isSubtle": true,
      "size": "Small"
    }
  ]
}
```

> **Tipp:** Sie können die Adaptive Card vorab im [Adaptive Cards Designer](https://adaptivecards.io/designer/) testen und anpassen, bevor Sie sie in Copilot Studio einfügen.

### 7.5 Rückfrage hinzufügen

1. Fügen Sie nach der Adaptive Card einen weiteren **Nachricht-Knoten** hinzu:

   ```
   Möchten Sie auch die aktuelle Temperatur oder eine 
   Vorhersage für {varWarnStadt} sehen?
   ```

2. **Speichern** Sie das Topic

> **Ergebnis:** Sie haben ein Topic mit Adaptive Card erstellt, das Wetterwarnungen visuell ansprechend darstellt.

📖 **Doku:** [Adaptive Cards in Copilot Studio](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-send-message#adaptive-card) | [Adaptive Cards Designer](https://adaptivecards.io/designer/) | [Adaptive Cards Schema](https://adaptivecards.io/explorer/)

---

## Aufgabe 8 – Topic: Wettervergleich zweier Städte erstellen

Erstellen Sie ein Topic, das das Wetter zweier Städte vergleicht – eine tolle Funktion, die zeigt, wie der Agent mehrere Tool-Aufrufe hintereinander machen kann.

### 8.1 Neues Topic erstellen

1. Erstellen Sie ein neues Topic: **Wettervergleich**
2. Trigger-Phrasen:
   - `Vergleiche das Wetter`
   - `Wo ist es wärmer?`
   - `Wetter vergleichen`
   - `Wetterunterschied zwischen`
   - `Was ist wärmer, ... oder ...?`

### 8.2 Zwei Städte abfragen

1. Fügen Sie einen **Frage-Knoten** hinzu:
   - **Frage:** `Welche erste Stadt möchten Sie vergleichen?`
   - **Variable:** `varStadt1`

2. Fügen Sie einen zweiten **Frage-Knoten** hinzu:
   - **Frage:** `Und welche zweite Stadt?`
   - **Variable:** `varStadt2`

### 8.3 Wetter für beide Städte abrufen

1. Fügen Sie einen **Aktion aufrufen**-Knoten hinzu:
   - Aktion: **`get_current_weather`**
   - `city` → `varStadt1`
   - Ergebnis speichern in: `varWetter1`

2. Fügen Sie einen zweiten **Aktion aufrufen**-Knoten hinzu:
   - Aktion: **`get_current_weather`**
   - `city` → `varStadt2`
   - Ergebnis speichern in: `varWetter2`

### 8.4 Vergleich anzeigen

1. Fügen Sie einen **Nachricht-Knoten** hinzu:

   ```
   🌍 Wettervergleich:
   
   📍 {varStadt1}:
   {varWetter1}
   
   📍 {varStadt2}:
   {varWetter2}
   
   Möchten Sie eine detailliertere Vorhersage für eine der Städte?
   ```

2. **Speichern** Sie das Topic

> **Ergebnis:** Sie haben ein Topic erstellt, das Live-Wetterdaten zweier Städte vergleicht.

📖 **Doku:** [Topics mit Variablen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-variables) | [Frage-Knoten](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-ask-a-question)

---

## Aufgabe 9 – Agent zur Lösung hinzufügen

1. Öffnen Sie **Power Apps**: https://make.powerapps.com/
2. Wechseln Sie zur Umgebung **„Wetter-Agent Entwicklung"**
3. Navigieren Sie zu **Lösungen** → **Wetter-Agent Lösung**
4. Klicken Sie auf **+ Vorhandene hinzufügen** → **Chatbot** → **Agent**
5. Wählen Sie den **Wetter-Auskunft Agent** aus und fügen Sie ihn zur Lösung hinzu

> **Ergebnis:** Der Agent ist nun Teil der verwalteten Lösung.

📖 **Doku:** [Lösungen verwalten](https://learn.microsoft.com/de-de/power-apps/maker/data-platform/solutions-overview)

---

## Aufgabe 10 – Agent veröffentlichen und testen

### 10.1 Agent veröffentlichen

1. Öffnen Sie den **Wetter-Auskunft Agent** in **Copilot Studio**
2. Klicken Sie oben rechts auf **Veröffentlichen** (Publish)
3. Bestätigen Sie die Veröffentlichung
4. Warten Sie, bis der Veröffentlichungsprozess abgeschlossen ist

### 10.2 Agent testen

Öffnen Sie das **Testfenster** (rechte Seite in Copilot Studio) und testen Sie folgende Szenarien:

| # | Testszenario | Erwartetes Ergebnis |
|---|---|---|
| 1 | Schreiben Sie: `Hallo` | Begrüßungsnachricht mit Funktionsübersicht |
| 2 | Schreiben Sie: `Wie ist das Wetter in Berlin?` | Agent ruft `get_current_weather` auf → Temperatur, Beschreibung, Wind |
| 3 | Schreiben Sie: `Wettervorhersage für München` | Topic wird getriggert → Fragt nach Tagesanzahl → Zeigt Vorhersage |
| 4 | Schreiben Sie: `Gibt es Wetterwarnungen für Hamburg?` | Topic wird getriggert → Adaptive Card mit Warnungen |
| 5 | Schreiben Sie: `Vergleiche das Wetter` | Wettervergleich-Topic → Fragt nach zwei Städten → Zeigt Vergleich |
| 6 | Schreiben Sie: `Brauche ich morgen in Köln einen Regenschirm?` | Agent nutzt Vorhersage-Tool und gibt Empfehlung |
| 7 | Schreiben Sie: `Wie warm ist es in London?` | Internationale Stadt funktioniert ebenfalls |
| 8 | Schreiben Sie: `Wetter in Fantasiestadt123` | Fehlermeldung – Stadt nicht gefunden |
| 9 | Schreiben Sie: `Tschüss` | Verabschiedungsnachricht |

### 10.3 Test-Checkliste

- [ ] Begrüßung wird korrekt angezeigt
- [ ] Aktuelles Wetter wird für verschiedene Städte korrekt abgerufen
- [ ] Vorhersage-Topic fragt Stadt und Tage ab
- [ ] Wetterwarnungs-Topic zeigt Adaptive Card an
- [ ] Wettervergleich-Topic fragt zwei Städte ab und zeigt Ergebnisse
- [ ] Agent gibt sinnvolle Empfehlungen (Regenschirm, Sonnencreme, etc.)
- [ ] Internationale Städte funktionieren (London, Paris, Tokio, etc.)
- [ ] Agent reagiert sinnvoll auf ungültige Städtenamen
- [ ] Verabschiedung funktioniert korrekt

> **Ergebnis:** Der Agent ist veröffentlicht und liefert Live-Wetterdaten über den MCP Server.

📖 **Doku:** [Agent veröffentlichen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/publication-fundamentals-publish-channels)

---

## Zusammenfassung

| Schritt | Ergebnis |
|---|---|
| Aufgabe 1 | Power Apps Entwicklungsumgebung erstellt |
| Aufgabe 2 | Lösung „Wetter-Agent Lösung" angelegt |
| Aufgabe 3 | Copilot Agent erstellt und konfiguriert |
| Aufgabe 4 | MCP Server als Aktion verbunden (3 Tools erkannt) |
| Aufgabe 5 | Begrüßung und Verabschiedung angepasst |
| Aufgabe 6 | Topic: Wettervorhersage mit Stadtabfrage erstellt |
| Aufgabe 7 | Topic: Wetterwarnungen mit Adaptive Card erstellt |
| Aufgabe 8 | Topic: Wettervergleich zweier Städte erstellt |
| Aufgabe 9 | Agent zur Lösung hinzugefügt |
| Aufgabe 10 | Agent veröffentlicht und getestet |

---

## Was habt ihr gelernt?

Nach Abschluss dieser Aufgabe habt ihr:

✅ Verstanden, was das **Model Context Protocol (MCP)** ist  
✅ Einen **MCP Server** in Copilot Studio als Aktion verbunden  
✅ Erfahren, wie Copilot Studio Tools **automatisch erkennt**  
✅ Eigene **Topics** erstellt, die MCP-Tools aufrufen  
✅ Eine **Adaptive Card** zur Wetteranzeige eingesetzt  
✅ Einen **erweiterten Agenten** gebaut, der **Live-Daten** verwendet  
✅ Den Unterschied zwischen **statischen Wissensquellen** (Agent 1: SharePoint) und **dynamischen Tool-Aufrufen** (Agent 2: MCP/API) verstanden

---

## Bonus-Aufgaben (optional)

### Bonus 1 – Erweiterte Adaptive Card für Wetteranzeige

Erstellen Sie ein weiteres Topic „Aktuelles Wetter (detailliert)", das eine ansprechende Adaptive Card mit dem aktuellen Wetter anzeigt. Verwenden Sie den [Adaptive Cards Designer](https://adaptivecards.io/designer/) und gestalten Sie die Karte mit:

- Stadtname und Wetterbeschreibung als Überschrift
- Temperatur groß dargestellt
- Detailinformationen als Fakten-Liste (Luftfeuchtigkeit, Wind, Bewölkung, Gefühlte Temperatur)

**Beispiel-JSON für die Adaptive Card:**

```json
{
  "type": "AdaptiveCard",
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "version": "1.5",
  "body": [
    {
      "type": "ColumnSet",
      "columns": [
        {
          "type": "Column",
          "width": "auto",
          "items": [
            {
              "type": "TextBlock",
              "text": "☀️",
              "size": "ExtraLarge"
            }
          ]
        },
        {
          "type": "Column",
          "width": "stretch",
          "items": [
            {
              "type": "TextBlock",
              "text": "Wetter in {varStadt}",
              "weight": "Bolder",
              "size": "Large"
            },
            {
              "type": "TextBlock",
              "text": "Live-Daten via MCP Server",
              "spacing": "None",
              "isSubtle": true
            }
          ]
        }
      ]
    },
    {
      "type": "FactSet",
      "facts": [
        { "title": "🌡️ Temperatur:", "value": "Wird dynamisch befüllt" },
        { "title": "💧 Luftfeuchtigkeit:", "value": "Wird dynamisch befüllt" },
        { "title": "💨 Wind:", "value": "Wird dynamisch befüllt" },
        { "title": "☁️ Bewölkung:", "value": "Wird dynamisch befüllt" }
      ]
    }
  ]
}
```

> **Tipp:** Verwenden Sie die Variablen aus dem Tool-Aufruf, um die Fakten dynamisch zu befüllen.

📖 **Doku:** [Adaptive Cards Designer](https://adaptivecards.io/designer/) | [Adaptive Cards in Copilot Studio](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-send-message#adaptive-card)

---

### Bonus 2 – Agent in Microsoft Teams veröffentlichen

1. Navigieren Sie in Copilot Studio zu **Kanäle** (Channels)
2. Wählen Sie **Microsoft Teams**
3. Klicken Sie auf **In Teams verfügbar machen**
4. Testen Sie den Agent direkt in einem Teams-Chat

📖 **Doku:** [Agent in Teams veröffentlichen](https://learn.microsoft.com/de-de/microsoft-copilot-studio/publication-add-bot-to-microsoft-teams)

---

### Bonus 3 – Reiseplanungs-Topic erstellen

Erstellen Sie ein kreatives Topic „Reisewetter-Berater", das:

1. Den **Reisezielort** abfragt
2. Die **Anzahl der Reisetage** erfasst
3. Sowohl **aktuelles Wetter** als auch die **Vorhersage** abruft (zwei Aktionsaufrufe)
4. Über einen **Bedingungsknoten** (Condition) passende Empfehlungen ausspricht:
   - Bei Regen → „Denken Sie an einen Regenschirm! ☂️"
   - Bei Sonne und hohen Temperaturen → „Sonnencreme nicht vergessen! ☀️"
   - Bei Kälte → „Packen Sie warme Kleidung ein! 🧥"
   - Bei starkem Wind → „Eine Windjacke wäre sinnvoll! 💨"

📖 **Doku:** [Bedingungsknoten in Topics](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-using-conditions) | [Variablen verwenden](https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-variables)

---

## Tipps für Teilnehmer

- **Umgebung prüfen:** Stellt immer sicher, dass ihr in der richtigen Umgebung arbeitet (oben rechts im jeweiligen Portal)
- **Testfenster nutzen:** In Copilot Studio könnt ihr den Agent jederzeit rechts im Testfenster ausprobieren – auch ohne Veröffentlichung
- **Generative Orchestrierung:** Der Agent kann die MCP-Tools auch **selbstständig** aufrufen, ohne dass ein spezielles Topic nötig ist – die Topics helfen bei strukturierteren Abläufen
- **Adaptive Cards testen:** Nutzt den [Adaptive Cards Designer](https://adaptivecards.io/designer/) um eure Karten vorab zu testen
- **MCP-Verbindung weg?** Wenn der Trainer den Server neu startet, müsst ihr die Aktion ggf. neu verbinden
- **Vergleich zu Agent 1:** Agent 1 nutzt statisches Wissen aus SharePoint – Agent 2 greift auf **Live-Daten** aus dem Internet zu. Das ist der große Unterschied!

---

## Häufige Fehler & Troubleshooting

| Problem | Lösung |
|---|---|
| MCP Server nicht erreichbar | Fragen Sie den Trainer – der Server muss laufen |
| „Verbindung fehlgeschlagen" bei MCP-Aktion | Prüfen Sie die URL (muss auf `/sse` enden) |
| Tools werden nicht erkannt | Trennen und erneut verbinden. URL prüfen |
| Agent antwortet nicht mit Wetterdaten | Prüfen Sie, ob die MCP-Aktion aktiviert und gespeichert ist |
| Adaptive Card wird nicht angezeigt | Prüfen Sie das JSON auf Syntaxfehler (z. B. fehlende Kommas) |
| Variablen werden nicht im Nachrichtentext angezeigt | Verwenden Sie die richtige Schreibweise: `{VariablenName}` |
| Topic wird nicht getriggert | Prüfen Sie die Trigger-Phrasen – fügen Sie ggf. weitere hinzu |
| Agent nutzt falsches Tool | Verbessern Sie die Anweisungen (Instructions) des Agenten |
