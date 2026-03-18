# ClearAI Webslides Styling — Claude Skill

This folder contains a Claude Code skill for building ClearAI-branded interactive pitch deck websites.

## Installation

Copy `clearai-webslides-styling.md` into your Claude Code commands folder:

```bash
cp clearai-webslides-styling.md ~/.claude/commands/clearai-webslides-styling.md
```

## Usage

In any Claude Code session, type:

```
/clearai-webslides-styling
```

Claude will then guide you through building a new pitch deck website for a client, matching the exact fonts, colours, component patterns, and navigation used in the QSHF Scoping Webslides project.

## What the skill covers

- Vite + React project setup
- Thorndale AMT Regular font (system serif fallback)
- Full colour palette (obsidian, clay, parchment, red, mid, lightClay)
- Shared component library (Wrap, WhiteSlide, SlideFooter, DotIcon, Rule)
- Dark slide pattern (cover / thank you)
- White and parchment content slide patterns
- Keyboard + click navigation with slide-in animation
- Dynamic calculator pattern (editable inputs → derived financials)
- Image import conventions (ES module, not public folder)
- Slide architecture checklist (cover → problem → solution → ROI → investment → timeline → thank you → appendix)
- Netlify deployment
