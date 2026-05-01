<div align="center">
  
# 🎱 BINGO — Echtzeit Multiplayer

**Eine browserbasierte Multiplayer-Bingo-App als einzelne HTML-Datei. Ohne Installation. Ohne Backend. Einfach im Browser spielen.**

### 🎮 [HIER KLICKEN UM DAS SPIEL ZU STARTEN](https://mello2110.github.io/bingo/) 🎮

</div>

---

## 🚀 So funktioniert's

Schnapp dir deine Freunde, Kollegen oder Familie und los geht's:

1. **Spiel öffnen:** Klicke auf den [Link zum Spiel](https://mello2110.github.io/bingo/).
2. **Session starten:** Gib deinen Namen ein und erstelle eine neue Runde.
3. **Freunde einladen:** Teile den angezeigten Code oder den direkten Einladungslink.
4. **Begriffe eintragen:** Jeder Spieler füllt seine 5x5 Karte selbst mit eigenen Wörtern, Insidern oder Sätzen.
5. **Mischen & Starten:** Misch deine Karte einmal durch und klick auf "Karte einreichen". Sobald alle bereit sind, geht das Spiel los!
6. **BINGO:** Klicke auf Felder, die eintreffen. Wer zuerst 5 in einer Reihe hat (horizontal, vertikal oder diagonal), gewinnt! 🎉

---

## ✨ Features im Überblick

- **🕹️ Keine Vorgaben:** Ihr bestimmt die Begriffe! Perfekt für Büro-Meetings, Trash-TV-Abende oder als Trinkspiel.
- **⚡ Echtzeit-Action:** Sieh live, wie andere Spieler Felder abhaken. Du bekommst Pop-up-Benachrichtigungen bei Aktionen der anderen.
- **📱 Optimiert für Handys:** Die App fühlt sich auf dem Smartphone an wie eine native App (kein nerviges Zoomen, perfekte Touch-Steuerung, Momentum-Scrolling).
- **👀 Spicken erlaubt:** Du kannst jederzeit die Karten deiner Mitspieler ansehen (ohne sie verändern zu können).
- **🔄 Neu starten:** Mit einem Klick auf "Neue Runde" werden alle Karten zurückgesetzt und ihr könnt direkt ein neues Spiel starten.
- **🛡️ Verbindungsabbruch? Kein Problem:** Dein Spielfortschritt wird lokal gespeichert. Wenn du die Seite neu lädst, bist du sofort wieder im laufenden Spiel.

---

## 🛠️ Für die IT-Nerds: Architektur & Deep Dive

Das gesamte Spiel ist extrem leichtgewichtig und besteht aus **einer einzigen `index.html` Datei** (~1000 Zeilen inklusive CSS und JS). Es gibt keinen Build-Step (Webpack/Vite), keine Frameworks (React/Vue) und kein Backend. 

### Tech Stack
- **Frontend:** Vanilla HTML5, Vanilla CSS3 (Custom Properties für Dark Mode, Responsive Grid), Vanilla ES2022 JavaScript.
- **Backend/State:** Firebase Realtime Database (via CDN importiert).
- **Hosting:** GitHub Pages.

### Datenstruktur (Firebase)
Die App nutzt eine flache, JSON-basierte NoSQL-Struktur in Firebase, um alle Clients in Echtzeit synchron zu halten:

```json
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
        marked:   12          ← Anzahl abgehakter Felder (für den globalen Fortschrittsbalken)
    cards/
      {PLAYER_ID}/
        cells:   ["Begriff 1", "Begriff 2", …]  ← Array (25 Strings)
        marked:  [true, false, true, …]          ← Array (25 Booleans)
    events/
      {PUSH_ID}/
        type:       "mark" | "bingo"
        playerId:   "abc123"
        playerName: "Max"
        field:      "Das abgehakte Feld"
        ts:         1714000000000
```

### Spielphasen-Automat (State Machine)
Der Spielfluss wird über einen global synchronisierten State (`phase`) gesteuert:
```text
home ──► lobby ──► fill ──► play
                    ▲         │
                    └─────────┘  (Reset über "Neue Runde")
```
Alle Clients haben Firebase-Listener (`onValue`) auf diesem Pfad abonniert. Wechselt der Host die Phase, rendern alle Clients automatisch den entsprechenden Screen.

### Design-Entscheidungen & Herausforderungen

1. **State Persistence (Reload-Schutz):**  
   Damit ein versehentlicher Reload auf dem Smartphone nicht das Spiel zerstört, wird der aktuelle Client-State (Session-ID, Player-ID, Host-Status) nach jeder Aktion via `sessionStorage` lokal gesichert. Beim Neuladen prüft `tryRestore()`, ob die Session in Firebase noch existiert und reiht den Spieler nahtlos wieder in die richtige Spielphase ein.
2. **Firebase Array-Normalisierung (`toArr`):**  
   Firebase Realtime Database unterstützt native Arrays nur mäßig und konvertiert sogenannte "Sparse Arrays" (Arrays mit Lücken) gerne in Objekte. Um Bugs beim Mapping der 5x5 Matrix zu verhindern, gibt es eine clevere `toArr()` Helferfunktion, die sicherstellt, dass die Kartendaten immer als iterierbares Array vorliegen.
3. **Event Log & Notification System:**  
   Die "Activity Log" (wer hat was wann abgehakt) wird als append-only Log (`push()`) in Firebase realisiert. Clients sortieren diese nach Timestamp. Ein Timer-Mechanismus sorgt dafür, dass nur Events triggern (und Live-Toasts anzeigen), deren Timestamp *neuer* ist als der lokale `lastEventTs`.
4. **Isolierte Sessions:**  
   Es gibt keine globalen Daten. Alles ist strikt unter einer `sessionId` gekapselt. Ein "Neue Runde"-Reset (`remove()` auf Karten und Events) in Session A hat null Auswirkungen auf Session B.

---

### 💻 Selbst hosten

Du möchtest das Spiel selbst hosten oder modifizieren?

1. Klone dieses Repository.
2. Erstelle ein Projekt in der [Firebase Console](https://console.firebase.google.com/).
3. Aktiviere die *Realtime Database* und setze die Regeln auf `.read: true, .write: true` (für den privaten Gebrauch völlig ausreichend. Für Produktion sollten granularere Session-Rules implementiert werden).
4. Tausche das `firebaseConfig` Objekt im Quellcode (`index.html`) gegen deine eigenen Werte aus.
5. Fertig! Lade die `index.html` hoch (z.B. GitHub Pages oder Vercel).

---

<div align="center">
  <i>Made with ❤️ for Multiplayer Fun. MIT License.</i>
</div>
