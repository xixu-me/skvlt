# AGENTS.md

## Project Overview

This repository is a lightweight Agent Skills manifest repo.

The primary artifact is `skvlt.yaml`, which tracks approved skill sources, installed skill names, and manifest counts.

The main companion document is `MANIFEST_POLICY.md`, which defines how agents should decide whether to add, replace, reject, or escalate manifest changes.

There is currently no application code or automated test suite checked into this repository, but there is now a lightweight bun-based formatting toolchain and GitHub Actions formatting workflows.

## Repository Layout

- `skvlt.yaml` - source-of-truth manifest for installed skills
- `MANIFEST_POLICY.md` - maintenance policy for agent-driven manifest changes
- `package.json` - local formatter entrypoint and scripts
- `.prettierrc.json` - repository formatting rules
- `.github/workflows/format-check.yml` - formatting validation workflow
- `.github/workflows/format-fix.yml` - manual formatting remediation workflow
- `LICENSE` - repository license

## Working Rules

- Treat `skvlt.yaml` as the primary file.
- Keep `MANIFEST_POLICY.md` aligned with actual maintenance behavior.
- Prefer small, auditable edits over broad manifest churn.
- Do not invent missing metadata about skills; inspect local skill files or trusted upstream sources first.
- Do not add agent-specific, repo-specific, or runtime-bound skills unless the policy explicitly allows them.

## Manifest Editing Instructions

When editing `skvlt.yaml`:

1. Preserve the generated header comment if present.
2. Keep `total_sources` equal to the number of source blocks.
3. Keep `total_skills` equal to the sum of all per-source counts.
4. Keep each source `count` equal to the number of listed skills in that block.
5. Remove a source block entirely if its last skill is removed.
6. Avoid duplicate skill names across the manifest.
7. Prefer updating an existing trusted source block over adding a new source block.
8. Treat source-level additions or removals as higher risk than skill-level additions or removals.

## Change Decision Rules

Follow `MANIFEST_POLICY.md` before making substantive manifest changes.

High-level defaults:

- Add specialized or dependency-closing skills only when they fit an already trusted source.
- When two general skills substantially overlap, prefer the one that is more complete, more practical, and more worth retaining; use maintenance and authority as tie-breakers rather than the primary decision rule.
- Reject skills that are tightly bound to a specific agent, repository, or runtime unless explicitly approved.
- Escalate ambiguous overlap, close calls between broad general skills, process-shaping changes, or source-level churn to a human.

## Validation

Before claiming a manifest edit is complete, verify:

- repository formatting still passes
- the YAML remains readable
- the manifest counts are internally consistent
- the edited source block count matches the listed skills
- any dependency-based addition is actually justified by installed skills or policy

Useful local checks:

```powershell
bun install
```

```powershell
bun run format:check
```

If you need upstream skill discovery, prefer checking the local installed skill files first, then use the relevant skills tooling or trusted upstream listing.

## Documentation Updates

- Update `MANIFEST_POLICY.md` when maintenance behavior changes.
- Update `AGENTS.md` when repository layout, validation steps, or working rules change.
- Keep docs concise and operational; this repo does not need broad product documentation.

## Commit and Review Guidance

- Keep commits focused on one manifest or documentation change at a time.
- Mention the affected source or skill family in the commit message when possible.
- Summarize why a skill was added, replaced, rejected, or escalated.
- Call out count updates explicitly if `total_sources`, `total_skills`, or source `count` fields changed.

## Common Gotchas

- Forgetting to update manifest counts after editing a source block
- Leaving an empty source block behind after removing its last skill
- Adding a skill dependency without adding the dependency itself
- Treating a specialized skill as a duplicate of a general skill
- Keeping a skill only because it exists locally, without checking whether it belongs in the manifest
