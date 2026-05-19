# Klaude-Code

A terminal coding agent powered by the Anthropic API. KlaudeCode is an open-source effort to reproduce the developer experience of [Claude Code](https://www.anthropic.com/claude-code) from the ground up.

This is the **first iteration**: an agent loop that can hold a conversation, run shell commands on your behalf, and iterate until the task is done. Future versions will grow toward feature parity with Claude Code.

## Features

- Interactive REPL in the terminal
- Multi-turn conversation with full history kept in context
- Tool use loop: the agent calls `bash`, reads the output, and decides what to do next
- Works with any Anthropic-compatible endpoint via `ANTHROPIC_BASE_URL`
- Minimal dangerous-command guard (`rm -rf /`, `sudo`, `shutdown`, `reboot`)
- 2-minute per-command timeout, output truncated at 50k characters

## Requirements

- Node.js 20+
- An Anthropic API key

## Quickstart

```bash
git clone https://github.com/Kepler2024/KlaudeCode.git
cd ./KlaudeCode
npm install
cp .env.example .env
# edit .env and fill in ANTHROPIC_API_KEY
npm start
```

You will land in a `User>>` prompt. Type a task and hit enter. Type `quit` to exit.

## Configuration

`.env` keys:

| Variable | Required | Description |
|---|---|---|
| `ANTHROPIC_API_KEY` | yes | Your Anthropic API key |
| `MODEL_ID` | yes | Model to use, e.g. `claude-sonnet-4-5` |
| `ANTHROPIC_BASE_URL` | no | Override the API endpoint (proxies, gateways) |

## How it works

The core is a loop:

1. Send the conversation + tool definitions to the model.
2. If the model responds with `tool_use`, run the requested `bash` command and feed the output back as `tool_result`.
3. Repeat until the model stops calling tools.
4. Print the final assistant message and wait for the next user turn.

Conversation history is preserved across turns, so the agent remembers what it has already done in this session.

## Safety

KlaudeCode lets an LLM execute arbitrary shell commands on your machine. The built-in blocklist is a courtesy, not a sandbox.

- Run it inside a container, VM, or a throwaway directory.
- Do not point it at directories containing secrets, production credentials, or anything you cannot afford to lose.
- Review what the agent is about to do before letting it run for long stretches.

## Roadmap

Toward Claude Code parity. Not in strict order:

- Streaming output
- Richer tool set: `read_file`, `edit_file`, `write_file`, `grep`, `glob`
- Slash commands (`/clear`, `/help`, `/model`, ...)
- Per-tool permission prompts and an allowlist
- Token and cost accounting
- Prompt caching
- Session save / resume
- MCP server support
- Subagents
- Configurable system prompt and project-level instructions (`CLAUDE.md` equivalent)

Issues and PRs welcome.

## License

MIT. See [LICENSE](./LICENSE).
