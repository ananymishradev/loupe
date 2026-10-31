# Loupe

Loupe is a read-only coding tutor for the coding agents you already use: Claude Code, OpenCode, GitHub Copilot, and others.

It reads your code, answers questions, and explains what you did wrong. It never touches a file.

I built it because getting code written for me felt productive and taught nothing. When I paste a bug, I want to be told where the mistake is and why it happens, then fix it myself. When I write something slow, I want it called out by name: this loop is O(n²), a set would make it O(n).

## What it does

- Answers with one or two plain sentences first. Ask "elaborate" when you want the step-by-step version instead.
- Finds bugs in the code it can read and describes the fix in words. Shows a small snippet in chat when that helps.
- Reports time and memory cost when it matters. The expensive pattern gets named, and so does the better fix, like swapping repeated `list.count()` calls for a set.
- Refuses to apply changes. This is deliberate. Switch to your normal coding agent when you actually want edits, then come back to talk about them.

## The honest catch

A prompt only makes a model try to behave. What actually keeps an agent read-only is the tool's permission system. So every drop-in file here uses the strongest enforcement its tool supports.

- In Claude Code, a `tools:` allow-list. If the tool is not listed, it does not exist.
- In OpenCode, permission rules. Writes denied everywhere, shell denied except read-only git commands.
- In Copilot, a `tools:` list with the edit and command tools left out.
- In Cursor and Windsurf, you turn off the edit, apply, and terminal toggles yourself, then paste the prompt. There the file is context, not a boundary.
- Aider is read-only by design with `/ask`, so it is already guaranteed.

Full instructions live in [INSTALL.md](INSTALL.md).

## Files

| File | Use it for |
|---|---|
| `loupe-core-prompt.md` | The plain, tool-agnostic prompt. The source of truth. |
| `loupe-claude-code.md` | Drop-in subagent for Claude Code |
| `loupe-opencode.md` | Drop-in agent for OpenCode |
| `loupe-copilot.chatmode.md` | Custom chat mode for Copilot in VS Code |
| `SKILL.md` | For the `npx skills` CLI and skill-aware agents |
| `INSTALL.md` | Install steps per tool, plus Cursor, Windsurf, Aider, and anything else |

## Install

Claude Code:

```
mkdir -p ~/.claude/agents
cp loupe-claude-code.md ~/.claude/agents/loupe.md
```

OpenCode:

```
cp loupe-opencode.md ~/.config/opencode/agents/loupe.md
```

Copilot in VS Code:

```
mkdir -p .github/chatmodes
cp loupe-copilot.chatmode.md .github/chatmodes/loupe.chatmode.md
```

### With npx skills

The same repo works as a skill package:

```
npx skills add ananymishradev/loupe
```

By default this installs into the agents it detects in your current project. Add `-g` for global, or `--agent '*'` to install everywhere.

### Renaming

If you would rather call it Didact, Compass, Socratic, or something of your own, the rename steps sit at the bottom of [INSTALL.md](INSTALL.md).
