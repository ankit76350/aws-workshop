# AWS Workshop — Claude Code on Amazon Bedrock (Notes)

---

## 1. Setup: Connecting Claude Code to Amazon Bedrock

### Why Bedrock?
By default, Claude Code calls Anthropic's API directly. In this workshop, we rerouted it through **Amazon Bedrock** — AWS's managed service for accessing foundation models. This is useful if you want:
- Billing via your AWS account
- Data residency guarantees (traffic stays in your AWS region)
- Integration with AWS IAM/security policies

> **Clarification — "Bedrock is not a compute layer":**  
> Amazon Bedrock is a **fully managed API service** — you call it like an HTTP endpoint and get model responses back. You do *not* provision servers, GPUs, or EC2 instances. AWS handles all the infrastructure underneath. You only interact with it at the model/API level.

### Workshop Credentials
```bash
# These were temporary workshop session credentials (they expire after the event)
# NEVER save real/permanent AWS credentials in plain text files or commit them to git

export AWS_DEFAULT_REGION="us-west-2"
export AWS_ACCESS_KEY_ID="ASIA..."         # starts with ASIA = temporary/assumed-role key
export AWS_SECRET_ACCESS_KEY="..."
export AWS_SESSION_TOKEN="..."             # session tokens are issued for short-lived access
```

Verify your identity is set up correctly:
```bash
aws sts get-caller-identity
```

Expected output:
```json
{
    "UserId": "...:Participant",
    "Account": "292510940771",
    "Arn": "arn:aws:sts::292510940771:assumed-role/WSParticipantRole/Participant"
}
```
This confirms your terminal is authenticated with AWS as the workshop participant role.

---

## 2. Configuring Claude Code to Use Bedrock

Create or open the Claude Code settings file:
```bash
mkdir -p ~/.claude && code ~/.claude/settings.json
```

Paste this configuration:
```json
{
  "model": "opusplan",
  "env": {
    "CLAUDE_CODE_USE_BEDROCK": "1",
    "AWS_REGION": "us-west-2",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "global.anthropic.claude-sonnet-4-6",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "global.anthropic.claude-opus-4-6-v1",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "global.anthropic.claude-haiku-4-5-20251001-v1:0"
  }
}
```

**What each setting does:**
- `CLAUDE_CODE_USE_BEDROCK: "1"` — tells Claude Code to route all API calls through Bedrock instead of Anthropic's API
- `AWS_REGION` — which AWS region's Bedrock endpoint to use
- The three `ANTHROPIC_DEFAULT_*_MODEL` entries — map the model tier names (sonnet/opus/haiku) to the specific Bedrock model IDs

### The `opusplan` model
This is a special Claude Code mode, not a single model. It means:
- Use **Claude Opus 4.6** for planning, complex reasoning, and agentic decision-making
- Use **Claude Sonnet 4.6** for the actual execution/coding steps

The `/status` output confirms this: `Model: opusplan (global.anthropic.claude-sonnet-4-6)` — the current turn is running on Sonnet, within the broader opusplan strategy. This balances quality and cost.

---

## 3. Running Claude Code

```bash
claude
```

You can run this from **any directory** in your file system. Claude Code uses the current working directory as its project root — it will read files, run commands, and create files relative to where you launched it.

### Useful slash commands
| Command | What it does |
|---|---|
| `/status` | Shows current version, model, provider, region, IDE connection |
| `/context` | Shows what is currently loaded into the context window (files, tools, history) |
| `/cost` | Shows token usage and cost breakdown for the current session |
| `/init` | Explores the current codebase and auto-generates a `CLAUDE.md` file |
| `/rename <name>` | Names the current session for easier reference |

---

## 4. CLAUDE.md — Persistent Project Context

`CLAUDE.md` is a plain markdown file that Claude Code reads automatically at the start of every session. It gives Claude persistent knowledge about your project without you having to re-explain things each time.

### File hierarchy (all are loaded when relevant)
```
$HOME/
└── .claude/
    └── CLAUDE.md        ← your global preferences (applies to all projects)

my-project/
├── CLAUDE.md            ← general project rules
├── backend/
│   └── CLAUDE.md        ← backend-specific rules (e.g. "use SQLAlchemy, not raw SQL")
└── frontend/
    └── CLAUDE.md        ← frontend-specific rules (e.g. "use Tailwind, React 18")
```

Claude reads the most specific `CLAUDE.md` relevant to the files it is working on. If you're editing a backend file, it reads both the root and backend-level `CLAUDE.md`.

### Generating one automatically
```
/init
```
This command makes Claude explore your codebase (structure, dependencies, patterns) and write a `CLAUDE.md` for you. Good starting point — then edit it to add team conventions.

---

## 5. What Actually Gets Sent to the LLM

Every API call to Claude (via Bedrock or directly) packages together:
- Your conversation history (messages so far)
- The contents of files Claude has read
- Results from tool calls (bash output, file reads, search results)
- Any MCP (Model Context Protocol) data that was fetched
- The `CLAUDE.md` context
- System prompts and instructions

This is the **context window**. Use `/context` to see exactly what's currently loaded. The context window has a size limit, so Claude Code manages what to keep and what to summarize/drop as sessions grow long.

---

## 6. MCP — Model Context Protocol

> "MCP is basically an API"

More precisely: **MCP is a standardized protocol that lets Claude connect to external tools and data sources**. Think of it as a plugin system.

Before MCP, every tool integration needed custom code. MCP defines a standard interface so that:
- Any tool (GitHub, Slack, databases, file systems, custom APIs) can expose itself as an MCP server
- Claude can discover and call those tools without custom integration code
- The same MCP server works across different AI clients (Claude Code, Claude.ai, etc.)

Analogy: MCP is to AI tools what REST/HTTP is to web services — a common interface everyone agrees on.

---

## 7. Permission Gates

When Claude Code is about to do something potentially risky — run a shell command, write a file, make a network request — it **pauses and asks for your approval** before proceeding.

This is intentional safety behavior. You might notice:
- Claude is very confident in its plan, but still asks
- Sometimes it proposes changes that seem unnecessary or overly broad

**The key behavior to understand:** Claude will generate its full plan first, then ask permission at each action boundary. If you're unsure about a proposed action, you can deny it — Claude will adjust its approach. You are always in control of what actually executes.

---

## 8. Model Routing & Cost (`/cost`)

Claude Code automatically routes tasks to different models based on complexity:

| Task type | Model used | Why |
|---|---|---|
| Complex reasoning, planning, architecture | Opus 4.6 | Most capable, higher cost |
| Standard coding, editing, explanation | Sonnet 4.6 | Balanced quality/cost |
| Simple tasks, quick lookups, short replies | Haiku 4.5 | Fastest, lowest cost |

The `/cost` command shows the session's token usage broken down by model, so you can see exactly what each model tier was used for and what it cost.

---

## 9. Sub-Agents (Parallel Agents)

Claude Code can spawn **multiple agent instances in parallel** to tackle independent subtasks simultaneously. For example:

- Deploy frontend and backend at the same time
- Run tests in parallel across different modules
- Explore multiple parts of a large codebase concurrently

This is powerful for complex tasks where steps don't depend on each other. Sub-agents each have their own context and tool access, and their results are returned to the main agent to synthesize.

---

## 10. Demo: Q&A App on AWS

**Prompt used:**
> Deploy an app where users can anonymously send questions that can be upvoted (no downvoting) with a profanity filter. Users provide a nickname and can only upvote the same question once. Use API Gateway, Lambda, and DynamoDB and deploy step-by-step via the AWS CLI. Create a QR code for the link and then open it. Keep it super minimal.

**Architecture deployed:**
- **API Gateway** — HTTP endpoint, routes requests to Lambda
- **Lambda** — serverless functions for question submission, upvoting, profanity filtering
- **DynamoDB** — NoSQL table for questions + upvote tracking

This is a good example of Claude Code's strength: you describe what you want at a high level, and it handles the boilerplate (IAM roles, CLI commands, JSON config, deployment order) that would otherwise take hours.

### Iterating toward production

When asked to design a production version, Claude asked clarifying questions before writing any code. This is good practice — it's called **requirements gathering**. The answers given:

> Multi-event Q&A system, anonymous (nickname-based), admin moderation, real-time updates via WebSocket, upvote-only, AWS scalable backend. Hundreds to thousands of concurrent users. Event archiving after close. Optional admin authentication.

Giving Claude specific constraints (scale, auth model, real-time requirements) results in significantly better architecture proposals than vague requests.

---

## 11. Working with an Existing Repo (Locust Example)

**Locust** is an open-source Python load testing tool (locust.io). The demo showed how to bring Claude Code into an *existing* project you didn't write.

**Workflow:**
1. Clone the repo
2. Run `/init` — Claude explores the codebase and writes a `CLAUDE.md` with project context
3. Now Claude understands the project's structure, conventions, and build system
4. You can ask for features, bug fixes, or refactors and Claude has the right context to do them properly

Example feature added: **custom metrics dashboard** — keeping it minimal (write the test, then run it).

---

## 12. Skills

Skills are reusable prompt templates or workflows that extend Claude Code's built-in capabilities. You can invoke them with `/skill-name`. They handle common workflows like committing, reviewing PRs, scheduling tasks, etc. — without you writing the same instructions repeatedly.

---

## 13. Claude's Limitations

Claude (and all LLMs) are **not perfect**. Common issues:
- Hallucinating function/package names that don't exist
- Being overconfident about a wrong approach
- Generating unnecessary complexity for simple tasks
- Adding features you didn't ask for

**The biggest takeaway from this workshop:** Just try it. The workflow of prompt → review → correct → iterate is fast enough that imperfections don't block you. The goal is to stay in the driver's seat while Claude handles the repetitive parts.
