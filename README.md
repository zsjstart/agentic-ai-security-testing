# agentic-ai-security-testing

**Can an AI agent systematically test the security of agentic AI systems?**

Systematic security testing is important for safely adopting agentic AI systems.
This project is **"Tester,"** an AI agent that uses red-teaming techniques to
assess agentic AI security.

From an attacker's perspective, Tester explores a target agentic system to
identify potential weaknesses, executes attack tests to validate them, and
generates a security assessment report.

## Methodology

Tester uses a **five-phase testing pipeline**:

- **Phases 1–3 — Reconnaissance and planning.** Tester gathers information about
  the target, analyzes its behavior and structure, builds an attack-surface
  profile, and develops an attack test plan.
- **Phases 4–5 — Attack execution and reporting.** Tester executes the planned
  tests, validates potential vulnerabilities, and documents the findings.

A **dedicated agent handles each phase**. This modular approach keeps contexts
manageable and makes the testing process easier to debug and audit. A
deterministic workflow engine ([`workflow_engine.py`](workflow_engine.py))
dispatches one agent per phase, handing off through persisted artifact files
rather than shared conversation state.

## Key mechanisms

1. **[OWASP Top 10 for Agentic Applications](references/owasp-agentic-top10.md).**
   For each identified attack surface, Tester systematically considers the
   relevant OWASP categories to ensure test coverage.
2. **[MITRE ATLAS](references/mitre-atlas-techniques.md).** ATLAS helps translate
   high-level attack categories into specific attack techniques, enabling more
   detailed attack testing.
3. **[Payload templates](references/payload-template-library.md).** Attacks
   against agentic systems often target the underlying LLM through techniques
   such as prompt injection and jailbreaks. Rather than generating LLM inputs
   entirely at random, Tester uses templates for common LLM attacks to guide
   payload generation.
4. **Attack variants.** A single attack technique can have multiple variants, and
   changes in prompt phrasing can lead to different LLM outcomes. When an initial
   payload fails, Tester tries alternative variants to explore additional attack
   possibilities.
5. **Repeated trials.** LLM behavior can be nondeterministic, so each attack test
   is executed multiple times to estimate its attack success rate rather than
   relying on a single trial.

## Evaluation in a controlled testbed

Tester was evaluated against the **Damn Vulnerable AI Application (DVAIA)**
deployed in a local test environment. As an intentionally vulnerable agentic
application, DVAIA provides a suitable testbed for this approach. Tester was iteratively refined
based on the evaluation results until its performance stabilized.

In the final evaluation:

- **25 of 27** benchmark ground-truth vulnerabilities were covered by the
  executed attack tests.
- **15 of those 25** covered vulnerabilities were successfully exploited.

## Limitations and next steps

Tester mainly focuses on **individual attack techniques**, so it may miss more
complex attacks that combine multiple attack steps.

**Key takeaway:** by combining existing security frameworks with a systematic
testing approach, we can make agentic AI security testing more structured,
measurable, and reproducible. This is just a first step — more work is needed to
see how well it works in real-world systems and how to better design and test
multi-step attacks.

## Repository layout

| Path | What it is |
|------|------------|
| `SKILL.md` | The full assessment methodology (five phases, evidence rules, gates). |
| `workflow_engine.py` | The deterministic per-phase dispatch/sequencing engine. |
| `references/owasp-agentic-top10.md` | OWASP Agentic Top 10 (ASI01–ASI10) reference. |
| `references/mitre-atlas-techniques.md` | MITRE ATLAS technique reference. |
| `references/evidence-schema.md` | Evidence log schema and the `exploitation_confirmed` criterion. |
| `references/payload-template-library.md` | Attacker-tradecraft payload templates. |

## Note

This is a research work-in-progress. Use Tester only against agentic systems you
are authorized to test. Testing runs black-box by default — the only admissible
source of information is direct interaction with the running target — and
high-risk actions require explicit human approval.
