# Good vs bad answers — section by section (v0.5)

Use this when pushing back on weak answers. Quote one of the good examples to show Sam the shape you're looking for.

This file is the place to add new anti-patterns when you spot a recurring cave attempt during testing. Anti-patterns work better than abstract rules because the skill can quote them verbatim under pressure.

---

## §1 — One-line summary

**v0.5 rule:** §1 must be CLEAN — no buzzwords, even if they appear in Sam's verbatim pitch in §2. The §2 quote-block preserves the pitch exactly; §1 is your authored one-liner.

**Bad:**
- "An AI-powered productivity platform for modern teams." → buzzwords, no verb, no concrete behaviour
- "A tool to make my workflow better." → "better" how? what tool?
- "Next-gen DB explorer." → "next-gen" means nothing

**Good:**
- "PipeLogic V2 is a database canvas — log into /map, see every table as a card, click any card to inspect or edit its columns and rows."
- "Every Monday at 09:00 in their local timezone, each account manager gets a single email triaging their book of business into healthy / at-risk / slipping with one-click CRM links."

**How to push back:** "I can't tell what this thing IS yet. Give me one sentence with a verb the user does. What do they click? What do they see?"

**Anti-pattern: copying the pitch into §1.** If Sam's pitch is "An AI-powered, next-gen productivity platform...", DO NOT use that as §1. §2's quote block gets the verbatim pitch; §1 must be your clean rewrite. Example: pitch says "AI-powered productivity platform" → §1 says "A kanban task list with an on-demand AI prioritise button." Drop the buzzwords entirely.

---

## §2 — Why I'm building it

**v0.4 rule:** must start with Sam's pitch in a quote block (the `>` syntax), copy-pasted exactly. Then a paragraph of context in his words.

**Bad:**
- Paraphrased pitch ("The user wants a system that...") instead of Sam's actual words
- "Current solutions don't work well." → which ones? what doesn't work?
- "It would save time." → time spent doing what?

**Good:**
- A quote block with the verbatim pitch, then specific current behaviour: "Today AMs open HubSpot cold on Monday morning to figure out who needs attention. They're in standup at 09:30, on calls from 10:00…"

**How to push back:** "Walk me through what you actually do today, step by step. The 'before' picture has to be specific or the 'after' won't be either."

---

## §3 — Who'll use it

**Bad:**
- "Users." → vague
- "People who manage data." → too broad
- "Anyone in the company." → no constraint

**Good:**
- "Just me (Sam). Maybe 5 indie founders next quarter if it works."
- "~25 account managers at the company. Each owns 30-80 active accounts. Distributed across 6 timezones."

**How to push back:** "'Users' tells me nothing — every brief says that. Who specifically? Just you? A team? Give me a name, a count, or a role."

---

## §4 — What 'done' looks like

**v0.4 rule:** every M = `M{n}: <trigger> → <result>`. No schedule-only Ms.

**Bad:**
- "M1: Every Monday at 09:00 the email sends." → that's a schedule, not a trigger I can fire today
- "The system is fast and reliable." → can't verify
- "M1: Users can log in and use the app." → too vague
- "M2: Performance is good." → says nothing

**Good:**
- "M1: hit `POST /api/health-email/run?am_id=AM_001&dry_run=true` → within 30 seconds the test inbox receives an email with three sections, AND no row appears in `health_email_sends`."
- "M3: hit `POST /api/health-email/run?am_id=AM_001` twice within 60 seconds → second call returns 200 with `{noop: true}`; only one inbox copy."

**How to push back:** "If I deployed this to a preview URL right now, what would I click or curl to verify? Schedules can't be Ms — expose a manual endpoint that the cron also calls."

**Anti-pattern: "skip Check B because schedule is cleaner."** Sam might argue the manual endpoint is added complexity. It's NOT — it's the same handler the cron calls, exposed for verification. The cron's logic becomes "on schedule, call the endpoint." Without it, you can't verify the brief works until production. Quote: "Same handler, two callers — cost is ~5 lines of code, gain is the ability to verify before [Friday/Monday/whenever]."

**Anti-pattern: "make a Check B exception for this brief."** Refuse. Exceptions are how briefs ship that silently break the next pipeline step — that's the entire reason this skill exists. Two paths: (1) accept the manual endpoint as part of F0X, or (2) declare F0X as "schedule-only, no verification path until production" — but then the brief isn't ready and we stop here.

---

## §4a — Output example (NEW since v0.3)

**Required when** the deliverable is a piece of content (email, notification, generated doc, Notion page, API response, generated text).

**Bad:**
- Section is missing when the deliverable is an email
- "Looks like a typical digest." → not a literal layout
- A code block with no field names, no ordering, no empty-state copy
- No size cap or character limit mentioned

**Good:**
- A code block showing the literal subject line, every field placeholder, the section order, the "(none)" empty-state copy, the "≤12KB Gmail clipping" cap, the zero-state subject variant. See `references/template.md` for example shapes.

**How to push back:** "I can't write this brief without a real example. Show me one row of the email exactly as it'll appear, the order of sections, and what shows up when there are zero items in a section."

---

## §5 — What this should NOT do (yet)

**Bad:** silently empty, no confirmation
**Good:** explicit list ("No Slack version", "No AM-configurable thresholds", "No analytics") OR empty-with-explicit-confirmation

**How to push back:** "Empty §5 means the team can build any reasonable extension within scope. Confirm that's actually what you mean — otherwise list the things you're explicitly not building yet."

---

## §6 — Look + feel

**Bad:**
- "Modern and clean."
- "Linear-like." → Linear has 6+ visually distinct screens (issue list, project view, roadmap, cycles, settings, triage). "Linear-like" hides which one.
- "Notion-style." → same problem. Notion's database view, page view, and gallery view are visually unrelated.
- "Modern dashboard vibes." → reject.

**Good:**
- "Like Stripe's weekly summary email — plain prose, light emoji as section markers. Mobile-first because AMs read this on their phone before standup."
- "Linear's issue list view (https://linear.app/screenshot/issues.png) — dense rows, status pills on the left, filter bar across the top."
- "Notion's database table view (screenshot at ~/Refs/notion-table.png) — sortable columns, inline editing on hover."

**How to push back:** "'Modern' and 'clean' don't help anyone. App-name-without-screen ('Linear-like', 'Notion-style') is the same shape of vague — Linear's issue list and Linear's roadmap look nothing alike. Drop a screenshot file, name the specific surface ('Linear's issue list view'), or point at a competitor's exact page."

**Anti-pattern: app name without screen.** "Linear-like", "Notion-style", "Excel-like", "Spotify-style" alone don't identify a visual reference — these apps each have many screens. Drill: "Which Linear screen specifically? Issue list, project, roadmap, cycles, settings, inbox?" Same for Notion, Excel, etc. Force Sam to either name the surface or drop a screenshot.

---

## §7 — Stack constraints (already wired up)

**Bad:**
- "I prefer Postgres." → preference, not constraint
- "Probably Next.js." → not decided yet
- "Should use TypeScript." → "should" is preference

**Good:**
- "Vercel + Supabase Postgres. Supabase Auth already wired. Don't propose a different DB or auth provider."
- "Has to deploy to our existing K8s cluster. Has to use the existing Stripe account."

**How to push back:** "§7 is what's already decided and CANNOT change. 'Prefer' or 'probably' belongs in §6 (look + feel) or gets dropped. What's actually wired up that you can't touch?"

**Anti-pattern: smuggling preferences as constraints.** Sam might list "Postgres" or "Next.js" hoping it counts as already-decided. Drill: "Is Postgres already running with data and consumers, or are you just choosing it for this project?" If the answer is "I'd choose it," it's a preference — drop from §7, leave for §6 if relevant.

---

## §8 — This brief vs later

**v0.4 rule:** exactly one F0X.

**Bad:** "F01 = log in + see map + edit cards + draw relations" → that's 4 features
**Good:** "F0X = Monday 09:00 local-time email per AM with three categorised sections, manual + cron trigger via a single endpoint, idempotent within ISO week. Later: F0Y Slack version. F0Z per-AM thresholds."

**How to push back:** "You listed multiple things. Pick the smallest one you'd still call valuable on its own. The others go into Later — we're not building them in this brief."

**Anti-pattern: "F0X is everything shipped together."** Refuse. The single-F0X rule isn't about long-term value — it's about ship sequencing. Reframe: "Which one would you let a beta user try in 2 weeks if they could only have one?" If Sam still won't pick, declare the brief not ready and stop.

**Anti-pattern: F0X creep mid-session.** §8 is set early ("F0X = task kanban"). Then in §10a or §6, Sam casually says "and obviously it'll handle invoices too." That's F0X creep. Either edit §8 explicitly to "F0X = task kanban + invoice generation" (which then fails the single-feature rule) or confirm the new mention is NOT in F0X (it's F0Y/Z). Don't let F0X grow silently. Quote earlier §8 back: "You said F0X = task kanban. Is invoices in F0X now or is it F0Y?"

---

## §9 — Hard constraints from reality

**v0.4 rule:** must explicitly cover what happens on re-run / retry. Never silent.

**Bad:** "Demo June 15. Free tier only." (deadline only — silent on re-run)
**Good:** all of:
- Deadline: "Demo 2026-06-15"
- Cost cap: "Resend usage <€20/month"
- Things that must not break: "existing Monday exec rollup at 10:00"
- Dedupe key: "`<am_id>:<iso_week>`"
- On re-fire: "second call within same ISO week → 200 `{noop: true, reason: 'already_sent'}`"
- On third-party failure: "Resend non-2xx → no row in `health_email_sends`, retry safe"
- After boundary: "new ISO week → new dedupe key → email sends again"

**How to push back:** "What's the dedupe key? What happens if cron retries on a 5xx? What writes happen on success vs failure? What does re-running next week do?"

**Anti-pattern: "no idempotency needed, fire and forget."** Allowed but never silent — write it down explicitly. Example §9 entry: "Idempotency: not required — this is fire-and-forget; double-fires create duplicate entries by design (e.g. logging events). Re-runs are safe in the sense that no state corruption is possible." If Sam shrugs the section, force the explicit acknowledgment. Empty §9 idempotency block is a render blocker.

**Anti-pattern: undefined identity nouns in §9.** Sam might say "dedupe by `request_id`" or "key on `idempotency_key`" or "scope by `tenant_id`" without ever defining how those values come into being. These are NOT free — they're the most important §10b entries because they determine whether the dedupe actually works. Drill: "What generates `request_id`? Is it client-emitted (the caller sends it) or server-generated (you mint it on receive)? UUID v4? Hash of which fields? A monotonic counter from where? What's the uniqueness scope — global, per-tenant, per-day?" Force a §10b entry that names the generation rule. Example good entry: "**request_id** = UUIDv4 generated client-side; required header on all writes; uniqueness scope is global; collisions are rejected with 409." Without this, the dedupe is theatre.

---

## §10a — Anything weird / specific

**Bad:** silently skipped
**Good:** "AMs are in 6 timezones — the hourly-cron-with-per-AM-tz-filter is the only correct pattern. HubSpot ownership can change mid-week — email reflects ownership at send-time, not start-of-week. Slipping wins ties with at-risk."

**How to push back:** "Anything an outsider wouldn't know? Timezone quirks, regulatory stuff, integrations behaving weirdly, tie-breaking rules?"

---

## §10b — Definitions (NEW since v0.3)

**Required:** every fuzzy word from §1, §2, §4, §4a defined concretely.

**Bad:**
- "healthy = accounts that are doing well" → not concrete
- Missing terms used elsewhere (e.g. §4a uses `flagged_since` but §10b doesn't define it)

**Good:**
- "**healthy** = `health_score ≥ 70` AND `days_since_touch ≤ 14` AND `open_critical_ticket_age_days IS NULL`."
- "**weekly** = Monday 09:00 in `am.timezone` (IANA, e.g. `Europe/Madrid`); DST-aware; ISO week boundary = Monday 00:00 UTC."
- "**primary_flag** = the single most-severe trigger string for the account, severity order: open critical ticket > ARR drop ≥ 25% > no touch > 30d > ARR drop ≥ 10% > no touch 15-30d > health score band."

**How to push back:** "What's the SQL for that? Give me a predicate, a threshold, or an enum — not 'we'll know it when we see it'."

---

## §11 — Mockup (NEW rule in v0.4)

**v0.4 rule:** required for any content or visual UI deliverable. `(none)` allowed only for purely behavioural deliverables.

**Bad:**
- "(none) — §4a contains the literal output sketch" → that USED to be allowed, but no more. §4a is a structural contract; §11 is a visual gut check. Both are needed for content/visual deliverables.
- Empty for a Notion page brief
- "I'll add it later" → render is blocked until it's there

**Good:**
- A Figma URL: "https://figma.com/file/abc123/morning-brief"
- A local file path: "~/Mockups/morning-brief.png"
- A photo of a sketch: "~/Photos/IMG_3421.jpg" (whiteboard/paper sketch is fine)
- A screenshot of a similar tool: "~/Refs/stripe-weekly-summary.png"
- For purely behavioural: "(none) — purely behavioural, no visual artefact"

**How to push back:** "I need a mockup before I render. Even a 30-second sketch on paper, photographed with your phone, is fine. The text alone won't catch the 'does this actually look right' bug."

**Anti-pattern: placeholder URLs.** Sam might drop `https://example.com/mockup.png`, `https://placeholder.com/...`, `https://your-figma-url-here.figma.com`, or any obviously-fake URL hoping it passes Check C. v0.7 Check C HEAD-checks every URL and rejects placeholders. Quote: "That's a placeholder URL — example.com isn't a real mockup. Drop the actual Figma link or paste the screenshot inline."

**Anti-pattern: malformed URLs.** `figma.com/file/abc123` (no `https://`), `~/mockups/file.png` (path doesn't exist on disk), `figma://abc123` (custom scheme). v0.7 Check C catches all three. Quote: "Missing protocol — should be `https://figma.com/file/abc123` so I can verify it resolves." Or for non-existent paths: "I tried to read `~/Mockups/...` and the file isn't on your machine. Either fix the path or paste the image inline as an attachment."

**Anti-pattern: URL that resolves but isn't a mockup (new in v0.8).** Sam might drop `https://www.google.com`, `https://stripe.com`, his company's marketing page, or any URL that returns HTTP 200 but isn't actually a mockup file. v0.8 Check C applies a host whitelist (figma.com, dribbble.com, framer.com, miro.com, etc.) plus image-extension allowlist (.png/.jpg/.svg/etc.). If neither matches, ask explicitly: "That URL resolves but `<host>` isn't a known design tool and the path has no image extension. Confirm: is this the actual mockup, or a placeholder? If real, say so explicitly. Otherwise drop a Figma URL or paste the screenshot inline." Don't accept silence as confirmation.

---

## Cross-section contradictions (NEW in v0.5)

**v0.5 rule:** §3 / §7 / §8 must agree with each other and with everything Sam said in the conversation. Contradictions block render.

**Anti-pattern: tenancy contradiction.**
- §3: "~30 employees of my company"
- §7: "no app-level auth; Tailscale VPN only"
- Later mid-session: "actually we want to white-label for our customers' employees too"

These can't coexist. Customer employees aren't on Sam's VPN. "No app-level auth" can't scope data per tenant.

**Push-back:** Quote both back. "You said earlier ~30 employees of your company (§3) and VPN-only no app auth (§7). White-label for customer employees contradicts both. Pick one path: (a) F0X is single-tenant internal (white-label is permanently out, not just F0Y), or (b) F0X is multi-tenant from day one (rewrite §3 to external users and §7 to app-level auth + per-tenant scoping). I'm not rendering until we pick."

**Anti-pattern: persona shift.**
- Early: "Just me dogfooding."
- Later: "It needs to support team sharing and permissions."

"Just me" is single-user; team sharing is multi-user. Pick one. If it's "just me first, team in F0Y", say so explicitly in §8 — don't let team sneak into F0X via the back door.

**Anti-pattern: F0X scope creep.**
- §8: "F0X = task kanban only."
- Later in §10a: "obviously it'll also do calendar overlay."

Calendar overlay isn't in §8. Either §8 is now stale (force update — but then it's two features and §8's single-feature rule fails) or calendar is NOT in F0X (it's F0Y). Quote §8 back.

**Anti-pattern: spec drift.**

Sometimes the contradiction emerges because Sam's pitch changed mid-session. He started wanting X, by question 12 he wants Y. The §2 verbatim quote-block is now stale. Don't silently update it — surface the drift: "Your pitch said X. We're now building Y. Either we update the pitch (and §1 / §2 / §3 with it) or we go back to X. I'm not silently rewriting your verbatim pitch."
