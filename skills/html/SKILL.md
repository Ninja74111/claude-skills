---
name: html
description: >
  Persistent HTML-output mode for Claude Code. Once toggled on (via /html or
  "html on"), Claude appends substantive answers and plans to a single session
  HTML file you keep open in a browser beside your chat — rendered with real
  SVG / Chart.js visuals, tables, and cards instead of plain terminal text.
  Click a sticky Refresh button to see new entries. Stays active across every
  turn until you say "html off" or toggle /html again. Output follows your chat
  language by default, or a fixed language if you append one (e.g. /html Thai).
  Use when the user invokes /html or says "html on", "html mode", or the
  equivalent in any language (e.g. "เปิด html").
---

# html — Persistent HTML-output mode

Toggle on once → every substantive answer + plan goes to a session HTML file → user opens it in a browser beside chat → clicks Refresh to see new appends → toggle off to stop.

Stateful and persistent: once active, it stays active across every subsequent response until the user explicitly disables it.

The terminal is a poor place to read a multi-section analysis, a comparison table, or anything that wants a chart. This mode pipes those answers into a clean, auto-styled HTML page with **real visuals** (inline SVG, Chart.js, CSS bars) — while short status and confirmations stay in chat where they belong.

---

## Persistence

ACTIVE EVERY RESPONSE once triggered. No silent revert. No drift after many turns. If unsure, check whether a session file from the current `/html` invocation exists; if yes, the mode is on.

Off only when the user says **"html off"**, **"stop html"**, or invokes **`/html`** again (toggle). Localized off-phrases also work (e.g. "ปิด html").

---

## Trigger phrases

Triggers work in any language — match the intent, not the exact string.

| User says | Action |
|---|---|
| `/html`, "html on", "html mode on", "เปิด html" | Toggle ON in **Mode 1** (output matches the chat language). Create session file. Render last substantive answer as entry #1 if one exists in conversation. |
| `/html <language>`, "html on Thai", "html mode in French", "เปิด html อังกฤษ" | Toggle ON in **Mode 2** (lock all output to `<language>`). Same as above + record the fixed language (see "Output language"). |
| "html language English", "switch html to English" (while on) | Change the fixed output language mid-session. |
| `/html` (when already on), "html off", "stop html", "ปิด html" | Toggle OFF. Reply with link to session file + entry count. |
| Turn prefixed with "in chat", "chat only", "short" (or e.g. "สั้นๆ") | Skip HTML for that turn only. Mode stays on. |

---

## Session file

- **Location**: `./.claude-html/session_<YYYY-MM-DD>_<HHmm>.html` in the current working directory. Create the `.claude-html/` folder if it does not exist. (If you maintain a project-specific scratch folder, you may point this there instead.)
- **One file per `/html` ON event** — do not reuse yesterday's file even if the mode toggles on again.
- **Filename time** = the moment of toggle-on, not the current time per entry.
- After writing, reply in chat with a clickable path: `[.claude-html/session_X.html](.claude-html/session_X.html)`.
- **Output language**: by default entries match the chat language. If a language was appended at toggle-on (e.g. `/html Thai`), record it as a `<!-- html-output-language: ... -->` comment in the file and write every entry in that language (see "Output language").

> Tip: add `.claude-html/` to your `.gitignore` so session files don't get committed.

---

## Routing — HTML vs chat

| Response type | Channel |
|---|---|
| Substantive answer (analysis, research, comparison, multi-section explanation) | **HTML** (append entry) |
| Plan / todo list presented to the user as a deliverable | **HTML** (render as visual progress) |
| Short clarification ≤3 sentences | chat |
| Tool execution status ("reading file…", "wrote X") | chat |
| Errors and warnings | chat |
| Code edit + diff summary | chat (the IDE shows the diff) |
| Yes/no confirmations | chat |
| Numeric / free-form input asks | chat |
| **Destructive / irreversible confirms** (delete, push --force, drop, rm -rf, force-merge) | **chat ALWAYS** (Auto-Clarity Exception) |

**TodoWrite distinction**: Internal `TodoWrite` calls for tracking your own progress do NOT render to HTML — the IDE shows that widget. A plan/todo entry goes to HTML only when (a) the user explicitly asks for a plan, or (b) you present a multi-step plan as part of a substantive response.

**Auto-Clarity Exception**: Destructive / irreversible action confirmations stay in chat — never become HTML buttons, never become HTML entries. The frictionless "click → copy → paste" flow is too easy for actions that cannot be undone.

---

## Output language (CRITICAL)

Two modes, chosen when the mode is toggled on:

**Mode 1 — Match the chat (default).** With a plain `/html` / "html on", every HTML entry and chat reply is written in the same language the user is writing in (or the language configured in their project/global instructions). Thai in → Thai out; English in → English out.

**Mode 2 — Fixed output language.** If the user appends a language when toggling on — `/html Thai`, "html on Japanese", "html mode in French", "เปิด html อังกฤษ" — lock ALL output (HTML entries + chat replies) to that language for the whole session, no matter what language the user types their questions in. This mirrors a personal "always one language" setup, generalized to any language.
- **Persist the choice across turns**: write `<!-- html-output-language: Thai -->` directly under the opening `<html ...>` tag of the session file, and set `<html lang="th">` to match. On a later turn, if unsure which language is locked, read that comment to recover it.
- The user can change it mid-session — e.g. "html language English", "switch html to English" — update the marker comment and write subsequent entries in the new language.

Do not default to English just because the source log line or tool output was English. Whichever mode is active, **apply the active language to entry content**:
- All headings (H2/H3/H4)
- All paragraphs and explanations
- All table labels (column headers, row labels)
- All card titles and bodies
- All tile labels and sub-labels
- All chip / pill / badge text
- All tooltip / toast text
- All button labels (except short-code copy buttons like "A"/"B")

**Apply the active language to chat replies** too (the 1-line delta after writing an entry, status messages, clarifications).

**Keep the original token only for** things that are names, not prose:
- Tickers / asset names: `$AAPL`, `QQQ`, `BTC`
- File names / paths / CSS classes / variable names: `config.yaml`, `parser.py`, `.recommend`
- Technical jargon with no clean local equivalent: payoff diagram, hedge, strike, premium, breakeven, scheduler, manifest, alias, chain
- Tool / component / skill names: `my-processor`, `data-indexer`
- Acronyms / versions: `UTC`, `v5.8`, `Phase 2`

**Decision rule**: if a term in the user's language is equally clear and not longer, use it. Do NOT switch to English just because it looks professional or because the source line was English.

**Worked example** (user is writing in Thai — the headings follow suit):

| Wrong (defaulted to English) | Right (matched the user) |
|---|---|
| `<h3>Pipeline timeline</h3>` | `<h3>ไทม์ไลน์การทำงาน</h3>` |
| `<h4>Files touched</h4><div class="val">12</div>` | `<h4>ไฟล์ที่แก้</h4><div class="val">12</div>` |
| `<h3>Open follow-ups (5)</h3>` | `<h3>ค้างต้องตามต่อ (5 ข้อ)</h3>` |
| `<p><strong>Before</strong>: …</p>` | `<p><strong>ก่อนแก้</strong>: …</p>` |
| `<h3>Recommendation</h3>` | `<h3>ข้อแนะนำ</h3>` |

(If the user writes in English, keep everything in English — the same rule, mirrored.)

**Self-check before the chat reply**: if the entry headings are in a different language than the active language → rewrite to match BEFORE telling the user "added entry". Tool output may be in another language, but the framing around it follows the active language.

---

## Visualization mandate (soft enforce)

Before writing any HTML entry, analyze:

1. **What pattern is in the content?** Numerical comparison? Allocation / breakdown? Time series? Payoff math? Sensitivity? Process flow? Composition with inclusions/exclusions? Distribution?
2. **What rendering would a browser provide that markdown could not?** Inline SVG (custom curves, payoff diagrams, flow diagrams), Chart.js (bar / line / donut / radar), CSS horizontal bars with strikethrough audit, color-encoded heatmaps, grid layouts.
3. **If there is genuinely no viz-able pattern** (pure narrative, prose-only response) — append with tables / cards / typography only. Do NOT force a chart. Do NOT make a 3-item donut "to use HTML capability."

**Failure mode to avoid**: wrapping markdown in `<div>`s with a stylesheet. If the entry has zero `<svg>` / zero `<canvas>` / zero CSS-bar / zero visual encoding AND the source content had numerical patterns / payoff math / multi-item composition / sensitivity — that's wrong.

**Plan/todo entries specifically**: render as visual progress (Kanban columns, progress bars, status grid with colored chips) — NOT a `<ul><li>` bulleted list. A bullet list in HTML for a plan = template-wrap failure.

---

## Multiple-choice picks — chat vs HTML buttons

When asking the user to pick between options:

**Default — list in chat, the user types**:
- Choice labels are short (≤30 chars each)
- No visual context is needed to decide
- 2–4 options

  Example chat reply: "**A**: Keep / **B**: Trim 50% / **C**: Exit — reply A/B/C"

**HTML buttons only when**:
- Choice text is long (>30 chars per option, e.g. full order tickets / commands) AND would be tedious to type
- OR the choice requires visual context already in HTML (e.g., 4 overlaid payoff curves and the user picks which structure)
- OR there are many choices (8+) where scanning a grid in HTML beats reading a chat list

**When using HTML buttons**:
- Each button: `<button class="choice-btn" onclick="copyChoice('A')">A: full option text in the user's language</button>` where `copyChoice` writes the choice value to the clipboard and shows a 2s toast: "Copied 'A' — paste in chat"
- Chat output for that turn: **do NOT list the choices**. Just say something like: "see HTML — N options to click"
- The copied text is the SHORT label only ("A", "B", "C") — you infer the meaning from the HTML context.

---

## Edit-in-place vs append (hybrid)

Each user turn that produces a substantive HTML answer is either an **edit** of an existing entry or an **append** of a new entry.

**Edit existing entry when**:
- The user uses iteration keywords (in any language): "remove … ", "add … ", "change the strike", "adjust", "REVISE", "what if X", "the price is now …" (e.g. "ตัด…ออก", "ปรับ")
- The user references a specific prior entry ("in the hedge entry from earlier…")
- The answer is a refinement of the same artifact (same tickers, same scenario, adjusted parameters)

  → Use the `Edit` tool to update that entry's `<section id="entry-N">` block. Preserve excluded items as `<tr class="excluded">` rows with strikethrough + reason in the last column. Add `<span class="edit-marker">🔄 EDITED HH:MM — reason</span>` to the entry header. Update the TOC sidebar entry to include the EDITED badge.

**Append new entry when**:
- New topic / new domain / different question
- The user does not reference prior content
- It's the first substantive answer of the session

  → Add a new `<section id="entry-N+1">` block at the end of `<main>`, add a corresponding `<li>` to the TOC sidebar. Increment the entry counter.

**Chat link anchoring**: When editing an old entry (especially one buried mid-session), the chat link must include the anchor:
`[.claude-html/session_X.html#entry-3](.claude-html/session_X.html#entry-3) — edited entry 3 (removed AAPL)`

This lets the user click straight to the changed section instead of scrolling.

---

## HTML file structure (skeleton)

Set `<html lang="...">` to the user's language (e.g. `en`, `th`).

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>HTML session — YYYY-MM-DD HH:mm</title>
  <style>
    /* See "Styling" section below */
  </style>
</head>
<body>
  <header class="topbar">
    <h1>Session YYYY-MM-DD HH:mm</h1>
    <button id="refresh-btn" onclick="location.reload()">Refresh ↻</button>
  </header>

  <aside class="toc">
    <h2>Entries</h2>
    <a href="#entry-LATEST" class="latest-link">↓ Latest</a>
    <ol>
      <li><a href="#entry-1">[HH:mm] entry 1 title</a></li>
      <li><a href="#entry-2">[HH:mm] entry 2 title <span class="edit-marker">🔄</span></a></li>
      <!-- append more -->
    </ol>
  </aside>

  <main>
    <section id="entry-1" class="entry">
      <header>
        <h2>Entry 1 — title</h2>
        <time>HH:mm</time>
      </header>
      <!-- content: viz + tables + cards + buttons (all labels in the user's language) -->
    </section>
    <!-- append more entries -->
  </main>

  <div id="toast" class="toast"></div>

  <script>
    function copyChoice(value) {
      navigator.clipboard.writeText(value).then(() => {
        const t = document.getElementById('toast');
        t.textContent = "Copied '" + value + "' — paste in chat";
        t.classList.add('show');
        setTimeout(() => t.classList.remove('show'), 2000);
      });
    }
    // Update latest-link target to the most recent entry id on load
    (function() {
      const sections = document.querySelectorAll('section.entry');
      if (sections.length) {
        document.querySelector('.latest-link').href = '#' + sections[sections.length - 1].id;
      }
    })();
  </script>
</body>
</html>
```

When appending a new entry, use the `Edit` tool on the session file:
- Add a new `<li>` inside the TOC `<ol>` (before its closing tag)
- Add a new `<section>` inside `<main>` (before its closing tag)
- Do NOT rewrite the whole file. Append-only via targeted `Edit`.

---

## Styling

A clean default palette is below. Reuse the visual language across a session (look at recent files in `.claude-html/*.html` first if any exist), so the page stays consistent.

- **Layout**: `body { display: grid; grid-template-columns: 220px 1fr; }` on desktop; collapse the TOC to a top dropdown via `@media (max-width: 700px) { body { grid-template-columns: 1fr; } aside.toc { position: static; max-height: 200px; overflow-y: auto; } }`
- **TOC**: `aside.toc { position: sticky; top: 0; height: 100vh; overflow-y: auto; padding: 16px; border-right: 1px solid #e5e7eb; background: #f8f8f5; }`
- **Main**: `main { padding: 32px 24px; max-width: 980px; }`
- **Topbar**: sticky top with the Refresh button right-aligned
- **Refresh button**: `position: fixed; top: 12px; right: 12px; z-index: 10; padding: 6px 12px; background: #0b5394; color: white; border: none; border-radius: 4px; cursor: pointer;` — visible from anywhere
- **Background**: `#fafaf7` cream; foreground `#1a1a1a`
- **Font**: `-apple-system, BlinkMacSystemFont, "Segoe UI", "Sarabun", "Noto Sans Thai", sans-serif`; `font-size: 15px` (the Thai fonts are harmless if unused — swap in your locale's font as needed)
- **Headings**: H1/H2 blue accent `#0b5394`; H2 has a 1px bottom border
- **Entry header**: the `<header>` inside `<section.entry>` shows the entry title H2 + `<time>` right-aligned grey
- **Cards**: `.recommend` green (#166534, bg #f0fdf4), `.caveat` amber (#b45309, bg #fffbeb), `.danger` red (#b91c1c, bg #fef2f2), `.note` blue (#0b5394, bg #eff6ff) — 1px border + colored left border (4px) + 6px radius
- **Tables**: `border-collapse: collapse`; header bg `#eef2f7`; `tabular-nums` on numeric columns; `tfoot` bold grey-cream bg
- **`.excluded` rows**: red-tinted bg (`#fef2f2`) + `text-decoration: line-through`
- **Pills**: 10px radius, 2px 8px padding, 0.78em — for tiers, status, categories
- **Code**: `#f1f1ee` bg, monospace, 1px 5px padding, 3px radius
- **Toast**: `position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%); padding: 12px 20px; background: #1a1a1a; color: white; border-radius: 6px; opacity: 0; transition: opacity 0.3s;` + `.toast.show { opacity: 1; }`
- **Edit marker**: `.edit-marker { color: #b45309; font-size: 0.85em; margin-left: 8px; }`
- **Choice buttons**: `.choice-btn { display: block; width: 100%; padding: 12px 16px; margin: 6px 0; text-align: left; background: white; border: 1px solid #d1d5db; border-radius: 6px; cursor: pointer; font-size: 15px; } .choice-btn:hover { background: #eff6ff; border-color: #0b5394; }`

For charts beyond inline SVG, you can load Chart.js from a CDN inside the page `<head>`:
`<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>` and render into a `<canvas>`.

---

## Chat output format when mode is active

For every response:

1. **Substantive answer / plan** → append/edit the HTML entry (per routing + edit-policy rules), then chat = a 1-line delta (in the user's language) + a clickable link.
   - Without buttons: `[.claude-html/session_X.html](.claude-html/session_X.html#entry-N) — added entry: AAPL Q3 risk-reward, payoff diagram + 3-scenario table`
   - With buttons: `[.claude-html/session_X.html#entry-N](…) — see HTML, 4 hedge structures to click`
2. **Short clarification / tool status / errors / confirms** → answer in chat normally, do NOT touch HTML.
3. **Edit-in-place** of an older entry → the chat link includes the `#entry-N` anchor + "edited entry N (reason)".

Keep chat output terse — the user reads the detail in HTML. Do NOT duplicate the analysis in chat.

---

## Toggle-on behavior (first invocation in a session)

When `/html` (or an equivalent) fires and no session file exists yet:

1. Note the current timestamp; build the path `./.claude-html/session_<YYYY-MM-DD>_<HHmm>.html`
2. **Determine the output language**: if the trigger appended one (e.g. `/html Thai`) → Mode 2, that fixed language; otherwise → Mode 1, match the chat. For Mode 2, record a `<!-- html-output-language: ... -->` comment and set `<html lang>` accordingly.
3. Look at the most recent **substantive** assistant message in the conversation (skip greetings, tool status, clarifications).
4. If found: `Write` the full HTML skeleton with that message rendered as `entry-1` (apply the viz analysis + the active language). Chat reply: `[link] — html mode ON. brought the last answer in as entry 1`
5. If no prior substantive answer: `Write` the HTML skeleton with an empty `<main>` and empty TOC `<ol>`. Chat reply: `[link] — html mode ON. waiting for the first entry`

## Toggle-off behavior

When the user says off:

1. Chat reply: `html mode OFF. Session: [link] — N entries` (a brief one-liner; do not summarize each entry).
2. Do NOT delete the session file. The user can reopen / move it later.

---

## Anti-patterns (will lose points)

- ❌ Appending entries while the mode is off — wait for /html ON
- ❌ Writing entries in a different language than the user (see "Output language")
- ❌ Writing the substantive answer ALSO in chat (duplicates token cost)
- ❌ Listing short-code choices in chat AND building HTML buttons for them — pick one channel per turn
- ❌ HTML buttons for yes/no confirms
- ❌ HTML buttons for destructive action confirmation (Auto-Clarity Exception)
- ❌ A bulleted `<ul>` plan in HTML — render as visual progress instead
- ❌ Forcing a chart on prose-only content
- ❌ Rewriting the whole session file on each turn — always use targeted `Edit`
- ❌ Editing a buried entry without an `#entry-N` anchor in the chat link
- ❌ An auto-refresh meta-tag (the user picked a manual Refresh button — do not add `<meta http-equiv="refresh">`)

---

## Concrete example — one turn cycle

This example shows a user working in Thai; the entry labels follow the user's language. The same flow applies in any language.

User (mode already on): "เปรียบเทียบ 3 hedge structure สำหรับ QQQ ที่ราคา $480 กับ premium budget $3K"

Claude:
1. Pattern: financial payoff (option strikes × 3) → SVG payoff diagram mandatory
2. `Edit` the session file: append a new `<section id="entry-3">` containing:
   - H2 `เปรียบเทียบ 3 hedge structure — QQQ @ $480, premium ≤$3K`
   - An SVG payoff diagram overlaying 3 P/L curves vs underlying price + an unhedged baseline
   - 3 `.note` cards describing each structure with strikes / premium / breakeven (labels in the user's language: "ขา put", "ขา call", "ต้นทุน", "จุดคุ้มทุน")
   - A table with localized headers: "โครงสร้าง", "ขาดทุนสูงสุด", "กำไรสูงสุด", "จุดคุ้มทุน", "ต้นทุน"
   - A `.recommend` card titled `ข้อแนะนำ`
3. `Edit` the session file: append `<li><a href="#entry-3">[HH:mm] QQQ hedge — 3 structures</a></li>` to the TOC
4. Chat reply: `[.claude-html/session_2026-05-25_1430.html#entry-3](…) — added entry: QQQ hedge 3 structures, payoff diagram + 3-card comparison`

User: "use structure B but drop the 470 put leg"

Claude:
1. Iteration keyword "drop … " + reference "structure B" → edit-in-place on entry-3
2. `Edit` the session file: in `<section id="entry-3">`, mark the 470-put row `class="excluded"` + add an EDITED badge to the entry header
3. Recompute the payoff curve, update the SVG path for structure B
4. `Edit` the TOC `<li>` for entry-3 to include `<span class="edit-marker">🔄</span>`
5. Chat reply: `[…#entry-3](…) — edited entry 3 (dropped the 470 put, recomputed structure B payoff)`
