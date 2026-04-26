# kore-user-guide skill

An [Agent Skills](https://agentskills.io/home)-compatible skill that teaches AI agents how to guide users with [Kore](https://kore.ayfri.com/), the Kotlin library for Minecraft Java Edition datapack generation.

## Purpose

This repository packages practical, reusable guidance so an agent can:
- Set up Kore projects correctly (Gradle, dependency, compiler flags).
- Explain the core DSL workflow (`dataPack`, `function`, `generate*`).
- Route users to official documentation by topic.
- Distinguish end-user Kore usage from Kore contributor internals.

## Repository structure

```text
Kore-Skill/
├── SKILL.md          # Main skill metadata + instructions (required)
├── docs/
│   └── reference.md  # Curated documentation map for fast navigation
├── .agentignore      # Excludes non-essential files from skill loading
└── README.md
```

## Why this follows Agent Skills best practices

The skill is designed around the three-stage model documented on agentskills.io:
- **Discovery**: concise metadata and triggers in `SKILL.md`.
- **Activation**: complete instructions load only when Kore-related work is detected.
- **Execution**: references point to official Kore docs and machine-readable indexes.
- **Calibration**: includes defaults, boundaries, and gotchas to reduce false positives and vague outputs.
- **Progressive disclosure**: keeps core instructions in `SKILL.md` and loads `docs/reference.md` only when deeper navigation is needed.

## Usage

1. Place this folder where your agent client expects skills.
2. Ensure the client can discover `SKILL.md`.
3. Ask a Kore/datapack-related question (for example, setup, module choice, or generation flow).
4. The client should activate this skill and follow the instructions.

## Maintenance

- Keep links aligned with `https://kore.ayfri.com/docs/...`.
- Update module guidance when Kore introduces breaking changes.
- Keep examples and prerequisites current with Kotlin/JDK ecosystem updates.
- Prefer official docs over inferred behavior from stale code snippets.

## Related references

- Agent Skills overview: [https://agentskills.io/home](https://agentskills.io/home)
- Agent Skills specification: [https://agentskills.io/specification](https://agentskills.io/specification)
- Kore docs home: [https://kore.ayfri.com/docs/home](https://kore.ayfri.com/docs/home)
