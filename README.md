# skvlt

**_[汉语](./README.zh.md)_**

This repository is organized around a single-file Agent Skills manifest, [`skvlt.yaml`](./skvlt.yaml), for [Skills Vault](https://github.com/xixu-me/skills-vault).

It is intended for people who want a reviewed, portable snapshot of approved skill sources and installed skill names.

## Repository Contents

- [`skvlt.yaml`](./skvlt.yaml): the source-of-truth manifest
- [`MANIFEST_POLICY.md`](./MANIFEST_POLICY.md): the agent-facing policy for additions, replacements, rejections, and escalations

> [!IMPORTANT]
> In normal use, manifest changes are proposed and applied by agents following the policy.

## Manifest Overview

The manifest records:

- approved upstream skill sources
- installed skill names under each source
- integrity fields such as `total_sources`, `total_skills`, and per-source `count`
- the current manifest scope, `global`

Minimal example:

```yaml
total_sources: 21
total_skills: 137
scope: "global"

sources:
  "openai/skills":
    count: 27
    skills:
      - "openai-docs"
      - "slides"
      - "sentry"
```

Today, the manifest covers a broad set of real-world development tasks, including general engineering workflows, AI, cloud platforms, security, testing, documentation, design, deployment, and research-oriented work.

It is especially useful for:

- developers working across a wide range of modern software tasks
- teams that want a shared, reviewed skills baseline across domains
- Skills Vault users who do not want to assemble trusted sources one by one

## Using It With Skills Vault

This repository is a maintained manifest source intended to be used with Skills Vault.

Recommended workflow:

1. [Fork this repository](https://github.com/xixu-me/skvlt/fork) and clone it.
2. Run the restore flow through Skills Vault.

Example:

```bash
git clone https://github.com/xixu-me/skvlt.git
cd skvlt
bunx skvlt restore --all
```

This repository is not a standalone installer. Its role is to provide a maintained manifest for the Skills Vault toolchain.

> [!IMPORTANT]
> This repository is meant to be used with Skills Vault. Standalone use outside the Skills Vault workflow is not considered a supported primary workflow.

## Formatting

This repository now includes a lightweight Prettier-based formatting toolchain for Markdown and YAML files.

Local commands:

```bash
bun install
bun run format:check
bun run format
```

GitHub Actions workflows:

- `Format Check`: runs formatting validation on pull requests and pushes to `main`
- `Format Fix`: manual `workflow_dispatch` workflow that formats the selected branch and pushes a `chore: apply formatting` commit only when changes are needed

## Change Policy

Because this manifest is global rather than project-specific, changes are handled conservatively.

High-level rules include:

- prefer small, auditable changes
- prefer adding skills under existing trusted sources over introducing source churn
- reject skills tied to a specific repository, agent, or runtime unless explicitly allowed
- escalate ambiguous overlap, process-shaping changes, and source-level edits to a human reviewer

See [`MANIFEST_POLICY.md`](./MANIFEST_POLICY.md) for the complete decision model.

## Updating The Manifest Through Agents

In most cases, manifest changes should be requested through an agent rather than edited directly in [`skvlt.yaml`](./skvlt.yaml).

Recommended flow:

1. [Fork this repository](https://github.com/xixu-me/skvlt/fork) and clone it.
2. Give an agent access to the cloned repository and request a manifest change.
3. Provide the candidate skill or source and the change you want.
4. Let the agent evaluate the request against [`MANIFEST_POLICY.md`](./MANIFEST_POLICY.md) and prepare a patch if the change is justified.
5. Review the agent's rationale and diff before accepting the change.
6. Commit the result.

If the case is ambiguous, high-impact, or source-level, the agent should normally escalate to a human instead of applying the change automatically.

## License

Released under the MIT License. See [`LICENSE`](./LICENSE) for details.
