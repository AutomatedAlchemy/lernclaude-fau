# lernclaude

A stdlib-only launcher that starts [Claude Code](https://claude.com/claude-code)
inside an exam-prep folder, so that folder's `CLAUDE.md` drives the study loop:
open your reference sheets and the newest problem set, then feed you one
bite-sized exercise at a time, personalised from your actual mistakes. The
launcher encodes no study procedure: about 2000 lines that pick a folder, pick a
model and send a "let's study" trigger, so you improve the loop by editing a
workspace's `CLAUDE.md`, not this code. Tested on Linux with KDE.

This is the FAU variant of lernclaude. It adds the NHR@FAU gateway backend
(`fauclaude`, `fau:` models); the public base is
<https://github.com/AutomatedAlchemy/lernclaude>.

> **The tool speaks German** — prompts, workspace template, menu. The loop itself
> is language-agnostic: [docs/workspace.md](docs/workspace.md#using-it-in-another-language).

## Requirements

- [Claude Code](https://claude.com/claude-code) on your `PATH`
- Python 3.9+, **standard library only** (`requirements.txt` is empty)
- `konsole` (KDE) for the windowed launch; without it, it runs in your terminal

## Install

```bash
git clone https://github.com/AutomatedAlchemy/lernclaude-fau.git
cd lernclaude-fau
python3 main.py          # menu; first entry is "neuen Kurs anlegen"
python3 main.py --install # desktop icon + `lernen` alias
```

*neuen Kurs anlegen* starts a Claude session that searches your filesystem with
you, agrees on a course folder, scaffolds and registers it, then reads your
material and fills in exam date, topic map and starting points. `--install` needs
[`cli-tools-kit`](https://github.com/Probst1nator/cli-tools-kit); without it it
says what is missing, and the tool still runs by path.

## Usage

A bare `lernen` opens a curses picker of your registered courses, exam banner
above, progress note per row. `↑`/`↓` move · `Enter` starts · `Space` opens the
Meta multiselect · `g` Gärtner · `m` medium · `o` model · `e` effort · `d`
default · `x` removes · `q` quits. Untouched for ten seconds it autostarts your default.

| Launch | What it does |
|---|---|
| `lernen <workspace>` | Run that course's Lern-Loop; `--menu` forces the menu instead |
| `lernen --tutor` | Tutors Choice: one session picks the most urgent course from per-course dossiers and tutors it |
| `lernen --quickie` | One short, winnable Häppchen (about 5 min); counts a daily streak |
| `lernen --meta [ZIEL QUELLE…]` | Meta-Häppchen: one short Häppchen in ZIEL shaped by the QUELLE courses; bare reuses the last combination |
| `lernen --gaertner [PFAD…]` | Gärtner: a maintenance session, no studying. You tell it in chat which courses to clean up, merge or revise; the paths are its focus |

Mechanics of the four modes, the streak and the dossiers: [docs/modes.md](docs/modes.md).
Other flags: `--list` (`*` marks the default), `--set-default`, `--set-medium`,
`--set-model`, `--set-effort`, `--add` (guided onboarding), `--register`,
`--unregister`, `--print-prompt`, `--dry-run`, `--install [ws]`, `--remove [ws]`.

**Starting on login:** no `lernen` flag. Tick **Auto-Start** in the
`cli-tools-kit` installer (pre-ticked via `default_autostart`); it symlinks the
icon into `~/.config/autostart` and clears it on removal. What starts is the menu
with its countdown, so you can always get out.

## Media

Sessions work either in **Xournal++ + Firefox** (the default: exercise PDF in the
browser, calculations on a separate `.xopp` sheet) or on a **Tutor Board** (a web
whiteboard, one tab per exercise, needs an account on the author's invite-only
service). Switch with `m`, `lernen --set-medium xournalpp|board` or
`LERNCLAUDE_MEDIUM`; verbal mid-session switch and per-course pin in
[docs/workspace.md](docs/workspace.md#the-medium-switch).

**Tutor Board** lives at <https://beta.probable.work>. An agent connects as an
MCP (Model Context Protocol) connector, `https://beta.probable.work/mcp`, with
OAuth 2.1 + PKCE (Proof Key for Code Exchange); live docs at
<https://beta.probable.work/setup.md> and <https://beta.probable.work/agent.md>.
lernclaude ships no MCP client and no credentials — those come from your own
Claude Code MCP config. Status 2026-09-16: multi-user yes, several agents in
parallel on one account not safe yet, one agent per account link.

## The exam banner

Set `LERNCLAUDE_EXAMS` (or the registry key `"exams_file"`) to a markdown file of
exam dates and the menu lists the upcoming ones above the courses: soonest first,
days left, red inside three days, yellow inside ten. Unset, no banner. It reads
every table with **both** a date column (`Termin`, `Datum`, `Date`, `When`) and a
label column (`Fach`, `Kurs`, `Modul`, `Prüfung`, `Klausur`, `Subject`, `Course`,
`Exam`), so existing notes work as-is:

```markdown
| Prüf-Nr | Fach                  | ECTS | Termin              |
|---------|-----------------------|-----:|---------------------|
| 12345   | Lineare Algebra I     |  2,5 | **Mi 16.09. 09:00** |
| 23456   | Thermodynamik         |    5 | **Fr 25.09.**       |
```

The time is optional and a bare `12.01.` means the *next* 12 January. Rows past,
struck through (`~~…~~`) or marked `abgelegt` / `bestanden` / `Rücktritt` /
`entfällt` / `verschoben` / `TBD` drop out.

## Configuration

All optional.

| Variable | Effect |
|---|---|
| `LERNCLAUDE_REGISTRY` | Path to the course registry (default: `./data/registry.json`) |
| `LERNCLAUDE_DEFAULT_WORKSPACE` | Override the registered default for one launch |
| `LERNCLAUDE_ROOT` | Where guided onboarding starts searching (default: `$HOME`) |
| `LERNCLAUDE_EXAMS` | Markdown file with your exam-date table (banner off when unset; registry key `exams_file` does the same) |
| `LERNCLAUDE_BACKEND` | Override the active backend for one launch (`claude` \| `fauclaude`) |
| `LERNCLAUDE_FAUCLAUDE_CMD` | Explicit command / arguments to launch fauclaude |
| `LERNCLAUDE_MEDIUM` | Override the working medium for one launch (`xournalpp` \| `board`) |

Registered courses live in the gitignored `data/registry.json`, in the tool's own
directory rather than `~/.config`, so a synced or shared checkout carries the same
course list on every machine.

Sessions launch on **Opus at `medium` effort**. Medium is deliberate: the loop is
interactive tutoring, where latency is felt more than reasoning depth helps. `o`
(cycle) or `O` (interactive menu) and `e` in the menu (or `--set-model` / `--set-effort`)
change model (Opus | Sonnet | Fable, then the NHR@FAU gateway models shown as `fau: <name>`) and
effort (low | medium | high | xhigh | max); the pick is used as chosen and
persists in the registry.

Picking a `fau:` model launches through `fauclaude` instead of `claude`, with
`LERNCLAUDE_BACKEND` forcing the launcher regardless of the model. That path
needs an NHR@FAU account and the author's private `fauclaude` repo.

A workspace itself is a normal folder with your material plus a `CLAUDE.md`.
Onboarding stamps a minimal skeleton (`CLAUDE.md`, `todo.md`, `fehlermuster.md`,
`Personalisierte_Übungen/`) and never overwrites an existing file. Layout, the
overview contract and adding a course: [docs/workspace.md](docs/workspace.md).

## Tests and license

```bash
python3 -m pytest -q          # 18 offline tests, no network, no launch
```

MIT — see [LICENSE](LICENSE).
