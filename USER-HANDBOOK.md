# NotebookLM CLI — Handbuch

**Version 1.0.0** | Änderungen: siehe [CHANGELOG.md](CHANGELOG.md)

## Was ist das hier?

Du kennst Google NotebookLM — das Tool, in dem du Dokumente, PDFs, YouTube-Videos und Webseiten hochlädst und dann Fragen dazu stellst, Podcasts generieren lässt, Quizze erstellst und vieles mehr.

Normalerweise machst du das alles per Hand in der NotebookLM-Webseite. Mit der `notebooklm-py`-CLI kannst du dieselben Dinge **per Befehl** erledigen — und mit dem beiliegenden Claude-Skill sogar **Claude die Arbeit machen lassen**. Du sagst z. B. „Erstelle ein Notebook, lade diese 5 URLs rein und mach einen Podcast daraus" — und es wird automatisch erledigt, ohne dass du selbst klicken musst.

**Das funktioniert**, weil eine Open-Source-Community herausgefunden hat, wie NotebookLM intern kommuniziert, und daraus ein Werkzeug ([`notebooklm-py`](https://github.com/teng-lin/notebooklm-py)) gebaut hat, das diese Kommunikation nachahmt.

> **Wichtig:** Das Ganze basiert auf inoffiziellen Schnittstellen. Google kann die jederzeit ändern. Dann funktioniert es kurzzeitig nicht, bis die Community das Werkzeug anpasst. Für den täglichen Gebrauch kein Problem — aber nicht für geschäftskritische Prozesse nutzen, die niemals ausfallen dürfen.

---

## Was kann ich damit machen?

Alles, was NotebookLM in der Webseite kann — und einiges mehr:

**Notebooks verwalten:** Erstellen, auflisten, löschen — alles per Befehl statt per Klick.

**Wissensbasen aufbauen:** Webseiten, YouTube-Videos, PDFs, Word-Dokumente, Audiodateien und sogar Bilder als Quellen hinzufügen. Auch automatische Web-Recherche zu einem Thema starten.

**Fragen stellen:** NotebookLM antwortet mit Quellenangaben.

**Inhalte generieren lassen:**
- Podcasts in verschiedenen Formaten (Tiefenanalyse, kurze Zusammenfassung, Debatte, Kritik)
- Videos in verschiedenen Stilen (Whiteboard, Anime, Aquarell und mehr)
- Quizze und Lernkarten mit einstellbarem Schwierigkeitsgrad
- Slide-Decks als PDF oder bearbeitbare PowerPoint
- Infografiken, Mind Maps, Datentabellen, Study Guides, Blog-Posts

**Extras, die es NUR per CLI gibt** (nicht in der NotebookLM-Webseite):
- Quizze und Lernkarten als JSON oder Markdown exportieren
- Mind Maps als Datendatei herunterladen
- Slide-Decks als bearbeitbare PowerPoint-Datei (die Webseite gibt nur PDF)
- Einzelne Folien gezielt überarbeiten
- Den kompletten Text einer Quelle abrufen

---

## Voraussetzungen

Bevor du loslegst, brauchst du drei Dinge:

1. **Python** (Version 3.10 oder neuer). Wenn du nicht weißt, ob du Python hast: Terminal öffnen (Mac: „Terminal"-App, Windows: „PowerShell") und `python --version` eingeben. Erscheint eine Versionsnummer, hast du Python.
2. **Ein Google-Konto**, mit dem du NotebookLM nutzt. Du meldest dich einmalig an — danach merkt sich das Werkzeug deine Anmeldung.
3. **Google Chrome** für den einmaligen Login.

Optional: **Claude Code**, wenn Claude die Befehle für dich ausführen soll (siehe Abschnitt „Als Claude-Skill nutzen").

---

## Einrichtung — Schritt für Schritt

### Schritt 1: Isolierte Umgebung anlegen und Werkzeug installieren

Eine virtuelle Umgebung (venv) hält die Abhängigkeiten sauber getrennt.

```bash
# venv anlegen
python -m venv notebooklm-venv

# aktivieren — macOS / Linux:
source notebooklm-venv/bin/activate
# aktivieren — Windows (PowerShell):
notebooklm-venv\Scripts\Activate.ps1

# CLI + Playwright (für den Browser-Login) installieren
pip install notebooklm-py playwright
```

### Schritt 2: Bei Google anmelden

```bash
notebooklm login --browser chrome
```

Es öffnet sich dein Browser. Melde dich mit deinem Google-Konto an — dem Konto, mit dem du auch NotebookLM nutzt. Danach kannst du den Browser-Tab schließen. Die Anmeldung wird gespeichert; du musst das nicht jedes Mal machen.

### Schritt 3: Prüfen, ob alles funktioniert

```bash
notebooklm auth check --test
```

Erscheint „Authenticated", bist du startklar. Kommt stattdessen ein Fehler, `notebooklm login --browser chrome` erneut ausführen.

**Was, wenn mein Server keinen Browser hat?** (z. B. ein gemieteter Root-Server) — Dann die Anmeldung auf deinem normalen Rechner (mit Browser) durchführen und die gespeicherte Sitzung auf den Server kopieren. Ersetze `user` und `dein-server` entsprechend:

```bash
scp -r ~/.notebooklm user@dein-server:~/
```

Alternativ die Umgebungsvariable `NOTEBOOKLM_AUTH_JSON` mit dem Inline-Auth-JSON setzen (kein Dateizugriff nötig).

---

## Als Claude-Skill nutzen (optional)

Damit Claude Code die NotebookLM-Befehle kennt und regelkonform ausführt, lege die `SKILL.md` in dein Claude-Skills-Verzeichnis:

```bash
mkdir -p ~/.claude/skills/notebooklm
cp SKILL.md ~/.claude/skills/notebooklm/SKILL.md
```

Ab jetzt kannst du Claude in normaler Sprache bitten, Dinge in NotebookLM zu tun. Beispiele:

- „Erstelle ein NotebookLM-Notebook mit dem Titel ‚Workshop KI'."
- „Füge diese URL als Quelle hinzu: https://example.com"
- „Generiere einen kurzen Podcast auf Deutsch aus dem Notebook."
- „Erstelle ein Quiz mit mittlerem Schwierigkeitsgrad und lade die Lernkarten als Markdown herunter."

Du musst keine Befehle kennen — der Skill weiß, welche nötig sind, und fragt bei destruktiven oder lang laufenden Aktionen vorher nach.

---

## Alle Befehle auf einen Blick

Du musst diese Befehle nicht auswendig lernen. Wenn du selbst im Terminal arbeiten willst, findest du hier die komplette Liste. (Mit aktivierter venv reicht `notebooklm …`; sonst `python -m notebooklm …` mit dem venv-Python.)

### Notebooks

| Was du willst | Befehl |
|---------------|--------|
| Notebook erstellen | `notebooklm create "Mein Titel"` |
| Alle Notebooks anzeigen | `notebooklm list` |
| Ein Notebook auswählen | `notebooklm use <notebook_id>` |
| Notebook löschen | `notebooklm notebook delete <id>` |

Die `<notebook_id>` ist eine lange Zeichenkette, die du beim Erstellen oder Auflisten bekommst. Du kannst auch nur die ersten 6 Zeichen verwenden. Bei parallelen Abläufen immer `-n <notebook_id>` statt `use` nutzen.

### Quellen hinzufügen

| Was du willst | Befehl |
|---------------|--------|
| Webseite hinzufügen | `notebooklm source add "https://..."` |
| YouTube-Video hinzufügen | `notebooklm source add "https://youtube.com/watch?..."` |
| Datei hinzufügen (PDF, Word, etc.) | `notebooklm source add ./meine-datei.pdf` |
| Automatische Web-Recherche | `notebooklm source add-research "Suchbegriff"` |
| Tiefe Web-Recherche | `notebooklm source add-research "Suchbegriff" --mode deep` |
| Alle Quellen anzeigen | `notebooklm source list` |
| Quelle löschen | `notebooklm source delete <source_id>` |

Unterstützte Dateitypen: PDF, Word (DOCX), Text, Markdown, Audio, Video, Bilder.

### Fragen stellen

| Was du willst | Befehl |
|---------------|--------|
| Einfache Frage | `notebooklm ask "Deine Frage hier"` |
| Antwort als Notiz speichern | `notebooklm ask "Frage" --save-as-note` |
| Gespräch fortsetzen | `notebooklm ask "Folgefrage" -c <conversation_id>` |
| Gesprächsverlauf anzeigen | `notebooklm history` |

### Inhalte generieren

| Was du willst | Befehl |
|---------------|--------|
| Podcast erstellen | `notebooklm generate audio "Instruktionen"` |
| Kurzer Podcast | `notebooklm generate audio --format brief --length short` |
| Podcast als Debatte | `notebooklm generate audio --format debate` |
| Video erstellen | `notebooklm generate video` |
| Video im Whiteboard-Stil | `notebooklm generate video --style whiteboard` |
| Slide-Deck erstellen | `notebooklm generate slide-deck` |
| Quiz erstellen | `notebooklm generate quiz` |
| Schwieriges Quiz | `notebooklm generate quiz --difficulty hard` |
| Lernkarten erstellen | `notebooklm generate flashcards` |
| Infografik erstellen | `notebooklm generate infographic` |
| Mind Map erstellen | `notebooklm generate mind-map` |
| Datentabelle erstellen | `notebooklm generate data-table "Was vergleichen?"` |
| Briefing-Dokument | `notebooklm generate report --format briefing-doc` |
| Study Guide | `notebooklm generate report --format study-guide` |
| Blog-Post | `notebooklm generate report --format blog-post` |

Alle `generate`-Befehle unterstützen zusätzlich `--language <code>` (z. B. `de`), `--wait` (bis fertig blockieren) und `-s <source_id>` (nur bestimmte Quellen).

### Ergebnisse herunterladen

| Was du willst | Befehl |
|---------------|--------|
| Podcast herunterladen | `notebooklm download audio ./podcast.mp3` |
| Video herunterladen | `notebooklm download video ./video.mp4` |
| Folien als PDF | `notebooklm download slide-deck ./folien.pdf` |
| Folien als PowerPoint (bearbeitbar!) | `notebooklm download slide-deck ./folien.pptx --format pptx` |
| Quiz als Datei | `notebooklm download quiz ./quiz.json` |
| Quiz als Markdown | `notebooklm download quiz --format markdown ./quiz.md` |
| Lernkarten als Datei | `notebooklm download flashcards ./karten.json` |
| Mind Map herunterladen | `notebooklm download mind-map ./mindmap.json` |
| Datentabelle als CSV | `notebooklm download data-table ./tabelle.csv` |
| Report herunterladen | `notebooklm download report ./report.md` |
| Infografik herunterladen | `notebooklm download infographic ./infografik.png` |

---

## Beispiel: Von null zum fertigen Schulungsmaterial

Ein kompletter Durchlauf — so könnte es aussehen, wenn du Claude bittest, Schulungsmaterial zu erstellen:

**Du sagst zu Claude:**

„Erstelle ein NotebookLM-Notebook mit dem Titel ‚KI-Grundlagen Workshop'. Füge diese drei URLs als Quellen hinzu: [URL1], [URL2], [URL3]. Erstelle dann ein Quiz mit mittlerem Schwierigkeitsgrad, Lernkarten und einen kurzen Podcast. Lade alles herunter."

**Was im Hintergrund passiert:**
1. Notebook wird erstellt
2. Die drei URLs werden als Quellen hinzugefügt
3. Es wird gewartet, bis alle Quellen verarbeitet sind
4. Quiz wird generiert
5. Lernkarten werden generiert
6. Podcast wird generiert
7. Alles wird als Dateien heruntergeladen

Am Ende hast du alle Dateien fertig geliefert.

---

## Wenn etwas nicht funktioniert

**„Authentifizierung fehlgeschlagen" oder ähnliche Auth-Fehler** — Die Anmeldung bei Google läuft nach einiger Zeit ab. Einfach `notebooklm login --browser chrome` erneut ausführen.

**„Rate limit" oder „zu viele Anfragen"** — NotebookLM erlaubt nur eine bestimmte Anzahl Anfragen in kurzer Zeit. Ein paar Minuten warten und erneut versuchen.

**Podcast/Video/Quiz wird nicht fertig** — Die Generierung kann 5 bis 45 Minuten dauern — das ist normal. Status mit `notebooklm artifact list` prüfen. Steht dort „COMPLETED", kannst du herunterladen.

**Quelle wird nicht erkannt** — Prüfe, ob die Datei kleiner als 200 MB ist und ob das Format unterstützt wird (PDF, DOCX, TXT, Markdown, Audio, Video, Bilder).

**Nichts funktioniert mehr, obwohl es gestern noch ging** — Google hat möglicherweise etwas an NotebookLM geändert. Update durchführen: `pip install --upgrade notebooklm-py`. Siehe auch den nächsten Abschnitt zu Updates.

**Der Allround-Tipp bei jedem Problem:** `notebooklm auth check --test` — das prüft alles und sagt dir, was los ist.

---

## Limits je nach NotebookLM-Plan

| Dein Plan | Notebooks | Quellen pro Notebook |
|-----------|-----------|---------------------|
| Kostenlos (Standard) | 100 | 50 |
| Plus | 500 | 100 |
| Pro | 500 | 300 |
| Ultra | 500 | 600 |

*(Von Google festgelegt und gelegentlich geändert — im Zweifel im eigenen NotebookLM-Konto prüfen.)*

---

## Pro-Tipp: Automatisch über Updates informiert werden

Die `notebooklm-py`-Library wird regelmäßig aktualisiert. Du willst wissen, wann ein Update da ist, ohne selbst ständig nachzuschauen.

**So funktioniert es:** Du richtest in einem KI-Tool deiner Wahl (Gemini, ChatGPT, Grok — egal welches) eine wiederkehrende Prüfung ein. Das Tool schaut, ob es eine neue Version gibt. Wenn ja, erstellt es dir eine fertige Nachricht, die du nur in deinen Claude-Chat kopierst. Claude führt das Update dann durch.

### Schritt 1: Prompt kopieren

Kopiere den folgenden Text in das KI-Tool deiner Wahl:

```
Du bist mein Update-Monitor für die Python-Library "notebooklm-py".

Deine Aufgabe:
1. Prüfe die aktuelle Version auf PyPI: https://pypi.org/project/notebooklm-py/
2. Prüfe die neuesten Releases auf GitHub: https://github.com/teng-lin/notebooklm-py/releases
3. Vergleiche die gefundene Version mit der letzten dir bekannten Version.

Wenn eine NEUE Version verfügbar ist, erstelle mir folgenden Output als
Copy-Paste-Block, EXAKT so formatiert, damit ich ihn direkt in meinen
Claude-Chat kopieren kann:

---ANFANG COPY-PASTE---

Bitte führe folgendes Update für meinen NotebookLM-Skill durch:

1. Update die notebooklm-py Library:
   pip install --upgrade notebooklm-py

2. Prüfe die neue Version:
   notebooklm --version

3. Teste die Auth:
   notebooklm auth check --test

4. Zeige mir die Änderungen:
   Die neue Version ist [VERSION]. Hier die wichtigsten Änderungen:
   [CHANGELOG ZUSAMMENFASSUNG]

5. Falls es neue CLI-Befehle oder geänderte Parameter gibt:
   Aktualisiere die SKILL.md mit den neuen Befehlen.

---ENDE COPY-PASTE---

Wenn KEINE neue Version verfügbar ist:
Antworte kurz: "notebooklm-py ist aktuell auf Version [VERSION]. Kein Update nötig."

Fasse die Changelog-Einträge immer auf Deutsch zusammen.
Fokus auf: Neue Features, Breaking Changes, wichtige Bugfixes.
```

### Schritt 2: Regelmäßig prüfen lassen

- **Gemini:** Erstelle einen „Gem" mit dem Prompt als System-Instruktion. Öffne ihn wöchentlich und tippe „Prüfe auf Updates".
- **ChatGPT:** Erstelle einen Custom GPT mit dem Prompt als Instructions. Öffne ihn wöchentlich und tippe „Check".
- **Grok:** Speichere den Prompt als Konversation und führe sie wöchentlich erneut aus.

### Schritt 3: Update durchführen

Meldet dein Monitor eine neue Version, bekommst du einen fertigen Text-Block. Den kopierst du in deinen Claude-Chat — den Chat, in dem du den Skill eingerichtet hast. Claude erledigt den Rest: Library aktualisieren, prüfen, ob alles funktioniert, und dir sagen, was sich geändert hat.

### Für Fortgeschrittene: Vollautomatisch mit n8n

Wenn du n8n nutzt (ein Automatisierungs-Tool), kannst du einen Workflow bauen, der das komplett ohne dein Zutun erledigt:

```
Jeden Montag 9:00 Uhr
  → PyPI abfragen: https://pypi.org/pypi/notebooklm-py/json
  → Version aus der Antwort lesen (Feld: info.version)
  → Mit der gespeicherten Version vergleichen
  → Wenn neu: Benachrichtigung senden (Slack, E-Mail oder Telegram)
    mit dem Update-Prompt fertig zum Kopieren
  → Wenn gleich: Nichts tun
```

---

## Lizenz

Dieses Projekt ist **Public Domain** ([The Unlicense](https://unlicense.org)) — ohne jede Bedingung frei verwendbar, anpassbar und weitergebbar, für jeden Zweck. Die zugrundeliegende `notebooklm-py`-Library hat ihre eigene Lizenz.

NotebookLM ist ein Produkt von Google. Dieses Projekt ist nicht mit Google verbunden.
