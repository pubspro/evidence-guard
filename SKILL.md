---
name: evidence-guard
description: Audit any AI-generated output for unsupported claims, then verify every factual and technical assertion against its real source before it ships.
version: 1.0.0
author: PubsProToolkit
tags: [verification, claims-qc, hallucination-reduction, documentation, code-review, quality-assurance, evidence-grounding]
---

# evidence-guard

Apply regulated-industry evidence standards (the same rigor used to pass medical and scientific MLR and peer review) to any AI-generated output. Do not approve output until every claim is traceable to a verifiable source.

## When to use

Invoke before finalizing documentation, READMEs, PR descriptions, API references, changelogs, technical claims, or customer-facing copy.

## Procedure: run a Claims-QC pass on the target text

1. **Extract claims.** List every factual, technical, or quantitative assertion (e.g., "this endpoint returns X", "supports Node 18+", "reduces latency 40%").
2. **Classify each claim** as one of: Verifiable-in-repo, Verifiable-externally, Opinion/recommendation, or Unsupported.
3. **Verify.** For repo claims, check the actual code, config, and tests. For external claims, require a citable source. Flag anything that cannot be traced.
4. **Grade evidence strength** as High, Moderate, or Unsupported, and record the exact supporting location (file:line or source URL).
5. **Flag risk patterns:** deprecated references, version mismatches, doc-vs-code drift, overstated performance numbers, missing required disclaimers.
6. **Verdict gate.** Output a short, audit-ready Verification Note. Return PASS only if zero Unsupported critical claims remain; otherwise list required fixes by severity (Critical / Warning / Note).

## Output format

```
VERIFICATION NOTE — <N> claims checked.
CRITICAL: <file:line or location> — <claim> — <why unsupported / mismatch>.
WARNING: <claim> — <missing evidence; suggested fix>.
NOTE: <minor or advisory item>.
VERDICT: PASS / FAIL (reason).
```

## Example

Input: a README stating "Supports Python 3.8+" and "40% faster than v1".

```
VERIFICATION NOTE — 7 claims checked.
CRITICAL: README — "Supports Python 3.8+" — setup.py requires >=3.11. Unsupported.
WARNING: README — "40% faster than v1" — no benchmark source found. Cite data or soften to "faster".
VERDICT: FAIL — 1 critical claim unresolved.
```

## Known limitations

- Cannot execute code to confirm runtime-only behavior.
- Needs read access to the referenced files or sources to verify claims.
- Opinion-class claims are advisory only, not pass/fail gated.
