# Plan: AI Horizons - FinOps Expert

Brief: `briefs/AI Horizons - FinOps Expert.md`
Agent folder: `Untitled Agent 2409/` (schemaName `cr85a_untitledagent2409_nvnqA8`, template
`cliagent-1.0.0`, recognizer `CLICopilotRecognizer` — confirmed CLI agentic-loop agent)
Target environment: `.mcs/conn.json` → `EnvironmentDisplayName: "Demo"` — safe, not production.

Execution order per house rules: **settings+instructions → knowledge → push**, each its own
layer with a diff summary + suggested commit message, push only on request, publish never.

---

## Layer 1 — Settings + instructions

File: `Untitled Agent 2409/settings.mcs.yml`

| Field | Current | New |
|---|---|---|
| `displayName` | `Untitled Agent 2409` | `AI Horizons - FinOps Expert` |
| `configuration.agentSettings.instructions.segments[0].value` | `test for instructions​‌` (placeholder, also contains stray zero-width chars) | full instructions block, see below |

Everything else (`schemaName`, `accessControlPolicy`, `authenticationMode`,
`authenticationTrigger`, `recognizer.kind`, `model.series`, `web.enableWebSearch`, `template`,
`language`) stays as-is — no reason to touch it.

**Shape constraint (hard-won #1):** instructions MUST stay as the structured object:
```yaml
instructions:
  segments:
    - kind: StaticSegment
      value: |
        <text>
```
Never collapse to a plain string. The current file already has this shape — we only replace the
`value:` content, we don't change the structure.

### Draft instructions (subject to final wording pass at execution time)

```
You are AI Horizons - FinOps Expert, an assistant for IT staff at AI Horizons Prague.

Your scope is strictly: Microsoft Fabric, Microsoft Foundry, Microsoft 365 Copilot, and Copilot
Studio. If a question falls outside these four products, say so and do not answer from general
knowledge.

Ground every factual answer in your knowledge sources only. All of them are pages from
learn.microsoft.com. Never use general web knowledge or assumptions to answer a product
question. Always cite the specific page (title and URL) each fact came from.

Never state a price, SKU name or tier, quota, or limit from memory or inference. These change
often and a wrong number is worse than no number. When asked for one:
- Retrieve it from your knowledge sources and cite the exact page, or
- If your sources don't contain the current value, say you cannot guess it and link the official
  Microsoft Learn page where it is maintained.

When a concept exists in more than one in-scope product (for example "capacity" in Fabric vs.
Foundry, or "quota" in Foundry vs. Copilot Studio), ask a brief clarifying question if the
product isn't clear from context, rather than guessing which one the user means.

Keep answers concise and technical, suitable for an IT audience. Use tables for comparisons.
Always end a factual answer with the source link(s) used.
```

Length: well under platform limits; second-person; plain English — matches house content rules.

**Commit message (suggested, for this layer only, applied after approval + edit):**
`Set AI Horizons - FinOps Expert display name and grounding instructions`

---

## Layer 2 — Knowledge sources

Hybrid strategy per hard-won constraint #3 (two-level path limit, `/en-us` counts as a level) and
the house content rule (never write prices/SKUs/limits/quotas as facts anywhere in YAML).

Verified so far with `curl -o /dev/null -w '%{http_code} %{url_effective}' -L`:

| # | Candidate URL (as given) | Result | Resolved URL | Path levels after host (excl. `/en-us`) | Verdict |
|---|---|---|---|---|---|
| 1 | `https://learn.microsoft.com/fabric/` | 200 | `.../en-us/fabric/` | 0 | OK — live scope, root of Fabric docs |
| 2 | `https://learn.microsoft.com/fabric/fundamentals/` | 200 | `.../en-us/fabric/fundamentals/` | 1 | OK — live scope |
| 3 | `https://learn.microsoft.com/fabric/enterprise/licenses` | 200 | `.../en-us/fabric/enterprise/licenses` | 2 | OK, but exactly at the 2-level limit → **snapshot instead**, this is a licensing page (volatile-adjacent, routing precision matters) |
| 4 | `https://learn.microsoft.com/fabric/admin/capacity-settings` | 200 | `.../en-us/fabric/admin/capacity-settings` | 2 | at limit → **snapshot**, this is Fabric capacity admin (overlaps conceptually with Foundry "capacity/quota" — needs its own precise scope) |
| 5 | `https://learn.microsoft.com/azure/ai-foundry/` | 200 (redirect) | `.../en-us/azure/foundry/` | 1 | **Deprecated path — do not use `/azure/ai-foundry/` even though it resolves; use the canonical `/azure/foundry/` directly** |
| 6 | `https://learn.microsoft.com/azure/foundry/` | 200 | `.../en-us/azure/foundry/` | 1 | OK — canonical, live scope |
| 7 | `https://learn.microsoft.com/azure/foundry/what-is-azure-ai-foundry` | 404 | — | — | rejected, do not use |
| 8 | `https://learn.microsoft.com/azure/foundry/concepts/pricing` | 404 | — | — | rejected, do not use |
| 9 | `https://learn.microsoft.com/azure/foundry/how-to/costs-plan-manage` | 404 | — | — | rejected, do not use |
| 10 | `https://learn.microsoft.com/azure/ai-services/openai/concepts/models` | 200 (redirect) | `.../en-us/azure/foundry/foundry-models/concepts/models-sold-directly-by-azure` | 3 | too deep for live scope → candidate for **snapshot** if needed for model/deployment conceptual coverage (to verify content relevance before using) |
| 11 | `https://learn.microsoft.com/copilot/microsoft-365/` | 200 (redirect) | `.../en-us/microsoft-365/copilot/` | 1 | OK — canonical, live scope (note: redirects from `/copilot/microsoft-365/` to `/microsoft-365/copilot/` — use the resolved URL directly to avoid relying on redirect) |
| 12 | `https://learn.microsoft.com/microsoft-365-copilot/` | 200 (redirect) | `.../en-us/microsoft-365/copilot/` | 1 | same target as #11 — use resolved canonical form |
| 13 | `https://learn.microsoft.com/microsoft-365-copilot/microsoft-365-copilot-licensing` | 200 (redirect) | `.../en-us/microsoft-365/copilot/microsoft-365-copilot-licensing` | 2 | at limit, and it's the licensing page → **snapshot** |
| 14 | `https://learn.microsoft.com/microsoft-365-copilot/microsoft-365-copilot-requirements` | 200 (redirect) | `.../en-us/microsoft-365/copilot/microsoft-365-copilot-requirements` | 2 | at limit → **snapshot** (rollout/requirements, routing precision matters vs. licensing) |
| 15 | `https://learn.microsoft.com/microsoft-copilot-studio/` | 200 | `.../en-us/microsoft-copilot-studio/` | 0 | OK — live scope, root of Copilot Studio docs |
| 16 | `https://learn.microsoft.com/microsoft-copilot-studio/requirements-licensing-subscriptions` | 200 | `.../en-us/microsoft-copilot-studio/requirements-licensing-subscriptions` | 1 | OK — live scope, but also a strong snapshot candidate since it's licensing-specific; decide at execution: keep as live sub-scope, since 1 level leaves routing room |
| 17 | `https://learn.microsoft.com/microsoft-copilot-studio/requirements-messages-management` | 200 | `.../en-us/microsoft-copilot-studio/requirements-messages-management` | 1 | OK — consumption/messages concept page, live scope |
| 18 | `https://learn.microsoft.com/microsoft-copilot-studio/billing-licensing` | 200 | `.../en-us/microsoft-copilot-studio/billing-licensing` | 1 | OK — billing structure page, live scope |
| 19 | `https://learn.microsoft.com/microsoft-copilot-studio/billing-consumption` | 404 | — | — | rejected, do not use (wrong slug) |
| 20 | `https://learn.microsoft.com/microsoft-copilot-studio/admin-pricing-licensing` | 404 | — | — | rejected, do not use (wrong slug) |
| 21 | `https://learn.microsoft.com/microsoft-copilot-studio/fundamentals-what-is-copilot-studio` | 200 | `.../en-us/microsoft-copilot-studio/fundamentals-what-is-copilot-studio` | 2 | at limit → conceptual overview page; likely redundant with live scope #15, drop unless #15 proves too broad |
| 22 | `https://learn.microsoft.com/fabric/fundamentals/microsoft-fabric-overview` | 200 | `.../en-us/fabric/fundamentals/microsoft-fabric-overview` | 2 | at limit → redundant with live scope #2, drop unless #2 proves too broad |

**Still to verify before the knowledge step** (not yet curl-checked, needed to complete Foundry
project/deployment coverage and Copilot Studio consumption depth): a Foundry "projects and
deployments" conceptual page, and a Foundry quota/limits page if one exists at a legal depth or
needs snapshotting. Will search via `microsoft_docs_search` at execution time rather than guess
slugs further.

### Planned source list (hybrid: live scope + snapshot)

**Live-scope website sources** (`kind: WebsiteKnowledgeSource`, ≤2 path levels, using resolved
canonical URLs):

| Name (componentName = URL) | Scope covers | Positive cue | Negative cue / disambiguation |
|---|---|---|---|
| `https://learn.microsoft.com/en-us/fabric/` | All Fabric docs except licensing/capacity-admin specifics (those are snapshotted) | General Fabric concepts, workspaces, items, tenant settings | Not for exact license/SKU pricing or capacity threshold numbers — see Fabric Licensing / Fabric Capacity Admin snapshots |
| `https://learn.microsoft.com/en-us/azure/foundry/` | All Microsoft Foundry docs (canonical path; do not use deprecated `/azure/ai-foundry/`) | Foundry projects, models, deployment concepts | Not for exact pricing/quota numbers — direct user to the official Foundry pricing page instead of citing a number |
| `https://learn.microsoft.com/en-us/microsoft-365/copilot/` | M365 Copilot docs root | General M365 Copilot capabilities, admin rollout | Not for licensing specifics — see M365 Copilot Licensing snapshot |
| `https://learn.microsoft.com/en-us/microsoft-copilot-studio/` | Copilot Studio docs root | Agent authoring, general product concepts | Not for billing/consumption specifics — see Copilot Studio Billing / Messages Management sources below |
| `https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-messages-management` | Messages/consumption model | "How is usage measured / what counts as a message" | Not for exact price-per-message — no numbers stated, link only |
| `https://learn.microsoft.com/en-us/microsoft-copilot-studio/billing-licensing` | Billing structure | "How does Copilot Studio billing work" | Not for exact prices — no numbers stated, link only |

**Snapshot sources** (`capabilities/knowledge/files/*.md` + metadata-only sidecar, no `kind:`),
used where routing precision matters or the canonical URL is right at the 2-level limit:

| Snapshot file (planned) | Source page | Positive cue | Negative cue / pointer to sibling |
|---|---|---|---|
| `fabric-licenses.md` | `https://learn.microsoft.com/en-us/fabric/enterprise/licenses` | Fabric SKU/tier **structure** (conceptual: what capacity tiers exist, what they're for) | Never states current prices — always says "see official page for current pricing" and links it. Not for capacity admin/monitoring — see Fabric Capacity Admin |
| `fabric-capacity-admin.md` | `https://learn.microsoft.com/en-us/fabric/admin/capacity-settings` | Fabric capacity **administration** (how to configure/monitor a capacity) | Not for "which SKU size do I need" or pricing — see Fabric Licenses |
| `m365-copilot-licensing.md` | `https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-licensing` | M365 Copilot licensing **model** (prerequisite licenses, how add-on licensing works conceptually) | Never states current price — links official page. Not for technical rollout requirements — see M365 Copilot Requirements |
| `m365-copilot-requirements.md` | `https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-requirements` | M365 Copilot technical/admin **requirements** for rollout | Not for licensing/cost — see M365 Copilot Licensing |

All four snapshots: markdown, starts with `# Name:` + one-line description comment, content
limited to structural/conceptual description of licensing & administration — **no numeric
prices, SKU names with prices, quotas, or limits** copied in. Each snapshot ends with an explicit
"For current prices/limits, see: <official URL>" line. Will `grep -iE` each snapshot for digit
patterns adjacent to currency/unit words before finishing, per house rule.

**Open item to resolve during knowledge step:** decide whether `requirements-licensing-subscriptions`
(Copilot Studio) becomes a fifth snapshot (it's licensing-specific like the other three) or stays
folded into the Copilot Studio live-scope root — leaning snapshot for consistency with the Fabric/
M365 treatment of licensing pages. Will confirm content overlap with `microsoft_docs_fetch` first.

**File naming at execution time** will follow `reference/knowledge-schema.md` exactly:
- Website sources: `capabilities/knowledge/cr85a_untitledagent2409_nvnqA8.<slug>_<id>.mcs.yml`
- Snapshot sidecars: `capabilities/knowledge/files/<slug>_<id>.mcs.yml` (no schemaName prefix,
  no `kind:`)

---

## Layer 3 — Push

- Only when explicitly requested.
- Before pushing: confirm active `pac auth` profile's environment URL matches
  `DataverseEndpoint` (`https://orgc64ac76d.crm.dynamics.com/`) in `.mcs/conn.json`. Stop on
  mismatch.
- After pushing: read `.mcs/botdefinition.json` and confirm
  `entity.configuration.agentSettings.instructions` shows a populated `segments` array (not an
  empty `{"$kind": "Instructions"}`). A "N changes pushed" message alone is not sufficient
  evidence.
- **Never publish** — that step is manual, in the Copilot Studio UI, by a human.

---

## Eval cases (to add under `evals/`, logged in `evals/runs.md` once run)

1. **Never-guess volatile fact** — Ask outright: *"What's the price per Fabric capacity unit for
   an F64 SKU?"* Expected: agent does NOT state a number; it either cites a retrieved figure with
   a source link, or says it can't guess and links the official Fabric licensing page. Fail
   condition: any bare number with no citation, or a number that doesn't match the cited page.

2. **Citation resolution / no deprecated variants** — Ask a question that should surface a
   Foundry citation, e.g. *"What is Microsoft Foundry and how do projects relate to
   deployments?"* Expected: response cites a `learn.microsoft.com/en-us/azure/foundry/...` URL
   (canonical), never `/azure/ai-foundry/...`. Test harness then curls every cited URL with
   `-L` and asserts: final status 200, and final resolved path does not contain `ai-foundry`
   when a non-deprecated equivalent exists.

3. **Routing pair — overlapping "capacity" concept** — Two prompts in the same eval set:
   - 3a: *"How do I check if my Fabric capacity is running out of headroom?"* → should route to
     Fabric Capacity Admin snapshot/source, not Foundry.
   - 3b: *"What's a Foundry project quota and how is it different from a Fabric capacity?"* →
     should route primarily to Foundry live-scope source, and may reference the Fabric capacity
     source only for the comparison, without conflating the two. Fail condition: 3a cites only
     Foundry sources, or 3b answers using Fabric capacity numbers/behavior as if it were Foundry's.

Additional eval to consider at execution time (not required by the brief but cheap given scope):
a fourth case testing the out-of-scope refusal (e.g. asking about Power BI Premium directly,
outside the four named products) — flagged here so it isn't lost, final call at knowledge step.

---

## Approval checkpoint

Per house workflow rule #2, **stopping here for approval.** No edits have been made under
`Untitled Agent 2409/` — `settings.mcs.yml` is unchanged from its cloned state. Once approved,
next turn proceeds to Layer 1 (settings + instructions) only, with its own diff summary and
suggested commit message before moving to Layer 2.
