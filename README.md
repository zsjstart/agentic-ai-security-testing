# agentic-ai-security-testing

An AI security testing agent (**"Tester"**) that uses red-teaming techniques to
systematically assess agentic AI systems — a single tool-using agent, a
multi-agent orchestration, a RAG-backed assistant, or an autonomous pipeline.

The emphasis is on **systematic, measurable, reproducible** security testing:
every attack surface is enumerated, every (entry point × technique) pair gets an
explicit disposition, and every result is backed by a persisted evidence record.
It is a controlled assessment methodology, not an open-ended jailbreak exercise.

> **Scope & honesty note.** The methodology is general, but it is primarily
> organized around **single attack techniques** per surface; support for
> multi-step attack chains is limited. It does not claim to be a fully
> autonomous red team or to simulate every real-world adversary. Its
> contribution is making agentic-AI security testing systematic and
> reproducible.

## What it does

Tester runs a fixed five-phase pipeline, scoped to the
[OWASP Top 10 for Agentic Applications](references/owasp-agentic-top10.md)
(ASI01–ASI10) and mapped to [MITRE ATLAS](references/mitre-atlas-techniques.md)
techniques:

1. **Reconnaissance** — discover the target's interfaces, entry points, tools,
   memory, and observability surface.
2. **Behavioral modeling** — build a wiring map of nodes (models, tools, stores,
   trusted contexts) and the trust boundaries each data flow crosses.
3. **Attack-surface analysis** — turn that map into an explicit entry-point
   registry and a flat coverage matrix (one cell per entry × ATLAS technique),
   then an independent cross-check pass.
4. **Exploitation & validation** — execute attack tests, record an evidence
   entry per request, and confirm whether each attack actually succeeded.
5. **Reporting** — assemble an evidence-backed findings report.

Each phase writes its output to a persisted file
(`asset-inventory.json`, `behavioral-model.json`, `registry.json` +
`coverage-matrix.json`, the evidence log, and the final report) so that results
are traceable rather than living only in conversation state.

## Architecture

This is the **multi-agent** variant: a deterministic Python workflow engine
([`workflow_engine.py`](workflow_engine.py)) dispatches one dedicated agent per
phase, handing off through the artifact files above rather than shared
conversation context. The engine owns sequencing, signal parsing, output-file
verification, and the mechanical gates (enum-explosion lint, asset↔registry
diff, coverage completeness). A thin human-interface layer handles the
authorization gate and any high-risk approval requests the engine pauses on.

## Methodology constraints

- **Black-box by default.** The only admissible source of information is direct
  interaction with the running target and what it emits in-band. A white-box or
  source-assisted engagement is possible but must be chosen explicitly up front
  and labeled as such — never folded into black-box findings.
- **Authorization first.** Written authorization, exact scope, time window, and
  approved intensity are confirmed before any testing begins. New surface
  discovered mid-assessment requires the scope to be re-confirmed.
- **Sandboxed & gated.** All fetched/generated content is treated as hostile and
  handled in a disposable sandbox. High-risk actions (real deletions, payments,
  messages to real recipients, writes to shared/production state, HITL-bypass
  attempts) require explicit human approval or are marked `not_applicable`.

## Repository layout

| Path | What it is |
|------|------------|
| `SKILL.md` | The full assessment methodology (five phases, evidence rules, gates). |
| `workflow_engine.py` | The deterministic dispatch/sequencing engine. |
| `references/owasp-agentic-top10.md` | OWASP Agentic Top 10 (ASI01–ASI10) reference. |
| `references/mitre-atlas-techniques.md` | MITRE ATLAS technique reference. |
| `references/evidence-schema.md` | Evidence log schema and the `exploitation_confirmed` criterion. |
| `references/payload-template-library.md` | Attacker-tradecraft payload templates. |

## Status

Research / work-in-progress. Use only against systems you are authorized to
test.
