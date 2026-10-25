# pre-optimization-audit

> **Make the AI go deeper, not wider.**
>
> A portable **Agent Skill**: before "deeply optimizing" existing content, run four gates of audit first.
> Not DSH-specific — works anywhere that reads `SKILL.md` (DSH, Claude Code, Cursor, Codex, …).

[中文说明](README.md)

---

## The problem

Ask any model to "optimize this" and it rewrites — tighter sentences, cleaner structure. But the valuable work rarely lives in the prose. It lives in the questions nobody asked:

- Should this be built this way at all?
- Is the input first-hand, second-hand, or third-hand?
- After the rewrite, what proves it got **better** (not just "reads nicer")?

This skill forces those four questions *before* any rewriting happens.

---

## The four gates

| Gate | Question | Output |
|---|---|---|
| **A** | What would an expert do that the user didn't ask for? | Challenge the premise; hunt for anomalies, contradictions, negative results; flag "imitation research with swapped materials" |
| **B** | How many hands has the source material passed through? | Classify first/second/third-hand; state what first-hand would look like; name the three things most likely lost in transcription |
| **C** | Which **independent** methods verify it? | At least two; not "read it again" or "think harder" |
| **D** | Beyond the user and the agent, what proves it right? | At least one evidence source **outside the conversation**, unaffected by either party's preference; if none exists, label the result "proposal only, not verified" |

Only then comes **Phase 1: optimization** — the direction must be driven by the audit, and the revision carries an **evidence anchor**. If the audit contradicts the user's own suggestion, state the conflict and offer options instead of silently complying.

---

## Install

### Universal (recommended)

```bash
git clone https://github.com/aliez656/pre-optimization-audit.git \
  ~/.agents/skills/pre-optimization-audit
```

> `~/.agents/skills/` is the Agent Skills convention. DSH reads it natively; other hosts can point at their own skills root.

### DSH

```powershell
git clone https://github.com/aliez656/pre-optimization-audit.git `
  "$env:USERPROFILE\.agents\skills\pre-optimization-audit"
```

Workspace-level alternative: `<your project>\.dsh\skills\pre-optimization-audit\`

DSH hot-loads skills — no restart needed.

### Claude Code

```bash
git clone https://github.com/aliez656/pre-optimization-audit.git \
  ~/.claude/skills/pre-optimization-audit
```

### Other hosts

Drop the whole directory into that host's skills root, keeping the name `pre-optimization-audit` and the `SKILL.md` inside it.

---

## Usage

It fires automatically when a request matches the `description`, or you can call it by name:

```
Use pre-optimization-audit to audit this, then deeply optimize
```

### What triggers it (direction-bound only)

| Phrase | Fires |
|---|---|
| "**deeply** optimize" / 深入优化 | ✅ |
| "optimize **in this direction**" / 在这个方向优化 | ✅ |
| "deeply improve" / 深入改进 | ✅ |
| "just optimize it" / 优化一下 | ❌ rewrites directly |
| "improve it" / 改进一下 | ❌ rewrites directly |

> By design: only **direction-bound** optimization requests justify the audit cost. A vague "optimize it" goes straight through.

---

## Output format

```
AUDIT
  A. … (3–5 sentences)
  B. … (3–5 sentences)
  C. … (3–5 sentences)
  D. … (3–5 sentences)

REVISION
  … (the revised content)
  Evidence anchor: <which judgement in this version traces to which audit finding>
```

If the audit concludes the **premise itself needs a rewrite**, output only `REWRITE PROPOSAL` — no REVISION.

---

## Design tradeoffs (honest)

**It does not guarantee better output.** Its value is forcing the reasoning into the open, not guaranteeing the reasoning is right.

Known limits:

- Answers depend on **the model's own domain knowledge** — outside that domain, gate A comes out hollow
- "Independent verification" is hard in pure-writing tasks — gate C is the one most likely to be perfunctory
- Gate D often finds **no external evidence source** for pure writing/analysis; it then says "proposal only" instead of inventing one
- The audit costs **30–60% extra tokens**, which is exactly why the trigger is deliberately narrow

**Not for**: generating from scratch, pure formatting, or cases where the user already named the exact sentence to fix.

---

## File structure

```
pre-optimization-audit/
├── SKILL.md      # the skill (agents read this)
├── README.md     # Chinese, for humans
├── README.en.md  # this file
└── LICENSE       # MIT
```

---

## License

MIT — see [LICENSE](LICENSE). Copyright (c) 2026 aliez656.
