<!-- ki-affairs-banner:start -->
<p align="center">
  <img src=".github/ki-affairs-banner.png" alt="KI AffAIrs — Sicherheit. Vertrauen. Nutzen." width="100%">
</p>
<!-- ki-affairs-banner:end -->

# NotebookLM CLI

**Deutsch** | [🇬🇧 English](README.en.md)

Automatisiere **Google NotebookLM** über die Kommandozeile — Notebooks anlegen, Quellen hinzufügen, Web-Research starten, mit deinen Quellen chatten und Podcasts (Audio Overviews), Reports (inkl. Blog-Post), Videos, Quizze, Flashcards und mehr generieren.

Das Repo besteht aus zwei schlanken Teilen rund um die CLI [`notebooklm-py`](https://github.com/teng-lin/notebooklm-py):

- **[`SKILL.md`](SKILL.md)** — ein Skill für **Claude Code**, damit Claude die NotebookLM-Befehle autonom und regelkonform ausführt („Erstelle ein Notebook, lade diese URLs rein und mach einen Podcast daraus").
- **[`USER-HANDBOOK.md`](USER-HANDBOOK.md)** — ein ausführliches, einsteigerfreundliches Handbuch mit allen Befehlen, Beispielen und Fehlerbehebung.

> ⚠️ **Wichtig — kein offizielles Google-Produkt:** Dies ist **nicht** von Google lizenziert und **nicht** mit Google verbunden. NotebookLM hat keine offizielle Consumer-API; `notebooklm-py` nutzt eine reverse-engineerte, undokumentierte Schnittstelle, die brechen kann, wenn Google etwas ändert. Die Automatisierung eines Kontos verstößt möglicherweise gegen Googles Nutzungsbedingungen und **kann zur Sperrung deines Google-Kontos führen**. Nutzung auf eigenes Risiko — am besten mit einem **dedizierten Zweitkonto**, niemals mit einem Konto, dessen Verlust wehtäte, und keine geschäftskritischen oder sensiblen Inhalte.

---

## Was du damit machen kannst

| Bereich | Beispiele |
|---------|-----------|
| Notebooks | erstellen, auflisten, auswählen, löschen |
| Quellen | URLs, YouTube, PDFs, Word, Audio, Video, Bilder hinzufügen; automatische Web-Research |
| Chat | Fragen an die Quellen stellen, Antworten inkl. Quellenangaben, als Notiz speichern |
| Generierung | Podcast (Audio Overview), Video, Report (Briefing / Study Guide / Blog-Post), Quiz, Flashcards, Slide-Deck, Infografik, Mind-Map, Datentabelle |
| Export | Audio (.mp3), Video (.mp4), Slides (.pdf/.pptx), Reports (.md), Quiz/Flashcards (.json/.md/.html), CSV, PNG |

Audio & Reports unterstützen **Format**, **Länge**, **Ausgabesprache** (z. B. Deutsch) und **Custom-Prompts** — z. B. ein *Brief*-Podcast auf Deutsch oder ein *Blog-Post*-Report. Einige Export-Formate (bearbeitbare PPTX, Quiz/Flashcards als JSON/MD, Mind-Map als JSON) gibt es **nur per CLI**, nicht in der Web-UI.

---

## Voraussetzungen

- **Python ≥ 3.10**
- **Google Chrome** (für den einmaligen Login)
- Ein **Google-Konto** mit NotebookLM-Zugang
- Optional: **Claude Code**, wenn du den Skill nutzen willst

---

## Installation

Nutze eine **isolierte virtuelle Umgebung (venv)**, damit die Abhängigkeiten nicht mit anderen Python-Tools kollidieren.

```bash
# 1) Isolierte venv anlegen
python -m venv notebooklm-venv

# 2) Aktivieren
#    macOS / Linux:
source notebooklm-venv/bin/activate
#    Windows (PowerShell):
notebooklm-venv\Scripts\Activate.ps1

# 3) Abhängigkeiten installieren (CLI + Playwright für den Browser-Login)
pip install -r requirements.txt
```

---

## Authentifizierung (einmalig)

Der folgende Befehl öffnet dein installiertes Chrome. Melde dich mit deinem NotebookLM-Google-Konto an — die Sitzung wird automatisch gespeichert (unter `~/.notebooklm/`).

```bash
notebooklm login --browser chrome

# Prüfen, ob die Anmeldung funktioniert:
notebooklm auth check --test
```

> Die Login-Sitzung läuft nach einiger Zeit ab (Google rotiert die Cookies). Wenn `auth check` fehlschlägt, einfach `notebooklm login` erneut ausführen.

**Server ohne Browser** (z. B. gemieteter Root-Server): Login auf einem Rechner mit Browser durchführen und die gespeicherte Sitzung nach `~/.notebooklm/` auf den Server kopieren. Alternativ die Umgebungsvariable `NOTEBOOKLM_AUTH_JSON` mit dem Inline-Auth-JSON setzen (kein Dateizugriff nötig).

---

## Nutzung als Claude-Code-Skill (optional)

Damit Claude Code die Befehle kennt, lege [`SKILL.md`](SKILL.md) in dein Claude-Skills-Verzeichnis:

```bash
mkdir -p ~/.claude/skills/notebooklm
cp SKILL.md ~/.claude/skills/notebooklm/SKILL.md
```

Danach kannst du Claude in normaler Sprache bitten:

> „Erstelle ein NotebookLM-Notebook ‚KI-Grundlagen', füge diese drei URLs als Quellen hinzu, generiere ein Quiz mit mittlerem Schwierigkeitsgrad und einen kurzen Podcast auf Deutsch, und lade alles herunter."

Der Skill bringt sinnvolle Autonomie-Regeln mit (lesende Befehle laufen automatisch, destruktive und lang laufende Aktionen fragen vorher nach).

---

## Beispiel: Research → Podcast → Blog-Post

```bash
# 1) Notebook anlegen (gibt eine notebook_id zurück)
notebooklm create "Research: Mein Thema" --json

# 2) Quellen hinzufügen und auf Indexierung warten
notebooklm source add "https://example.com" -n <notebook_id>
notebooklm source list -n <notebook_id> --json    # bis alle status=READY

# 3) Lücken prüfen, per Web-Research füllen
notebooklm ask "Was fehlt zum Thema X?" -n <notebook_id>
notebooklm source add-research "offene Fragen zu X" -n <notebook_id> --mode deep

# 4) Podcast (kurz, deutsch) generieren und herunterladen
notebooklm generate audio "Fokus auf die Kernpunkte" -n <notebook_id> --format brief --language de --wait
notebooklm download audio ./podcast.mp3 -n <notebook_id>

# 5) Blog-Post als Report generieren und herunterladen
notebooklm generate report --format blog-post -n <notebook_id> --language de --wait
notebooklm download report ./report.md -n <notebook_id>
```

Die Generierung läuft asynchron und dauert je nach Artefakt einige Minuten. Mit `--wait` blockiert der Befehl bis zur Fertigstellung; alternativ mit `notebooklm artifact list` pollen.

> Die vollständige Befehlsreferenz steht in [`USER-HANDBOOK.md`](USER-HANDBOOK.md) und [`SKILL.md`](SKILL.md).

---

## Sicherheit & Hinweise

- Die CLI handelt **im Namen deines eingeloggten Google-Kontos**. Bevorzugt ein dediziertes Konto nutzen und keine sensiblen Inhalte hochladen.
- Bei **parallelen Abläufen** immer `-n <notebook_id>` statt `notebooklm use` verwenden — sonst kollidieren die Prozesse am gemeinsamen Login-Profil. Für zuverlässige Automatisierung die Befehle **seriell** ausführen.
- Downloads landen dort, wo du sie hinschreibst — halte Ausgabe-Pfade bewusst innerhalb eines Projektordners.

---

## Fehlerbehebung

| Symptom | Lösung |
|---------|--------|
| `notebooklm: command not found` | venv aktivieren bzw. `python -m notebooklm …` mit dem venv-Python aufrufen. |
| `auth check` schlägt fehl / „Unauthorized" | `notebooklm login --browser chrome` erneut ausführen (Sitzung abgelaufen). |
| Login schlägt fehl (kein Browser) | Sicherstellen, dass `playwright` in der venv installiert ist und Chrome vorhanden ist (`--browser chrome`). |
| Zwei Python-Versionen auf dem Rechner | Immer den `python` der venv verwenden (venv aktivieren oder vollen Pfad angeben). |
| Generierung scheint zu hängen | Sie dauert Minuten; mit `notebooklm artifact list` pollen oder `--wait` nutzen. |
| „Rate limit" / zu viele Anfragen | Ein paar Minuten warten und erneut versuchen. |
| Gestern lief es, heute nicht | Google hat evtl. etwas geändert: `pip install --upgrade notebooklm-py`, dann `notebooklm auth check --test`. |

---

## Kompatibilität

Entwickelt und getestet mit **`notebooklm-py` 0.6.x**. Da NotebookLM eine inoffizielle Schnittstelle ist, kann sich das Verhalten mit neueren Versionen ändern. Bei Problemen die aktuelle CLI-Oberfläche prüfen:

```bash
notebooklm --help
notebooklm <befehl> --help
```

---

## Lizenz

**Public Domain** — freigegeben unter [The Unlicense](https://unlicense.org). Keine Bedingungen: frei kopieren, ändern, nutzen, verkaufen, weitergeben — für jeden Zweck. Siehe [LICENSE](LICENSE). Die zugrundeliegende `notebooklm-py`-CLI hat ihre eigene Lizenz.

NotebookLM ist ein Produkt von Google. Dieses Projekt ist nicht mit Google verbunden.
