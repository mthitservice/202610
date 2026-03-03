# Agent 1 – IT-Helpdesk Agent (Einfacher Agent)

> **Ziel:** Einen einfachen Copilot Agent erstellen, der IT-Helpdesk-Anfragen auf Basis von SharePoint-Betriebsanweisungen beantwortet, bei stationären PCs eine Seriennummer per Adaptive Card abfragt und am Ende veröffentlicht und getestet wird.

---

## Lernziele

- Verstehen, wie Agenten im Microsoft Copilot Studio funktionieren
- Eine Power Apps Entwicklungsumgebung und Lösung erstellen
- Einen deklarativen Agenten mit Wissensquelle (SharePoint) aufbauen
- Themen (Topics), Adaptive Cards und Geräteunterscheidung einsetzen
- Einen Agenten veröffentlichen und testen

---

## Schwierigkeitsgrad

🟢 **Einstieg** – Keine Vorkenntnisse in Copilot Studio erforderlich

---

## Voraussetzungen

> **Hinweis:** Euer Account ist bereits eingerichtet. Ihr habt alle nötigen Lizenzen und Berechtigungen.

| Was | Details |
|---|---|
| **M365 Account** | Wurde für euch bereitgestellt (inkl. Copilot Studio & SharePoint) |
| **Copilot Studio Pro-Lizenz** | Bereits zugewiesen |
| **Browser** | HTML5-fähiger Browser (Edge, Chrome) |

---

## Wichtige Links

| Portal | URL |
|---|---|
| **Power Platform Admin Center** | https://admin.powerplatform.microsoft.com/ |
| **Copilot Studio** | https://copilotstudio.microsoft.com/ |
| **Power Apps Maker Portal** | https://make.powerapps.com/ |
| **SharePoint** | https://\<IhrMandant\>.sharepoint.com/ |

---

## Dokumentation & Hilfe

| Thema | Link |
|---|---|
| Copilot Studio Übersicht | https://learn.microsoft.com/de-de/microsoft-copilot-studio/fundamentals-what-is-copilot-studio |
| Agent erstellen (Quickstart) | https://learn.microsoft.com/de-de/microsoft-copilot-studio/fundamentals-get-started |
| Wissensquellen hinzufügen | https://learn.microsoft.com/de-de/microsoft-copilot-studio/knowledge-copilot-studio |
| SharePoint als Wissensquelle | https://learn.microsoft.com/de-de/microsoft-copilot-studio/knowledge-add-sharepoint |
| Topics erstellen & verwalten | https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-create-edit-topics |
| Adaptive Cards in Copilot Studio | https://learn.microsoft.com/de-de/microsoft-copilot-studio/authoring-send-message#adaptive-card |
| Adaptive Cards Designer (Vorschau) | https://adaptivecards.io/designer/ |
| Adaptive Cards Schema-Referenz | https://adaptivecards.io/explorer/ |
| Agent veröffentlichen | https://learn.microsoft.com/de-de/microsoft-copilot-studio/publication-fundamentals-publish-channels |
| Power Apps Umgebungen | https://learn.microsoft.com/de-de/power-platform/admin/environments-overview |
| Lösungen in Power Apps | https://learn.microsoft.com/de-de/power-apps/maker/data-platform/solutions-overview |

---

## Euer Account

Euer M365-Account ist bereits vollständig eingerichtet:
- ✅ Copilot Studio Pro-Lizenz zugewiesen
- ✅ Berechtigung als Copilot Studio-Autor vorhanden
- ✅ SharePoint-Zugriff aktiv
- ✅ Power Platform Zugang freigeschaltet

Meldet euch einfach mit euren Zugangsdaten an den oben genannten Portalen an und legt los!

---

## Aufgabenübersicht

Die vollständige Schritt-für-Schritt-Anleitung befindet sich in der Datei:
📄 **[Aufgabe-IT-Helpdesk-Agent.md](Aufgabe-IT-Helpdesk-Agent.md)**

| Aufgabe | Beschreibung | Portal |
|---|---|---|
| **1** | Power Apps Entwicklungsumgebung erstellen | [Power Platform Admin Center](https://admin.powerplatform.microsoft.com/) |
| **2** | Lösung „IT-Helpdesk Lösung" anlegen | [Power Apps](https://make.powerapps.com/) |
| **3** | SharePoint-Seite mit Betriebsanweisungen erstellen | [SharePoint](https://sharepoint.com/) |
| **4** | Copilot Agent erstellen & SharePoint als Wissensquelle anbinden | [Copilot Studio](https://copilotstudio.microsoft.com/) |
| **5** | Begrüßung & Verabschiedung anpassen | [Copilot Studio](https://copilotstudio.microsoft.com/) |
| **6** | Topic mit Adaptive Card für Seriennummerabfrage (nur PCs) | [Copilot Studio](https://copilotstudio.microsoft.com/) |
| **7** | Agent zur Lösung hinzufügen | [Power Apps](https://make.powerapps.com/) |
| **8** | Agent veröffentlichen | [Copilot Studio](https://copilotstudio.microsoft.com/) |
| **9** | Agent testen (9 Testszenarien) | [Copilot Studio](https://copilotstudio.microsoft.com/) |

---

## Kurzübersicht: Was wird gebaut?

```
┌─────────────────────────────────────────────────┐
│           IT-Helpdesk Agent (Copilot Studio)     │
├─────────────────────────────────────────────────┤
│                                                  │
│  Wissensquelle:  SharePoint-Betriebsanweisungen │
│                  (BA-001 bis BA-006)             │
│                                                  │
│  Topics:                                         │
│  ├── Begrüßung (angepasst)                      │
│  ├── Verabschiedung (angepasst)                  │
│  └── PC-Problem – Seriennummer erfassen          │
│       ├── Stationärer PC → Adaptive Card         │
│       │   (Seriennummer, Standort, Problem)       │
│       └── Mobilgerät → Weiterleitung an          │
│           generative Antworten                    │
│                                                  │
│  Lösung:  IT-Helpdesk Lösung                     │
│  Umgebung: IT-Helpdesk Entwicklung               │
│                                                  │
└─────────────────────────────────────────────────┘
```

---

## Tipps für Teilnehmer

- **Umgebung prüfen:** Stellt immer sicher, dass ihr in der richtigen Umgebung arbeitet (oben rechts im jeweiligen Portal)
- **SharePoint-Inhalte:** Je mehr realistische Betriebsanweisungen ihr erstellt, desto besser antwortet der Agent
- **Testfenster nutzen:** In Copilot Studio könnt ihr den Agent jederzeit rechts im Testfenster ausprobieren – auch ohne Veröffentlichung
- **Adaptive Cards testen:** Nutzt den [Adaptive Cards Designer](https://adaptivecards.io/designer/) um eure Karten vorab zu testen
- **Fehler bei Veröffentlichung:** Stellt sicher, dass die Pro-Lizenz zugewiesen ist

---

## Ergebnis

Nach Abschluss aller Aufgaben habt ihr:

✅ Eine Power Apps Entwicklungsumgebung mit Lösung  
✅ Einen IT-Helpdesk Agent mit SharePoint-Wissensquelle  
✅ Ein Topic das bei PCs eine Seriennummer per Adaptive Card abfragt  
✅ Einen veröffentlichten und getesteten Agenten
 














