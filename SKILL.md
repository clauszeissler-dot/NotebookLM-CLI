---
name: notebooklm
version: 1.0.0
description: >
  Programmatischer Zugriff auf Google NotebookLM via notebooklm-py CLI.
  Notebooks erstellen, Sources hinzufügen (URLs, YouTube, PDFs, Audio, Video, Bilder),
  mit Inhalten chatten, alle Artefakt-Typen generieren, in verschiedenen Formaten downloaden.
  Aktiviert bei /notebooklm oder Intent wie "erstelle einen Podcast über X",
  "Notebook erstellen", "Sources hinzufügen", "Quiz generieren", "Flashcards erstellen",
  "Mind Map", "Slide Deck", "Infografik", "Audio Overview", "Podcast generieren",
  "Schulungsmaterial erstellen", "Research automatisieren", "Wissensmanagement",
  "NotebookLM befüllen", "Briefing erstellen", "Study Guide generieren".
---

# NotebookLM Automation

Programmatischer Zugriff auf Google NotebookLM — inklusive Features die es in der Web-UI nicht gibt.
Basiert auf der notebooklm-py CLI (https://github.com/teng-lin/notebooklm-py).

## Voraussetzungen

```bash
pip install notebooklm-py
notebooklm login          # Browser-Auth, einmalig
notebooklm list           # Prüfen ob Auth funktioniert
```

Bei Auth-Fehlern: `notebooklm login` erneut ausführen.

### Parallele Workflows / CI/CD

| Variable | Zweck |
|----------|-------|
| `NOTEBOOKLM_HOME` | Custom Config-Verzeichnis (Standard: `~/.notebooklm`) |
| `NOTEBOOKLM_AUTH_JSON` | Inline Auth-JSON, kein Dateizugriff nötig |

Bei parallelen Agenten: Immer `-n <notebook_id>` nutzen statt `notebooklm use`.

## Autonomie-Regeln

**Automatisch ausführen (ohne Rückfrage):**
- `notebooklm status`, `auth check`, `list`, `source list`, `artifact list`
- `notebooklm language list/get/set`
- `notebooklm use <id>`, `create`, `ask "..."`, `history`
- `notebooklm source add`

**Vorher fragen:**
- `notebooklm delete` — destruktiv
- `notebooklm generate *` — dauert lang, kann fehlschlagen
- `notebooklm download *` — schreibt ins Dateisystem
- `notebooklm ask "..." --save-as-note` — schreibt eine Notiz

## Kurzreferenz

| Aufgabe | Befehl |
|---------|--------|
| Auth prüfen | `notebooklm auth check --test` |
| Notebooks auflisten | `notebooklm list` |
| Notebook erstellen | `notebooklm create "Titel"` |
| Notebook auswählen | `notebooklm use <notebook_id>` |
| Status anzeigen | `notebooklm status` |
| URL hinzufügen | `notebooklm source add "https://..."` |
| Datei hinzufügen | `notebooklm source add ./datei.pdf` |
| YouTube hinzufügen | `notebooklm source add "https://youtube.com/..."` |
| Sources auflisten | `notebooklm source list` |
| Source löschen (ID) | `notebooklm source delete <source_id>` |
| Source löschen (Titel) | `notebooklm source delete-by-title "Exakter Titel"` |
| Warten auf Indexierung | `notebooklm source wait <source_id>` |
| Web-Research (schnell) | `notebooklm source add-research "query"` |
| Web-Research (tief) | `notebooklm source add-research "query" --mode deep --no-wait` |
| Research-Status | `notebooklm research status` |
| Research warten | `notebooklm research wait --import-all` |
| Frage stellen | `notebooklm ask "Frage"` |
| Frage (bestimmte Sources) | `notebooklm ask "Frage" -s src_id1 -s src_id2` |
| Frage (mit Referenzen) | `notebooklm ask "Frage" --json` |
| Antwort als Notiz | `notebooklm ask "Frage" --save-as-note` |
| Konversation fortsetzen | `notebooklm ask "Frage" -c <conversation_id>` |
| History anzeigen | `notebooklm history` |
| History als Notiz | `notebooklm history --save` |
| Source-Volltext | `notebooklm source fulltext <source_id>` |
| Source-Guide | `notebooklm source guide <source_id>` |
| Podcast generieren | `notebooklm generate audio "Instruktionen"` |
| Video generieren | `notebooklm generate video "Instruktionen"` |
| Report generieren | `notebooklm generate report --format briefing-doc` |
| Report mit Extra | `notebooklm generate report --format study-guide --append "Zielgruppe: Einsteiger"` |
| Quiz generieren | `notebooklm generate quiz` |
| Slide überarbeiten | `notebooklm generate revise-slide "prompt" --artifact <id> --slide 0` |
| Artefakt-Status | `notebooklm artifact list` |
| Warten auf Artefakt | `notebooklm artifact wait <artifact_id>` |
| Audio downloaden | `notebooklm download audio ./output.mp3` |
| Video downloaden | `notebooklm download video ./output.mp4` |
| Slides (PDF) | `notebooklm download slide-deck ./slides.pdf` |
| Slides (PPTX) | `notebooklm download slide-deck ./slides.pptx --format pptx` |
| Report downloaden | `notebooklm download report ./report.md` |
| Mind Map downloaden | `notebooklm download mind-map ./map.json` |
| Datentabelle downloaden | `notebooklm download data-table ./data.csv` |
| Quiz (JSON) | `notebooklm download quiz quiz.json` |
| Quiz (Markdown) | `notebooklm download quiz --format markdown quiz.md` |
| Flashcards (JSON) | `notebooklm download flashcards cards.json` |
| Flashcards (Markdown) | `notebooklm download flashcards --format markdown cards.md` |
| Infografik | `notebooklm download infographic ./infographic.png` |
| Notebook löschen | `notebooklm notebook delete <id>` |
| Sprachen auflisten | `notebooklm language list` |
| Sprache setzen | `notebooklm language set de` |

## Generierungs-Typen

Alle generate-Befehle unterstützen: `-s` (bestimmte Sources), `--language`, `--json`, `--retry N`.

| Typ | Befehl | Optionen | Download |
|-----|--------|----------|----------|
| Podcast | `generate audio` | `--format [deep-dive\|brief\|critique\|debate]`, `--length [short\|default\|long]` | .mp3 |
| Video | `generate video` | `--format [explainer\|brief]`, `--style [auto\|classic\|whiteboard\|kawaii\|anime\|watercolor\|retro-print\|heritage\|paper-craft]` | .mp4 |
| Slide Deck | `generate slide-deck` | `--format [detailed\|presenter]`, `--length [default\|short]` | .pdf / .pptx |
| Slide Revision | `generate revise-slide "prompt" --artifact <id> --slide N` | `--wait` | *(Deck neu laden)* |
| Infografik | `generate infographic` | `--orientation [landscape\|portrait\|square]`, `--detail [concise\|standard\|detailed]`, `--style [auto\|sketch-note\|professional\|bento-grid\|editorial\|instructional\|bricks\|clay\|anime\|kawaii\|scientific]` | .png |
| Report | `generate report` | `--format [briefing-doc\|study-guide\|blog-post\|custom]`, `--append "extra"` | .md |
| Mind Map | `generate mind-map` | *(sofort)* | .json |
| Datentabelle | `generate data-table` | Beschreibung erforderlich | .csv |
| Quiz | `generate quiz` | `--difficulty [easy\|medium\|hard]`, `--quantity [fewer\|standard\|more]` | .json/.md/.html |
| Flashcards | `generate flashcards` | `--difficulty [easy\|medium\|hard]`, `--quantity [fewer\|standard\|more]` | .json/.md/.html |

## JSON-Output

Befehle mit `--json` liefern strukturierte Daten:

```bash
notebooklm create "Research" --json
# {"id": "abc123de-...", "title": "Research"}

notebooklm source add "https://example.com" --json
# {"source_id": "def456...", "title": "Example", "status": "processing"}

notebooklm generate audio "Fokus auf Kernpunkte" --json
# {"task_id": "xyz789...", "status": "pending"}

notebooklm ask "Was ist X?" --json
# {"answer": "...", "conversation_id": "...", "references": [...]}
```

## Workflow: Research zu Podcast

1. `notebooklm create "Research: [Thema]"` — bei Fehler: `notebooklm login`
2. `notebooklm source add` für jede URL/Datei — bei Fehler: warnen, weitermachen
3. `notebooklm source list --json` bis alle status=READY — nötig vor Generation
4. `notebooklm generate audio "Fokus auf [Aspekt]"` — bei Rate Limit: 5 Min warten
5. Artifact-ID merken
6. `notebooklm artifact list` für Status prüfen
7. `notebooklm download audio ./podcast.mp3` wenn fertig

## Workflow: Dokument-Analyse

1. `notebooklm create "Analyse: [Projekt]"`
2. `notebooklm source add ./dokument.pdf`
3. `notebooklm ask "Fasse die Kernpunkte zusammen"`
4. `notebooklm ask "Was sind die Hauptargumente?"`
5. Weiter chatten nach Bedarf

## Features nur per API (nicht in der Web-UI)

| Feature | Befehl |
|---------|--------|
| Quiz/Flashcard als JSON/MD | `download quiz --format json` |
| Mind Map als JSON | `download mind-map` |
| Datentabelle als CSV | `download data-table` |
| Slides als editierbare PPTX | `download slide-deck --format pptx` |
| Einzelne Slides überarbeiten | `generate revise-slide` |
| Report-Template erweitern | `generate report --append "..."` |
| Source-Volltext | `source fulltext <id>` |
| Chat als Notiz speichern | `ask --save-as-note` |
