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
- **📱 Optimiert für Handys:** Die App fühlt sich auf dem Smartphone an wie eine native App (kein nerviges Zoomen, perfekte Touch-Steuerung).
- **👀 Spicken erlaubt:** Du kannst jederzeit die Karten deiner Mitspieler ansehen (ohne sie verändern zu können).
- **🔄 Neu starten:** Mit einem Klick auf "Neue Runde" werden alle Karten zurückgesetzt und ihr könnt direkt ein neues Spiel starten.
- **🛡️ Verbindungsabbruch? Kein Problem:** Dein Spielfortschritt wird lokal gespeichert. Wenn du die Seite neu lädst, bist du sofort wieder im laufenden Spiel.

---

## 🛠️ Für die IT-Nerds: Wie es unter der Haube funktioniert

Das gesamte Spiel besteht aus **einer einzigen `index.html` Datei** (inklusive CSS und JavaScript). Es gibt kein Backend und keine komplexen Build-Steps.

- **Datenbank:** Firebase Realtime Database
- **Architektur:** Vanilla JS + CSS Custom Properties. Komplett serverless.
- **Echtzeit-Synchronisation:** Status-Updates, Bingo-Erkennung und Events werden per WebSockets (über Firebase) synchronisiert.
- **Isolierte Sessions:** Es können beliebig viele Runden ("Sessions") gleichzeitig laufen, ohne dass sie sich gegenseitig stören.
- **Sicherer State:** Eine clevere `toArr()` Normalisierung fängt Firebases Eigenheiten mit Sparse-Arrays ab.

### 💻 Selbst hosten

Du möchtest das Spiel selbst hosten oder anpassen?

1. Klone dieses Repository.
2. Erstelle ein Projekt in der [Firebase Console](https://console.firebase.google.com/).
3. Aktiviere die *Realtime Database* und setze die Regeln auf `.read: true, .write: true` (für den privaten Gebrauch völlig ausreichend).
4. Tausche das `firebaseConfig` Objekt im Quellcode gegen deine eigenen Werte aus.
5. Fertig! Lade die `index.html` hoch, wo auch immer du möchtest (z.B. GitHub Pages).

---

<div align="center">
  <i>Viel Spaß beim Spielen! 🎲</i>
</div>
