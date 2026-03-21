# Deep Review

Evidence-based technical article review. Evaluates whether an article's
recommendations should be adopted into your system.

Based on LLM-as-judge research, CheckEval, and structured argumentation
literature. Avoids single-generation multi-role anti-patterns.

---

## How to use

Say `deep-review` and paste the article content.

---

## Pipeline

### Phase 0: Filter

Before any analysis, establish a baseline. Many articles exit here.

```
1. What problem does the article claim to solve? (one sentence)
2. Do we currently have this problem? (yes / no / unclear)
3. Severity? (blocking / pain point / minor friction / theoretical)
4. What happens if we change nothing? (baseline scenario)
```

**Exit condition:** "No" + "minor friction or below" = log a one-line summary,
inform the user, end the process. No further analysis needed.

---

### Phase 1: Atomic extraction

Pure extraction. No evaluation. Faithfully represent the author's claims.

- List core claims (if there's only 1, list 1 — don't pad)
- For each claim, annotate:
  - **Evidence type:** empirical data / case study / logical reasoning / bare assertion
  - **Verifiability:** can we verify this in our own system? At what cost?
- If the author is a well-known figure, note: "Would this claim be equally
  persuasive from an anonymous engineer?" (counters authority bias)

---

### Phase 2: System diff

Collect facts first. Judgment comes later. Must read actual files.

| Claim | Current state | File:line | Gap | Where we already do better |
|-------|--------------|-----------|-----|---------------------------|

- The "File:line" column must not be empty or vague ("system already has
  something similar" is not acceptable)
- When there are many claims, optionally spawn an Explore subagent to search
  the codebase in parallel

---

### Phase 3: Structured argumentation

Per-claim analysis. Replaces role-play personas.

```
For each claim:

Supporting arguments
- Evidence from the article + evidence strength
- Concrete benefit in our specific context (give examples)

Counter-arguments
- Implementation cost (tokens / complexity / maintenance)
- Conflicts with existing system
- Scenarios the author didn't consider

Missing information
- What else do we need to know to make a judgment?

Quality attribute impact (ATAM-style)
- Reliability:    improves / no change / degrades
- Maintainability: improves / no change / degrades
- Operability:    improves / no change / degrades
- Complexity:     improves / no change / degrades

Reversibility
- How costly is it to revert if adoption goes wrong? (low / medium / high)
```

---

### Phase 4: Decision cards (ADR format)

One card per claim.

```
Claim: [...]
Decision: [adopt / experiment / reject / needs discussion]
Reasons: [top 2-3]
Concrete change: [which file, which section]
Expected consequences:
  Positive: [...]
  Negative: [...]
```

Decision anchors (not a rigid formula):
- Problem severe + evidence strong + cost low + reversible = adopt
- Problem severe + evidence weak + verifiable = small-scale experiment
- Problem minor OR cost high OR irreversible = note for reference
- Problem doesn't exist = not applicable

"Change nothing" is a valid decision. Not every article needs action items.

---

### Phase 5: Red-flag audit (subagent)

**Must run as an independent subagent.** Same-generation self-evaluation has
near-zero discriminative power.

Pass the complete output from Phases 0-4 to the subagent with this prompt:

```
You are an independent auditor. Detect failure patterns in this analysis:

1. All claims adopted OR all rejected (no differentiation)
2. Diff table has no concrete file paths (hand-waving)
3. All counter-arguments are "need more data" (judgment avoidance)
4. Supporting arguments just restate the article (not contextualized)
5. All decision cards have the same decision type
6. Conclusions would change if the author's name were removed (authority bias)
7. Unsupported absolute language ("must", "always", "never") without evidence

Final question: If you skipped this entire analysis and made a 30-second
gut-feel decision, would the conclusion be the same?
If yes, flag: "analysis did not produce surplus value."

Output format:
- Red flags (list any found; say "none" if clean)
- Audit confidence: high / medium / low
- Recommendation: pass / flag weak points and deliver / redo Phase [N]
```

---

## Delivery

Present Phases 0-4 output + Phase 5 red-flag results together.
If the audit recommends redoing a phase, fix it before delivery.

---

## Constraints

- Don't manufacture action items. If there are none, say so.
- After Phase 1 extraction, don't re-summarize the article.
- System diff must cite actual files, not generalities.
- No role-play (Architect/Skeptic/Practitioner). Use structured questions
  instead of personas.
- If the article has low value overall, exit at Phase 0 — don't spend tokens
  completing the full pipeline.

## Self-improvement

No auto-loop. Human-anchored iteration:

1. After each review, note which decisions were adopted vs. rejected
2. Every 5-10 reviews, look for patterns in misjudgments
3. Adjust this file — one change at a time, test, keep or revert
4. Track versions in a comment at the top of this file
