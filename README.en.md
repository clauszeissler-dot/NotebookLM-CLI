# NotebookLM CLI

[🇩🇪 Deutsch](README.md) | **English**

Automate **Google NotebookLM** from the command line — create notebooks, add sources, run web research, chat with your sources, and generate podcasts (Audio Overviews), reports (incl. Blog Post), videos, quizzes, flashcards and more.

The repo is two thin pieces built around the [`notebooklm-py`](https://github.com/teng-lin/notebooklm-py) CLI:

- **[`SKILL.md`](SKILL.md)** — a skill for **Claude Code** so Claude runs the NotebookLM commands autonomously and safely ("Create a notebook, add these URLs, and turn them into a podcast").
- **[`USER-HANDBOOK.md`](USER-HANDBOOK.md)** — a thorough, beginner-friendly manual with every command, examples and troubleshooting.

> ⚠️ **Important:** NotebookLM has no official consumer API. `notebooklm-py` uses a reverse-engineered, undocumented interface. It can break when Google changes things, and automating an account may conflict with Google's Terms of Service. Use at your own risk, ideally with a dedicated account — no business-critical or sensitive content.

---

## What you can do

| Area | Examples |
|------|----------|
| Notebooks | create, list, select, delete |
| Sources | add URLs, YouTube, PDFs, Word, audio, video, images; automatic web research |
| Chat | ask your sources questions, answers incl. citations, save as a note |
| Generation | podcast (Audio Overview), video, report (briefing / study guide / blog post), quiz, flashcards, slide deck, infographic, mind map, data table |
| Export | audio (.mp3), video (.mp4), slides (.pdf/.pptx), reports (.md), quiz/flashcards (.json/.md/.html), CSV, PNG |

Audio & reports support **format**, **length**, **output language** (e.g. German) and **custom prompts** — e.g. a *brief* podcast in German, or a *blog-post* report. Some export formats (editable PPTX, quiz/flashcards as JSON/MD, mind map as JSON) are **CLI-only** — not available in the web UI.

---

## Requirements

- **Python ≥ 3.10**
- **Google Chrome** (for the one-time login)
- A **Google account** with NotebookLM access
- Optional: **Claude Code**, if you want to use the skill

---

## Installation

Use an **isolated virtual environment** so the dependencies never collide with other Python tools.

```bash
# 1) Create an isolated venv
python -m venv notebooklm-venv

# 2) Activate it
#    macOS / Linux:
source notebooklm-venv/bin/activate
#    Windows (PowerShell):
notebooklm-venv\Scripts\Activate.ps1

# 3) Install the CLI + Playwright (needed for the browser login)
pip install notebooklm-py playwright
```

---

## Authentication (one-time)

The command below opens your installed Chrome. Log in with your NotebookLM Google account — the session is saved automatically (under `~/.notebooklm/`).

```bash
notebooklm login --browser chrome

# Verify the login works:
notebooklm auth check --test
```

> The login session expires after a while (Google rotates cookies). When `auth check` starts failing, just re-run `notebooklm login`.

**Headless server** (e.g. a rented root server): run the login on a machine with a browser and copy the saved session under `~/.notebooklm/` to the server. Alternatively set the `NOTEBOOKLM_AUTH_JSON` environment variable with the inline auth JSON (no file access needed).

---

## Use as a Claude Code skill (optional)

To teach Claude Code the commands, place [`SKILL.md`](SKILL.md) into your Claude skills directory:

```bash
mkdir -p ~/.claude/skills/notebooklm
cp SKILL.md ~/.claude/skills/notebooklm/SKILL.md
```

Then ask Claude in plain language:

> "Create a NotebookLM notebook 'AI Basics', add these three URLs as sources, generate a medium-difficulty quiz and a short podcast in German, and download everything."

The skill ships sensible autonomy rules (read commands run automatically, destructive and long-running actions ask first).

---

## Example: research → podcast → blog post

```bash
# 1) Create a notebook (returns a notebook_id)
notebooklm create "Research: My Topic" --json

# 2) Add sources and wait for indexing
notebooklm source add "https://example.com" -n <notebook_id>
notebooklm source list -n <notebook_id> --json    # until all status=READY

# 3) Check gaps, fill them via web research
notebooklm ask "What's missing on topic X?" -n <notebook_id>
notebooklm source add-research "open questions about X" -n <notebook_id> --mode deep

# 4) Generate and download a podcast (brief, German)
notebooklm generate audio "Focus on the key points" -n <notebook_id> --format brief --language de --wait
notebooklm download audio ./podcast.mp3 -n <notebook_id>

# 5) Generate and download a blog post as a report
notebooklm generate report --format blog-post -n <notebook_id> --language de --wait
notebooklm download report ./report.md -n <notebook_id>
```

Generation runs asynchronously and takes a few minutes per artifact. With `--wait` the command blocks until it's done; otherwise poll with `notebooklm artifact list`.

> The full command reference lives in [`USER-HANDBOOK.md`](USER-HANDBOOK.md) and [`SKILL.md`](SKILL.md).

---

## Security & notes

- The CLI acts **on behalf of your logged-in Google account**. Prefer a dedicated account and avoid uploading sensitive content.
- For **parallel workflows** always use `-n <notebook_id>` instead of `notebooklm use` — otherwise processes collide on the shared login profile. For reliable automation, run commands **serially**.
- Downloads go wherever you point them — keep output paths deliberately inside a project folder.

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `notebooklm: command not found` | Activate the venv, or call `python -m notebooklm …` with the venv's Python. |
| `auth check` fails / "Unauthorized" | Re-run `notebooklm login --browser chrome` (session expired). |
| Login fails (no browser) | Ensure `playwright` is installed in the venv and Chrome is present (`--browser chrome`). |
| Two Python versions on the machine | Always use the venv's `python` (activate it, or give the full path). |
| Generation seems stuck | It takes minutes; poll with `notebooklm artifact list` or use `--wait`. |
| "Rate limit" / too many requests | Wait a few minutes and try again. |
| Worked yesterday, not today | Google may have changed something: `pip install --upgrade notebooklm-py`, then `notebooklm auth check --test`. |

---

## Compatibility

Built and tested with **`notebooklm-py` 0.6.x**. Because NotebookLM is an unofficial interface, behaviour can change with newer versions. If something breaks, check the current CLI surface:

```bash
notebooklm --help
notebooklm <command> --help
```

---

## License

**Public Domain** — released under [The Unlicense](https://unlicense.org). No conditions: copy, modify, use, sell, and redistribute freely, for any purpose. See [LICENSE](LICENSE). The underlying `notebooklm-py` CLI has its own license.

NotebookLM is a product of Google. This project is not affiliated with Google.
