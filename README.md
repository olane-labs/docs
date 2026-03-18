# Copass by Olane

**Teach Claude _why_, not just _what_.**

Ontology graph-powered context layer to enable Functional Memory for Claude Code.

> Claude Code plugin for [Olane](https://olane.com) · [Full docs](https://docs.olane.com)

### LongMemEval Benchmark (GPT-4o · 500 questions)

| System | Score |
|---|---|
| **Olane** | **92.2%** |
| Mastra OM | 84.2% |
| Supermemory | 81.6% |
| Zep | 71.2% |

[Full benchmark results →](docs/benchmarks.md)

## Installation

### 1. Install the CLI

```bash
brew install olane-labs/tap/olane
# or: npm install -g @olane/o-cli
```

### 2. Authenticate & Setup

```bash
olane login    # sign up via this command as well
olane logout   # clear credentials
olane setup    # adds the mcp via (.mcp.json) + file indexing for the current folder
# or: olane setup -y  # non-interactive with recommended settings (useful for CI)
```

<details>
<summary>Manual MCP configuration</summary>

If you prefer to configure the MCP server manually, add the following to your MCP config:

```json
{
  "mcpServers": {
    "copass": {
      "type": "stdio",
      "command": "olane",
      "args": ["copass", "--mcp"]
    }
  }
}
```

</details>

> **Tip:** Having trouble? Try re-indexing your project: `olane index --mode full`. Additional flags: `--max-files <n>`, `--dry-run`, `--json`.

## The Problem

Claude Code starts every conversation blank. It can read your code, but code doesn't capture **why** something was built a certain way, how entities in your system relate, what was decided in last week's session, or the tribal knowledge that lives in your team's heads. Without this context, Claude confidently makes changes based on incomplete understanding — and the more complex your project, the worse this gets.

## Functional Memory

Copass creates a **continuous knowledge loop** between Claude Code and a persistent ontology graph, so that every conversation starts with the context from all previous ones.

```mermaid
sequenceDiagram
    participant User
    participant Claude as Claude Code
    participant Copass as Copass Knowledge Layer

    User->>Claude: Submits prompt
    Note over Claude,Copass: Hook: auto-query context
    Copass-->>Claude: Inject relevant domain context,<br/>entity relationships, prior decisions

    Claude->>Claude: Works with enriched context

    Note over Claude,Copass: Hook: capture mutations
    Claude-->>Copass: Code changes, observations

    Claude->>User: Responds with plan
    Note over Claude,Copass: Hook: score the plan
    Copass-->>Claude: Cosync scores per entity —<br/>how well-understood is each part?

    Claude->>User: Shows confidence scores,<br/>flags knowledge gaps

    Note over Claude,Copass: Hook: ingest transcript
    Claude-->>Copass: Full session transcript
    Note over Copass: Knowledge layer grows →<br/>next conversation is smarter
```

### What happens at each stage

1. **Context injection (on every prompt)** — Before Claude sees your message, Copass is queried for relevant context — domain knowledge, entity relationships, prior decisions — and injects it automatically. You never need to re-explain what was discussed before.

2. **Mutation capture (on code changes)** — Every `Edit`, `Write`, and `NotebookEdit` is captured and ingested into the knowledge graph asynchronously. Claude's work product becomes future context without anyone curating it.

3. **Plan confidence scoring (before execution)** — When Claude proposes a plan, Copass scores each entity involved. High scores mean Copass has strong context — proceed confidently. Low scores mean Claude is operating in a knowledge gap and should confirm with you first.

4. **Transcript ingestion (on session end)** — The full conversation transcript is ingested when the session ends. Decisions, rationale, and discussion all become retrievable context for future sessions.

### Cosync Scores

Cosync scores answer the question: **"How much does Copass actually know about what Claude is about to do?"**

Scores are displayed automatically in two places:

- **On plans** — Claude shows per-entity scores before executing. Entities with low scores are flagged so you can provide missing context.
- **On permission requests** — When Claude asks to run a command or edit a file, you see at a glance whether the action is in well-understood territory.

```
━━ Copass Scores ━━

  PaymentService: ████████████ 92%
  WebhookHandler: ████████░░░░ 67%
  RateLimiter:    ██░░░░░░░░░░ 15%

  Total: ████████░░░░░░░░░░░░ 58%
```

In this example, Claude knows `PaymentService` well but has almost no context for `RateLimiter` — it should ask you before making changes there.

## Tools

| Tool | Description |
|---|---|
| `check_project_status` | Check if the current project has been indexed and its status |
| `context_query` | Ask a natural language question against the ontology knowledge graph |
| `context_summary` | Get an LLM-generated context summary for canonical entities or free-form text |
| `search_entities` | Search the ontology graph by name or description |
| `get_score` | Score how well the knowledge graph understands a topic |
| `ingest_code` | Ingest source code into the ontology for entity extraction |
| `ingest_text` | Ingest text content into the ontology for entity extraction |
| `index_project` | Index the current project for ontology enrichment |

## CLI Reference

Beyond setup and indexing, the CLI exposes several commands for interacting with the ontology graph directly.

### Search

```bash
olane search <query>        # search the ontology graph for entities, relationships, and context
```

### Copass (CLI equivalents of MCP tools)

```bash
olane copass question <question>   # ask a natural language question against the ontology
olane copass context               # get LLM-generated context summary for entities or text
olane copass score <query>         # score how well the knowledge graph understands a topic
```

### Watch

```bash
olane watch                        # start continuous incremental indexing
olane watch status                 # check watch status
olane watch install-service        # install and start as a background service
olane watch uninstall-service      # uninstall the background service
```

### API Key Management

```bash
olane api-key create [--name <name>]   # create a new API key
olane api-key list                     # list active API keys
olane api-key revoke <key-id>          # revoke an API key
```

### Configuration

```bash
olane config set <key> <value>     # set a config value
olane config get <key>             # get a config value
olane config list                  # list all config values
```

### Ingest

```bash
olane ingest code [file]           # ingest source code (or stdin with --stdin)
olane ingest text [file]           # ingest text content (or stdin with --stdin)
```

Common flags: `--additional-context <ctx>`, `--project-id <id>`, `--json`. Code-specific: `--language <lang>`, `--file-path <path>`. Text-specific: `--source-type <type>`, `--entity-hints <hints>`.

### Utilities

```bash
olane status [--json]              # check project indexing status
olane upgrade [--dry-run]          # upgrade CLI to latest version
olane transcript clean <file>      # clean Claude Code transcript JSONL into readable markdown
```

## Architecture

The plugin runs `olane copass --mcp` which starts a unified MCP server over stdio. All authentication and encryption is handled by the Olane CLI — no separate local/remote servers needed. Your raw text is encrypted client-side before transmission (see [Security & Indexing](docs/security.md) for details).

## Requirements

- [Olane CLI](https://www.npmjs.com/package/@olane/o-cli?activeTab=readme) v2.0.0+
- An Olane account (`olane login`)

## Further Reading

- [Security & Indexing](docs/security.md) — End-to-end encryption, key management, and the ingestion flow
- [LongMemEval Benchmark](docs/benchmarks.md) — Full benchmark results and methodology
