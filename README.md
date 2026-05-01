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

- **📐 Flexible Rastergrößen:** Der Host kann beim Erstellen zwischen 3x3 (für sehr schnelle Runden), 4x4 oder dem klassischen 5x5 Raster wählen.
- **🕹️ Keine Vorgaben:** Ihr bestimmt die Begriffe! Perfekt für Büro-Meetings, Trash-TV-Abende oder als Trinkspiel.
- **⚡ Echtzeit-Action:** Sieh live, wie andere Spieler Felder abhaken. Du bekommst Pop-up-Benachrichtigungen bei Aktionen der anderen.
- **📱 Als App installierbar (PWA):** Füge die Seite deinem Home-Bildschirm auf iOS/Android hinzu und nutze sie als vollwertige native App mit eigenem Icon.
- **👀 Spicken erlaubt:** Du kannst jederzeit die Karten deiner Mitspieler ansehen (ohne sie verändern zu können).
- **🔄 Smarte Revanche:** Mit einem Klick auf "Neue Runde" springen alle Spieler zurück in die Vorbereitungsphase. Das geniale daran: **Eure eingegebenen Begriffe bleiben erhalten!** Einfach auf "🔀 Mischen" klicken und sofort ein neues Spiel starten.
- **🧹 Felder leeren:** Vertippt? Über den Papierkorb-Button lassen sich alle Felder mit einem Klick wieder löschen.
- **🚪 Clevere Session-Logik:** Verlässt der Host das Spiel, werden alle anderen Spieler automatisch zurück ins Hauptmenü geleitet (Auto-Kick), damit niemand in "Geister-Lobbys" festhängt.
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
    gridSize: 3 | 4 | 5
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
   Damit ein versehentlicher Reload auf dem Smartphone nicht das Spiel zerstört, wird der aktuelle Client-State (Session-ID, Player-ID, Host-Status, etc.) nach jeder Aktion via `sessionStorage` lokal gesichert. Beim Neuladen prüft `tryRestore()`, ob die Session in Firebase noch existiert und reiht den Spieler nahtlos wieder ein.
2. **Dynamische Raster-Architektur:**  
   Die Layouts und Bingo-Gewinn-Abfragen (`getLines()`) sind vollständig algorithmisch aufgebaut. Anstatt harter 25er-Werte generiert die App die Matrix-Gewinnlinien (Zeilen, Spalten, Diagonalen) prozedural basierend auf der initialen Wahl des Hosts (GridSize = 3, 4 oder 5).
3. **Firebase Array-Normalisierung (`toArr`):**  
   Firebase Realtime Database unterstützt native Arrays nur mäßig und konvertiert sogenannte "Sparse Arrays" (Arrays mit Lücken) gerne in Objekte. Um Bugs beim Mapping der Matrix zu verhindern, gibt es eine clevere `toArr()` Helferfunktion.
4. **Isolierte Sessions & Host-Rechte:**  
   Es gibt keine globalen Daten. Alles ist strikt unter einer `sessionId` gekapselt. Wenn der Host die Session über den "🚪 Verlassen" Button schließt, wird der gesamte Firebase-Knoten der Session gelöscht. Listener bei den anderen Clients erkennen das sofort (`!snap.exists()`) und werfen die Spieler sauber zurück in ihr Startmenü.

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
