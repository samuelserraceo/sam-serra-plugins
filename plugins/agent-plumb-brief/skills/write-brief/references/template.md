# The brief format Sam wants (v0.4)

Use this exact structure. Match Sam's existing style: prose for free-form sections (§2 context paragraph, §6, §10a), bullets for list sections (§4, §5, §8, §11), code block for §4a, definition list for §10b.

The first line is the title — replace `<feature name>` with the actual name.

```
# <feature name> — brief

## 1. One-line summary
<one plain-English sentence describing what this thing IS — CLEAN, no buzzwords from the pitch>

## 2. Why I'm building it

> <Sam's verbatim pitch here, in a quote block — copy-pasted, no rephrasing>

<1-3 short paragraphs of context, in Sam's own words>

## 3. Who'll use it
<a specific person or team — name, role, or count>

## 4. What 'done' looks like — the clickthrough QA list
- M1: <trigger> → <result>
- M2: <trigger> → <result>
- M3: <trigger> → <result>

## 4a. Output example
<INCLUDE this section ONLY if the deliverable is a piece of content (email, notification, generated document, Notion page, API response, generated text). OMIT it entirely otherwise — don't write "(n/a)".>

```
<a literal example showing the actual output, with every field name, ordering, empty-state copy, character/size limits, zero-state variant>
```

<Below the code block, list the rules in plain English:>
- <field truncation rules>
- <section ordering rules>
- <character or size caps (e.g. "Total body must stay under 12KB to avoid Gmail clipping")>
- <empty-state and zero-state variants>

## 5. What this should NOT do (yet)
- <thing this brief is not trying to do>
- <thing this brief is not trying to do>

## 6. Look + feel
<inspiration, similar tools, a Figma URL, mobile vs desktop>

## 7. Stack constraints (already running with consumers/data)
<what's decided AND already running with real consumers or real data — existing tables in production with downstream readers, existing auth in use, source-of-truth APIs other systems depend on. Preferences ("I'd choose Postgres") belong in §6, NOT here. The test: if Sam can swap this out without breaking anything else, it's not a §7 entry.>

## 8. What's in THIS brief vs later
- This brief ships: F0X = <one sentence>
- Later (don't build yet):
  - F0Y: <description>
  - F0Z: <description>

## 9. Hard constraints from reality
<deadlines, cost caps, things that must not break>

<Re-run / retry rules — REQUIRED, never silent:>
- Dedupe key: <key shape, e.g. `<user_id>:<iso_week>`>
- If it runs twice within the key window: <e.g. "second call returns 200 with `{noop: true, reason: "already_done"}`">
- If a third-party returns a 5xx: <state which writes happen, which don't, whether retry is safe>
- After the boundary (e.g. new ISO week): <e.g. "new key → runs again">

## 10a. Anything weird / specific
<domain quirks, regulatory rules, integration gotchas, tie-breaking rules>

## 10b. Definitions
<every fuzzy word used in §1, §2, §4, §4a — defined concretely. Use SQL where the data is in a database.>

- **<term>** = <definition>
- **<term>** = <definition>

## 11. Mockup
<REQUIRED if the deliverable is a piece of content OR a visual UI. Drop one of:>

- A Figma URL
- A local file path (e.g. `~/Mockups/feature-name.png`)
- A photo of a hand-drawn sketch
- A Notion page screenshot

<For purely behavioural deliverables, write `(none) — purely behavioural, no visual artefact`.>
```

## Formatting rules

- `##` (h2) for every section header. No h3 inside sections.
- Section titles match exactly — copy them character-for-character including em-dashes.
- §2 starts with a quote-block line (the `>` symbol), then a blank line, then prose.
- §4 entries: `M{n}: <trigger> → <result>`. The arrow is `→` (Unicode U+2192). No schedule-only Ms.
- §4a: triple-backtick code block. Markup is freeform — pseudo-template, JSON-ish, ASCII layout, Markdown — whichever fits the artefact. Must include every field, ordering, empty-state, character limits.
- §8: `F0X = <description>`. Use the actual feature number Sam tells you (F01, F02). Ask if not specified.
- §9: include the re-run block even if Sam says "fire and forget, no idempotency needed" — write that down explicitly. Never silent.
- §10b: bold the term, `=`, then definition. SQL predicates where the underlying data is in a database.
- §11: a real mockup reference for content / visual deliverables. `(none)` allowed ONLY for purely behavioural.
- Sections that can be empty (with explicit Sam confirmation): §5, §6, §10a only.
- Sections that can NEVER be empty: §1, §2, §3, §4, §7, §8, §9, §10b, §11.
- §4a is omitted entirely (not empty, not "(n/a)") for behavioural deliverables.

## Reference: §4a code-block shapes

The code block is freeform — pick whichever shape best matches the artefact:

**For an email body** (Handlebars-style template):
```
From: AM Health <health@internal.example.com>
To: {{am.email}}
Subject: Your accounts this week — {{at_risk + slipping}} need attention

🔴 SLIPPING ({{slipping_count}})
  • {{account_name}} — €{{arr}} — {{primary_flag}} → {{crm_url}}
```

**For an API response** (JSON shape):
```
GET /api/search?q=<query>
200 OK
{
  "results": [{ "id": "string", "title": "string", "score": number }],
  "total": number,
  "next_cursor": "string | null"
}
```

**For a screen** (ASCII layout):
```
+------------------------------------------+
| [Logo]              Search ▢   Profile ▢ |
+------------------------------------------+
| Dashboard                                |
|   ┌─────────┐ ┌─────────┐ ┌──────────┐  |
|   │ KPI: X  │ │ KPI: Y  │ │ KPI: Z   │  |
|   └─────────┘ └─────────┘ └──────────┘  |
+------------------------------------------+
```

**For a Notion page or generated document** (Markdown shape):
```markdown
# {{date}} — Morning Briefing

🌤 London — {{weather.high}}°/{{weather.low}}° {{weather.condition}}

## ✨ TL;DR
{{tldr_summary_2_sentences}}

## 📅 Today's calendar
- {{HH:mm}}–{{HH:mm}} — {{title}} — {{location}} — {{attendee_count}} attendees
- (empty: "Nothing scheduled today.")

## ✅ Tasks due today (Asana)
- {{task_title}} — {{workspace}} → {{asana_url}}
- (empty: "Nothing due today.")
```
