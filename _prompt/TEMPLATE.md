Build a Copilot Studio agent called <<AGENT NAME>>.

Audience: <<WHO WILL USE IT>>.
Scope: <<TOPIC AREAS>>.
Ground only on <<ALLOWED DOMAIN, e.g. learn.microsoft.com>>. Cite every answer.
Never guess <<VOLATILE FACTS, e.g. prices, SKUs, limits, quotas>> — link the
official page instead.

Work in this order and stop for my approval after the plan:
brief -> plan -> settings+instructions -> knowledge -> push. Never publish.

### Environment safety
- The agent is already cloned. Discover it with `Glob: **/settings.mcs.yml`;
  do not scaffold one.
- Read `.mcs/conn.json` and tell me the EnvironmentDisplayName before writing
  anything. If it looks like production, stop and ask.
- Before any push, confirm the active `pac auth` profile's environment URL
  matches `DataverseEndpoint` in `conn.json`. They differ more often than you
  would expect. Do not push on a mismatch.

### Hard-won constraints — do not rediscover these

1. INSTRUCTIONS SHAPE (this silently fails, and a plain string looks correct).
   For a CLI agentic-loop agent (`recognizer.kind: CLICopilotRecognizer`,
   `template: cliagent-1.0.0`), instructions live in `settings.mcs.yml` as a
   STRUCTURED OBJECT:

       instructions:
         segments:
           - kind: StaticSegment
             value: |
               <instruction text>

   NOT a plain block scalar. The classic schema
   (`bot.schema.yaml-authoring.json`) resolves instructions to
   `TemplateLineNoKind` -> string; that is the STANDARD-HARNESS shape and is
   wrong here. A plain string parses fine, pushes with "1 change pushed", and
   is then silently dropped server-side.

2. VERIFY BINDING, NOT BYTES. After every push, read
   `.mcs/botdefinition.json` and confirm the field actually bound:
   `entity.configuration.agentSettings.instructions` must show a populated
   `segments` array. `{"$kind": "Instructions"}` with no segments means it was
   dropped. A successful push message is not evidence.

3. PUBLIC-WEBSITE URL DEPTH LIMIT. Website knowledge sources accept URLs up to
   TWO path levels. An `/en-us` locale prefix consumes one, so
   `/en-us/fabric/enterprise/licenses` is invalid. This applies to the GitHub
   Copilot harness too, despite the doc being labelled standard harness.
   Scopes are inclusive — a parent scope retrieves pages beneath it — so you
   lose routing precision and exclusion control, not reach.

4. UPLOADED-FILE SIDECARS are metadata-only: no `kind:`, no `source:`, and no
   `<schemaName>` prefix on the on-disk stem. They live in
   `capabilities/knowledge/files/` beside the payload. Website sources DO take
   `kind: KnowledgeSourceConfiguration` + `source:`. The two shapes differ;
   confirm each against `reference/knowledge-schema.md`.

5. VALIDATOR FALSE POSITIVE. The plugin validator reports
   `[FAIL] No 'kind' property found at root level` for `settings.mcs.yml` and
   for uploaded-file sidecars. Both are correct without a root `kind`. Ignore
   that specific failure; do not "fix" it.

6. URL VERIFICATION. Every knowledge URL must be checked with
   `curl -o /dev/null -w '%{http_code} %{url_effective}' -L`. Follow the
   redirect — a 200 is not enough. Microsoft Foundry pages under
   `/azure/ai-foundry/` frequently redirect to `/azure/foundry-classic/`,
   which is the deprecated variant. Prefer canonical `/azure/foundry/` URLs.
   Never write a URL you have not resolved.

### Knowledge strategy
Because of constraint 3, plan for a hybrid:
- Truncate broad areas to legal two-level scopes for live coverage.
- For pages where routing precision matters — anything adjacent to the facts
  the agent must never guess — snapshot the content into markdown under
  `capabilities/knowledge/files/` with its own sidecar, so each source keeps
  a distinct name and description.
- Write descriptions that let the orchestrator disambiguate. If two sources
  cover the same product, give each a positive cue, a negative cue, and a
  pointer to the other. Example: capacity SIZING ("which SKU do I need")
  versus capacity TROUBLESHOOTING ("why is my capacity at 90%").
- Every knowledge file starts with `# Name:` and a one-line description
  comment.
- Snapshots must contain NO <<VOLATILE FACTS>>. Grep to confirm before
  finishing.

### Delegation and trust
- Route all `.mcs.yml` reads/writes through `@copilot-studio:author`.
- Do not trust a subagent's report of its own writes. Verify on disk yourself:
  byte counts, the specific field, and the server snapshot. During the
  original build a subagent reported a detailed unified diff for an edit that
  never landed.
- Tell each subagent to re-read what it writes and report actual byte counts.

### Deliverables
- `briefs/<agent>.md`, `plans/<agent>-plan.md`, `evals/runs.md`
- Eval cases must include: one that asks outright for a <<VOLATILE FACT>>
  (the direct test of the never-guess rule), one that checks every cited URL
  resolves and is not a deprecated variant, and a routing pair that proves
  overlapping sources resolve to the right one.
- Push only when I ask. Never publish — that is a human action in the
  Copilot Studio UI.