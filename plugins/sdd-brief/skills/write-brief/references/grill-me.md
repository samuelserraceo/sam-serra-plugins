# How to grill (the interview style this skill uses)

Adapted from Matt Pocock's grill-me skill: https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md

The original skill, in his words:

> Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.
>
> Ask the questions one at a time.
>
> If a question can be answered by exploring the codebase, explore the codebase instead.

## How that applies here

The grilling phase happens AFTER Sam pitches the idea (1-3 sentences) and BEFORE you start writing the brief. Its job is to surface every decision the brief depends on, so the brief is concrete enough to ship without follow-up questions.

## Rules

1. **One question at a time.** Use `AskUserQuestion`. Never batch.
2. **Always recommend an answer.** Every question comes with your best guess and a one-sentence "why". Sam's job is to confirm, redirect, or refine — not to start from a blank page. This is the most important rule. If you ask without recommending, you're shifting the work back onto Sam.
3. **Walk the decision tree, not flat order.** Each answer might open new questions. Chase them. Resolve dependencies first — e.g. "is it multi-tenant?" must be answered before "do we need RLS?".
4. **Be relentless.** If an answer is fuzzy, drill in. Quote what Sam said back to him. Don't move on until you have something concrete enough to implement against.
5. **Read attached docs instead of asking.** If §11 has a Figma file, a schema doc, or a repo reference, read it before asking questions whose answers are inside it.
6. **Stop when you have shared understanding, not at a fixed count.** Could be 5 questions. Could be 40. Stop when you can fill all the brief's sections without guessing — not before, not after.

## Watch out for: the AskUserQuestion 4-option ceiling

The tool limits you to 4 options per question. If you have 5 or more discrete answers, do ONE of these:

- Split into two questions (one for category A, one for category B)
- Group related options into one option with a sub-explanation
- Use multi-select so Sam can pick several

**Don't** post 5 options, get rejected, then re-post with 4. That wastes a question.

## Branches to walk (pick which apply to Sam's pitch)

- **Identity** — primary verb the user does, landing screen, what makes this not just a CRUD app
- **User model** — single user, multi-user, multi-tenant, auth method
- **Data model** — entities, relations, source of truth, writes vs reads
- **State** — what persists, what's ephemeral, what's cached
- **Failure modes** — bad data, auth expired, network down, third-party 5xx
- **Scope edges** — what sounds in-scope but isn't (becomes §5)
- **Stack reality** — what's already wired vs what's open
- **Ship boundary** — smallest valuable cut, F0X vs F0Y vs F0Z
- **External constraints** — deadlines, cost caps, things that must not break
- **Look + feel** — references, similar tools, mobile vs desktop
- **Fuzzy-word drill** — every word like *weekly, healthy, active, ready, valid* must become a concrete rule
- **Output literal** — if the deliverable is a piece of content, get a real example showing what it looks like
- **Re-run behaviour** — what happens if it fires twice?

## Question template

```
**[The question]?**
My recommendation: **[concrete answer]**, because [one-sentence reason].
Match? Or do you want to change it?
```

When using `AskUserQuestion` with discrete options, put the recommended one first and label it `(Recommended)`.

When the question is open-ended and there's no clean option list, use free-text input but include the recommendation in the question text.

## When to stop grilling

Move to Phase 3 (the quality checks) only when you can answer ALL of these without guessing:

- The single sentence describing what it IS (§1)
- The specific current behaviour Sam wants replaced (§2)
- A specific persona — name, role, or count (§3)
- At least 2 ways to verify it works, each fireable in <60s (§4)
- A literal output example if the deliverable is content (§4a)
- Explicit non-goals — even if empty (§5)
- A concrete look + feel reference (§6)
- Hard stack constraints — already wired up, not preferences (§7)
- A single-line F0X (§8)
- Deadlines / cost / breakage AND re-run behaviour (§9)
- Domain quirks (§10a)
- A definition for every fuzzy word used (§10b)
- A mockup file/URL (§11) — required for content / visual deliverables

If any of those are still fuzzy, keep grilling. Don't advance to the quality check phase yet.
