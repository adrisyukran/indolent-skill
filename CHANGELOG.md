# Changelog

All notable changes to this project are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow [SemVer](https://semver.org/).

## [1.0.1] - 2026-10-08

Version jumps from `0.1.0` to `1.0.1`; `1.0.0` was never released.

### Added
- **Concise section**: response-level brevity rules on top of the existing word-level ones. Cuts preamble, intent narration, question restatement, filler phrases, recap of work just done, praise/apology/hedging and closing offers. Artefacts authored inside a reply (README sections, plans, PR bodies) obey the same density; carve-outs still override.
- **Anti-drift mechanism** for long sessions: a five-item pre-send check applied to every reply, plus a drift-symptom table with the correction for each. States explicitly that a context compaction, conversation summary, subagent return, long tool chain or topic switch does not end the mode, and that an unrecallable level falls back to the last one named, else `full`.
- **Mermaid support** for relationships too complex for ASCII. A selection table maps shape to form (linear path and ≤8 nodes stays ASCII; branching, loops, >8 nodes, state machines, 3+-actor sequences and ER shapes go to Mermaid), with Mermaid authoring rules: `LR` for pipelines, `TD` for hierarchy, labelled conditional edges, quoted node text, no styling, 15-node cap, so-what line beneath.
- Carve-out for **reasoning that must be read as a chain** (root-cause derivation, proof, dependent trade-off argument): table the conclusion, keep the chain as prose.
- `references/examples.md`: branching Mermaid `flowchart` and a `stateDiagram-v2`, alongside the existing ASCII path.

### Changed
- Diagram worked examples moved from `SKILL.md` into `references/examples.md`, keeping the skill body under the 5,000-token Agent Skills guidance (now 4,979 estimated Claude tokens, up from 3,603).
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
