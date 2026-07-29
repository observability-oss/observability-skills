# observability-skills

Agent skills for the [Progress Observability](https://www.progress.com/agentic-rag)
platform. Six skills, each self-contained, installable individually or together.

This repo is the **canonical source for skill content**. Packaging — the Claude
Code plugin, slash commands, the VS Code Copilot bundle — and the end-to-end
test harness live in
[`progress-observability-plugin`](https://github.com/observability-oss/progress-observability-plugin),
which consumes this repo.

## The skills

| Skill | What it does |
|---|---|
| `instrument-agent` | Adds instrumentation to an existing Python / TypeScript / .NET agent, then hands off to confirm traces at observability.progress.com |
| `health-check` | Checks whether a service is reporting, and what shape its traces have |
| `trace-triage` | Diagnoses a failing or suspicious trace |
| `cost-report` | Reports token and cost breakdowns from recorded spans |
| `coverage-gaps` | Finds parts of an app that produce no telemetry |
| `generate-eval` | Builds evaluations, from a recorded trace or from scratch |

All six read the platform over MCP. Each carries its own copy of the contract at
`skills/<name>/references/mcp.md`, so a skill works when copied out on its own.

## Install

Skills are plain directories. Copy the ones you want into the location your
agent reads:

```bash
# Claude Code
cp -r skills/instrument-agent ~/.claude/skills/

# GitHub Copilot CLI
cp -r skills/instrument-agent ~/.github/skills/
```

Per-project, drop them in `.claude/skills/` or `.github/skills/` instead.

For the packaged experience — slash commands, marketplace install, the VS Code
bundle — use the plugin repo linked above.

## Configuration

Every skill except `instrument-agent` reads the platform through the Progress
Observability MCP server (`instrument-agent` only writes instrumentation code and
never reads back). Two different keys are involved and they are not
interchangeable:

| Key | Prefix | Used by |
|---|---|---|
| Integration key | `ac_p_…` | the instrumented **app**, to write spans |
| MCP key | `acm_…` | the **agent**, to read spans back |

Set them as environment variables or in your MCP client config. Never paste a
key into a chat session.

## Layout

```
skills/<name>/SKILL.md              the skill itself
skills/<name>/references/           supporting detail, loaded on demand
references/mcp.md                   source of truth for the MCP contract
references/mcp-schema.json          tool schemas
scripts/sync_skill_refs.py          copies mcp.md into each skill
hooks/pre-commit                    runs the sync check before every commit
```

`references/mcp.md` is the single source of truth. The per-skill copies are
generated and carry a do-not-edit banner. After editing the source:

```bash
python scripts/sync_skill_refs.py          # write the copies
python scripts/sync_skill_refs.py --check  # exit 1 if any is stale
```

Install the hook once per clone so this is automatic:

```bash
git config core.hooksPath hooks
```

## Contributing

Two things here are consumed by name from the plugin repo, so changing them is
a breaking change rather than a rename:

- **Skill directory names** — slash commands and Copilot prompts refer to them
  as strings.
- **`SKILL.md` frontmatter `description`** — it is what agents match against to
  decide when a skill applies. Editing it changes trigger behaviour for every
  consumer.

Skill changes are verified end-to-end against the live platform before they
reach anyone: the plugin repo runs `instrument-agent` against real
uninstrumented fixtures in Python, TypeScript, and .NET, executes the result,
and asserts the spans arrive. That harness and its credentials stay in the
plugin repo.

## Licence

MIT — see [LICENSE](LICENSE).
