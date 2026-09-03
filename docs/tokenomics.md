# Tokenomics of `indolent`

Measured 2026-09-03. What this skill actually costs, against [caveman](https://github.com/juliusbrussee/caveman) and [attention-span](https://github.com/alexgreensh/attention-span).

**Headline: `indolent` is not a token-saving layer.** It cuts output 29–40% against default prose, which is *less* than caveman's 41%, and it costs about twice as much to load. What it buys instead is an explicit status word in every row — the thing prose omits when you skim.

![Token comparison](token-comparison.png)

## Method

No Anthropic API credentials were available on the measurement machine, so `messages.count_tokens` could not be used. Instead the OpenAI `cl100k_base` tokenizer was **calibrated against real Claude billing data** already on disk.

| Step | Detail |
|---|---|
| Source | 186 Claude Code session transcripts (`~/.claude/projects/**/*.jsonl`) |
| Grouping | Assistant messages grouped by `requestId`; `output_tokens` is per *request* and covers thinking and tool-call blocks, so ungrouped per-message counts are meaningless |
| Filter | Only requests whose entire response was text — no `thinking`, no `tool_use` — and ≥150 tokens. **n = 864** |
| Result | Claude bills **1.4912×** the `cl100k` count (median). p25 1.445, p75 1.535, aggregate 1.4121 |
| Bias check | Table-heavy replies (n=141) ratio 1.4846; prose replies (n=723) ratio 1.4934. **Difference 0.6%**, so the estimator is not biased for or against tables |

Every figure below is `cl100k × 1.4912`, rounded. Treat them as accurate to a few percent, not exact.

### Fairness control

Two scenarios were written five ways each: a 7-requirement spec audit and a 5-finding code review. Before counting, each rendering was machine-checked against a list of required facts using paraphrase-tolerant regexes, so that "24 hours" and "24h", or "`KeyService.php` at line 88" and "`KeyService.php:88`", both count as present.

All five renderings of both scenarios carry the same facts. The comparison is not measuring one mode dropping content to look cheap.

## Output cost — the number that matters

| Mode | Scenario A | Scenario B | Mean | vs default prose |
|---|---|---|---|---|
| Default prose | 708 | 508 | 608 | baseline |
| caveman `full` | 365 | 336 | 350 | **−41%** |
| `indolent ultra` | 407 | 321 | 364 | **−40%** |
| attention-span (`spartan`) | 416 | 368 | 392 | **−34%** |
| `indolent full` | 470 | 382 | 426 | **−29%** |

**Table markup is the gap.** Pipes, the header rule and bold markers are overhead that prose does not pay. `indolent full` spends them on five columns; `ultra` recovers the difference by shrinking cells, never by dropping rows.

## Input cost

Skills load progressively — the description sits in the system prompt every session, the body loads only on invocation. Output styles have no progressive disclosure: the whole file is resident whenever the style is active.

| Layer | Always resident | On invoke | Total |
|---|---|---|---|
| attention-span `spartan` (style) | 1,259 | — | 1,259 |
| attention-span `spartan-cave` (style) | 1,643 | — | 1,643 |
| attention-span `attention-kind` (style) | 2,492 | — | 2,492 |
| caveman | 154 | 1,763 | 1,917 |
| **`indolent`** | **200** | **3,603** | **3,803** |

`references/examples.md` is a further 1,713 tokens and loads only when a table shape is unclear.

**`indolent`'s `SKILL.md` is the largest of the three**, roughly double caveman's. That is the honest cost of carrying column recipes, a status vocabulary and carve-outs in one file. It is within the Agent Skills recommendation of under 5,000 tokens for a skill body.

## Break-even

| Mode | Saved per reply | Load | Uncached | With prompt cache (~0.1×) |
|---|---|---|---|---|
| attention-span | 216 | 1,259 | 6 replies | 1 reply |
| caveman | 258 | 1,917 | 7 replies | 1 reply |
| `indolent ultra` | 244 | 3,803 | 16 replies | 2 replies |
| `indolent full` | 182 | 3,803 | 21 replies | 2 replies |

## In money, it is noise

On Claude Opus 5 (USD 5 per 1M input, USD 25 per 1M output — output is billed **5×** input), a 40-reply session using `indolent full` instead of default prose saves roughly:

```
output saved   40 × 182 = 7,280 tokens  ×  $25/1M  =  $0.182
input paid              3,803 tokens  ×  $5/1M   =  $0.019
                                              net ≈  $0.16
```

Sixteen cents. **Do not adopt this skill to save money.** Adopt it to stop missing rows.

## What the tables actually buy

Across both scenarios, `indolent` forced an explicit fixed-vocabulary status word (**Met / Partial / Not met / Disputed / Not measured**, **Blocker / High / Med / Low**) into **12 cells**. Default prose, caveman and attention-span produced **zero** — all three encode severity narratively: *"the most serious one"*, *"and this is minor"*, *"this is where things get complicated"*.

A skimmer reads a status column. A skimmer does not reliably read "this is where things get complicated". That difference, not the token count, is the reason to run this skill.

## Can the table markup be made cheaper?

Measured per occurrence, on the same 7-row table.

| Change | Saving | Verdict |
|---|---|---|
| Drop `**` around the status word | 2 tokens per row | Works, and defeats the point — bold is the scanning mechanism |
| Drop outer pipes (`a\|b`, not `\|a\|b\|`) | 1–2 tokens per row | Valid GFM, small win, renderer-dependent |
| Drop spaces around pipes | **0** | ` \|` is already a single token |
| Shorten `---` to `-` in the separator | **0** padded, 6 unpadded | Only pays combined with removing spaces |
| Shorten headers (`Requirement` → `Req`) | **0** | Every variant tokenizes to 11 |
| Glyph `✓` instead of `Met` | **0** | Both 1 token. `✗` costs **2** — a UTF-8 split, worse than the word |
| `\| \|` instead of `\| — \|` for an empty cell | 1 token per row | Blank cells hurt scanning |

Whole-table variants of the same content:

| Variant | Billed | vs current |
|---|---|---|
| Current `indolent full` | 397 | — |
| No outer pipes | 383 | −3.4% |
| No spaces + no outer pipes + minimal separator | 376 | −5.3% |
| Above, plus no bold | 355 | −10.5% |
| 4 columns (merge Gap into Evidence) | 385 | −3.0% |
| **3 columns** | 341 | **−13.9%** |
| No table, one bold line per row | 355 | −10.5% |
| No table, plain `ID: status. evidence` | 313 | −21.1% |

**Column count beats every markup trick by roughly 3×.** Five columns is the tax, not the pipes. The invented-abbreviation lesson applies to markup as well: whitespace and short headers feel like savings and tokenize to nothing.

**Decision: the markup is left alone.** The full micro-optimization saves about 40 tokens per reply, near a tenth of a cent, in exchange for the bold that carries the answer. If a table needs to be cheaper, drop a column — not the syntax.

## Limitations

| Limitation | Effect |
|---|---|
| Proxy tokenizer, not `count_tokens` | Figures are calibrated, not exact. Ratios between modes are more reliable than absolute counts |
| Renderings authored for the test, not sampled from live traffic | Directional, not a controlled trial. The fact-preservation check bounds the main bias but does not remove it |
| Two scenarios | Both are list-shaped findings work — this skill's core case. Says nothing about narrative or exploratory replies |
| Calibration is one machine's traffic | The 1.4912 factor reflects English, markdown-heavy Claude Code output. Other languages or content shapes may differ |
| No historical A/B from real sessions | Session logs could not attribute a mode to a session: the caveman mode log held 3 entries with no session IDs, and the output-style marker appears in only one transcript |

## Reproducing

Requires `tiktoken` and Claude Code session logs.

```python
import json, glob, collections, statistics as st, tiktoken
enc = tiktoken.get_encoding("cl100k_base")

groups = collections.defaultdict(lambda: {"types": set(), "text": "", "ot": 0})
for f in glob.glob('~/.claude/projects/**/*.jsonl', recursive=True):
    for line in open(f, errors='ignore'):
        if '"type":"assistant"' not in line:
            continue
        d = json.loads(line)
        m, rid = d.get('message') or {}, d.get('requestId')
        if not rid or not isinstance(m.get('content'), list):
            continue
        g = groups[(f, rid)]
        for b in m['content']:
            g["types"].add(b.get('type'))
            if b.get('type') == 'text':
                g["text"] += b.get('text', '')
        g["ot"] = max(g["ot"], (m.get('usage') or {}).get('output_tokens') or 0)

# only requests that produced text and nothing else
ratios = [g["ot"] / len(enc.encode(g["text"]))
          for g in groups.values()
          if g["types"] == {"text"} and g["ot"] > 0 and len(enc.encode(g["text"])) >= 150]
print(len(ratios), st.median(ratios))   # -> 864, 1.4912
```

Multiply any `cl100k` count by that median to estimate billed Claude tokens.
