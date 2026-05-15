# agent-plumb-brief

A Cowork plugin that turns a rough feature idea into a strong brief in Sam's SDD framework format. Plain English, no jargon. Grilling style adapted from Matt Pocock's [grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) skill.

## How it works

1. **Pitch** — you give it 1-3 sentences on what you want to build.
2. **Grill** — it asks you questions, one at a time, each with a recommended answer. Refuses fuzziness. Watches for sneaky words like *weekly, healthy, active, ready* and forces them to become concrete rules.
3. **Quality check** — four checks run visibly in chat. If any fail, it loops back to grilling.
4. **Write the brief** — saves a markdown file you can paste into your pipeline.

## Trigger phrases

The skill activates when you type:

- `/write-brief`
- "write a brief"
- "draft a PRD"
- "new SDD brief"
- "spec a feature"

Or just paste a feature idea and ask for a brief.

## What's in the brief (the 11 sections)

1. One-line summary
2. Why I'm building it (starts with your verbatim pitch in a quote block)
3. Who'll use it
4. What 'done' looks like — the clickthrough QA list (M1, M2…)
4a. **Output example** — required if the deliverable is a piece of content (email, doc, API response, Notion page)
5. What this should NOT do (yet)
6. Look + feel
7. Stack constraints (already wired up)
8. What's in THIS brief vs later (exactly one F0X)
9. Hard constraints from reality (including what happens on re-run)
10a. Anything weird / specific
10b. **Definitions** — every fuzzy word, defined concretely
11. **Mockup** — required for any content or visual deliverable

## What it refuses

- "Users" as a persona
- "Better UX" / "modern" / "next-gen" as pain points or summaries
- Acceptance criteria you can't verify by clicking
- Empty §5 (non-goals) without explicit confirmation
- More than one feature in §8 (the F0X must be a single thing)
- Preferences disguised as stack constraints in §7
- Schedule-only Ms (e.g. "Monday 9am the email sends" — not a fireable trigger)
- §9 silent on what happens if it runs twice
- Fuzzy words used in earlier sections without a §10b definition
- §11 empty for a content or visual deliverable

## Output

Writes `outputs/<feature-name>-brief.md` and replies with a clickable link plus a 2-line summary (what's shipping in F0X + how many M's in §4).

## What's new in v0.8

- **§11 URL host whitelist + image-extension allowlist.** A URL that resolves isn't necessarily a mockup. Check C now requires the URL's host to be on the design-tool whitelist (Figma, Dribbble, Framer, Miro, Excalidraw, Whimsical, Sketch, Marvel, InVision, Lunacy, Notion public, Cloudinary, Imgur, S3, etc.), OR the path to end in a known image extension (`.png/.jpg/.jpeg/.svg/.webp/.gif/.heic/.pdf/.fig`). If neither matches, the skill asks Sam to confirm explicitly that the URL is the actual mockup. Closes the v0.7 redteam gap where `https://www.google.com` would pass.

## What's new in v0.7

- **§11 URL/path validation in Check C.** Every §11 reference is now verified before render: web URLs get an HTTP HEAD (must return 200/301/302), local paths get a `Read` existence check, obvious placeholders (`example.com`, `TBD`, `placeholder`, `your-figma-url-here`) are auto-rejected, malformed URLs (missing protocol) are caught. If nothing resolves, Sam gets the inline-paste-as-attachment fallback. Closes the v0.6 redteam Session 4 BROKE.
- **§6 app-name-without-screen anti-pattern.** "Linear-like", "Notion-style", "Excel-like" alone are now in the Bad list. Each app has many visually-distinct screens — Sam must name the specific surface or drop a screenshot. The §6 "good" examples are rewritten to require app + screen pair. Closes the v0.6 redteam Session 5a BROKE.
- **Periodic consistency snapshot is now mandatory.** At Q5/Q10/Q15/Q20, the skill MUST emit `Consistency check: clean (Q{n})` or `Consistency check: conflict — ...`. The skill is told explicitly not to advance past those checkpoints without the line. Phase 3's Check D remains the structural backstop.
- **Longer-cycle detection in Check A.** Walk-the-chain now maintains a "seen" set and detects cycles of any length, not just direct A→B→A. Catches three-hop loops where intermediate terms look concrete but ultimately reference the original.
- **§2 quote-block staleness check (Check D sub-rule).** If §2's verbatim pitch describes a different deliverable type, scope, or primary verb than §1/§8 now describe, render is blocked until Sam either re-pitches or reverts. No more silent quote-block drift.

## What's new in v0.6

- **Identity nouns now in Check A's watchlist.** `request_id`, `correlation_id`, `dedupe_key`, `idempotency_key`, `tenant_id`, `event_id`, `user_id`, `job_id`, `session_id`, `trace_id` — each must define its generation rule (client vs server emitted, UUID vs hash vs counter, uniqueness scope). Closes the v0.5 redteam Session B softening.
- **Watchlist is now illustrative, not exhaustive.** Check A's rule rewritten: "ANY noun, adjective, or verb that gates a routing, scoping, dedupe, eligibility, or identity decision counts." The watchlist is a starter pack, not the law.
- **Structured files added to Case 1.** JSON, CSV, Parquet, generated config files, log files written to disk or object store are content even when no human reads them directly. §4a required. Closes the v0.5 Session F borderline.
- **§7 wording tightened.** From "already wired up" to "already running with consumers/data". Includes the swap test: "if Sam can swap this out without breaking anything else, it's not a §7 entry." Removes the "lock it in" loophole.
- **Periodic consistency snapshot every 5 questions.** Light, observable. Prints `Consistency check: clean (Q5)` or quotes a contradiction. Catches drift in long sessions where real-time checking gets cognitively heavy.
- **New anti-pattern entry** for undefined identity nouns in §9 — gives the skill a verbatim quote to use under pressure.

## What's new in v0.5

- **Check D — Cross-section consistency.** §3 / §7 / §8 are diffed against each other and against the conversation log. Contradictions block render. (Fixes the v0.4 redteam Session D break.)
- **Real-time contradiction detection in Phase 2.** When a new answer conflicts with an earlier confirmed claim, grilling stops, both answers get quoted back, and Sam picks one before continuing.
- **§1 must be clean.** Buzzwords from Sam's pitch ("AI-powered", "next-gen", "synergy") are NOT allowed in §1. The §2 quote-block keeps the pitch verbatim; §1 is the agent's clean rewrite.
- **Verbatim-pitch exemption from Check A.** Words appearing only in §2's quote-block are exempt from §10b — they're quoted, not authored. Same word elsewhere (§1, §4, §4a, §10a) still needs a definition.
- **Walk-the-chain rule for §10b.** A definition that resolves to another fuzzy word is undefined until the chain terminates at SQL / number / enum / deterministic rule. Catches subtle circularity.
- **Phase 2 escape hatch.** Three rounds on the same stuck drill = suspend. "Your pitch isn't ready yet — come back when [X] is concrete."
- **New anti-pattern entries** for the most common cave attempts: skip Check B, F0X = everything, "no idempotency needed", preferences smuggled as constraints, F0X creep mid-session, tenancy contradictions.

## What's new in v0.4

- **§11 mockup is now required** for any content or visual deliverable. §4a (the literal code-block sketch) catches missing fields; §11 (a real visual mockup) catches misaligned intuition. Both are needed.
- **All wording rewritten** in plain English — no jargon, concrete examples for non-technical users.
- **AskUserQuestion 4-option ceiling** is now called out so the agent doesn't waste a question by hitting it.
- **Three quality checks** keep their loop-back behaviour but use plainer names: Check A (fuzzy words), Check B (verifiable), Check C (output example + mockup).

## Customising

- `skills/write-brief/SKILL.md` — the main instructions
- `skills/write-brief/references/template.md` — exact output format
- `skills/write-brief/references/lint-gate.md` — the three quality checks
- `skills/write-brief/references/examples.md` — good vs bad answers per section
- `skills/write-brief/references/grill-me.md` — the grilling rules
