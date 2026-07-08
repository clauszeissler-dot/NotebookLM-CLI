# Changelog — NotebookLM CLI

Alle nennenswerten Änderungen an diesem Projekt werden hier dokumentiert.
Format nach [Keep a Changelog](https://keepachangelog.com/de/1.0.0/),
Versionierung nach [Semantic Versioning](https://semver.org/lang/de/).

---

## [1.0.0] — 2026-07-08

### Erstveröffentlichung

- **`SKILL.md`** — Skill für Claude Code zur autonomen Steuerung von Google NotebookLM
  über die `notebooklm-py`-CLI (Notebooks, Quellen, Chat, Web-Research, Generierung von
  Audio/Video/Report/Quiz/Flashcards/Slide-Deck/Infografik/Mind-Map/Datentabelle, Download).
- **`USER-HANDBOOK.md`** — ausführliches, einsteigerfreundliches Handbuch mit allen
  Befehlen, einem End-to-End-Beispiel und Fehlerbehebung.
- **`README.md` / `README.en.md`** — zweisprachige Kurzanleitung (Deutsch / Englisch),
  cross-platform (macOS / Linux / Windows).
- Sinnvolle Autonomie-Regeln im Skill: lesende Befehle laufen automatisch, destruktive
  und lang laufende Aktionen (`delete`, `generate`, `download`) fragen vorher nach.

### Kompatibilität

- Entwickelt und getestet mit `notebooklm-py` **0.6.x**. NotebookLM ist eine inoffizielle
  Schnittstelle — das Verhalten kann sich mit neueren Versionen ändern.
