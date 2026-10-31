# Installing Loupe

Five files, one agent:

| File | What it's for |
|---|---|
| `loupe-core-prompt.md` | The source of truth — edit this one, then re-copy its body into the others when you change it |
| `loupe-claude-code.md` | Drop-in subagent for Claude Code |
| `loupe-opencode.md` | Drop-in agent for OpenCode |
| `loupe-copilot.chatmode.md` | Drop-in custom chat mode for GitHub Copilot in VS Code |
| `INSTALL.md` | This file |

**Important caveat:** a tool's *permission system* (which tools it's even
allowed to call) is the only hard guarantee that this agent never writes
anything. The prompt itself is a second layer, not the first — a model can
in principle be talked out of following instructions, but it can't call a
tool it was never given. Wherever a tool below supports real permission
enforcement, use it; where it only supports a pasted system prompt, say so
plainly to yourself (and treat "never writes" as a strong norm, not a
guarantee) in that tool.

---

## Claude Code

```
mkdir -p ~/.claude/agents          # available in every project
cp loupe-claude-code.md ~/.claude/agents/loupe.md
```
Or `.claude/agents/loupe.md` inside one specific project instead, if you
only want it there. Invoke it explicitly with `Use the loupe agent to...`,
or let Claude Code delegate to it automatically based on the `description`
field. Enforcement: the `tools:` allow-list in the frontmatter.

## OpenCode

```
mkdir -p ~/.config/opencode/agents
cp loupe-opencode.md ~/.config/opencode/agents/loupe.md
```
Or `.opencode/agents/loupe.md` for one project. Enforcement: the
`permission` block in the frontmatter (edit/write/patch denied, bash
allow-listed by command pattern) — this is real, tool-level enforcement.

## GitHub Copilot (VS Code)

```
mkdir -p .github/chatmodes
cp loupe-copilot.chatmode.md .github/chatmodes/loupe.chatmode.md
```
Open Copilot Chat in VS Code, pick "Loupe" from the chat-mode dropdown at
the top of the panel. Enforcement: the `tools:` array in the frontmatter
(no `edit`, `runCommands`, or `runTasks`).

If your VS Code version has migrated to the newer `.agent.md` format
instead of `.chatmode.md`, the same frontmatter (`description`, `tools`)
still applies — just rename the file to `loupe.agent.md` in
`.github/agents/` and add `target: vscode` to the frontmatter.

## Cursor

Cursor's custom modes are built in the Cursor Settings UI, not from a
dropped-in file:
1. Cursor Settings → Chat → Custom modes → add a mode named **Loupe**.
2. Turn **off** Edit/Apply and Terminal; leave Search/Codebase and web
   access on.
3. Paste the full contents of `loupe-core-prompt.md` into the mode's
   custom-instructions box.
Enforcement: the tool toggles in step 2 — the prompt alone isn't enough
here. You can also drop `loupe-core-prompt.md`'s body into a project-root
`AGENTS.md` (Cursor reads this automatically in every mode), but that's
context, not a permission boundary — keep the toggles off regardless.

## Windsurf

Same idea as Cursor: Windsurf reads a project-root `AGENTS.md` for shared
instructions, but enforcing "no edits" still means configuring Windsurf's
own read-only/Cascade mode or tool permissions in its settings, not just
the file.

## Aider

Aider already ships a read-only mode, so there's nothing to install:
```
aider --chat-mode ask
```
or type `/ask` in an existing session. To give it the Loupe prompt as
context for that session:
```
/read-only loupe-core-prompt.md
```
`/ask` mode is read-only by Aider's own design — it genuinely can't call
an edit tool in that mode, so this is as strongly enforced as Claude Code
or OpenCode above.

## Anything else (Codex CLI, Gemini CLI, Continue, Zed, JetBrains AI, etc.)

Most CLIs and IDE assistants in 2026 read a project-root `AGENTS.md` for
shared instructions, so put `loupe-core-prompt.md`'s body there (or in a
section of your existing one) as a baseline. Then, specifically for this
agent's job, look for whatever that tool calls its read-only / plan /
suggest-only / "ask before any change" mode or permission setting, and use
it — the file alone only changes what the model *tries* to do, not what
it's *able* to do.

---

## Renaming it

Decided against "Loupe"? Change:
- The `name:` field (Claude Code) or the filename (OpenCode, Copilot).
- The title line and every "Loupe" in the body of all four files
  (`grep -rl Loupe .` then a find-and-replace is faster than doing it by
  hand).

A few other names that fit the same "inspects, never alters" idea, if you
want options: **Didact** (from "didactic" — a teacher who explains rather
than does), **Compass** (points the direction, doesn't walk the path for
you), **Socratic** (a direct nod to guided-questioning), **Beacon** or
**Lantern** (lights up what's going on without touching it). 
