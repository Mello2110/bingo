# 🎱 BINGO — Echtzeit Multiplayer im Browser

> **Keine Installation. Kein Backend. Einfach Link teilen — los spielen.**

Eine browserbasierte Multiplayer-Bingo-App als einzelne HTML-Datei. Jeder Spieler füllt seine eigene Karte selbst aus, alles läuft live über Firebase Realtime Database. Perfekt für Büro-Events, Spieleabende oder als Trinkspiel.

---

## 🚀 Schnellstart (für Spieler)

1. **Link öffnen** → `https://mello2110.github.io/bingo/`
2. **Namen eingeben**
3. Entweder:
   - **Neue Session starten** → Code teilen
   - **Session beitreten** → Code eingeben oder direkten Link öffnen
4. **25 Felder ausfüllen** (eigene Begriffe, Sätze, whatever)
5. **Karte einreichen** → warten bis alle fertig sind
6. **Spielen** → Felder anklicken wenn sie zutreffen → **BINGO!** 🎉

---

## ✨ Features

### Für Spieler
| Feature | Beschreibung |
|---|---|
| 🎴 Eigene Karte | Jeder füllt seine 25 Felder selbst aus — keine vorgefertigten Begriffe |
| 🔀 Shuffle | Felder zufällig neu mischen (Fisher-Yates) |
| 👆 Live-Abhaken | Felder per Klick markieren, sofort für alle sichtbar |
| 👀 Karten ansehen | Andere Spielerkarten live beobachten (read-only) |
| 🔔 Live-Benachrichtigung | Pop-up wenn jemand anderes ein Feld abhakt |
| 📊 Fortschrittsbalken | Alle Spieler auf einen Blick (x/25) |
| 🏆 BINGO-Erkennung | Automatisch für alle 5 Reihen, 5 Spalten & 2 Diagonalen |
| 🎉 Gewinn-Animation | Konfetti-Effekt auf Gewinn-Feldern + BINGO-Modal |
| 🔥 Status-Warnung | Hinweis bei 1–2 Feldern bis zum nächsten BINGO |
| 🔄 Neue Runde | Ein Klick — alle landen wieder in der Füll-Phase |

### Technisch
| Feature | Beschreibung |
|---|---|
| 🔗 Session-Links | Direkter Beitritt via `?session=CODE` in der URL |
| 💾 Reload-Schutz | `sessionStorage` speichert den Spielzustand — kein Datenverlust bei versehentlichem Reload |
| 📱 Mobile-optimiert | Touch-Targets, kein iOS-Zoom, Momentum-Scrolling, Kompaktmodus |
| 🏠 Multi-Session | Beliebig viele parallele Sessions — vollständig isoliert voneinander |
| 📜 Activity Log | Scrollbarer Verlauf aller Aktionen mit Uhrzeit |
| 🟡 BINGO-Badge | Erscheint im Tab des Gewinners, live aktualisiert |

---

## 🛠 Technische Übersicht

```
bingo.html  (einzige Datei, ~1000 Zeilen)
│
├── CSS          Inline, CSS Custom Properties (Dark Mode, responsive)
├── Firebase SDK 10.12.0 via CDN (ESM-Import)
│   ├── firebase-app.js
│   └── firebase-database.js
└── JavaScript   Vanilla ES2022, kein Build-Step, keine Dependencies
```

### Datenstruktur (Firebase Realtime Database)

```
sessions/
  {SESSION_ID}/
    phase:    "lobby" | "fill" | "play"
    host:     {PLAYER_ID}
    createdAt: {timestamp}
    players/
      {PLAYER_ID}/
        name:     "Max"
        ready:    false
        hasBingo: false
        marked:   12          ← Anzahl abgehakter Felder (für Fortschrittsbalken)
    cards/
      {PLAYER_ID}/
        cells:   ["Begriff 1", "Begriff 2", …]  ← 25 Felder
        marked:  [true, false, true, …]          ← 25 Booleans
    events/
      {PUSH_ID}/
        type:       "mark" | "bingo"
        playerId:   "abc123"
        playerName: "Max"
        field:      "Das abgehakte Feld"
        ts:         1714000000000
```

### Spielphasen-Automat

```
home ──► lobby ──► fill ──► play
                    ▲         │
                    └─────────┘  (neue Runde)
```

### Bingo-Erkennung

12 Gewinn-Kombinationen werden nach jedem Klick geprüft:
- 5 Zeilen (horizontal)
- 5 Spalten (vertikal)  
- 2 Diagonalen

### Wichtige Implementierungsdetails

- **`toArr()`** — Firebase-Array-Normalisierung: Firebase konvertiert sparse Arrays zu Objekten, `toArr()` macht das rückgängig
- **`bingoShown`** — Flag verhindert, dass das BINGO-Modal bei jedem Re-Render erneut aufpoppt
- **`offListeners()`** — Alle Firebase-Listener werden beim Phasenwechsel sauber abgemeldet
- **`persistState()`** — Schreibt aktuellen Zustand in `sessionStorage` nach jeder relevanten Aktion
- **`tryRestore()`** — Prüft beim Boot ob eine laufende Session im `sessionStorage` liegt und kehrt dorthin zurück

---

## ⚙️ Setup (Selbst hosten)

### Schritt 1 — Firebase Realtime Database

1. Auf [firebase.google.com](https://firebase.google.com) einloggen
2. Neues Projekt erstellen
3. **Build → Realtime Database** aktivieren → Region: **Europe West**
4. Unter **Regeln** eintragen:
   ```json
   { "rules": { ".read": true, ".write": true } }
   ```
5. **Projekteinstellungen → Deine Apps → Web App** → Config kopieren
6. In `index.html` die Platzhalter in `firebaseConfig` ersetzen:

```javascript
const firebaseConfig = {
  apiKey:            "...",
  authDomain:        "...",
  databaseURL:       "https://DEIN_PROJEKT-default-rtdb.europe-west1.firebasedatabase.app",
  projectId:         "...",
  storageBucket:     "...",
  messagingSenderId: "...",
  appId:             "..."
};
```

### Schritt 2 — GitHub Pages (kostenloses Hosting)

```bash
git clone https://github.com/[dein-name]/bingo.git
cp bingo.html index.html
git add index.html README.md
git commit -m "Initial release"
git push origin main
```

Dann im Repository: **Settings → Pages → Branch: main → Save**

✅ App läuft unter `https://mello2110.github.io/bingo/`

---

## 📊 Limits & Kapazität

| Metrik | Wert |
|---|---|
| Spieler pro Session | Technisch unbegrenzt, empfohlen **max. 10–12** |
| Gleichzeitige Sessions | ~20 Sessions à 5 Spieler (Firebase Spark Free Tier) |
| Simultane Verbindungen | 100 (Firebase Spark kostenfrei) |
| Datentransfer | 1 GB/Monat (reicht für privaten Gebrauch) |
| Kosten | **$0** — komplett kostenlos |

---

## 🔒 Sicherheit (optional, später)

Die Default-Regeln (`".read": true, ".write": true`) sind für den privaten Gebrauch ausreichend. Für den produktiven Einsatz empfehlen sich granulare Regeln:

```json
{
  "rules": {
    "sessions": {
      "$session_id": {
        ".read": true,
        ".write": "auth != null || root.child('sessions').child($session_id).exists()"
      }
    }
  }
}
```

> Automatisches Löschen alter Sessions: Firebase TTL-Regel einrichten oder manuell in der Console löschen.

---

## 🧰 Entwicklung

Keine Build-Tools notwendig. Einfach `index.html` im Browser öffnen — done.

Für lokale Firebase-Emulation:
```bash
npm install -g firebase-tools
firebase emulators:start --only database
```

---

## 📄 Lizenz

MIT — mach damit was du willst.
