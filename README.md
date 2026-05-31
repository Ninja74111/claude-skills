# claude-skills

A small, growing collection of [Claude Code](https://claude.com/claude-code) skills I build for my own work and share freely — because good tools are better when more people have them.

The first one is **`html`** — and it might change how you read Claude's longer answers.

---

## ✨ `html` — Persistent HTML-output mode

Claude Code is brilliant in the terminal, until the answer gets *big*. A multi-section analysis, a comparison of five options, anything that really wants a chart — all of it gets squeezed into scrolling monospace text.

**`html` fixes that.** Toggle it on once, and from then on every substantive answer is rendered into a clean, auto-styled HTML page that you keep open in a browser beside your chat:

- 📊 **Real visuals** — inline SVG curves, Chart.js bar/line/donut charts, CSS comparison bars, color-coded cards. Not markdown wrapped in a `<div>` — actual browser rendering that markdown can't do.
- 🧵 **One living document per session** — each answer is appended as a new entry with a sticky table-of-contents sidebar. Click **Refresh ↻** to see the latest.
- ✏️ **Edit-in-place** — say "drop that option" or "what if the price is X" and Claude *revises the existing entry* (with a 🔄 edited badge and strikethrough audit trail) instead of dumping a fresh wall of text.
- 🌍 **Speaks your language** — entries follow whatever language you chat in (Thai in, Thai out; English in, English out), or pin a fixed output language at toggle-on with `/html Thai`.
- 🧭 **Smart routing** — long analysis goes to HTML; short confirmations, errors, and *especially* destructive-action prompts stay in chat where you'll actually see them.

Short clarifications stay in the terminal. The big stuff gets a real page. That's the whole idea.

### What it looks like

Open [`examples/demo-session.html`](examples/demo-session.html) in your browser to see a live sample session (no install needed — just download and open the file).

---

## 🚀 Install (Claude Code)

### Option A — the `skills` CLI (quickest)

```bash
npx skills@latest add Ninja74111/claude-skills -g --all
```

This installs the skill globally so it's available in every project. Drop `--all` to pick interactively, or `-g` to scope it to the current project only. (`npx` fetches the [`skills`](https://github.com/vercel-labs/skills) CLI for you.)

### Option B — copy it yourself (zero dependencies)

Grab [`skills/html/SKILL.md`](skills/html/SKILL.md) and drop it here:

```text
~/.claude/skills/html/SKILL.md     # global (all projects)
# — or —
.claude/skills/html/SKILL.md       # this project only
```

That's it. Restart Claude Code (or start a new session) and the skill is live.

---

## 🎮 Usage

Toggle it **on**:

```text
/html
```

…or just say `html on`, `html mode`, or the equivalent in your language. Claude creates a session file at `./.claude-html/session_<date>_<time>.html`, opens with your last answer as entry #1, and replies with a clickable link. Open that file in a browser and keep it beside your chat.

**Choose the output language.** By default, entries are written in whatever language you're chatting in. To pin a fixed language regardless of how you type, append it when toggling on:

```bash
/html Thai      # every entry in Thai — even when you ask in English
/html English   # force English
```

Change it any time with `html language English`.

From then on:

| You do this | What happens |
|---|---|
| Ask a big question (analysis, comparison, plan) | A new visual **entry** is appended to the HTML page |
| Ask a short / yes-no / numeric question | Answered in **chat** as usual |
| Refine a previous answer ("drop X", "what if…") | The existing entry is **edited in place** |
| Click **Refresh ↻** in the browser | You see the newest entries |
| Prefix a turn with "in chat" / "short" | That one turn skips HTML; mode stays on |

Toggle it **off**: `/html` again, or `html off`. The session file is kept — reopen it any time.

> Tip: add `.claude-html/` to your project's `.gitignore` so session files aren't committed.

---

## 🔧 Customize

The skill is a single Markdown file — open [`skills/html/SKILL.md`](skills/html/SKILL.md) and adjust to taste:

- **Folder** — change `.claude-html/` to wherever you like to keep scratch output.
- **Palette & fonts** — the *Styling* section holds the full default theme (cream background, blue accent, colored cards). Swap colors or the font stack freely.
- **Visualization rules** — the *Visualization mandate* section is what pushes Claude toward real charts instead of plain tables. Loosen or tighten it.

---

## 💡 Why I made this

I run an AI-assisted "second brain" for my investing research, and Claude kept handing me dense, multi-part answers — payoff math, position breakdowns, option comparisons — that were genuinely painful to read in a terminal. I wanted those answers to *look like* the analysis they were: charts, tables, a clean page I could scroll and revisit. So I built this, used it daily, and sanded down the rough edges over many sessions. It's been good enough to me that I figured it might be good to you too.

---

## 👋 About

Built by **Nui** — Entrepreneur & Financial Investor, exploring how far AI can go as a genuine thinking partner.

If this saved you some squinting, I'd love to connect:

- **LinkedIn** → [linkedin.com/in/chakraphol](https://www.linkedin.com/in/chakraphol/)

Always happy to swap notes on AI tooling, investing, or building things — reach out and say hi. 🙂

---

## 🤝 Contributing

Issues and PRs welcome. If you build a cool variation (a different theme, a new chart type, a per-language preset), open a PR — that's the whole point of sharing.

## 📄 License

[MIT](LICENSE) — use it, fork it, ship it, no strings attached.

---

<sub>Made with Claude Code. If you found this via the skill itself — yes, that page you're reading it on was rendered by `html`. 😄</sub>
