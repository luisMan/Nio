# Why Nio: it finishes the job, checks its own work and remembers it

Most AI coding tools write code for you. Nio delivers **working** code: it builds, runs and checks the project, fixes what fails, and remembers the project the next time you come back.

[![Watch Nio build an app, then recall it the next day](https://nioai.run/media/nio-promo-poster.jpg)](https://nioai.run/media/nio-promo.webm)

<video src="https://nioai.run/media/nio-promo.webm" poster="https://nioai.run/media/nio-promo-poster.jpg" controls width="100%"></video>

*70 seconds, a real recorded run (Nio v0.1.221), not a mockup. Day 1: Nio builds TaskFlow from scratch in 76 s with 8 tests passing. Day 2: "where did we leave off?" is answered from project memory with **0 provider requests and 0 tokens**. Nio then adds priorities and due dates in 55 s, with 14 tests passing.*

---

## The numbers: Nio + LLM vs direct LLM

Nio isn't a model. It works *with* your model, and every comparison below uses the same model both ways: once through Nio, once called directly.

Every figure below comes from a benchmark we ran ourselves and can rerun. The caveats are listed with them.

### 1. Nio + LLM finishes what a direct LLM leaves half-done

**Calculator app benchmark (2026-10-19, Nio v0.1.249, 8 models, 23 runs)**

| | Nio + LLM | Direct LLM (same model) |
| --- | ---: | ---: |
| Complete, fully working apps | **8 of 12** | 1 of 11 |
| Average score | **88** | 67 |
| Cost per *complete* app | **$1.53** | $3.63 |
| Sonnet 5.5, three runs | **100 / 100 / 100** | 76 / 76 / 0 |

Nio + LLM spent more in total, but because it finished far more often, each working app cost **58% less**. It is the third run of this benchmark (October 5: 7 of 12 vs 1 of 12), and Sonnet 5.5 has scored 100 through Nio in all nine runs. [Results and method](benchmarks/2026-10-19-v0.1.249-nio-vs-direct.md).

**Fix a broken site (2026-10-12, Claude Sonnet 4.6 on both sides)**

| | Nio + Sonnet 4.6 | Direct Sonnet 4.6 |
| --- | --- | --- |
| Result | **8/8 checks on the first turn** | 5/8, then 0/8, then 7/8: never passed |
| Cost | $0.64 | $0.66 |

For about the same money, one side delivered a fixed site and the other didn't.

### 2. It keeps getting cheaper

We measure **cost per finished task**, because a cheap answer that doesn't work isn't cheap.

| Release | Cost per finished task (same benchmark) |
| --- | ---: |
| v0.1.235 with the approval fixes | $0.47 |
| v0.1.236 (2026-10-15) | **$0.23** |

That is **half the cost** on the same tasks, with 6 of 6 finished, each after an independent review and a passing build. A new app now costs about **$0.11**. [Results and method](benchmarks/2026-10-15-v0.1.236-cost-per-task.md).

### 3. Your project's memory costs nothing to recall

- Asking "where did we leave off?" the next day is answered locally: **0 requests, 0 tokens, $0.**
- Work that was already verified is reused instead of redone: **$0 and about 1 s** in 4 of 11 repeat runs.

### 4. It handles real-size projects

Measured on 2026-10-09 against generated projects:

| Project | Before | Now |
| --- | ---: | ---: |
| 1,000,000 files | 337 s, 1.1 GB RAM | **19 s, 45 MB** |
| One 4 GB data file | over 4 GB RAM | **36 MB** |
| 100,000 asset files, time to first request | 10 s | **0.4 s** |

---

## Why it works

**It verifies, it doesn't guess.** Nio runs the project's own install, build and tests, checks pages in a real server, and doesn't call a task done until the checks pass. A reviewer then compares the result against what you asked for.

**It remembers.** Decisions, unfinished work and verification results are stored with the project. Tomorrow you continue; you don't re-explain.

**You decide what it may do.** You pick the mode: read-only, ask before every change, or full authority inside the project. Anything that reaches outside the project folder or is destructive still asks you first.

**It sends the hard parts to strong models and the easy parts to cheap ones.** Routing escalates only when a task needs it, so you aren't paying top-model prices for every small step.

**Your keys, your choice.** Use Nio's hosted plans, or bring your own provider keys (BYOK). With BYOK your requests run on your own account, with no markup.

**Any language.** Talk to Nio in the language you work in. It understands meaning through the model, not through an English keyword list.

---

## Where Nio is *not* the best choice (yet)

We'd rather you trust us than be surprised. Measured on Nio v0.1.237 (October 16, 2026) and the v0.1.251 changes (October 20, 2026), 3 runs of each task:

- **Very small edits** cost more through Nio than a single direct model call, because Nio also runs the tests and checks the result. In our v0.1.237 test, a one-line bug fix with Claude Sonnet 5.5 cost about **$0.11 through Nio vs $0.05 direct**; both fixed it every time. A one-line change doesn't need that.
- **Small models** gain little: with the v0.1.251 changes, GPT-5 mini averages **98 through Nio and 98 called directly** on the calculator benchmark, so the same result for about twice the cost ($0.19 vs $0.09 per run). Claude Haiku 4.5 does gain: **96 through Nio vs 48 direct**.
- **GPT-5.1** still does better called directly: **95 vs 78 through Nio**, which often plans instead of making the changes. We're working on it. [October 20 results](benchmarks/2026-10-20-v0.1.251-small-models.md).
- **Nio is slower than a single model reply**, because it actually runs your builds and tests: that one-line fix took about **30 s through Nio vs 6 s direct**. You're paying for a verified result, not for the first draft.

Nio pays off on real work: fixing a broken site, building a feature end to end, coming back to a project the next day.

---

## Try it in two minutes

**macOS / Linux**

```bash
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/luisMan/Nio/releases/latest/download/nio-installer.sh | sh
```

**Windows (PowerShell)**

```powershell
powershell -c "irm https://github.com/luisMan/Nio/releases/latest/download/nio-installer.ps1 | iex"
```

Then give it something real: a bug you've been putting off, or the app you've been meaning to build. Ask it again tomorrow and see what it remembers.

- Website and plans: **[nioai.run](https://nioai.run)**
- Downloads and release notes: **[github.com/luisMan/Nio/releases](https://github.com/luisMan/Nio/releases)**
- Questions: **[support@nioai.run](mailto:support@nioai.run)**

### More videos

| Format | Link |
| --- | --- |
| Full promo, 16:9 with music | [nio-promo.webm](https://nioai.run/media/nio-promo.webm) |
| Social cut, 16:9 | [nio-promo-social-16x9.webm](https://nioai.run/media/nio-promo-social-16x9.webm) |
| Reel / Shorts, 9:16 | [nio-promo-reel-9x16.webm](https://nioai.run/media/nio-promo-reel-9x16.webm) |

---

<sub>How we measured: results and method for each benchmark are in [benchmarks/](benchmarks/). Costs are list-price estimates of model usage. The calculator rubric checks source patterns, and its direct baseline was one-shot with no tool loop. The fix-site and build-app checks are browser checks on the running app. Results vary from run to run; three runs of the same task can differ by about ±25% in cost.</sub>
