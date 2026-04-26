---
name: kore-user-guide
description: Teaches and guides use of Kore, the Kotlin library for generating Minecraft Java Edition datapacks. Covers Gradle setup, modules (kore, oop, helpers, bindings), DSL patterns, generation APIs, and where to read official documentation. Use when the user works with Kore, io.github.ayfri.kore, Minecraft datapacks in Kotlin, or asks how to build or structure Kore projects without browsing the full Kore repository.
license: GPL-3.0-only
metadata:
  author: Ayfri
  tags: kotlin,minecraft,datapack,kore,game-dev
  triggers: kore,datapack,minecraft function,mcfunction,kotlin
  version: "1.0.0"
---

# Kore user guide (for assistive teaching)

Kore is a **Kotlin** library for **Minecraft Java Edition 1.20+** datapacks: type-safe builders for functions, JSON data, commands, selectors, and worldgen. Bedrock and older MC versions are out of scope unless the user explicitly targets a fork.

The agent should **not** assume the user has the Kore monorepo checked out. Prefer **official docs** and **Maven artifacts**. Use the [documentation index](./references/REFERENCE.md) for page-by-page lookup.

## Activation boundaries

Use this skill when the user asks about:
- Kore project setup or dependency configuration.
- Building datapacks in Kotlin with Kore DSL.
- Choosing between `kore`, `oop`, `helpers`, and `bindings`.
- Finding official Kore docs for a concrete feature.

Do not use this skill as the primary guidance for:
- Bedrock edition tooling.
- Vanilla datapack-only workflows with no Kotlin/Kore intent.
- General Kotlin language teaching unrelated to Kore.

## Default response procedure

Follow this order unless the user already provided a narrower request:
1. Confirm target: user wants **Kore for Java datapacks**.
2. Provide minimal setup (dependency + compiler flag + JDK/Gradle baseline).
3. Give a small, runnable DSL skeleton (`dataPack`, `function`, `generate*`).
4. Route to exactly one or two official docs pages for next depth.
5. Add optional module guidance (`oop`/`helpers`) only if needed.

## Prerequisites checklist

1. **JDK 21+** and **Gradle** (Kore template uses the wrapper).
2. Dependency: `implementation("io.github.ayfri.kore:kore:<VERSION>")` (start with the `kore` module only).
3. Kotlin **compiler option**: add `-Xcontext-parameters` (required by Kore).
4. Optional: [Kore Template](https://github.com/Kore-Minecraft/Kore-Template) for a ready project.

**Snapshots:** Sonatype snapshot repo plus `VERSION-SNAPSHOT` when the user needs unreleased features (see [Getting Started](https://kore.ayfri.com/docs/getting-started)).

## Gotchas

- `-Xcontext-parameters` is required. Missing it often produces confusing compile errors.
- `function("...")` path is logical datapack path, not a filesystem path with `.mcfunction` extension.
- Start with `kore` only. Adding `oop` and `helpers` too early increases API noise and weakens guidance quality.
- If behavior seems undocumented, check [Known Issues](https://kore.ayfri.com/docs/advanced/known-issues) before proposing workarounds.
- Prefer stable docs and artifacts by default; use snapshots only when the user explicitly needs unreleased features.

## Installable modules (pick by need)

| Module      | Coordinates `io.github.ayfri.kore:` | Role |
|------------|--------------------------------------|------|
| `kore`     | `kore`                               | Core DSL: datapacks, functions, data-driven JSON, commands. **Always the first dependency.** |
| `oop`      | `oop`                                | Higher-level gameplay: entities, teams, scoreboards, timers, boss bars, spawners, game state, events. |
| `helpers`  | `helpers`                            | Cross-cutting utilities: raycasts, renderers, scoreboard math, state delegates, scheduler patterns, VFX. |
| `bindings` | `bindings`                           | Experimental: import existing datapacks and generate Kotlin bindings. |

Add `oop` / `helpers` only when the user’s feature set justifies the extra API surface.

## Mental model (minimum viable understanding)

- **Entry:** `dataPack("namespace_id") { ... }` builds a `DataPack`.
- **Functions:** `function("path/without_mcf_extension") { ... }` emit `.mcfunction` files; commands and helpers live in these blocks.
- **Output:** `DataPack` exposes `generate()`, `generateZip()`, `generateJar()` (see [Creating a Datapack](https://kore.ayfri.com/docs/guides/creating-a-datapack) for paths and options).
- **Style:** heavy use of **lambdas** and **extension functions** on `DataPack` to split large packs into register functions (documented in Getting Started).

## Suggested order for help or self-study

1. [Getting Started](https://kore.ayfri.com/docs/getting-started) (project, first pack, `pack {}`, `load` patterns).
2. [Creating a Datapack](https://kore.ayfri.com/docs/guides/creating-a-datapack) (lifecycle, `generate*` APIs, structure).
3. [Configuration](https://kore.ayfri.com/docs/guides/configuration) (JSON formatting, dev-friendly output).
4. [Commands](https://kore.ayfri.com/docs/commands/commands) and [Functions](https://kore.ayfri.com/docs/commands/functions) (command DSL, function layout, tags).
5. [Cookbook](https://kore.ayfri.com/docs/guides/cookbook) (end-to-end recipes).
6. Concepts as needed: [Selectors](https://kore.ayfri.com/docs/concepts/selectors), [Chat Components](https://kore.ayfri.com/docs/concepts/chat-components), [Scoreboards](https://kore.ayfri.com/docs/concepts/scoreboards), [Components](https://kore.ayfri.com/docs/concepts/components).
7. Data-driven: [Tags](https://kore.ayfri.com/docs/data-driven/tags), [Predicates](https://kore.ayfri.com/docs/data-driven/predicates), [Loot Tables](https://kore.ayfri.com/docs/data-driven/loot-tables), [Recipes](https://kore.ayfri.com/docs/data-driven/recipes), [Advancements](https://kore.ayfri.com/docs/data-driven/advancements), [Worldgen](https://kore.ayfri.com/docs/data-driven/worldgen), etc.
8. [Macros](https://kore.ayfri.com/docs/commands/macros) when reusing command fragments.
9. Optional modules: [OOP Utilities](https://kore.ayfri.com/docs/oop/oop-utilities), [Helpers Utilities](https://kore.ayfri.com/docs/helpers/utilities), [Bindings](https://kore.ayfri.com/docs/advanced/bindings).
10. [Known Issues](https://kore.ayfri.com/docs/advanced/known-issues) before debugging odd behavior.

**Migration from hand-written datapacks:** [From Datapacks to Kore](https://kore.ayfri.com/docs/guides/from-datapacks-to-kore).

## LLM-friendly and machine-readable docs

- [llms.txt](https://kore.ayfri.com/llms.txt) (short index)
- [llms-full.txt](https://kore.ayfri.com/llms-full.txt) (expanded)

Point users or tools at these when they want crawlable, consolidated reference outside the website navigation.

## Progressive disclosure rules

- Read `./references/REFERENCE.md` when the user asks for a specific feature area, page path, or topic map.
- Prefer linking only the most relevant docs page first; avoid dumping many links unless explicitly asked.
- Use `llms.txt` or `llms-full.txt` only when broad, machine-readable indexing is requested.

## Quality loop before final answer

Before finalizing guidance:
1. Verify the answer stays inside Java Edition + Kore scope.
2. Verify setup includes dependency and `-Xcontext-parameters` when setup is discussed.
3. Verify all links point to official Kore docs or clearly-labeled template/repo resources.
4. Trim generic Kotlin/Minecraft advice that is not Kore-specific.

## Working inside the Kore repository (contributors)

If the task is **Kore development** rather than **using** Kore: contributor docs live under `website/src/jsMain/resources/markdown/doc/` in the repo. The public site mirrors that content at `https://kore.ayfri.com/docs/...`. For internals (generators, architecture), use [Contributing](https://kore.ayfri.com/docs/contributing/contributing) and [Architecture and Patterns](https://kore.ayfri.com/docs/contributing/architecture-and-patterns).

## Full documentation map

For a complete topic list with links, read [reference.md](./references/REFERENCE.md).
