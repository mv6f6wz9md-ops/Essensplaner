# Familien-Wochenplan

Eine Web-App für den wöchentlichen Essensplan: 95 Gerichte, Rezepte, automatische
Einkaufsliste, Nährwerte pro Portion und ein Ampel-Check für die Ausgewogenheit
der Woche. Läuft als eine einzige HTML-Datei (React/Babel-Standalone, kein
Build-Schritt), genau wie ClientOS und FamilyHub.

Der Plan wird in Firebase Firestore gespeichert und **live synchronisiert** —
du und deine Frau seht denselben Stand in Echtzeit, ohne Neuladen.

---

## 1. Firebase-Projekt anlegen (ca. 5 Minuten)

1. Gehe zu **https://console.firebase.google.com**
2. **Projekt hinzufügen** → Name z. B. `familien-wochenplan`
3. Google Analytics kannst du deaktivieren (wird nicht benötigt)
4. Im Projekt: links im Menü **Build → Firestore Database** → **Datenbank erstellen**
   - Modus: **Produktionsmodus** (wir setzen gleich eigene Regeln)
   - Standort: `eur3 (europe-west)` für niedrige Latenz in Deutschland
5. Links im Menü: **Projektübersicht → Web-Symbol (</>) anklicken** → App registrieren
   - Name z. B. `Wochenplan Web`
   - **Firebase Hosting** NICHT aktivieren (wir nutzen GitHub Pages)
6. Du bekommst einen Codeblock mit `firebaseConfig = { apiKey: ..., ... }`
   → **diese Werte brauchst du gleich für Schritt 3**

### Firestore-Regeln setzen

1. In der Firebase Console: **Firestore Database → Regeln (Tab oben)**
2. Den Inhalt von `firestore.rules` (aus diesem Paket) komplett einfügen
3. **Veröffentlichen** klicken

Das beschränkt den Zugriff auf genau ein Dokument (`mealplan/current`) — niemand
kann andere Daten lesen oder anlegen, selbst wenn die Konfigurationswerte
bekannt sind.

> **Hinweis zur Sicherheit:** Mit dieser einfachen Regel kann jeder, der den
> App-Link kennt, den Plan lesen und ändern — es gibt keinen Login. Für ein
> privates Familientool ohne sensible Daten ist das ein üblicher, pragmatischer
> Kompromiss (genau wie bei den meisten kleinen Firebase-Familienapps). Falls
> du es später abschließen willst, kann eine simple Passwort-Abfrage oder
> Firebase Authentication ergänzt werden — sag einfach Bescheid.

---

## 2. GitHub-Repo anlegen (ca. 3 Minuten)

1. Auf **github.com** → **New repository**
2. Name: `mealplan` (oder wie du magst)
3. **Public** (für kostenloses GitHub Pages nötig — der Code ist nicht
   sicherheitsrelevant, der eigentliche Schutz läuft über die Firestore Regeln)
4. Lade alle Dateien aus diesem Paket in das Repo hoch:
   - `mealplan.html`
   - `manifest.json`
   - `icon-192.png`
   - `icon-512.png`
   - (README und firestore.rules kannst du mit hochladen, werden aber nicht
     von der App selbst benötigt)
5. **Settings → Pages** (linkes Menü)
   - Source: **Deploy from a branch**
   - Branch: `main`, Ordner `/ (root)`
   - Speichern
6. Nach ca. 1 Minute ist die App erreichbar unter:
   `https://DEIN-GITHUB-NAME.github.io/mealplan/mealplan.html`

---

## 3. Firebase-Konfiguration eintragen

1. Öffne `mealplan.html` (im Repo, z. B. direkt im GitHub-Web-Editor mit dem
   Stift-Symbol, oder lokal)
2. Suche den Block:
   ```js
   const firebaseConfig = {
     apiKey: "DEIN_API_KEY",
     authDomain: "DEIN_PROJEKT.firebaseapp.com",
     projectId: "DEIN_PROJEKT",
     storageBucket: "DEIN_PROJEKT.appspot.com",
     messagingSenderId: "DEINE_SENDER_ID",
     appId: "DEINE_APP_ID",
   };
   ```
3. Ersetze die Werte mit den echten Werten aus Schritt 1 (aus der Firebase
   Console kopiert)
4. Speichern / committen

Die Seite lädt sich automatisch neu (GitHub Pages braucht nach jedem Push
ca. 30–60 Sekunden zum Aktualisieren).

---

## 4. Als App auf den Homescreen legen

**iPhone/iPad (Safari):**
1. Öffne den Link `https://DEIN-GITHUB-NAME.github.io/mealplan/mealplan.html`
2. Teilen-Symbol (Quadrat mit Pfeil nach oben) → **Zum Home-Bildschirm**
3. Fertig — die App öffnet sich jetzt ohne Browser-Leiste, mit eigenem Icon

**Android (Chrome):**
1. Link öffnen → Menü (drei Punkte) → **App installieren** bzw.
   **Zum Startbildschirm hinzufügen**

Wiederhole das auf dem Gerät deiner Frau mit demselben Link — beide Geräte
greifen auf denselben Firestore-Datensatz zu, der Plan ist also identisch und
synchronisiert sich in Echtzeit.

---

## Wie die Synchronisation funktioniert

- Der komplette Wochenplan liegt als ein Dokument in Firestore
  (`mealplan/current`), gespeichert als Liste von 7 Rezept-IDs (oder `null`
  für einen leeren Tag)
- Die App lauscht per `onSnapshot` auf Änderungen — ändert ein Gerät den
  Plan, bekommen alle anderen offenen Geräte die Änderung **automatisch und
  sofort**, ohne Neuladen
- Im Kopfbereich der App zeigt ein kleiner grüner Punkt "Live", wenn die
  Verbindung steht; bei Verbindungsproblemen erscheint ein Hinweis

## Spätere Anpassungen

Da alles in `mealplan.html` steckt (Rezeptdatenbank + Logik + Darstellung),
reicht es, diese eine Datei zu bearbeiten und neu hochzuladen, um z. B.
weitere Gerichte zu ergänzen oder das Design anzupassen — genau wie bei
ClientOS/FamilyHub.
