# Ramp — Devpost draft (review before submit)

> **Pitch:** axe detects *presence*; Ramp understands *meaning* — and ships merge-ready fixes.

---

## Inspiration

Over 1.3 billion people live with disabilities, yet most web apps still ship code that blocks them at the door. Industry tools like axe-core and Lighthouse are indispensable for catching mechanical WCAG violations — missing alt attributes, empty button names, contrast failures — but they stop at **"does a name exist?"** They cannot judge whether `alt="image"` or `aria-label="button"` actually helps a blind user. Roughly 70% of accessibility work requires human-level semantic judgment that rule engines were never built to do — and none of these tools close the loop with a fix.

---

## What it does

Ramp does two things axe cannot:

**① Semantic quality review.** On the same page where axe reports **0 issues**, Ramp flags garbage-but-present accessible names — `alt="image"`, `alt="DSC_0042.JPG"`, `aria-label="button"`, link text `"click here"` — explains *why* they fail a screen reader, and proposes meaningful replacements (`"Acme company logo"`, `"Search"`, `"Learn more about our services"`).

**② Automated fix loop.** Audit → Claude Code applies minimal patches → axe re-verifies → Ramp opens a **merge-ready GitHub PR** with before/after scores, axe counts, screen-reader transcripts, and diffs. We don't ship PDF reports; we ship code.

**A11y-Bench (51 tasks, 15 html-live)** grounds everything: real accessibility-fix PRs from GitHub, fully annotated pages with decoys, covering contrast, landmarks, forms, icons, and semantic naming.

**One line:** axe detects; Ramp understands and fixes.

---

## How Claude Code powered Ramp

Claude Code is the core agent in our fix loop. For each finding — whether a mechanical WCAG violation or a semantic name-quality issue — Claude Code reads the evidence, edits the smallest correct diff, and self-validates before we open a PR. It also helped us parallelize building the monorepo: harness instrumentation, control-plane orchestration, and dashboard wiring shipped in the same sprint because Claude Code could work across packages simultaneously. For the Codebreaker track, Claude Code is not a chat sidebar — it is the engineer that turns audit output into shippable pull requests.

---

## How we built it

- **TypeScript monorepo** — `packages/harness`, `packages/bench`, `packages/scoring`, `packages/control-plane`, `apps/dashboard`
- **Playwright + axe-core** for rendered-page rule checks; **semantic review layer** (LLM) for accessible-name quality
- **A11y tree serialization, screen-reader simulation, live contrast measurement** so models audit like experts, not like grep
- **Vercel AI SDK** for multi-provider audit calls; **Drizzle + SQLite** for run/score persistence
- **React + Vite + Tailwind dashboard** for live runs, Ramp-vs-axe comparison, and PR cards
- **Claude Code headless** for fixes; **Octokit** to branch, commit, and open PRs

---

## The axe vs Ramp proof (same page)

On `garbage-names.html`:

| Tool | Verdict |
|------|---------|
| **axe-core** | **0 issues** — every element has *some* accessible name |
| **Ramp** | **5 semantic issues axe cannot see** — meaningless names that pass rules but harm blind users |

Each Ramp finding includes a concrete fix suggestion. After the fix loop, axe still shows 0 (it never saw the problem) — but Ramp's semantic review drops from 5 → 0, and screen-reader output goes from `"image, button, click here"` to descriptive names.

---

## Real fix PRs (4 demos)

1. **`bad.html` WCAG repair** — compliance **60 → 96**, axe violations **5 → 0**, screen reader: `"image, button, button"` → `"image: Acme logo, button: Search, button: Add"`. ([Ramp PR #7](https://github.com/yangzhang75/Ramp/pull/7))
2. **Real open-source project (aigov-ops)** — compliance **92 → 100** on a production fork, not a toy fixture.
3. **Semantic-only fixture (`garbage-names.html`)** — axe **0 → 0** (unchanged — axe is blind), Ramp semantic issues **5 → 0**, with merge-ready PR and diffs.
4. **[PR URL — confirm with teammate]** — fourth end-to-end demo PR from the fix pipeline.

---

## Benchmark & evaluation methodology

We built **51 benchmark tasks** from real GitHub accessibility-fix PRs (15 rendered html-live pages with full gold annotations and decoys).

**Why not score on single-PR diffs alone?** Auditing one PR punishes thoroughness — a model that finds every real issue on a page also gets penalized for reporting problems *outside* the PR's changed lines. That rewards under-reporting, not good accessibility engineering.

**Our approach:** measure **precision** (and recall) on **fully annotated pages** where every real violation and every decoy is labeled. On 10 complex fixture pages (gpt-4o-mini, naked vs harness, averaged over 3 runs):

- **Harness precision: 84%** — models equipped with a11y tree, screen-reader output, and contrast probes report fewer false positives than raw LLM audit.
- Recall is reported honestly; our product value is **semantic judgment + fix delivery**, not inflating detection counts.

---

## Challenges

- Stitching audit, semantic review, sandboxed rendering, Claude Code, and GitHub into one reproducible loop — each step has different failure modes.
- Learning that raw **recall** on mechanical rules is the wrong north star (axe already wins presence checks). The gap is **meaning** and **merge-ready fixes**.
- Designing benchmarks that reward real engineering: full-page annotations instead of partial-PR gold labels.

---

## Accomplishments & what we learned

- **"axe checks presence; only language models judge meaning."** Rule engines and Ramp are complementary — Ramp catches what axe silently passes.
- **"The PR is the correct interface for accessibility work."** Developers already review, CI-test, and merge via GitHub; Ramp meets them there.
- **"Value is not how much you report — it is precision plus fix delivery."** We optimize for merge-ready outcomes, not alarm volume.
- Shipped **two score jumps on real code** (60→96, 92→100) and a semantic demo where axe never flinched.

---

## What's next

- Wire Ramp into CI so newly disclosed a11y issues on real repos auto-reproduce, fix, and PR upstream.
- Expand source-code-layer auditing (benchmark tasks already seeded).
- Partner integrations: block merge on Ramp semantic review, not just axe presence.

---

## Built with

playwright · axe-core · claude-code · anthropic · openai · vercel-ai-sdk · react · vite · tailwind · drizzle · sqlite · typescript · octokit · github

---

## Submission checklist (internal)

- [ ] Replace PR #4 placeholder URL
- [ ] Confirm 84% precision number matches final benchmark run
- [ ] Add team members on Devpost
- [ ] Pick track: TOOLBOX or WORLD
- [ ] Attach dashboard screenshot (Ramp vs axe page) + PR screenshots
