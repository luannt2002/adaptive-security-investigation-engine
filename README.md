# Adaptive Security Investigation Engine (ASIE)

> An evidence-driven, multi-step security investigation engine for web applications and APIs.
> It does not just run a fixed list of checks — it investigates: it decides *what* to test, *why*, and *what the result actually proves*.

Status: **early development.** The core investigation loop, scanner contract, and evidence/verdict model are being built first; the test catalogue and intelligence layers are growing on top of them.

---

## What it is

ASIE takes an authorized web or API target and runs an adaptive investigation:

1. **Discover** — understand the technology, application structure, API surface, and attack surface.
2. **Research** — understand versions, changelogs, known advisories, patches, and the conditions under which they apply.
3. **Investigate** — form hypotheses, select the tests that actually fit the target, and probe adaptively within controlled limits.
4. **Verify** — separate confirmed findings from suspected, not-applicable, and not-yet-verified ones, backed by evidence.
5. **Explain** — report the evidence, root cause, risk, remediation, and — just as importantly — the coverage: what was and was not tested.

The guiding idea: **a successful scan does not have to find a vulnerability. A good scan can precisely explain what was tested, what was proven, and what remains unknown.**

## Core principles

These are hard rules, not aspirations:

- **Evidence first.** Nothing is marked *confirmed* without evidence strong enough to support the claim.
- **Unknown is not clean.** Missing data, a failed tool, a blocked target, or zero probes performed never becomes a "safe" verdict.
- **Applicability before execution.** A test only runs where it actually applies; otherwise it is `not_applicable`, not silently passed.
- **One root cause, one primary finding.** Repeated symptoms are linked to their underlying cause instead of inflating the count.
- **Safe by default.** No action that could disrupt a service, change data, or leave the authorized scope runs unless explicitly permitted.
- **Deterministic verdicts.** Models may assist analysis and hypothesis generation; the final verdict is decided by checkable, reproducible rules.
- **Honest coverage.** A scan is never called complete while important areas remain untested.
- **Version intelligence is branch-aware.** A single numeric comparison is never used across ecosystems and release branches.
- **Reproducible.** Every finding can be re-checked from its evidence, test definition, and recorded state.
- **Adaptive is not unlimited.** The planner runs under a budget, with stop conditions and a cap on investigation rounds.

## What it looks at

ASIE focuses entirely on **security** of web applications and APIs:

- Technology, framework, library and runtime identification, with version and vulnerability intelligence (CVE / advisory / EOL / patch status, branch-aware).
- Transport and HTTP hardening — TLS, HTTPS redirection, HSTS, CSP, framing, cookies, caching.
- Authentication, session and token handling.
- Authorization — object- and function-level access, ownership, cross-tenant isolation (when test identities are provided).
- Input validation and injection surfaces.
- Client-side and DOM risks.
- API security — REST, GraphQL and WebSocket surfaces, schema and object exposure.
- File handling, SSRF and external-request behaviour, deserialization and parser edge cases.
- Business-logic invariants (when flows can be modelled).
- Secrets and sensitive information exposure, misconfiguration, and supply-chain / third-party component risk.

## Verdict model

Component and advisory assessment uses a vocabulary that keeps "we couldn't tell" distinct from "it's fine":

```
known_affected · known_fixed · not_in_affected_range · configuration_dependent
version_unknown · source_conflict · data_unavailable
```

Finding verdicts separate the claim from its certainty and its reach:

```
confirmed · needs_validation · rejected
```

with `severity`, `confidence`, `exploitability`, `coverage`, and `execution_status` tracked independently — so a component that *falls in an affected range* is never automatically reported as *exploitable*, and a hardening gap is never reported as a confirmed vulnerability.

## Input levels

The same engine runs at three levels of input, and always states which data it could not see:

| Level | Input | Capability |
|---|---|---|
| Domain-only | an HTTPS URL | fingerprinting, surface discovery, passive and safe active investigation |
| Authenticated | URL + test identities | session, authorization and business-flow checks |
| Evidence-enhanced | URL + optional metadata / SBOM / config | more precise version, dependency and advisory assessment |

## Built with

Go (1.26+). The engine runs scanners concurrently, keeps a per-job request budget and SSRF-safe transport, and produces a single machine-readable assessment.

## Scope and authorization

ASIE is a tool for assessing systems you are authorized to test. It defaults to safe behaviour and blocks requests to private/internal address space unless explicitly enabled for a controlled environment. You are responsible for having permission to scan any target you point it at.

## License

To be decided.
