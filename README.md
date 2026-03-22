# Claude Code VSCode Template

A dev container template for using [Claude Code](https://claude.ai/claude-code) within VSCode. Copy this template as a starting point for any project where you want Claude Code as your AI coding assistant — whether that's building applications, automating tasks, evaluating MCP servers, or exploring skills and plugins.

## What's included

| File | Purpose |
|------|---------|
| `.devcontainer/devcontainer.json` | Dev container config with Claude Code pre-installed |
| `.devcontainer/Dockerfile` | Python 3.13 slim base with zsh, git, uv, pip |
| `docker-compose.yml` | App service + optional Postgres/Redis (commented out) |
| `.env.example` | Template for secrets and environment variables |
| `requirements.txt` | Minimal Python dev tooling (black, ruff, pytest) |
| `CLAUDE.md` | Project context Claude reads at the start of every session |
| `.claude/settings.json` | MCP servers and hooks (auto-lint on file edit) |
| `.claude/agents/reviewer.md` | Starter sub-agent for code review |
| `.claude/commands/summarize.md` | Starter `/summarize` slash command |

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [VSCode](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
- A Claude account (for Claude Code authentication)

## Getting started

1. **Copy this template** — click "Use this template" on GitHub or clone and remove the git history:
   ```sh
   git clone <this-repo> my-project
   cd my-project
   rm -rf .git && git init
   ```
   Then replace this `README.md` with one describing your project, and fill in the TODO sections in `CLAUDE.md`.

2. **Set up environment variables:**
   ```sh
   cp .env.example .env
   # Edit .env and fill in your values
   ```

3. **Open in VSCode and reopen in container:**
   - Open the folder in VSCode
   - When prompted, click **Reopen in Container** (or run `Dev Containers: Reopen in Container` from the command palette)
   - The container builds and installs Claude Code automatically

4. **Authenticate Claude Code** — open a terminal inside the container and run:
   ```sh
   claude
   ```
   Follow the prompts to log in via browser (OAuth). Auth is persisted in a named Docker volume tied to your workspace, so it survives container restarts and rebuilds — you only do this once.

   > **Alternative — API key:** If you prefer key-based auth, add `ANTHROPIC_API_KEY=sk-ant-...` to `.env` and Claude Code will pick it up automatically.

## Customizing for your project

### Add dependencies
Edit `requirements.txt` and rebuild the container, or install directly:
```sh
pip install <package>
# Then add it to requirements.txt to persist across rebuilds
```

### Enable Postgres or Redis
Uncomment the relevant service blocks in `docker-compose.yml` and the corresponding variables in `.env`.

### Give Claude project context
Create a `CLAUDE.md` file at the project root. Claude reads this automatically and uses it to understand your project conventions, architecture, and preferences:
```markdown
# CLAUDE.md
## Project overview
...
## Conventions
...
```

### Configure MCP servers
MCP servers extend Claude's capabilities (filesystem access, browser control, external APIs, databases, etc.). A starter `.claude/settings.json` is included with commented-out examples. You can also manage them interactively with:
```sh
claude mcp
```

### Hooks
Hooks run shell commands automatically in response to Claude Code events — no manual intervention needed. Configure them in `.claude/settings.json` under `"hooks"`.

| Event | Runs when... |
|-------|-------------|
| `PreToolUse` | Before Claude uses any tool |
| `PostToolUse` | After Claude uses a tool |
| `Notification` | Claude sends a notification (e.g. waiting for input) |
| `Stop` | Claude finishes a response |

The included starter hook auto-lints and formats Python files with `ruff` and `black` every time Claude edits one. Enable or extend it in `.claude/settings.json`.

### Agents
Agents are sub-instances of Claude with a specific role, instruction set, and scope. Define them as markdown files in `.claude/agents/` — Claude can spawn them automatically or you can invoke them explicitly.

A starter `reviewer` agent is included. It reviews code changes for correctness, clarity, and project conventions. Add your own by creating `.claude/agents/my-agent.md` with a frontmatter `name` and `description`.

### Commands, skills, and agents — when to use each

| Mechanism | Where | Invocation | Context | Use when... |
|-----------|-------|------------|---------|-------------|
| **Commands** | `.claude/commands/` | Manual (`/name`) | Inline (shared) | *(legacy — use skills instead)* |
| **Skills** | `.claude/skills/<name>/SKILL.md` | Manual (`/name`) or auto by Claude | Inline by default; isolated with `context: fork` | Reusable workflows, reference knowledge, prompts Claude should apply automatically |
| **Agents** | `.claude/agents/<name>.md` | Explicit delegation or Claude-initiated | Always isolated (fresh context window) | Complex self-contained tasks; keeping verbose output out of the main context |

**Key distinctions:**
- Skills use *progressive disclosure* — Claude loads descriptions upfront and full content on demand. Agents are only invoked when explicitly delegated a task.
- Skills run inline by default (they see your conversation history). Agents always start fresh.
- Use `disable-model-invocation: true` in a skill's frontmatter to prevent Claude from auto-triggering it (e.g. for `/deploy` or other side-effect commands).

### Add custom slash commands
Project-level slash commands live in `.claude/commands/` as markdown files. A starter `/summarize` command is included. Add your own:
```
.claude/commands/my-command.md
```
Then invoke it in Claude Code with `/my-command`.

## Claude Code auth persistence

Claude config is stored in a named Docker volume (`claude-code-config-<devcontainerId>`), so authentication and settings survive container rebuilds. The volume is tied to the devcontainer ID, so each project gets its own isolated Claude config.

## Project structure after customization

```
my-project/
├── .devcontainer/
│   ├── devcontainer.json
│   └── Dockerfile
├── .claude/
│   ├── agents/          # sub-agents with specific roles (e.g. reviewer)
│   ├── commands/        # custom slash commands (e.g. /summarize)
│   └── settings.json   # MCP servers, hooks, and Claude Code project settings
├── .env                 # secrets (gitignored)
├── .env.example         # committed template
├── .gitignore
├── CLAUDE.md            # project context for Claude
├── docker-compose.yml
└── requirements.txt
```
