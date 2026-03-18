# ClearAI Skills

A collection of Claude Code skills (slash commands) for the ClearAI team. Each skill lives in its own subfolder and can be installed individually into Claude Code to automate recurring tasks, enforce brand standards, and accelerate delivery across ClearAI projects.

## What are Claude Code skills?

Skills are markdown files placed in `~/.claude/commands/`. Once installed, they are invoked inside any Claude Code session with a `/skill-name` command. Claude reads the skill and follows its instructions, giving every team member a consistent, repeatable starting point for complex tasks.

## Installation

Copy the `.md` file from whichever skill subfolder you need into your Claude commands directory:

```bash
cp <skill-folder>/<skill-name>.md ~/.claude/commands/<skill-name>.md
```

Then invoke it in Claude Code with:

```
/<skill-name>
```

---

## Available Skills

### [`clearai-webslides-styling/`](./clearai-webslides-styling/)
Build a ClearAI-branded interactive pitch deck website (React + Vite) from scratch. Encodes the exact colour palette, font, shared component library, slide patterns, navigation, and deployment process used across ClearAI client proposals.

---

## Contributing a new skill

1. Create a new subfolder named after the skill (e.g. `clearai-proposal-writer/`)
2. Add the skill markdown file inside it (e.g. `clearai-proposal-writer.md`)
3. Add a `README.md` inside the subfolder describing what the skill does and how to use it
4. Add an entry to the **Available Skills** table above in this root README
5. Open a PR or push directly to `main`
