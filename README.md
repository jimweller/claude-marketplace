# jimweller Claude Code Marketplace

Plugin marketplace indexing jimweller fork plugins for Claude Code.

## Plugins

| Plugin              | Fork of                                   | Description                                                                                   |
| ------------------- | ----------------------------------------- | --------------------------------------------------------------------------------------------- |
| claude-mem          | thedotmack/claude-mem                     | Persistent cross-session memory with semantic search                                          |
| superpowers         | obra/superpowers                          | TDD, debugging, code review, plan-driven development skills                                   |
| lsp-enforcement-kit | nesaminua/claude-code-lsp-enforcement-kit | Stateless hooks redirecting grep/glob to LSP/Serena                                           |
| session             | original                                  | Search, resume, and migrate Claude Code sessions across project folders                       |
| clanker-chat        | original                                  | Clanker Register rules, output style, SessionStart injection, and per-turn reinforcement hook |
| clanker-prose       | original                                  | Prose-contract writing rules, the prose skill, and prose evals                                |

## Install

```bash
claude plugin marketplace add https://github.com/jimweller/claude-marketplace.git
claude plugin install claude-mem
claude plugin install superpowers
claude plugin install lsp-enforcement-kit
claude plugin install session
claude plugin install clanker-chat
claude plugin install clanker-prose
```
