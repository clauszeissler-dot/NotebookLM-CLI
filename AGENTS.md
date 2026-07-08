# AGENTS.md — Setup guide for coding agents

This repository automates **Google NotebookLM** from the command line via the
[`notebooklm-py`](https://github.com/teng-lin/notebooklm-py) CLI. It ships a
Claude Code skill (`SKILL.md`) plus bilingual docs (`README.md` / `README.en.md`,
`USER-HANDBOOK.md`).

If you are an AI coding agent and a user pointed you at this repo, follow the
steps below **in order**.

---

## ⚠️ Read this first — tell the user before you install anything

- This is **NOT an official, Google-licensed product** and is **not affiliated
  with Google**. NotebookLM has no official consumer API; `notebooklm-py` drives
  a reverse-engineered, undocumented interface.
- Automating a NotebookLM account may **violate Google's Terms of Service** and
  **can get the user's Google account suspended or banned**.
- Make the user aware of this and recommend a **dedicated secondary/throwaway
  Google account** — never their primary account, and no business-critical or
  sensitive content. Only proceed with the user's informed consent.

---

## 1. Setup (you can automate this)

Create and activate an isolated virtual environment, then install the deps:

```bash
python -m venv notebooklm-venv
source notebooklm-venv/bin/activate       # Windows: notebooklm-venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## 2. (Optional) Install the Claude Code skill

So the agent knows the NotebookLM commands and their autonomy rules:

```bash
mkdir -p ~/.claude/skills/notebooklm
cp SKILL.md ~/.claude/skills/notebooklm/SKILL.md
```

## 3. Authentication — STOP, this is a human step

The NotebookLM login is an **interactive browser login** and **cannot be
automated** (cookie-profile auth, no API). **Do not script, headless, or fake
it.** After setup, stop and ask the user to run:

```bash
notebooklm login --browser chrome
notebooklm auth check --test              # should print "Authenticated"
```

Continue only once the user confirms `auth check` succeeds.

## 4. After login (you can automate again)

You now have the full CLI. Smoke test:

```bash
notebooklm list
```

- Command reference & autonomy rules: **`SKILL.md`** (read commands run freely;
  `delete`, `generate`, `download` ask the user first).
- Detailed examples & troubleshooting: **`USER-HANDBOOK.md`**.

---

## Operating notes

- Always pass `-n <notebook_id>` instead of `notebooklm use`, and run commands
  **serially** — the shared login profile is not safe for parallel/fan-out use.
- The login session expires periodically. If `auth check` starts failing, ask
  the user to re-run `notebooklm login`. Never assume success — verify with
  `auth check --test` before reporting that anything works.
