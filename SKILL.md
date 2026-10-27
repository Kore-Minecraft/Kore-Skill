---
name: kore-user-guide
description: Teaches and guides use of Kore, the Kotlin library for generating Minecraft Java Edition datapacks. Covers Gradle setup, the Kore Gradle plugin, modules (kore, oop, helpers, bindings), DSL patterns, generation APIs, API renames between releases, and where to read official documentation. Use when the user works with Kore, io.github.ayfri.kore, Minecraft datapacks in Kotlin, or asks how to build or structure Kore projects without browsing the full Kore repository.
license: GPL-3.0-only
metadata:
  author: Ayfri
  tags: kotlin,minecraft,datapack,kore,game-dev
  triggers: kore,datapack,minecraft function,mcfunction,kotlin
  version: "1.1.0"
---

# Kore user guide (for assistive teaching)

Kore is a **Kotlin Multiplatform** library (JVM and JS) for **Minecraft Java Edition** datapacks: type-safe builders for functions, JSON data, commands, selectors, and worldgen. Each release targets exactly one Minecraft version, the latest at release time; older game versions use the last Kore release made for them (see the [Version Matrix](https://kore.ayfri.com/docs/home#version-matrix)). Bedrock is out of scope.

The agent should **not** assume the user has the Kore monorepo checked out. Prefer **official docs** and **Maven artifacts**. Use the [documentation index](./references/REFERENCE.md) for page-by-page lookup.

## Activation boundaries

Use this skill when the user asks about:
- Kore project setup or dependency configuration.
- Building datapacks in Kotlin with Kore DSL.
- Choosing between `kore`, `oop`, `helpers`, and `bindings`.
- Code that stopped compiling after a Kore update.
- Finding official Kore docs for a concrete feature.

Do not use this skill as the primary guidance for:
- Bedrock edition tooling.
- Vanilla datapack-only workflows with no Kotlin/Kore intent.
- General Kotlin language teaching unrelated to Kore.

## Default response procedure

Follow this order unless the user already provided a narrower request:
1. Confirm target: user wants **Kore for Java datapacks**, and which Minecraft version.
2. Provide minimal setup (Gradle plugin + dependency + JDK toolchain).
3. Give a small, runnable DSL skeleton (`dataPack`, `function`, `generate*`).
4. Route to exactly one or two official docs pages for next depth.
5. Add optional module guidance (`oop`/`helpers`) only if needed.

## Versions

Every artifact is published as `<kore version>-<minecraft version>`, e.g. `2.28.0-26.3` is Kore 2.28.0 for Minecraft 26.3, and `2.12.0-26.2-snapshot-8` targets a snapshot. It is not plain semver: split on the first `-`. A new second number (`2.27.0 → 2.28.0`) can break the API. Look up the current release in the Version Matrix or on [GitHub releases](https://github.com/Ayfri/Kore/releases) rather than guessing one. Details: [Version Support](https://kore.ayfri.com/docs/guides/version-support).

## Prerequisites checklist

1. **JDK 25** (`jvmToolchain(25)`): Kore is compiled for Java 25, the version Minecraft 26.x runs on.
2. **Kotlin 2.4+**: Kore relies on context parameters, stable since 2.4, so no compiler flag is needed.
3. **Gradle** with the wrapper, and the dependency `implementation("io.github.ayfri.kore:kore:<VERSION>")` (start with the `kore` module only).
4. Recommended: the [Kore Gradle plugin](https://kore.ayfri.com/docs/guides/gradle-plugin) `io.github.ayfri.kore` (same version string), which runs the pack, copies it into worlds and reloads a server.
5. Fastest start: [Kore Template](https://github.com/Kore-Minecraft/Kore-Template), already set up with all of the above.

```kotlin
plugins {
	kotlin("jvm") version "2.4.20"
	id("io.github.ayfri.kore") version "<VERSION>"
}

dependencies {
	implementation("io.github.ayfri.kore:kore:<VERSION>")
}

kotlin {
	jvmToolchain(25)
}

kore {
	mainClass = "MainKt"
	packName = "my_pack" // must match the folder the pack is written to
	worlds = listOf("My World") // `gradlew koreWorlds` lists them
}
```

`gradlew koreRun --continuous` then regenerates the pack and copies it into the worlds on every save.

**Snapshots:** Sonatype snapshot repo plus `<VERSION>-SNAPSHOT` when the user needs unreleased fixes (see [Getting Started](https://kore.ayfri.com/docs/getting-started)). Never for a pack the user ships.

## Gotchas

- An old Kotlin (below 2.4) or JDK (below 25) gives confusing compile or class-version errors: check both first.
- The plugin does not add the Kore dependency, declare both, with the same version.
- `function("...")` path is logical datapack path, not a filesystem path with `.mcfunction` extension.
- Start with `kore` only. Adding `oop` and `helpers` too early increases API noise and weakens guidance quality.
- If behavior seems undocumented, check [Known Issues](https://kore.ayfri.com/docs/advanced/known-issues) before proposing workarounds.
- Snippets from older blog posts, issues or model memory may use renamed APIs (see below). Trust the current docs over them.

## API renames to know (2.28.0)

2.28.0 (Minecraft 26.3) renamed a lot to match the game. When the user's code uses a left-column name, it was written for an older release: suggest the right column and link the [migration notes](https://github.com/Ayfri/Kore/releases/tag/v2.28.0-26.3), which have a before/after for every change.

| Before | Since 2.28.0 |
|--------|--------------|
| `conditions { }` (loot tables, pools, entries, item functions, triggers) | `condition { }`, several conditions become one `all_of` |
| `functions { }` in loot tables, pools and entries | `modifier { }` |
| `constant(1f)` / `uniform(1f, 5f)` on int fields | int providers: `constant(1)`, `uniform(1, 5)`, or a plain value: `pool(1..3)`, `setCount(1..5)`, `maxUses(12)` |
| `sum(...)`, `scoreNumber(...)`, `storageNumber(...)` | `add(...)`, `score(...)`, `intStorage(...)` / `floatStorage(...)` |
| `blockStateProperty(block) { this["level"] = "0" }` | `matchBlock(block) { state("level", "0") }` |
| `LootTableType.CHEST` | `ContextKeySets.CHEST` |
| `ConfiguredFeatures.X`, `ConfiguredFeatureArgument` | `Features.X`, `FeatureArgument` |
| `configuredCarvers { }`, `configuredCarver(...)` | `carvers { }`, `carver(...)` |
| `surfaceRules { }` | `materialRules { }` |
| `randomOffset(...)` placement | `offset(...)`, one int provider per axis |
| `smelting("x") { cookingTime = 200 }` | `smelting("x", cookingTime = 200) { }` |
| `givenItemModifiers { }` (villager trades) | `givenItemModifier { }` |

Prefer the plain-value overloads (`count(10)`, `rolls(1..3)`) in new code: they are what the docs show.

## Installable modules (pick by need)

| Module      | Coordinates `io.github.ayfri.kore:` | Role |
|------------|--------------------------------------|------|
| `kore`     | `kore`                               | Core DSL: datapacks, functions, data-driven JSON, commands. **Always the first dependency.** |
| `oop`      | `oop`                                | Higher-level gameplay: entities, teams, scoreboards, timers, boss bars, spawners, game state, events. |
| `helpers`  | `helpers`                            | Cross-cutting utilities: menus, sidebars, NPCs and conversations, raycasts, renderers, scoreboard math, state delegates, scheduler, VFX. |
| `bindings` | `bindings`                           | Experimental: import existing datapacks and generate Kotlin bindings. |

Add `oop` / `helpers` only when the user's feature set justifies the extra API surface.

## Mental model (minimum viable understanding)

- **Entry:** `dataPack("namespace_id") { ... }` builds a `DataPack`.
- **Functions:** `function("path/without_mcf_extension") { ... }` emit `.mcfunction` files; commands and helpers live in these blocks.
- **Output:** `DataPack` exposes `generate()`, `generateZip()`, `generateJar()` on the JVM and Node, and `generateZipBytes()` in the browser (see [Creating a Datapack](https://kore.ayfri.com/docs/guides/creating-a-datapack) and [Multiplatform](https://kore.ayfri.com/docs/advanced/multiplatform)).
- **Style:** heavy use of **lambdas** and **extension functions** on `DataPack` to split large packs into register functions (documented in Getting Started).

## Suggested order for help or self-study

1. [Getting Started](https://kore.ayfri.com/docs/getting-started) (project, first pack, `pack {}`, `load` patterns).
2. [Gradle Plugin](https://kore.ayfri.com/docs/guides/gradle-plugin) (build, link into worlds, reload loop).
3. [Creating a Datapack](https://kore.ayfri.com/docs/guides/creating-a-datapack) (lifecycle, `generate*` APIs, structure).
4. [Configuration](https://kore.ayfri.com/docs/guides/configuration) (JSON formatting, dev-friendly output).
5. [Commands](https://kore.ayfri.com/docs/commands/commands), [Execute](https://kore.ayfri.com/docs/commands/execute) and [Functions](https://kore.ayfri.com/docs/commands/functions) (command DSL, function layout, tags).
6. [Cookbook](https://kore.ayfri.com/docs/guides/cookbook) (end-to-end recipes).
7. Concepts as needed: [Selectors](https://kore.ayfri.com/docs/concepts/selectors), [Chat Components](https://kore.ayfri.com/docs/concepts/chat-components), [Scoreboards](https://kore.ayfri.com/docs/concepts/scoreboards), [Components](https://kore.ayfri.com/docs/concepts/components), [Runtime Logic](https://kore.ayfri.com/docs/concepts/runtime-logic).
8. Data-driven: [Tags](https://kore.ayfri.com/docs/data-driven/tags), [Predicates](https://kore.ayfri.com/docs/data-driven/predicates), [Loot Tables](https://kore.ayfri.com/docs/data-driven/loot-tables), [Recipes](https://kore.ayfri.com/docs/data-driven/recipes), [Advancements](https://kore.ayfri.com/docs/data-driven/advancements), [Worldgen](https://kore.ayfri.com/docs/data-driven/worldgen), etc.
9. [Macros](https://kore.ayfri.com/docs/commands/macros) when reusing command fragments.
10. Optional modules: [OOP Utilities](https://kore.ayfri.com/docs/oop/oop-utilities), [Helpers Utilities](https://kore.ayfri.com/docs/helpers/utilities), [Bindings](https://kore.ayfri.com/docs/advanced/bindings).
11. [Debugging](https://kore.ayfri.com/docs/guides/debugging) and [Known Issues](https://kore.ayfri.com/docs/advanced/known-issues) before debugging odd behavior.

**Migration from hand-written datapacks:** [From Datapacks to Kore](https://kore.ayfri.com/docs/guides/from-datapacks-to-kore).

## LLM-friendly and machine-readable docs

- [llms.txt](https://kore.ayfri.com/llms.txt) (short index)
- [llms-full.txt](https://kore.ayfri.com/llms-full.txt) (expanded)
- Every doc page has a Markdown copy at its path plus `.md`, e.g. `https://kore.ayfri.com/docs/getting-started.md`.

Fetch the `.md` copy of a page to read its current API instead of relying on memory. Point users or tools at `llms.txt` and `llms-full.txt` when they want crawlable, consolidated reference outside the website navigation.

## Progressive disclosure rules

- Read `./references/REFERENCE.md` when the user asks for a specific feature area, page path, or topic map.
- Prefer linking only the most relevant docs page first; avoid dumping many links unless explicitly asked.
- Use `llms.txt` or `llms-full.txt` only when broad, machine-readable indexing is requested.

## Quality loop before final answer

Before finalizing guidance:
1. Verify the answer stays inside Java Edition + Kore scope.
2. Verify setup includes the dependency, JDK 25 and Kotlin 2.4+ when setup is discussed.
3. Verify no snippet uses a name from the left column of the renames table.
4. Verify all links point to official Kore docs or clearly-labeled template/repo resources.
5. Trim generic Kotlin/Minecraft advice that is not Kore-specific.

## Working inside the Kore repository (contributors)

If the task is **Kore development** rather than **using** Kore: contributor docs live under `website/src/jsMain/resources/markdown/doc/` in the repo. The public site mirrors that content at `https://kore.ayfri.com/docs/...`. For internals (generators, architecture), use [Contributing](https://kore.ayfri.com/docs/contributing/contributing) and [Architecture and Patterns](https://kore.ayfri.com/docs/contributing/architecture-and-patterns).

## Full documentation map

For a complete topic list with links, read [reference.md](./references/REFERENCE.md).
