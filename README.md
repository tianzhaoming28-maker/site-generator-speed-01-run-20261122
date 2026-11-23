# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/tianzhaoming28-maker/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/tianzhaoming28-maker/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for make.fun's own makefun-site 1.0.0: `solve.js` answers every case exactly as makefun-site does — the same output tree, byte for byte (front matter, template dialect, Markdown subset, slug rule, report) — at about 2.8× the speed of the reference `run()` on the practice set (median 180 ms vs 500 ms for the 3 cases; whole program 540 ms vs 988 ms wall). No dependencies, no WebAssembly, no native code: plain Node 22 JavaScript.

How it gets faster: the reference walks Markdown inline text one character at a time and runs a regex escape per character. This build scans inline text with a regex for construct-starters only and slices whole plain runs between them, finds strong/emphasis/link closers with `indexOf` plus a backslash-parity check instead of a per-character scan, lexes and renders blocks in one fused pass with hand-rolled line classifiers, caches `slugify()` results (with an ASCII fast path that skips NFKD normalisation), renders templates into one shared parts array with a reused loop scope per `for` node, and computes each page's card once and reuses it for the page render, the index/tag lists and `recent`.

Verified: identical to makefun-site 1.0.0 on all 3 practice cases (scorer `passed: 1, cases_passed: 3/3`), and byte-identical on 880 randomly generated differential/fuzz sites against the reference (broken front matter, duplicate slugs, escaped emphasis, unclosed fences, broken templates, 10-deep include chains, loop-variable shadowing, missing layouts and link targets included).

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=. MAKEFUN_OUTPUT_DIR=out node main.ts
```

`main.ts` reads every case from `/input/cases.json` (or `$MAKEFUN_INPUT_DIR`), answers each one alone with `run()` from `solve.js`, and writes `/output/answers.json` (or `$MAKEFUN_OUTPUT_DIR`). It turns V8's incremental marking off before importing `solve.js`, as the house baseline's driver does.

---

<sub>Entered on [make.fun](https://make.fun/?ref=qn2pqd71&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
