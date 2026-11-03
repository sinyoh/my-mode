# my-mode

A personal Claude Code skill. It makes Claude:

- **Ask before coding.** It checks the ticket against the code, refuses a wrong ticket with evidence, and asks about open decisions as numbered questions with lettered options (`1a 2b`).
- **Write the simplest correct code.** Stdlib and framework built-ins first, no speculative abstractions, and never lazy about security, input validation, or data loss.
- **Fix root causes.** It reproduces first, traces back to the first wrong state, and fixes it there.
- **Prove it works.** It runs the code before calling it done, and says plainly when it couldn't.
- **Reply in an ADHD-friendly shape.** The next action comes first, steps are numbered, real time estimates, and replies end with one `Next:` line.

It was built from 4 plugins: [i-have-adhd](https://github.com/ayghri/i-have-adhd), [ponytail](https://github.com/dietrichgebert/ponytail), [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills), and [pstack](https://github.com/michael-denyer/pstack-claude). I kept the parts that held up in A/B tests and dropped the rest.

## Install

Requires Claude Code. Opus 5.5 needs Claude Code 2.1.280 or newer.

```bash
git clone https://github.com/sinyoh/my-mode.git ~/.claude/skills/my-mode
```

On Windows, `~` is `C:\Users\<name>`. Check that it works by opening Claude Code in any folder and typing `/my-mode`.

## Make it always on (optional)

Without this step, you type `/my-mode` at the start of each session. To load it in every session, create or edit `~/.claude/CLAUDE.md` and add this line:

```
@~/.claude/skills/my-mode/SKILL.md
```

If ponytail, pstack, i-have-adhd, or karpathy-guidelines are also installed, turn them off in `~/.claude/settings.json` → `enabledPlugins`. With all 4 stacked, Opus 5.5 implemented a deliberately wrong ticket in testing. Plain Opus and my-mode both refused it.

## Per-project facts

my-mode is global and knows nothing about any one project. Each project describes itself in a `CLAUDE.md` at its root, which Claude Code loads automatically:

```markdown
# <Project name>

- Stack: <language + framework + version, DB>
- Mode: <learning: explain the why fully | production: terse>
- Run: `<command>`
- Test: `<command>` (none yet? say "none, use a self-check script")
- Secrets: <where they live; never print or commit them>
- Never: <project-specific don'ts>
```

If a project has no `CLAUDE.md`, my-mode reads the README and config files to find the stack instead of assuming it.

## Use

| You type | Effect |
|---|---|
| `/my-mode` | Turn it on for this session |
| `1a 2b 3a` | Answer its questions |
| `explain` / `teach` / `walk me through` | Full explanation with headers |
| `normal mode` | Turn it off for this session |

## Update

```bash
cd ~/.claude/skills/my-mode
git pull
```

Edit `SKILL.md` only. Claude doesn't load this README.

## Remove

Delete `~/.claude/skills/my-mode/`, and the `@` line in `~/.claude/CLAUDE.md` if you added it.

## Tested

All results are from headless `claude -p` runs on Opus 5.5, 1 run per setup per ticket.

- **Wrong ticket** ("switch login to md5"): my-mode refused and cited the schema line saying the hashes are bcrypt. The 4 plugins stacked together implemented it.
- **Vague tickets** (CRUD, pagination, retry): my-mode answered what the code could answer and asked about the rest. After the answers, it built exactly what was chosen, and its test passed.
- **Cost:** asking adds a turn, so a ticket costs about 2× a no-questions run.
