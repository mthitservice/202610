# Aufgabe: IT-Helpdesk Agent mit Microsoft Copilot Studio

## Übersicht

In dieser Übung erstellen Sie einen **Microsoft Copilot Agent** für einen IT-Helpdesk. Der Agent beantwortet Fragen zu IT-Problemen auf Basis von Betriebsanweisungen, die auf einer SharePoint-Seite hinterlegt sind. Zusätzlich wird ein Topic erstellt, das bei Problemen mit stationären PCs (keine Mobilgeräte) eine **Adaptive Card** anzeigt, um die Seriennummer des Geräts abzufragen.

---

## Ziele

- Eine **Power Apps Entwicklungsumgebung** erstellen
- Eine **Lösung** in der Umgebung anlegen
- Einen **Copilot Agent** für den IT-Helpdesk erstellen
- Eine **SharePoint-Seite** mit Betriebsanweisungen als Wissensquelle anlegen
- Ein **Topic mit Adaptive Card** zur Seriennummerabfrage bei PCs erstellen
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
| SharePoint | https://\<IhrMandant\>.sharepoint.com/ |

---

## Aufgabe 1 – Power Apps Entwicklungsumgebung erstellen

1. Öffnen Sie das **Power Platform Admin Center**: https://admin.powerplatform.microsoft.com/
2. Klicken Sie im linken Menü auf **Umgebungen** → **+ Neue Umgebung**
3. Vergeben Sie folgende Einstellungen:

   | Eigenschaft | Wert |
   |---|---|
   | Name | `IT-Helpdesk Entwicklung` |
   | Typ | **Entwicklung** |
   | Region | Europa |
   | Dataverse-Datenbank | **Ja** |

4. Klicken Sie auf **Speichern** und warten Sie, bis die Umgebung bereitgestellt ist

> **Ergebnis:** Sie haben eine Entwicklungsumgebung mit Dataverse-Datenbank erstellt.

---

## Aufgabe 2 – Lösung erstellen

1. Öffnen Sie **Power Apps**: https://make.powerapps.com/
2. Stellen Sie oben rechts sicher, dass die Umgebung **„IT-Helpdesk Entwicklung"** ausgewählt ist
3. Navigieren Sie im linken Menü zu **Lösungen**
4. Klicken Sie auf **+ Neue Lösung**
5. Vergeben Sie folgende Einstellungen:

   | Eigenschaft | Wert |
   |---|---|
   | Anzeigename | `IT-Helpdesk Lösung` |
   | Name | `ITHelpdeskLoesung` |
   | Herausgeber | Verwenden Sie den Standardherausgeber oder erstellen Sie einen neuen |

6. Klicken Sie auf **Erstellen**

> **Ergebnis:** Sie haben eine Lösung erstellt, in der alle Komponenten des Agenten verwaltet werden.

---

## Aufgabe 3 – SharePoint-Seite mit Betriebsanweisungen anlegen

### 3.1 SharePoint-Seite erstellen

1. Öffnen Sie **SharePoint**: `https://<IhrMandant>.sharepoint.com/`
2. Klicken Sie auf **+ Website erstellen** → **Teamwebsite** (privat)
3. Vergeben Sie den Namen: **IT-Betriebsanweisungen**
4. Fügen Sie die relevanten Mitglieder hinzu

### 3.2 Dokumentbibliothek befüllen

1. Öffnen Sie die erstellte SharePoint-Seite
2. Navigieren Sie zu **Dokumente**
3. Laden Sie Beispiel-Betriebsanweisungen hoch (Word- oder PDF-Dateien), z. B.:

   | Dateiname | Inhalt (Beispiel) |
   |---|---|
   | `BA-001_Passwort-zuruecksetzen.docx` | Passwort zurücksetzen |
   | `BA-002_VPN-Verbindung.docx` | VPN-Einrichtung unter Windows |
   | `BA-003_Drucker-einrichten.docx` | Druckerinstallation im Firmennetzwerk |
   | `BA-004_Outlook-Profil-reparieren.docx` | Outlook-Synchronisationsprobleme |
   | `BA-005_WLAN-Verbindung.docx` | Firmen-WLAN verbinden |
   | `BA-006_Software-Installation.docx` | Software über Company Portal anfordern |

### 3.3 Texte für die Betriebsanweisungen

Erstellen Sie jeweils ein Word-Dokument (.docx) und kopieren Sie den folgenden Text hinein:

---

#### 📄 BA-001_Passwort-zuruecksetzen.docx

```
Betriebsanweisung BA-001
Titel: Passwort zurücksetzen
Gültig ab: 01.01.2026
Abteilung: IT-Service

1. Zweck
Diese Anweisung beschreibt, wie Mitarbeiter ihr Windows-/Microsoft-365-Passwort 
selbstständig zurücksetzen können.

2. Voraussetzung
- Sie haben Ihr Mobiltelefon für die Multi-Faktor-Authentifizierung (MFA) registriert.
- Sie haben Zugang zu einem beliebigen Gerät mit Internetverbindung.

3. Ablauf – Self-Service-Passwort zurücksetzen
   a) Öffnen Sie im Browser die Seite: https://passwordreset.microsoftonline.com
   b) Geben Sie Ihre geschäftliche E-Mail-Adresse ein (z. B. vorname.nachname@firma.de).
   c) Führen Sie die Captcha-Prüfung durch.
   d) Wählen Sie die Verifizierungsmethode:
      - Option 1: SMS an Ihr hinterlegtes Mobiltelefon
      - Option 2: Anruf an Ihr hinterlegtes Telefon
      - Option 3: Bestätigung über die Microsoft Authenticator App
   e) Geben Sie den erhaltenen Code ein oder bestätigen Sie die Push-Nachricht.
   f) Vergeben Sie ein neues Passwort. 
      Anforderungen: Mindestens 12 Zeichen, Großbuchstabe, Kleinbuchstabe, 
      Zahl und Sonderzeichen.
   g) Bestätigen Sie mit „Fertigstellen".

4. Passwort am gesperrten Windows-PC zurücksetzen
   a) Klicken Sie auf dem Sperrbildschirm auf „Kennwort zurücksetzen".
   b) Folgen Sie den Schritten wie oben beschrieben.
   c) Nach erfolgreicher Änderung melden Sie sich mit dem neuen Passwort an.

5. Häufige Fehler
- „Konto nicht gefunden": Prüfen Sie die E-Mail-Adresse auf Tippfehler.
- „MFA nicht eingerichtet": Wenden Sie sich an den IT-Helpdesk (helpdesk@firma.de).
- Passwort wird nicht akzeptiert: Stellen Sie sicher, dass es nicht eines der 
  letzten 10 Passwörter ist.

6. Kontakt bei Problemen
IT-Helpdesk: helpdesk@firma.de | Telefon: 0800-1234567
```

---

#### 📄 BA-002_VPN-Verbindung.docx

```
Betriebsanweisung BA-002
Titel: VPN-Verbindung einrichten (Windows)
Gültig ab: 01.01.2026
Abteilung: IT-Service

1. Zweck
Diese Anweisung beschreibt die Einrichtung und Nutzung der VPN-Verbindung 
zum Firmennetzwerk unter Windows 10/11.

2. Voraussetzung
- Firmenlaptop oder genehmigtes Gerät mit Windows 10/11
- Internetverbindung
- Gültige Firmenzugangsdaten (E-Mail + Passwort)
- Multi-Faktor-Authentifizierung (MFA) eingerichtet

3. VPN-Client installieren
   a) Öffnen Sie das Company Portal (im Startmenü suchen nach „Company Portal").
   b) Suchen Sie nach „GlobalProtect VPN Client".
   c) Klicken Sie auf „Installieren" und warten Sie, bis die Installation 
      abgeschlossen ist.
   d) Starten Sie den Rechner bei Aufforderung neu.

4. VPN-Verbindung herstellen
   a) Öffnen Sie „GlobalProtect" über das Taskleisten-Symbol (unten rechts).
   b) Geben Sie als Portal-Adresse ein: vpn.firma.de
   c) Klicken Sie auf „Verbinden".
   d) Melden Sie sich mit Ihrer geschäftlichen E-Mail und Ihrem Passwort an.
   e) Bestätigen Sie die MFA-Abfrage (Authenticator App oder SMS).
   f) Status wechselt auf „Verbunden" – Sie haben nun Zugriff auf das 
      Firmennetzwerk.

5. VPN-Verbindung trennen
   a) Klicken Sie auf das GlobalProtect-Symbol in der Taskleiste.
   b) Klicken Sie auf „Trennen".

6. Häufige Fehler
- „Portal nicht erreichbar": Prüfen Sie Ihre Internetverbindung.
- „Anmeldung fehlgeschlagen": Prüfen Sie E-Mail und Passwort, ggf. Passwort 
  zurücksetzen (siehe BA-001).
- „MFA-Timeout": Führen Sie die Anmeldung erneut durch und bestätigen Sie 
  die MFA-Anfrage innerhalb von 60 Sekunden.
- Langsame Verbindung: Schließen Sie nicht benötigte Programme und prüfen Sie 
  Ihre Internetbandbreite.

7. Kontakt bei Problemen
IT-Helpdesk: helpdesk@firma.de | Telefon: 0800-1234567
```

---

#### 📄 BA-003_Drucker-einrichten.docx

```
Betriebsanweisung BA-003
Titel: Netzwerkdrucker einrichten
Gültig ab: 01.01.2026
Abteilung: IT-Service

1. Zweck
Diese Anweisung beschreibt, wie Sie einen Netzwerkdrucker auf Ihrem 
Windows-Arbeitsplatzrechner einrichten.

2. Voraussetzung
- PC oder Laptop im Firmennetzwerk (WLAN oder LAN) oder per VPN verbunden
- Druckername oder Standort des gewünschten Druckers bekannt 
  (siehe Drucker-Übersicht im Intranet)

3. Drucker über Windows-Einstellungen hinzufügen
   a) Öffnen Sie „Einstellungen" → „Bluetooth und Geräte" → „Drucker und Scanner".
   b) Klicken Sie auf „Gerät hinzufügen".
   c) Warten Sie, bis die verfügbaren Netzwerkdrucker angezeigt werden.
   d) Wählen Sie den gewünschten Drucker aus der Liste aus.
   e) Klicken Sie auf „Gerät hinzufügen".
   f) Der Druckertreiber wird automatisch installiert.

4. Drucker manuell über UNC-Pfad hinzufügen
   Falls der Drucker nicht automatisch angezeigt wird:
   a) Drücken Sie Windows-Taste + R.
   b) Geben Sie den Druckerpfad ein: \\printserver\DruckerName
      (z. B. \\printserver\EG-Flur-HP4520)
   c) Drücken Sie Enter.
   d) Der Drucker wird automatisch installiert und eingerichtet.

5. Drucker als Standarddrucker festlegen
   a) Gehen Sie zu „Einstellungen" → „Drucker und Scanner".
   b) Klicken Sie auf den gewünschten Drucker.
   c) Klicken Sie auf „Als Standard festlegen".

6. Druckerstandorte (Beispiele)
   | Drucker-Name | Standort | Modell |
   |---|---|---|
   | EG-Flur-HP4520 | Erdgeschoss, Flur | HP LaserJet 4520 |
   | OG1-Raum201-Canon | 1. OG, Raum 201 | Canon iR-ADV C5535 |
   | OG2-Kueche-HP402 | 2. OG, Küche | HP LaserJet Pro M402 |

7. Häufige Fehler
- „Drucker offline": Prüfen Sie, ob Sie im Firmennetzwerk/VPN sind.
- „Zugriff verweigert": Kontaktieren Sie den IT-Helpdesk für Druckberechtigungen.
- Schlechte Druckqualität: Wählen Sie im Druckdialog eine höhere Qualitätsstufe.

8. Kontakt bei Problemen
IT-Helpdesk: helpdesk@firma.de | Telefon: 0800-1234567
```

---

#### 📄 BA-004_Outlook-Profil-reparieren.docx

```
Betriebsanweisung BA-004
Titel: Outlook-Profil reparieren (Synchronisationsprobleme)
Gültig ab: 01.01.2026
Abteilung: IT-Service

1. Zweck
Diese Anweisung beschreibt die Fehlerbehebung bei häufigen 
Outlook-Synchronisationsproblemen (E-Mails werden nicht empfangen/gesendet, 
Kalender synchronisiert nicht, Adressbuch ist leer).

2. Schritt 1 – Verbindungsstatus prüfen
   a) Öffnen Sie Outlook.
   b) Prüfen Sie in der Statusleiste unten, ob dort „Verbunden" steht.
   c) Wenn „Offline" angezeigt wird: Klicken Sie auf „Senden/Empfangen" → 
      „Offlinemodus" um diesen zu deaktivieren.

3. Schritt 2 – OST-Datei reparieren
   a) Schließen Sie Outlook vollständig (auch im System-Tray).
   b) Drücken Sie Windows-Taste + R und geben Sie ein:
      %localappdata%\Microsoft\Outlook
   c) Suchen Sie die Datei mit der Endung .ost (z. B. vorname.nachname@firma.de.ost).
   d) Benennen Sie die Datei um (z. B. in „alt_vorname.nachname.ost").
   e) Starten Sie Outlook erneut – die OST-Datei wird automatisch neu erstellt.
   f) Warten Sie, bis die Synchronisation abgeschlossen ist (kann 10–30 Minuten dauern).

4. Schritt 3 – Outlook-Profil neu erstellen
   Falls Schritt 2 das Problem nicht löst:
   a) Schließen Sie Outlook vollständig.
   b) Öffnen Sie „Systemsteuerung" → „Mail (Microsoft Outlook)".
   c) Klicken Sie auf „Profile anzeigen".
   d) Klicken Sie auf „Hinzufügen" und geben Sie einen Profilnamen ein 
      (z. B. „Outlook-Neu").
   e) Geben Sie Ihre E-Mail-Adresse ein und folgen Sie dem Assistenten.
   f) Setzen Sie das neue Profil als Standard.
   g) Starten Sie Outlook – alle Daten werden vom Server synchronisiert.

5. Schritt 4 – Outlook reparieren (Office-Reparatur)
   Falls das Problem weiterhin besteht:
   a) Öffnen Sie „Einstellungen" → „Apps" → „Installierte Apps".
   b) Suchen Sie „Microsoft 365" und klicken Sie auf „Ändern".
   c) Wählen Sie „Schnellreparatur" und klicken Sie auf „Reparieren".
   d) Wenn das nicht hilft: Wiederholen Sie mit „Onlinereparatur".

6. Häufige Fehler
- „Kennwortabfrage erscheint immer wieder": Entfernen Sie gespeicherte 
  Anmeldedaten unter Systemsteuerung → Anmeldeinformationsverwaltung.
- „Freigegebene Postfächer fehlen": Öffnen Sie Datei → Kontoeinstellungen 
  und fügen Sie das Postfach erneut hinzu.
- Große Postfächer synchronisieren langsam: Reduzieren Sie unter 
  Kontoeinstellungen den Offline-Zeitraum auf 6 Monate.

7. Kontakt bei Problemen
IT-Helpdesk: helpdesk@firma.de | Telefon: 0800-1234567
```

---

#### 📄 BA-005_WLAN-Verbindung.docx

```
Betriebsanweisung BA-005
Titel: Verbindung mit dem Firmen-WLAN herstellen
Gültig ab: 01.01.2026
Abteilung: IT-Service

1. Zweck
Diese Anweisung beschreibt, wie Sie Ihr Firmengerät mit dem 
WLAN-Netzwerk der Firma verbinden.

2. Verfügbare WLAN-Netzwerke
   | Netzwerkname (SSID) | Verwendung | Authentifizierung |
   |---|---|---|
   | FIRMA-CORP | Firmengeräte (automatisch) | Zertifikat/802.1X |
   | FIRMA-GAST | Gäste und private Geräte | Tagespasswort (Empfang) |

3. Firmengerät mit FIRMA-CORP verbinden
   a) Klicken Sie auf das WLAN-Symbol in der Taskleiste (unten rechts).
   b) Wählen Sie das Netzwerk „FIRMA-CORP".
   c) Aktivieren Sie „Automatisch verbinden".
   d) Klicken Sie auf „Verbinden".
   e) Bei Erstverbindung: Melden Sie sich mit Ihrer Firmen-E-Mail und 
      Ihrem Passwort an.
   f) Bestätigen Sie die MFA-Abfrage.
   g) Das Gerät ist nun verbunden und merkt sich die Verbindung.

4. Gast-WLAN (FIRMA-GAST) nutzen
   a) Wählen Sie das Netzwerk „FIRMA-GAST".
   b) Klicken Sie auf „Verbinden".
   c) Es öffnet sich eine Anmeldeseite im Browser.
   d) Geben Sie das Tagespasswort ein (erhältlich am Empfang).
   e) Die Verbindung ist auf 24 Stunden begrenzt und bietet nur 
      Internetzugang (kein Zugriff auf Firmenressourcen).

5. Häufige Fehler
- „Netzwerk FIRMA-CORP wird nicht angezeigt": 
  Stellen Sie sicher, dass WLAN am Gerät aktiviert ist. 
  Prüfen Sie, ob der Flugmodus deaktiviert ist.
- „Authentifizierung fehlgeschlagen": 
  Vergessen Sie das Netzwerk (Rechtsklick → „Nicht speichern") und 
  verbinden Sie sich erneut.
- „Verbunden, aber kein Internet": 
  Starten Sie den WLAN-Adapter neu: 
  Einstellungen → Netzwerk → WLAN → aus- und wieder einschalten.
- Schwaches Signal: 
  Bewegen Sie sich näher an einen Access Point. 
  Access-Point-Standorte sind im Intranet unter IT → Netzwerkplan einsehbar.

6. Kontakt bei Problemen
IT-Helpdesk: helpdesk@firma.de | Telefon: 0800-1234567
```

---

#### 📄 BA-006_Software-Installation.docx

```
Betriebsanweisung BA-006
Titel: Software über das Company Portal installieren
Gültig ab: 01.01.2026
Abteilung: IT-Service

1. Zweck
Diese Anweisung beschreibt, wie Mitarbeiter genehmigte Software über das 
Microsoft Company Portal selbstständig auf ihrem Firmengerät installieren.

2. Voraussetzung
- Firmenverwaltetes Windows-Gerät (in Microsoft Intune registriert)
- Anmeldung mit dem Firmenkonto

3. Company Portal öffnen
   a) Klicken Sie auf das Startmenü und suchen Sie nach „Company Portal" 
      (Unternehmensportal).
   b) Alternativ: Öffnen Sie die Website https://portal.manage.microsoft.com 
      im Browser.
   c) Melden Sie sich mit Ihrem Firmenkonto an.

4. Software suchen und installieren
   a) Klicken Sie im Company Portal auf „Apps".
   b) Durchsuchen Sie den Katalog oder nutzen Sie die Suchleiste.
   c) Verfügbare Software (Beispiele):
      | Software | Kategorie | Beschreibung |
      |---|---|---|
      | Adobe Acrobat Reader | PDF | PDF-Dateien anzeigen und kommentieren |
      | 7-Zip | Werkzeuge | Dateien komprimieren und entpacken |
      | Notepad++ | Entwicklung | Erweiterter Texteditor |
      | Microsoft Visio | Diagramme | Flussdiagramme und Organigramme |
      | Microsoft Project | Projektmanagement | Projektplanung und -steuerung |
      | Mozilla Firefox | Browser | Alternativer Webbrowser |
   d) Klicken Sie auf die gewünschte Software.
   e) Klicken Sie auf „Installieren".
   f) Die Installation läuft im Hintergrund. Der Fortschritt wird im 
      Company Portal angezeigt.
   g) Nach Abschluss finden Sie die Software im Startmenü.

5. Software nicht im Company Portal vorhanden?
   Wenn die benötigte Software nicht im Katalog verfügbar ist:
   a) Erstellen Sie eine Anfrage per E-Mail an helpdesk@firma.de.
   b) Geben Sie an: Name der Software, Version, Begründung.
   c) Die IT prüft die Anfrage (Lizenz, Sicherheit, Kompatibilität).
   d) Nach Genehmigung wird die Software im Company Portal bereitgestellt.

6. Software deinstallieren
   a) Öffnen Sie das Company Portal.
   b) Gehen Sie zu „Installierte Apps".
   c) Wählen Sie die zu entfernende Software.
   d) Klicken Sie auf „Deinstallieren".

7. Häufige Fehler
- „Installation fehlgeschlagen": Starten Sie das Gerät neu und versuchen 
  Sie es erneut.
- „App nicht verfügbar": Möglicherweise haben Sie nicht die nötige 
  Berechtigung. Kontaktieren Sie den IT-Helpdesk.
- Installation dauert lange: Große Anwendungen benötigen je nach 
  Internetverbindung bis zu 30 Minuten. Beenden Sie die Installation 
  nicht vorzeitig.

8. Kontakt bei Problemen
IT-Helpdesk: helpdesk@firma.de | Telefon: 0800-1234567
```

> **Hinweis:** Kopieren Sie den jeweiligen Text (zwischen den \`\`\`-Markierungen) in ein neues Word-Dokument und speichern Sie es unter dem angegebenen Dateinamen in der SharePoint-Dokumentbibliothek.

> **Tipp:** Erstellen Sie mindestens 3–4 Dokumente mit realistischem Inhalt, damit der Agent sinnvolle Antworten liefern kann.

> **Ergebnis:** Sie haben eine SharePoint-Seite mit Betriebsanweisungen als Wissensquelle vorbereitet.

---

## Aufgabe 4 – Copilot Agent erstellen

### 4.1 Agent anlegen

1. Öffnen Sie **Copilot Studio**: https://copilotstudio.microsoft.com/
2. Stellen Sie sicher, dass die Umgebung **„IT-Helpdesk Entwicklung"** ausgewählt ist
3. Klicken Sie auf **+ Erstellen** → **Neuer Agent**
4. Konfigurieren Sie den Agenten:

   | Eigenschaft | Wert |
   |---|---|
   | Name | `IT-Helpdesk Agent` |
   | Beschreibung | `Beantwortet IT-Support-Anfragen auf Basis von Betriebsanweisungen` |
   | Anweisungen | Siehe unten |

5. Geben Sie folgende **Anweisungen** (Instructions) ein:

   ```
   Du bist ein freundlicher und kompetenter IT-Helpdesk-Mitarbeiter.
   Deine Aufgabe ist es, Mitarbeitern bei IT-Problemen zu helfen.
   
   Beantworte Fragen ausschließlich auf Basis der bereitgestellten 
   Betriebsanweisungen. Wenn du keine passende Antwort in den 
   Betriebsanweisungen findest, weise den Benutzer darauf hin, 
   dass er ein Ticket beim IT-Support eröffnen soll.
   
   Antworte immer auf Deutsch. Sei höflich, präzise und 
   strukturiere deine Antworten in klaren Schritten.
   ```

6. Klicken Sie auf **Erstellen**

### 4.2 Wissensquelle hinzufügen

1. Wählen Sie im Agenten den Bereich **Wissen** (Knowledge) aus
2. Klicken Sie auf **+ Wissen hinzufügen**
3. Wählen Sie **SharePoint** als Quelle
4. Geben Sie die URL Ihrer SharePoint-Seite ein: `https://<IhrMandant>.sharepoint.com/sites/IT-Betriebsanweisungen`
5. Bestätigen Sie die Auswahl und klicken Sie auf **Hinzufügen**

> **Ergebnis:** Der Agent ist erstellt und kann auf die Betriebsanweisungen in SharePoint zugreifen.

---

## Aufgabe 5 – Topics verwalten

### 5.1 Begrüßung anpassen

1. Navigieren Sie im Agenten zu **Topics** (Themen)
2. Öffnen Sie das System-Topic **Begrüßung** (Greeting)
3. Passen Sie die Begrüßungsnachricht an:

   ```
   Willkommen beim IT-Helpdesk! 👋
   
   Ich bin Ihr virtueller IT-Support-Assistent. 
   Beschreiben Sie mir Ihr IT-Problem und ich suche in unseren 
   Betriebsanweisungen nach einer passenden Lösung.
   
   Wie kann ich Ihnen helfen?
   ```

4. Speichern Sie das Topic

### 5.2 Verabschiedung anpassen

1. Öffnen Sie das System-Topic **Ende der Unterhaltung** (End of Conversation)
2. Passen Sie die Nachricht an:

   ```
   Vielen Dank für Ihre Anfrage! 
   Falls Ihr Problem nicht gelöst werden konnte, erstellen Sie bitte 
   ein Ticket unter: helpdesk@ihrefirma.de
   
   Einen schönen Tag noch! 😊
   ```

3. Speichern Sie das Topic

---

## Aufgabe 6 – Topic mit Adaptive Card für Seriennummerabfrage erstellen

Dieses Topic wird ausgelöst, wenn der Benutzer ein Problem mit einem **stationären PC** (Desktop, Workstation) hat – **nicht bei Mobilgeräten** (Smartphone, Tablet).

### 6.1 Neues Topic erstellen

1. Navigieren Sie zu **Topics** → **+ Hinzufügen** → **Topic** → **Von Grund auf neu**
2. Benennen Sie das Topic: **PC-Problem – Seriennummer erfassen**

### 6.2 Trigger-Phrasen definieren

Fügen Sie folgende **Trigger-Phrasen** hinzu:

- `Mein PC funktioniert nicht`
- `Problem mit meinem Desktop`
- `Computer startet nicht`
- `Workstation hat ein Problem`
- `Mein Rechner ist defekt`
- `Desktop-PC Fehler`
- `Problem mit meinem stationären Computer`

### 6.3 Gerätetyp-Abfrage einbauen

1. Fügen Sie einen **Frage-Knoten** (Ask a question) hinzu:
   - **Frage:** `Handelt es sich um einen stationären PC (Desktop/Workstation) oder um ein Mobilgerät (Laptop/Tablet/Smartphone)?`
   - **Identifizieren:** Wählen Sie **Multiple-Choice-Optionen**
   - **Optionen:**
     - `Stationärer PC / Desktop / Workstation`
     - `Mobilgerät (Laptop/Tablet/Smartphone)`
   - **Speichern als Variable:** `varGeraetetyp`

2. Fügen Sie einen **Bedingungsknoten** (Condition) hinzu:
   - **Bedingung:** `varGeraetetyp` ist gleich `Stationärer PC / Desktop / Workstation`

### 6.4 Adaptive Card für Seriennummerabfrage (PC-Zweig)

Im **Ja-Zweig** (stationärer PC) erstellen Sie eine **Adaptive Card**, um die Seriennummer abzufragen:

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
      "text": "🖥️ PC-Problem melden",
      "weight": "Bolder",
      "size": "Large",
      "wrap": true
    },
    {
      "type": "TextBlock",
      "text": "Bitte geben Sie die folgenden Informationen zu Ihrem stationären PC an, damit wir Ihnen schnell helfen können.",
      "wrap": true,
      "spacing": "Medium"
    },
    {
      "type": "TextBlock",
      "text": "Seriennummer des PCs *",
      "weight": "Bolder",
      "spacing": "Large",
      "wrap": true
    },
    {
      "type": "Input.Text",
      "id": "seriennummer",
      "placeholder": "z.B. SN-2024-ABC-12345",
      "isRequired": true,
      "errorMessage": "Bitte geben Sie die Seriennummer ein.",
      "label": "Seriennummer"
    },
    {
      "type": "TextBlock",
      "text": "Standort / Raumnummer",
      "weight": "Bolder",
      "wrap": true
    },
    {
      "type": "Input.Text",
      "id": "standort",
      "placeholder": "z.B. Gebäude A, Raum 201",
      "label": "Standort"
    },
    {
      "type": "TextBlock",
      "text": "Problembeschreibung",
      "weight": "Bolder",
      "wrap": true
    },
    {
      "type": "Input.Text",
      "id": "problembeschreibung",
      "placeholder": "Beschreiben Sie das Problem kurz...",
      "isMultiline": true,
      "label": "Problembeschreibung"
    }
  ],
  "actions": [
    {
      "type": "Action.Submit",
      "title": "📨 Problem melden",
      "data": {
        "action": "submitPCProblem"
      }
    }
  ]
}
```

4. Klicken Sie auf die **Adaptive Card** und konfigurieren Sie die **Ausgabevariablen**:
   - `seriennummer` → Variable `varSeriennummer`
   - `standort` → Variable `varStandort`
   - `problembeschreibung` → Variable `varProblembeschreibung`

5. Fügen Sie nach der Adaptive Card einen **Nachricht-Knoten** hinzu:

   ```
   Vielen Dank! Ihre Meldung wurde erfasst:
   
   📋 Seriennummer: {varSeriennummer}
   📍 Standort: {varStandort}
   📝 Problem: {varProblembeschreibung}
   
   Ein Techniker wird sich in Kürze bei Ihnen melden.
   ```

### 6.5 Mobilgerät-Zweig (Nein-Zweig)

Im **Nein-Zweig** (Mobilgerät) fügen Sie einen **Nachricht-Knoten** hinzu:

```
Für Mobilgeräte (Laptops, Tablets, Smartphones) benötigen wir keine 
Seriennummer direkt. 

Bitte beschreiben Sie Ihr Problem und ich suche in den 
Betriebsanweisungen nach einer Lösung.
```

Fügen Sie danach eine **Weiterleitung zum Thema** hinzu, die zum generativen Antworten (Generative Answers) weiterleitet.

### 6.6 Topic speichern

1. Überprüfen Sie den gesamten Flow im visuellen Editor
2. Speichern Sie das Topic

> **Ergebnis:** Sie haben ein Topic erstellt, das bei PC-Problemen eine Adaptive Card zur Seriennummerabfrage anzeigt und bei Mobilgeräten zur Wissenssuche weiterleitet.

---

## Aufgabe 7 – Agent zur Lösung hinzufügen

1. Öffnen Sie **Power Apps**: https://make.powerapps.com/
2. Wechseln Sie zur Umgebung **„IT-Helpdesk Entwicklung"**
3. Navigieren Sie zu **Lösungen** → **IT-Helpdesk Lösung**
4. Klicken Sie auf **+ Vorhandene hinzufügen** → **Chatbot** → **Agent**
5. Wählen Sie den **IT-Helpdesk Agent** aus und fügen Sie ihn zur Lösung hinzu

> **Ergebnis:** Der Agent ist nun Teil der verwalteten Lösung.

---

## Aufgabe 8 – Agent veröffentlichen

1. Öffnen Sie den **IT-Helpdesk Agent** in **Copilot Studio**
2. Klicken Sie oben rechts auf **Veröffentlichen** (Publish)
3. Bestätigen Sie die Veröffentlichung
4. Warten Sie, bis der Veröffentlichungsprozess abgeschlossen ist

> **Ergebnis:** Der Agent ist veröffentlicht und steht für Tests bereit.

---

## Aufgabe 9 – Agent testen

### 9.1 Test im Copilot Studio

1. Öffnen Sie das **Testfenster** (rechte Seite in Copilot Studio)
2. Testen Sie folgende Szenarien:

| # | Testszenario | Erwartetes Ergebnis |
|---|---|---|
| 1 | Schreiben Sie: `Hallo` | Begrüßungsnachricht wird angezeigt |
| 2 | Schreiben Sie: `Wie setze ich mein Passwort zurück?` | Agent antwortet mit Informationen aus der Betriebsanweisung BA-001 |
| 3 | Schreiben Sie: `Wie richte ich VPN ein?` | Agent antwortet mit Informationen aus der Betriebsanweisung BA-002 |
| 4 | Schreiben Sie: `Mein PC funktioniert nicht` | Topic wird getriggert, Gerätetyp-Frage erscheint |
| 5 | Wählen Sie: `Stationärer PC` | Adaptive Card zur Seriennummerabfrage wird angezeigt |
| 6 | Füllen Sie die Adaptive Card aus und senden Sie ab | Bestätigungsnachricht mit den eingegebenen Daten erscheint |
| 7 | Starten Sie einen neuen Test und wählen Sie bei Gerätetyp: `Mobilgerät` | Hinweis auf Wissenssuche, kein Seriennummer-Formular |
| 8 | Schreiben Sie: `Wie installiere ich einen Drucker?` | Agent antwortet mit Informationen aus BA-003 |
| 9 | Schreiben Sie eine Frage ohne passende Betriebsanweisung | Agent weist auf Ticket-Erstellung hin |

### 9.2 Test-Checkliste

- [ ] Begrüßung wird korrekt angezeigt
- [ ] Agent beantwortet Fragen basierend auf SharePoint-Dokumenten
- [ ] PC-Problem-Topic wird korrekt ausgelöst
- [ ] Adaptive Card wird bei stationären PCs angezeigt
- [ ] Seriennummer und andere Felder werden korrekt erfasst
- [ ] Bei Mobilgeräten wird keine Seriennummer abgefragt
- [ ] Verabschiedung funktioniert korrekt
- [ ] Bei unbekannten Problemen wird auf Ticket-Erstellung verwiesen

---

## Zusammenfassung

| Schritt | Ergebnis |
|---|---|
| Aufgabe 1 | Power Apps Entwicklungsumgebung erstellt |
| Aufgabe 2 | Lösung für den IT-Helpdesk angelegt |
| Aufgabe 3 | SharePoint-Seite mit Betriebsanweisungen erstellt |
| Aufgabe 4 | Copilot Agent erstellt und Wissensquelle verknüpft |
| Aufgabe 5 | Begrüßung und Verabschiedung angepasst |
| Aufgabe 6 | Topic mit Adaptive Card für Seriennummerabfrage erstellt |
| Aufgabe 7 | Agent zur Lösung hinzugefügt |
| Aufgabe 8 | Agent veröffentlicht |
| Aufgabe 9 | Agent getestet und validiert |

---

## Bonus-Aufgaben (optional)

- **Bonus 1:** Erstellen Sie einen **Power Automate Flow**, der bei Absenden der Adaptive Card automatisch ein Ticket in einer SharePoint-Liste erstellt
- **Bonus 2:** Veröffentlichen Sie den Agenten in **Microsoft Teams** als App
- **Bonus 3:** Fügen Sie ein weiteres Topic hinzu, das bei Netzwerkproblemen automatisch den Netzwerkstatus prüft
