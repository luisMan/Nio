# Nio vs direct provider calls — October 5, 2026

We gave the same coding task to eight models two ways: through Nio, and as a
direct call to the provider's API. **Nio produced a complete app in 7 of 12
attempts; direct calls produced one in 1 of 12.** Nio cost about 2.8 times as
much in total, and less per complete app.

- Nio version: v0.1.208 (the Haiku fix found here ships in v0.1.209)
- 24 arms across 8 models; Sonnet 5.5 and GPT-5 mini ran 3 times each
- Total API spend for the whole study: $18.76

## Summary

| | Through Nio | Direct call |
| --- | ---: | ---: |
| Complete apps (score 100) | **7 of 12** | 1 of 12 |
| Average score (out of 100) | **94** | 78 |
| Total cost | $13.82 | $4.94 |
| Cost per complete app | **$1.97** | $4.94 |

## Results by model

Scores are out of 100; 100 means every requirement was met. "Tests" is the
app's own `npm test`, re-run independently after the benchmark. Costs use list
prices and include prompt-cache reads and writes.

| Model | Mode | Runs | Score | Tests | Cost | Time |
| --- | --- | ---: | --- | --- | ---: | ---: |
| Claude Opus 5.5 | Nio | 1 | **100** | 50/50 pass | $4.15 | 22.6 min |
| Claude Opus 5.5 | Direct | 1 | 0 | no app: response cut off at 32K tokens | $0.66 | 4.4 min |
| Claude Sonnet 5.5 | Nio | 3 | **100, 100, 100** | all pass | $1.00 avg | 7.4 min avg |
| Claude Sonnet 5.5 | Direct | 3 | 69, 69, 76 | 2 of 3 runs fail tests | $0.62 avg | 6.6 min avg |
| Claude Sonnet 4.6 | Nio | 1 | **100** | 99/99 pass | $1.55 | 16.0 min |
| Claude Sonnet 4.6 | Direct | 1 | 72 | 77/77 pass | $0.34 | 10.4 min (timed out) |
| GPT-5 mini | Nio | 3 | 100, 100, 82 | 1 run fails 1 test | $0.23 avg | 14.6 min avg |
| GPT-5 mini | Direct | 3 | 94, 94, **100** | all pass | $0.09 avg | 5.0 min avg |
| GPT-4.1 mini | Nio | 1 | 88 | 17/17 pass | $0.12 | 2.6 min |
| GPT-4.1 mini | Direct | 1 | 93 | test suite crashes | $0.06 | 2.5 min |
| GPT-5.1 | Nio | 1 | 88 | 1 failing test | $0.65 | 7.8 min |
| GPT-5.1 | Direct | 1 | 93 | 8 of 18 fail | $0.57 | 4.8 min |
| Claude Haiku 4.5 | Nio | 1 | 78 | 42/46 pass | $2.21 | 39.7 min |
| Claude Haiku 4.5 | Direct | 2 | **96**, 85 | 1 run fails 2 tests | $0.59 avg | 9.8 min avg |
| Nio auto (mixed models) | Nio | 1 | 93 | 22/22 pass | $1.46 | 8.3 min |

## Where Nio helps

- **Strong models finish the job.** With Sonnet 5.5, Sonnet 4.6 and Opus 5.5,
  Nio delivered a complete app on every run. The direct call never did.
- **Large tasks.** A direct Opus 5.5 call ran out of room in one 32K-token
  response and returned no app. Nio builds the app in steps.
- **Verification.** Nio runs the tests and repairs failures. Two direct apps
  shipped with failing or crashing test suites.
- **Repeated requests are free.** When the identical feature request was sent
  again, Nio answered in 1.6 seconds for $0 in 4 of the 7 arms that finished at
  100: it recognized the work was already done and re-ran `npm test` to confirm.
  Direct calls paid $0.01–0.17 and 12–158 seconds for each repeat.
- **Prompt caching.** 78–81% of Nio's Opus 5.5 and Sonnet 5.5 input was served
  from the provider's prompt cache, billed at a tenth of the input price.

## Where it doesn't, yet

- **Small, cheap models.** On GPT-5 mini, Nio was not better than a direct
  call, at 2.6 times the cost and 3 times the time.
- **Claude Haiku 4.5** scored lower through Nio (78 for $2.21) than direct
  (85 and 96 for under $0.80).
- **Speed.** Nio took longer in almost every pair, up to 23 minutes against 4
  for Opus 5.5.

## Bug found and fixed

Nio's Runner role list named Claude Haiku 4.5 by an id that never resolved,
so automatic mode could not pick Haiku for that role, and selecting it by that
id returned an error. Fixed in v0.1.209.

## How it was run

1. Task: build a dependency-free Node.js calculator web app, then add operator
   precedence and parentheses, scientific functions, memory keys, a persisted
   history, a theme toggle, accessibility and unit tests.
2. Every arm received the build, the enhancement, two repair rounds, and one
   identical repeat of the enhancement.
3. Nio arms ran through a local Nio gateway with disposable Pro accounts.
   Direct arms sent the same prompts to the provider API and wrote the files
   from the response.
4. The score comes from the benchmark rubric: required files, features found in
   the source, and whether the generated tests pass.

## Limits of this data

- Most models ran once. Only Sonnet 5.5 and GPT-5 mini have three runs each.
- The rubric checks source patterns and can miss or misjudge features.
- The direct baseline is the model answering in one response with no tool
  loop. It measures the raw model, not another agent.
- Nio's automatic mode mixes several models, so it has no direct counterpart.
- Earlier comparisons (September 17 and 21, Nio v0.1.172) found no advantage for
  Nio. These results are for v0.1.208 and later.
