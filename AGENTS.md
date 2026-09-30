# AGENTS.md — agent context for svx-parityrun

**What this repo is.** The pre-build product repo for SVX gap-registry
row 13 — a vendor-neutral capture→replay→deterministic-compare harness
for legacy (mainframe-first) migrations, sold to fixed-price systems
integrators. The complete work orders live in
[`AGENT-GOALS.md`](AGENT-GOALS.md); the evidence case and verification
status live in the [README](README.md). Both were written 2026-09-30
from three kill-search passes (R8, R13b, V1) in the
[research repo](https://github.com/srivtx/svx-research) — the whole
story of how this repo came to exist is that repo's `AGENT-MISSION.md`.

## The three facts that survive any context switch

1. **Determinism is the product** — every goal, test, and report in
   this repo reduces to "same inputs → byte-identical outputs."
2. **Never translate code** — parityrun compares behavior; the
   translation/copilot/inventory categories are occupied and were
   killed in research.
3. **The buyer is the SI** — everything user-facing should read like
   it was written for a delivery lead carrying fixed-price risk, not
   for an agency and not for a developer admiring the COBOL.

## Working rules

- **Tests-first.** Every feature lands with a test that fails without
  it. Fixtures are checked in; CI (GitHub Actions) runs on every push
  from the first commit and must stay green.
- **Python 3.10+, std-lib only at runtime.** Lint = ruff, types =
  mypy, both clean before merge. The evalgate sibling
  (svx-evalgate) is the house reference for CI shape, report
  formats, and version discipline.
- **Versioning is conservative and owner-gated.** 0.x until the
  harness runs a real JCL capture-replay end to end; minors batch
  capabilities; majors are earned in production, never announced.
  Docs-only commits never bump.
- **Evidence discipline.** Product claims cite the research reports
  or checked-in artifacts. Marketing numbers that lack a source get
  deleted, not softened.
- **Gates are gates.** `AGENT-GOALS.md` Goal 0 (build gates) must be
  resolved before code; Goal 3 is gated behind its own kill-pass.
  Gate outcomes go to `docs/gate-log.md`, dated, with links.
- **Kill-list respect.** If market evidence surfaces a closer for
  this gap, record it honestly in gate-log + README verification
  status and notify the maintainer — a killed gap is a *successful
  finding* in this system, not a failure. Never quietly rebuild a
  closed category.
- **Worklog protocol.** Substantial work sessions append to the
  shared worklog at `/home/z/my-project/worklog.md` (research-side
  agents) and to `CHANGELOG.md` under `[Unreleased]` (this repo).
  Commit messages: imperative, one line, no version numbers in the
  subject.

## Repo map (as of the task-brief commit)

```
README.md           # product brief + necessity case + verification status
AGENT-GOALS.md      # THE TASK BRIEF — work orders, constraints, gates
AGENTS.md           # this file — mechanics for any agent
CHANGELOG.md        # 0.0.1 = task brief only, no code
LICENSE             # MIT
.github/workflows/brief-guard.yml   # CI: validates the brief stays intact
docs/               # gate-log.md lands here when Goal 0 runs
```
