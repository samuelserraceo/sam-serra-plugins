# sam-serra-plugins

A Claude plugin marketplace maintained by Sam Serra. Currently contains one plugin: **`sdd-brief`**.

## What's in this repo

| Plugin | What it does | Version |
|--------|--------------|---------|
| **sdd-brief** | Interview-style PRD writer for Sam's SDD framework. Grills you with one question at a time, refuses fuzziness, writes a clean markdown brief. | 0.8.0 |

---

## Install — for non-developers (Cowork desktop app)

Three clicks, no terminal needed.

1. Click **`sdd-brief.plugin`** in the file list above.
2. Click GitHub's **"Download raw file"** button (the icon next to the file size).
3. Once downloaded, **double-click** the file. Cowork will open an install dialog. Click **Install**.

After install, type `/` in any Cowork chat and pick **`write-brief`** to start a new PRD interview. Or just say "write a brief for [your feature]" and the skill will activate.

### Updating

When a new version ships, you'll get a heads-up. Repeat the three steps above with the new file — Cowork will prompt to upgrade.

---

## Install — for developers (Claude Code CLI)

One command:

```bash
/plugin marketplace add samuelserraceo/sam-serra-plugins
/plugin install sdd-brief@sam-serra-plugins
```

Replace `samuelserraceo/sam-serra-plugins` with the actual `samuelserraceo/sam-serra-plugins` once you create the repo on GitHub. Claude Code pulls from `main` by default and auto-detects updates.

---

## What `sdd-brief` does

You pitch a feature idea in 1-3 sentences. The skill then:

1. **Grills you** one question at a time, each with a recommended answer — until every fuzzy word ("weekly", "active", "healthy", `request_id`, etc.) is pinned to a concrete rule.
2. **Surfaces contradictions in real time.** If you say "just for my 30 employees" early and "for our customers' employees too" later, it stops, quotes both back, forces you to pick one.
3. **Runs four quality checks** in chat before writing the brief — fuzzy words defined, acceptance criteria fireable on a preview deploy, output example + mockup attached, no contradictions between sections.
4. **Writes a markdown PRD** to your Cowork outputs folder. The PRD is concrete enough that another engineer or LLM could just build it without follow-up questions.

Built on top of Matt Pocock's [grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) methodology, with an 11-section output template designed for Sam's spec-driven development pipeline.

---

## Repo layout

```
sam-serra-plugins/
├── .claude-plugin/
│   └── marketplace.json      ← Claude Code marketplace manifest
├── plugins/
│   └── sdd-brief/            ← plugin source (skills, references, manifest)
│       ├── .claude-plugin/plugin.json
│       ├── skills/write-brief/...
│       └── README.md
├── sdd-brief.plugin          ← packaged bundle for Cowork (Option 1 install)
└── README.md                 ← this file
```

---

## Troubleshooting

**"Plugin validation failed" on install.** Make sure you downloaded the raw file, not GitHub's HTML preview of the file. The `.plugin` file should be ~32 KB. If you see a few KB of HTML instead, repeat step 2 making sure you click the raw-download button.

**Two `write-brief` entries in the slash menu after install.** You may have installed both the `.plugin` and a separate `.skill` file in the past. Go to Customize → Personal skills, remove the standalone `write-brief`, keep only the one under the `sdd-brief` plugin.

**`/plugin marketplace add` errors in Claude Code.** Check that the repo is public (or that your Claude Code session has access to it). Marketplace add doesn't work for private repos on personal plans.

---

## Issues / suggestions

Open a GitHub issue on this repo, or message Sam directly.
