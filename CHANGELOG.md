# Changelog

All notable changes to this project are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow [SemVer](https://semver.org/).

## [1.0.2] - 2026-10-08

Folds four edits that existed only in the local install at `~/.claude/skills/indolent` back into the repo, so the published skill and the installed one stop diverging.

### Changed
- `description` uses the shorter trigger phrasing ("asks for output as a table or matrix, asks to be concise, asks for a diagram, says there is too much text") instead of a quoted keyword list, and still names the 1.0.1 capabilities.
- Prose length is governed by its point, not by a sentence count: "Keep a prose block to the one point its bold lead-in carries" replaces "at most 2 sentences". The anti-drift pre-send check follows suit, so it no longer reimposes the cap it was meant to enforce.
- `ultra` cells are "the shortest fragment that stays unambiguous" rather than "at most 8 words". A word count was the wrong instrument: eight words of identifiers can be clearer than four of prose.
- The compression carve-out names commit messages in its heading list, and states explicitly that they are the exception to the structure half of the rule — tables are allowed for everything else in that list, never for a commit message.
- Worked audit example moved from `SKILL.md` into `references/examples.md`, which is now the single home for examples. The skill body holds rules only: 4,654 estimated Claude tokens against the 5,000-token Agent Skills guidance, 346 to spare, down from 15 at 1.0.1.
- `docs/tokenomics.md` and `README.md` carry the re-measured figures: 252 resident plus 4,654 on invoke, break-even 21 replies at `ultra` and 27 at `full` uncached.

## [1.0.1] - 2026-10-08

Version jumps from `0.1.0` to `1.0.1`; `1.0.0` was never released.

### Added
- **Concise section**: response-level brevity rules on top of the existing word-level ones. Cuts preamble, intent narration, question restatement, filler phrases, recap of work just done, praise/apology/hedging and closing offers. Artefacts authored inside a reply (README sections, plans, PR bodies) obey the same density; carve-outs still override.
- **Anti-drift mechanism** for long sessions: a five-item pre-send check applied to every reply, plus a drift-symptom table with the correction for each. States explicitly that a context compaction, conversation summary, subagent return, long tool chain or topic switch does not end the mode, and that an unrecallable level falls back to the last one named, else `full`.
- **Mermaid support** for relationships too complex for ASCII. A selection table maps shape to form (linear path and ≤8 nodes stays ASCII; branching, loops, >8 nodes, state machines, 3+-actor sequences and ER shapes go to Mermaid), with Mermaid authoring rules: `LR` for pipelines, `TD` for hierarchy, labelled conditional edges, quoted node text, no styling, 15-node cap, so-what line beneath.
- Carve-out for **reasoning that must be read as a chain** (root-cause derivation, proof, dependent trade-off argument): table the conclusion, keep the chain as prose.
- `references/examples.md`: branching Mermaid `flowchart` and a `stateDiagram-v2`, alongside the existing ASCII path.

### Changed
- Diagram worked examples moved from `SKILL.md` into `references/examples.md`, keeping the skill body under the 5,000-token Agent Skills guidance (now 4,985 estimated Claude tokens, up from 3,603).
- Skill `description` now names concise output, Mermaid and session persistence so the mode triggers on "be concise" and "diagram this".
- `docs/tokenomics.md`: input-cost and break-even tables re-measured for this version; output-cost study unchanged from 2026-09-03.

## [0.1.0] - 2026-09-03

### Added
- `indolent` skill: table-first, diagram-when-relational output mode with `lite`, `full`, `ultra`, `off` levels.
- Eleven column recipes and a fixed status vocabulary (Met / Partial / Not met / Disputed / Not measured; ✓ ✗ —).
- Carve-outs: word-compression off for security findings, audit evidence, approval records, QA reports, destructive-action warnings and order-sensitive procedures; commit messages never tabulated.
- `references/examples.md` with fictional worked table shapes.
- Claude Code plugin manifest so the repo installs as a plugin or via `npx skills add`.
- `docs/tokenomics.md`: measured token study against caveman and attention-span, with a calibrated tokenizer, break-even analysis and a table-markup micro-optimization study, plus `docs/token-comparison.png`.
