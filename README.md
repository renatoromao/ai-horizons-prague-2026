# AI Horizons Prague 2026 — Copilot Studio + Claude Code Demo

## Session

**Copilot Studio + Claude Code: From Idea to Agent in Minutes**

15:30–16:20 · Sál A3 · 200 – intermediate · Angličtina/EN

Building AI agents usually means writing instructions, defining skills, structuring
knowledge sources and configuring actions. What if AI helped build those agents faster?
This session shows a new development pattern where AI assists in creating other AI agents
by combining Copilot Studio with Claude Code.

Using a real example — a FinOps expert agent built on Microsoft Learn content about M365,
Fabric, Foundry and Copilot — we'll show how an agent can be designed, tuned and deployed
in minutes instead of hours.

### Speaker

**Renato Romão de Souza**
6x Microsoft MVP (Azure AI & Copilot Studio) · 5x MCT

- Website: [aihorizonsprague.cz](https://aihorizonsprague.cz/)
- LinkedIn: [linkedin.com/in/renatoromao](https://www.linkedin.com/in/renatoromao/)

## About this repo

This is the demo agent used in the session: a Copilot Studio CLI agentic-loop agent
(**AI Horizons - Demo template**) scoped to Microsoft Fabric, Microsoft Foundry,
Microsoft 365 Copilot and Copilot Studio, grounded only on learn.microsoft.com, with
every answer cited and every volatile fact (price, SKU, quota, limit) retrieved from
knowledge rather than guessed.

The starting prompt used to build it with Claude Code is in
[`_prompt/TEMPLATE.md`](_prompt/TEMPLATE.md).

### Layout

- `_prompt/TEMPLATE.md` — the reusable prompt template that kicks off the brief → plan →
  settings+instructions → knowledge → push workflow.
- `briefs/` — the written brief for this agent.
- `plans/` — the approved implementation plan.
- `evals/` — eval run log.
- `Untitled Agent 2409/` — the cloned Copilot Studio agent project (settings, instructions,
  knowledge sources).
