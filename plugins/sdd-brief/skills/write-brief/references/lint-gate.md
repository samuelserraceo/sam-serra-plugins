# The four quality checks (run before writing the brief)

After grilling Sam, before writing the brief, run all four checks and print them in chat. If any fail, name what's missing, go back to grilling on that gap, then run all four again from the top. Loop until all four pass. Show every pass.

## Format to paste in chat

```
=== Quality Check — Pass 1 ===
[ ] Check A — Fuzzy words all defined (chain-walked)
[ ] Check B — Every "how to verify" is fireable right now
[ ] Check C — Output example + mockup attached
[ ] Check D — No contradictions between sections
```

Replace `[ ]` with `[✓]` for pass and `[✗]` for fail. Under each fail, write `Missing: ...` pointing at exactly what's not yet there. Then say which question(s) you're going back to in Phase 2.

---

## Check A — Fuzzy words all defined (chain-walked)

**Goal:** every fuzzy word in §1, §4, §4a, §10a has a concrete definition in §10b. Definitions must terminate at SQL / number / enum / deterministic rule — walk the chain.

**Where Check A looks:**

- §1 (one-line summary)
- §4 (M items)
- §4a (output sketch)
- §10a (quirks)

**Where Check A does NOT look:**

- §2 verbatim pitch quote-block. **Exemption:** words inside the `>` quote block of §2 are quoted, not authored — Sam said them in the pitch and they're preserved verbatim. They don't need §10b definitions UNLESS the same word also appears in §1, §4, §4a, or §10a.
- The prose paragraph below §2's quote block (this IS authored — it counts).

**Why this matters:** Sam's pitches often contain marketing language ("AI-powered", "next-gen", "synergy") that has no operational role. Forcing a §10b definition for these would either fake a definition ("no operational meaning") or grind the brief to a halt. The clean fix is to keep them in the §2 quote (verbatim per spec) and demand a clean §1 (your authored one-liner with no buzzwords).

**How to run Check A:**

1. Scan §1, §4, §4a, §9, §10a. Find every word that hides a rule. The watchlist below is illustrative, not exhaustive — ANY noun, adjective, or verb that gates a routing, scoping, dedupe, eligibility, or identity decision counts as fuzzy and must be defined in §10b.

   **Fuzzy adjectives/verbs (always fail unless defined):** *weekly, daily, hourly, recent, active, ready, valid, healthy, at-risk, slipping, scannable, fast, slow, modern, stable, complete, finished, eligible, qualified, top, best, similar*.

   **Identity / routing / dedupe nouns (always fail unless their generation rule is defined):** `request_id`, `correlation_id`, `dedupe_key`, `idempotency_key`, `tenant_id`, `event_id`, `user_id`, `job_id`, `session_id`, `trace_id`. For each, §10b must answer: who emits it (client or server), what algorithm (UUID v4, hash of what fields, monotonic counter, etc.), and what guarantees it has (uniqueness scope, collision behaviour).

   **Domain-specific routing words also count** (e.g. "primary flag", "test inbox", "active workspace", "qualified lead", "stale ticket"). Same rule: each needs a §10b entry that terminates at SQL / number / enum / generation rule.
2. For each word, find its §10b entry.
3. **Walk the chain.** If the §10b definition references another fuzzy word, jump to that word's §10b entry. Keep walking. The chain must terminate at one of:
   - A SQL predicate (e.g. `health_score >= 70`)
   - A numeric threshold (e.g. "≤14 days")
   - A list of values (e.g. "one of: ACCEPTED, TENTATIVE")
   - A deterministic rule (e.g. "Monday 09:00 in `am.timezone`, IANA")
4. **Detect cycles of any length.** Maintain a "seen" set as you walk. If you ever revisit a term you've already seen, the chain loops and the term is undefined — even if the loop has 2 hops (A → B → A), 3 hops (A → B → C → A), or longer. Don't let a "concrete-looking" intermediate term hide a longer cycle.
5. If the chain loops or never reaches a concrete terminator, the term is undefined.

**Example pass:**

```
"active workspace" appears in §4 M1.
§10b: active workspace = workspace_id where ≥3 distinct users had ≥1 event in last 7d.
Chain terminates at threshold (3, 7d). ✓
```

**Example fail (surface circular):**

```
Check A — FAIL
"active" defined as "recent activity"
"recent" defined as "active behaviour"
Chain loops: active → recent → active. Never terminates.
Going back to grill: ask Sam for a number — how many distinct users, over what window?
```

**Example fail (subtle circular with thresholds that look concrete):**

```
Check A — FAIL
"active workspace" defined as "workspace touched by recent_user in 7d"
"recent_user" defined as "user whose last activity was in an active workspace"
Chain loops through "active workspace" → "recent_user" → "active workspace". Both definitions have a number (7d, "last activity") but neither is independently concrete.
Going back to grill: define "recent_user" without referencing "active workspace".
```

**Example fail (three-hop loop with intermediate term that looks defined):**

```
Check A — FAIL
"request_id" defined as "what the upstream system emits"
"upstream system" defined as "the service that owns request_id"
"service that owns request_id" reduces back to the original term.
Chain: request_id → upstream system → service-that-owns-request_id → request_id. Three hops, all loop.
Going back to grill: define request_id with a generation rule (UUIDv4, hash of fields, monotonic counter) that doesn't reference any of these terms.
```

---

## Check B — Every "how to verify" is fireable right now

**Goal:** every M in §4 is a real trigger Sam can fire on a preview build, with a result he can see in under 60 seconds.

**How to run it:**

For each M, three sub-checks:

1. **Is the trigger fireable RIGHT NOW?** Acceptable triggers: HTTP request, button click, CLI command, file write, message send, DB row insert. NOT acceptable: "every Monday at 9am", "after 30 days of inactivity", "when the cron fires", "when the user has been logged in for >24h". Schedules are NOT triggers — they're convenient automations on top of triggers.
2. **Is the result observable in under 60 seconds?** Acceptable: inbox receives email, DB row exists, response body matches shape, page renders element X, file appears at path. NOT acceptable: "the system feels fast", "users are happy", "performance is good", "no errors in logs over a week".
3. **Does the trigger directly cause the result?** Cron fires → email sends is FINE only if the cron's trigger is exposed as a manual endpoint that Sam can also call by hand.

Pass: all Ms have valid trigger + observable result + linkage. Fail: name the failing M and which sub-check broke.

**Example fail:**

```
Check B — FAIL
M1 says "Monday 09:00 the AM gets an email." That's a schedule, not a fireable trigger — there's no way to verify on a preview deploy without waiting until Monday.
Going back to grill: ask Sam to expose POST /api/health-email/run?am_id=<id> so cron and manual share one endpoint.
```

---

## Check C — Output example + mockup attached

**Goal:** the brief has the right shape of evidence for what's being built.

**How to run it:**

Three cases:

**Case 1 — A piece of content** (email body, notification card, generated document, Notion page, API response, generated text block, OR structured files written to disk or object store — JSON, CSV, Parquet, generated config files, log files, anything with a schema even if no human reads it directly):
- §4a MUST be present with a code block containing the literal layout. For structured files: include the schema, field types, partition keys / file naming convention, and any embedded metadata.
- §11 MUST reference a visual mockup (Figma URL, screenshot file, photo of a sketch). For structured files where no UI exists: a hand-drawn diagram of the schema or a screenshot of an example file rendered in a tool (DuckDB, jq output, spreadsheet) counts.

**Case 2 — A visual UI** (a screen, a dashboard, a multi-page flow):
- §4a may have an ASCII layout (optional)
- §11 MUST reference a visual mockup

**Case 3 — Purely behavioural** (a workflow, a permission system, a sync process, an internal job):
- §4a is OMITTED
- §11 may be `(none) — purely behavioural, no visual artefact`

Pass: matches one of these three patterns exactly. Fail: name what's missing.

**Watch out for misclassification.** If Sam says "it's just a backend job, no §4a needed" but the job produces an email or writes to a Notion page — that job IS Case 1 because it produces content. The classifier follows the artefact, not Sam's framing.

### §11 mockup VALIDATION (new in v0.7)

Just having §11 filled in isn't enough. The reference has to actually resolve to something real. Validate it before passing Check C:

1. **Reject obvious placeholders.** If §11 contains any of these literal strings, fail Check C: `example.com`, `placeholder`, `TBD`, `TODO`, `localhost`, `your-figma-url-here`. Quote the placeholder back and ask for the real one.

2. **Web URLs:** if §11 looks like a URL (starts with `http://` or `https://`), perform an HTTP HEAD via `mcp__workspace__web_fetch`. Acceptable response codes: 200, 301, 302. Anything else (404, 403, ENOTFOUND, timeout) fails Check C.

   **Host content sanity check (new in v0.8).** A URL that resolves isn't necessarily a mockup. Apply the host whitelist below — if the URL's host is on it, OR the path ends in a known image/design extension, accept. Otherwise, ask Sam to confirm it's actually a mockup before passing.

   **Known design-tool hosts (whitelist):** `figma.com`, `framer.com`, `dribbble.com`, `behance.net`, `miro.com`, `excalidraw.com`, `whimsical.com`, `sketch.com`, `marvelapp.com`, `invisionapp.com`, `lunacy.app`, `notion.so` (when the path looks like a public page), `cloudinary.com`, `imgur.com`, `imagekit.io`, `s3.amazonaws.com`, `googleusercontent.com`.

   **Known image extensions in the path:** `.png`, `.jpg`, `.jpeg`, `.svg`, `.webp`, `.gif`, `.heic`, `.pdf`, `.fig`.

   **For unknown hosts (e.g. a generic site, a corporate landing page, a search result):** fail Check C with: "That URL resolves but I can't tell if it's actually a mockup — the host (`<host>`) isn't a known design tool and the path doesn't end in an image extension. Confirm: is this URL the actual mockup file, or did you paste a placeholder? If it's the real one, say so explicitly and I'll accept it. Otherwise drop a Figma/Dribbble URL or paste the screenshot inline."

3. **URLs without protocol** (e.g. `figma.com/file/abc123` with no `https://`): fail Check C and ask Sam to add the protocol. Don't auto-fix; the URL might be a typo.

4. **Local paths** (anything starting with `/`, `~`, `./`, or matching the user's workspace): use the `Read` tool to confirm the file exists. If `Read` errors with "file not found", fail Check C and ask Sam to either upload the file or fix the path.

5. **Inline-paste fallback.** If Sam doesn't have a hosted mockup and the local path can't be reached, ask him to paste the screenshot directly into the Cowork chat as an attachment. The attachment's path goes into §11.

**Example fails:**

```
Check C — FAIL
§11 = "https://example.com/mockup.png" — placeholder URL.
Going back to ask Sam for the real mockup reference.
```

```
Check C — FAIL
§11 = "~/Mockups/customer-success-email.png" — Read tool reports file does not exist on user's machine.
Going back to ask Sam to fix the path or paste the image inline.
```

```
Check C — FAIL
§11 = "figma.com/file/abc123/customer-email" — missing protocol (https://).
Going back to ask Sam to confirm the full URL.
```

```
Check C — FAIL (v0.8 host check)
§11 = "https://www.google.com" — URL resolves (HEAD 200) but host is not a known design-tool, and the path has no image extension.
Going back to ask Sam to confirm this is actually the mockup, or replace with a Figma/Dribbble URL or an inline screenshot.
```

**Example fail:**

```
Check C — FAIL
Building a Notion morning-briefing page — that's Case 1 (a piece of content).
§4a is present and looks complete.
But §11 says "(none) — §4a contains the literal output sketch" — that's not a mockup.
Going back to ask Sam: drop a Figma URL, a screenshot, or a quick paper sketch photo.
```

---

## Check D — No contradictions between sections (NEW in v0.5)

**Goal:** §3, §7, §8 don't contradict each other or anything else Sam said in the conversation.

**How to run it:**

1. Read §3 (who uses it). Note: tenancy (single-tenant vs multi-tenant), persona count, persona type.
2. Read §7 (stack constraints). Note: auth model, deployment scope, network boundary.
3. Read §8 (F0X scope). Note: which features are in F0X, which are in F0Y/Z.
4. Cross-check pairs:

   - **§3 vs §7 — tenancy + auth alignment:**  
     Single-tenant + VPN-only + internal users → consistent.  
     Multi-tenant + per-tenant auth → consistent.  
     Single-tenant claim + external users with auth → CONTRADICTION.  
     VPN-only + customer/external users → CONTRADICTION.

   - **§3 vs §8 — persona scope alignment:**  
     "Just me" + F0X has multi-user features (sharing, permissions, collaboration) → CONTRADICTION.  
     "~25 employees" + F0X has tenant management → CONTRADICTION.

   - **§8 vs conversation log — scope creep:**  
     §8 says F0X = X. Search the conversation transcript for any later "obviously it'll also do Y" / "and Y is part of F0X too." If found, either §8 is stale (force update) or Y was a slip (force confirmation it's NOT in F0X).

5. **§2 quote-block staleness check.** Compare §2's verbatim pitch against the current §1 (clean summary) and §8 (F0X scope). If the pitch describes a different deliverable type, scope, or primary verb than what §1/§8 now say, the quote-block is STALE — Sam revised the spec without re-pitching.

   **Check:** does §2's quote describe the same thing the rest of the brief describes? If §2 says "a daily email" but §8 says "F0X = a Notion page," that's drift. If §2 says "for my engineering team" but §3/§8 now describe customer-facing functionality, that's drift.

   **On drift, fail Check D and ask Sam:** "Your pitch in §2 says [exact quote]. The rest of the brief now describes [paraphrased current state]. Two paths: (a) re-pitch — give me a new 1-3 sentence pitch matching what we built, OR (b) revert — back to the original pitch, drop the additions. I'm not silently rewriting your verbatim quote."

6. If any contradiction is found, name BOTH sides and quote them. Force Sam to pick.

Pass: no contradictions, no quote-block drift. Fail: list each contradiction or drift with both quotes.

**Example fail:**

```
Check D — FAIL
Contradiction between §3 and §7.
§3 says: "~30 employees of my company"
§7 says: "no app-level auth; access via Tailscale VPN only"
But also in §8: "F0Y = white-label deployment for customer tenants"

Customer tenants aren't on Sam's VPN, and "no app-level auth" can't support multi-tenant per-customer scoping.
Going back to grill: pick one model. Either F0X is single-tenant internal-only (and white-label is permanently out of scope, not just F0Y), OR F0X is multi-tenant from day one (rewrite §3 and §7 to reflect external users with proper auth).
```

**Why a separate Check D when Phase 2 already has the consistency rule:**

Phase 2's real-time check catches contradictions as Sam introduces them. Check D is the safety net for cases where the contradiction was subtle, where the skill missed it during grilling, or where two answers turn out to be incompatible only after a third answer is added. Belt and braces.

---

## Looping rules

After grilling on a failing check, re-run ALL FOUR checks from the top — not just the one that failed. Fixing one can break another. Show every pass in chat. Stop only when one pass passes all four.

If you're past three passes, stop and check whether Sam's pitch has shifted under you. Sometimes the goalposts move while you're chasing them.

---

## Why a mockup is required for content / visual deliverables

A literal code-block sketch (§4a) and a visual mockup (§11) catch different bugs.

§4a catches MISSING FIELDS — every field name, every empty-state, every char limit. It's a structural contract.

§11 catches MISALIGNED INTUITION — when the layout looks fine in code but feels wrong as a picture. The "is this actually how I want it to look?" gut check.

Sam's experience: text-only specs ship features that "look fine on paper" and feel terrible in real life. A 30-second hand-drawn sketch on paper, photographed with his phone, catches that.

If Sam doesn't have a mockup yet, ask him to make one before you render. Even a paper sketch is fine. Don't render without it.
