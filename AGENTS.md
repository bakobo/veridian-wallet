<!-- Local-only file (gitignored). Not for upstream contribution. -->

## Contributing upstream: keep `.gitignore` out of PRs

When raising PRs from this Bakobo fork to upstream (`cardano-foundation/veridian-wallet`),
**do not include `.gitignore`** as long as its only differences from `upstream/main` are the
local tooling entries (`/.tick`, `.tick.lock`, `AGENTS.md`). Those exist only to hide local
task-tracking and coding-agent artifacts and should never leak into an upstream PR. If
`.gitignore` ever gains a genuinely shareable change, contribute *that* line specifically —
not the whole local diff.

## Development policy: strict TDD + coverage ratchet (MANDATORY)

**All feature work and bugfix work in this repo MUST use true TDD** — no exceptions:

1. **Red** — write a *failing* test first that captures the desired behavior/bug.
2. **Green** — write the minimum implementation to make that test pass.
3. **Refactor** — clean up with the test staying green.

Never write implementation code before a failing test for it exists.

**Coverage is a one-way ratchet:**

- **Never lower coverage.** Every change that adds code adds tests.
- The enforced floor is jest's `coverageThreshold` in `jest.config.ts`. It may only
  ever go **up** — as real coverage rises, raise the threshold toward it so the floor
  ratchets and can't regress. Current enforced floor: statements 80 / branches 50 /
  functions 50 / lines 80.
- Baseline *actual* coverage numbers are recorded in a `tick` so we can check back and
  ratchet the threshold upward over time.

<!-- >>> tick stanza >>> (managed by `tick init`) -->

## Task tracking: `tick`

This repo tracks tasks, tech debt, and ideas in a local [`tick`](https://github.com/dhh1128/tick)
ledger (an orphan `tick` branch; the `tick` CLI is the interface). Reads are plain
files — do **not** use an external API for task tracking.

- **First, if a `tick` command says the repo isn't initialized**, run `tick init`
  once to connect this clone to the ledger — it adopts the existing remote ledger
  if a colleague already set one up, or creates a new one otherwise.
- **A tick mark is the sigil `~` immediately followed by a digit-first 4-char
  base32 id** (the id part looks like `4mz3`, so the full mark is that id with a
  leading `~`). It pins a tick to a code location.
- **Before editing a file**, grep it for marks and read what they reference:
  `rg '~[2-7][a-z2-7]{3}\b' <file>` then `tick show <id>`. A mark means recorded
  context exists for that spot — read it first.
- **Search** existing ticks with `tick grep <text>`; **list** with `tick ls`.
- **Capture** new work with `tick add "<title>"` and place the printed mark
  (`~` + the new id) at the relevant code spot.
- When your change **resolves** a tick, run `tick off <id>` and **delete the
  mark(s)** it reports still in the code.

<!-- <<< tick stanza <<< -->
