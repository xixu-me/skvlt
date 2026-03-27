# MANIFEST POLICY

This document defines how agents should maintain the Agent Skills manifest at `skvlt.yaml`.

It covers four outcomes:

- addition
- replacement
- rejection
- escalation to humans

The policy is intentionally conservative because the manifest is global. A bad addition creates global maintenance cost, trigger noise, and weaker default skill selection. The goal is not merely to minimize overlap, but to keep the most complete, practical, and worth-retaining skills in each category.

## Audience

This policy is for:

- agents proposing changes to `skvlt.yaml`
- humans reviewing manifest changes
- future automation that scans, scores, and patches the manifest

## Scope

This policy applies to:

- adding a skill to an existing source block
- replacing one installed skill with another
- rejecting candidate skills
- escalating ambiguous or high-impact cases to humans
- maintaining manifest integrity fields such as `total_sources`, `total_skills`, and per-source `count`

This policy does not define:

- how to install skills on disk
- how to evaluate runtime correctness of a skill beyond metadata, prerequisites, and trigger scope
- how to author new skills

## Operating Model

Manifest maintenance should be split across small agent roles.

### Scout Agent

Finds candidate additions, upstream updates, and removal opportunities from approved sources.

### Metadata Agent

Extracts:

- source repository
- skill name
- trigger description
- documentation completeness signal
- practical utility signal
- depth or breadth of reusable guidance
- explicit prerequisites
- references to a specific agent, repository, or runtime
- dependency skills
- external API or paid service requirements
- installation count
- author trust signal
- maintenance activity signal

### Taxonomy Agent

Maps each skill to a task category such as:

- browser automation
- documentation authoring
- Cloudflare development
- LLM security
- skill authoring
- social research

### Comparator Agent

Compares a candidate against installed skills in the same category.

Its job is to answer:

- Is this a general skill or a specialized skill?
- Which skill is more complete, practical, and worth retaining if only one remains?
- Does it substantially overlap an installed skill's trigger scope?
- If there is overlap, is it "general vs. general" or "general vs. specialized"?
- Does the overlap create redundant surface area, or does it add meaningful complementary depth?
- Does it introduce new constraints that make it less portable?

### Decision Agent

Returns exactly one outcome:

- `add`
- `replace`
- `reject`
- `escalate`

### Manifest Editor Agent

Prepares the patch to `skvlt.yaml`, updates counts, and records the rationale.

### Human Reviewer

Approves only escalated changes or batched high-impact changes.

## Core Principles

### 1. Conservative Global Bias

`skvlt.yaml` is a global manifest. The default should be to avoid broad, noisy, or brittle skills unless they add clear value. However, reducing noise is a secondary constraint after choosing the skill that is strongest to keep long-term within its category.

### 2. Retention Value First

When comparing skills in the same category, first ask which one is more complete, more practical, and more worth retaining for repeated real-world use.

Completeness and practical utility should outrank superficial trigger neatness. If one skill is materially better as a lasting default, that matters more than preserving a narrower trigger boundary.

### 3. Trigger Overlap Matters

Substantial similarity of task triggers means two skills occupy the same task category without a real capability distinction. Overlap should be judged after content quality and retention value are assessed, not before.

### 4. General and Specialized Can Coexist

Do not force a trade-off between a broad general skill and a clearly narrower specialized skill.

Trade-offs are required only when the comparison is effectively "general vs. general".

### 5. Trust and Maintenance Break Ties

When quality and practical value are otherwise close, prefer:

- official or domain-relevant authors
- repositories with strong installation counts
- active maintenance

### 6. Dependencies Must Close

A skill should not be auto-added if it depends on another skill that is missing from the manifest, unless the same change adds the dependency or a human approves an exception.

## Decision Workflow

1. The Scout Agent identifies a candidate.
2. The Metadata Agent extracts the candidate's signals.
3. The Taxonomy Agent places it into a task category.
4. A hard exclusion pass runs first.
5. The Comparator Agent evaluates overlap against installed skills in the same category.
6. The Decision Agent chooses `add`, `replace`, `reject`, or `escalate`.
7. If the decision is `add` or `replace`, the Manifest Editor Agent drafts a patch.
8. If the decision is `escalate`, a human receives a short review packet.

## Hard Exclusion Rules

The Decision Agent should immediately return `reject` when any of the following is true, unless the skill is explicitly whitelisted:

- the skill is tightly bound to a specific agent
- the skill assumes a specific repository context
- the skill depends on a runtime or execution model that is outside the manifest's baseline
- the skill's prerequisites cannot be satisfied from the current manifest or the same proposed patch
- the trigger description is broad but the actual capability is narrow and misleading

## Action Policy

### Addition

Return `add` only when all of the following are true:

- the candidate survives hard exclusion
- its task category is already allowed in the manifest
- it is either specialized, genuinely non-overlapping, or clearly complementary after comparing retention value against incumbents
- its dependencies close cleanly
- it does not introduce broad trigger ambiguity without enough added practical value to justify coexistence

Typical examples:

- adding a specialized companion skill under an already trusted source
- adding a dependency skill already referenced by installed skills

### Replacement

Return `replace` only when all of the following are true:

- the candidate and incumbent are both general skills
- their trigger scopes are substantially similar enough that keeping both mostly duplicates maintenance surface area
- the candidate is clearly more complete, practical, and worth retaining for repeated use
- the candidate is at least as portable as the incumbent
- trust and maintenance signals do not materially undercut the candidate
- there is no meaningful capability loss

Replacement should not be used for "general vs. specialized" comparisons.

### Rejection

Return `reject` when any of the following is true:

- the skill is bound to a specific agent, repository, or runtime
- it is a weaker or lower-retention-value duplicate of an installed general skill
- it creates dependency debt
- it adds noise without enough incremental practical value
- it loses on completeness, practicality, and retention value, with trust and maintenance unable to reverse that judgment

### Escalation

Return `escalate` when the case is not safely automatable.

Escalation is required when:

- the overlap judgment depends on interpretation rather than clear capability boundaries
- the completeness or practical-utility comparison is close rather than clear
- the change adds a brand-new source block
- the change removes the last skill from a source block
- the candidate introduces external auth, paid APIs, or unusual runtime assumptions
- content quality and trust signals point in different directions
- the skill shapes broad workflow behavior across many tasks

## Scoring Rubric

Use the scoring rubric only after the hard exclusion pass.

### Positive Signals

- `+4` clearly more complete and practical than the incumbent in the same category
- `+3` clear specialization with distinct capability
- `+2` official or strongly domain-relevant author
- `+2` active maintenance
- `+2` strong installation signal
- `+2` dependency closure is already satisfied
- `+1` complements an already trusted source

### Negative Signals

- `-4` shallower, less actionable, or less worth retaining than an incumbent
- `-3` vague or noisy trigger wording
- `-3` external service requirement that is not already normal for the manifest
- `-4` missing dependency
- `-4` portability concerns

### Hard Stop

- `reject immediately` for explicit agent / repo / runtime binding unless whitelisted

### Decision Bands

- `score >= 5`: eligible for `add` if overlap review still shows clear incremental retention value
- `score 2 to 4`: prefer `escalate`
- `score <= 1`: prefer `reject`

For `replace`, compare candidate score against incumbent score. Auto-replace only when the candidate is ahead by at least `3` points, clearly ahead on completeness and practical value, and no escalation condition is present.

## Human Escalation Packet

When escalation is required, provide a short packet with:

- candidate skill
- source repository
- task category
- incumbent skill, if any
- completeness and practical utility comparison
- overlap summary
- binding and dependency notes
- trust and maintenance comparison
- recommended action
- confidence level

## Manifest Invariants

Every accepted patch must preserve the following:

- `total_sources` matches the number of source blocks
- `total_skills` matches the sum of per-source counts
- each source `count` matches the number of listed skills
- no duplicate skill names appear in the manifest
- no hard-reject skill is present unless explicitly whitelisted
- dependency exceptions are documented in the review note

## Change Classes

Treat these classes differently:

### Safe to Automate

- adding a skill to an existing trusted source
- adding a missing dependency that is already referenced by installed skills
- replacing a lower-value general skill with a clearly more complete and practical general skill

### Must Escalate

- new source added
- source removed
- process-shaping skill added, removed, or replaced
- ambiguous overlap between two broad general skills with close retention value
- any change that would alter maintenance policy itself

## Recommended Cadence

### Weekly

- scout for new candidates in already approved sources
- check for dependency gaps and broken references

### Monthly

- review overlapping general skills
- re-evaluate trust and maintenance signals

### Quarterly

- review category taxonomy
- review source allowlist
- review any existing whitelists for otherwise excluded skills

## Output Format for Agents

Every maintenance run should emit a compact decision log.

Recommended fields:

```json
{
  "candidate": "nanobanana",
  "source": "resciencelab/opc-skills",
  "category": "image generation",
  "incumbent": null,
  "action": "add",
  "reason": "dependency closure for installed skills",
  "confidence": 0.91,
  "needs_human": false
}
```

## Current Manifest Guidance

For the current `skvlt.yaml`:

- broad workflow skills should be treated as high-impact
- source-level edits should escalate more readily than skill-level edits
- when two broad general skills overlap, first ask which one would still be worth keeping if only one remained
- prefer the skill that is more complete, more practical, and more worth retaining long-term; use trigger noise, authority, and maintenance as secondary constraints
- dependency-completion additions inside an existing trusted source can be auto-approved

This means a change like "add a missing dependency skill under an existing source" is lower risk than "replace a broad browser automation skill family", but for overlapping general skills the primary question should be retention value rather than trigger neatness alone.
