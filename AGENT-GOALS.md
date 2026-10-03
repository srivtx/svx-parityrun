# AGENT-GOALS.md — parityrun work orders

**Self-contained work orders for svx-parityrun's open goals.** Any agent
(human or AI) should be able to pick one goal from this file and execute
it with no other context than this document plus
[`AGENTS.md`](AGENTS.md) and the [README](README.md). The research that
justified this product lives in the
[research repo](https://github.com/srivtx/svx-research) — specifically
track reports R8, R13b, and V1 — but everything needed to *build* is
here. This is the task brief the product was born from.

**Claiming a goal:** read it fully, check the repo state hasn't already
shipped it, re-run the build gates if the goal says so, do the work, add
a `CHANGELOG.md` entry under `[Unreleased]`, and move the goal to the
"Shipped" section at the bottom with its commit SHA.

## Standing constraints (apply to every goal)

1. **Determinism is the product.** Same captured run + same replay +
   same tolerances → byte-identical report. No wall-clock time, no
   unseeded randomness, no network at runtime, no unversioned
   environment reads. If a comparison is not reproducible, it is a bug,
   not a feature.
2. **Zero runtime dependencies in the core** (std-lib only), the same
   rule the evalgate sibling enforces. Optional adapters may accept
   formats; they may not require installs.
3. **Exit-code contract:** 0 = equivalent within tolerance, 1 = at
   least one case degrades beyond tolerance, 2 = harness misuse
   (missing artifacts, malformed capture). CI failures must name the
   failing job/case and show both sides of the diff.
4. **Versioning discipline:** stay 0.x until a real JCL capture-replay
   runs end to end against a real rewritten job. Minor bumps per
   capability batch, patches for fixes, never a bump for docs. A 1.0
   must be earned by an SI (or SI-shaped pilot) actually running it.
5. **Never translate code.** parityrun compares behavior; it does not
   generate, rewrite, or "improve" anything. Feature creep toward
   translation is the single fastest way to collide with four
   occupied categories (see README "Is not").
6. **Evidence discipline:** every product claim traceable to the
   research reports or to a checked-in capture/replay artifact. No
   invented benchmarks.

---

## Goal 0 — Build gates (RESOLVED 2026-10-04 — see `docs/gate-log.md`)

**Outcome: gate 1 partially fired; the row stays OPEN, narrowed.**
Mechanical Orchard's "Imogen by Mechanical Orchard" is now on AWS
Marketplace as a **private-offer SaaS** (harness embedded,
byte-for-byte-equivalence language, Thoughtworks and Perficient
partner listings alongside) — but it is not vendor-neutral, not
engine-agnostic, and not separable, and MO's own funnel keeps code in
MO-controlled environments. **Goal 1 proceeds on the re-framed wedge:
the engine-agnostic, SI-owned, any-target slices Imogen structurally
cannot serve.** Full evidence and the re-frame rationale:
[`docs/gate-log.md`](docs/gate-log.md). The pre-ship kill-search
refresh (original §2 below) remains in force.

**Original work order (kept for the record):**

**Why this goal exists.** The gap was verified OPEN at medium-high
confidence on 2026-09-30, not high — two threads were left open, and
either one can change the product's shape. This goal converts them into
facts before engineering starts.

**Work order:**

1. **Verify the AWS Marketplace listing "Rhino Agentic Mainframe
   Modernization"** (Mechanical Orchard / Imogen: refactors COBOL,
   PL/I, JCL, Assembler, CICS, IMS to Amazon EKS). Read the listing
   detail page directly (browser or API, not web search — the
   "parallel run" query family is structurally unsearchable on our
   search service, 6+ consecutive noise failures across R8/R13b/V1).
   Record: seller, listing type (software SKU vs. professional
   services vs. partner solution), pricing model, and whether the
   capture/test harness is separately licensable.
   - **If it is a software SKU containing the harness:** stop, write
     the finding into `docs/gate-log.md`, and re-frame Goal 1 around
     vendor-neutrality (works-with-any-engine, on-prem, and
     non-EKS targets — the slices Imogen by construction does not
     serve). Ping the maintainer in the CHANGELOG entry.
   - **If it is services or a bundled transform platform:** record and
     proceed — the harness-for-SIs slice stays open.
2. **One kill-search refresh:** `mainframe migration equivalence
   testing tool` + `regression testing rewritten COBOL equivalence`
   (fresh phrasings, dated today). Absence of a standalone product
   confirms the row; a hit goes to `docs/gate-log.md` and the
   maintainer.
3. Write `docs/gate-log.md` with both results, dated, sources linked.

**Acceptance criteria:** `docs/gate-log.md` exists with both gates
resolved (verified-open or re-framed) and dated entries. If either gate
fires, the README's verification-status section is updated in the same
commit.

---

## Goal 1 — The first ship: JCL batch capture-replay MVP

**Why this goal exists.** JCL batch is the workload class the evidence
actually covers — ThoughtWorks/Mechanical Orchard's flagship case study
is JCL batch jobs (Sep 7 2026), and batch I/O is deterministic by
nature, which makes it the honest first target for a determinism-first
tool. The MVP proves the whole thesis: equivalence as a CI-consumable
artifact.

**Product shape (the contract):**

```
parityrun capture   # on z/OS or from exported datasets: record job inputs/outputs
parityrun replay    # feed captured inputs to the rewritten job; capture outputs
parityrun compare   # deterministic diff, tolerance rules, JUnit-style report
```

- **Capture** reads a job's input datasets (QSAM/VSAM sources, SYSIN,
  PDS members) and output datasets (SYSOUT, created files), plus a job
  manifest (job card, step sequence, COND/RETURN codes). The storage
  layer is a content-addressed local directory (hashes, no database).
  MVP reads **exported datasets** (downloaded via FTP/SFTP or the z/OS
  Utilities) — no live z/OS connection in v0; document the export
  recipe instead.
- **Replay** is execution-agnostic: it shells out to whatever command
  the SI's rewritten job exposes (a Python entry point, a container,
  a jar), passing captured inputs by path, capturing outputs to a
  parallel tree. The harness never assumes the target's stack.
- **Compare** is where the product lives:
  - byte-level first, then **tolerance rules** the user declares per
    dataset: trim trailing spaces (fixed-width padding), ignore
    columns by range (timestamps, sequence counters, ABEND codes that
    map to equivalent RCs), numeric epsilon for COMP-3/float fields,
    record-order tolerance for explicitly unordered outputs;
  - dataset-level and step-level verdicts with per-case rows;
  - JUnit XML + markdown + JSON reports (the evalgate report pattern);
  - direction-aware semantics: "degrades" is defined relative to the
    legacy baseline — the legacy system is the oracle, always.
- **Determinism rules:** sort file listings before hashing; pin
  timezone to UTC in reports; tolerate nothing wall-clock.

**Work order (tests-first, per house rules):**

1. Fixtures before features: check in two synthetic job fixtures
   (a two-step JCL batch with QSAM in/out; a COMP-3 numeric case) plus
   their "rewritten" twins — one equivalent, one with a real defect
   (a truncated field). Every feature lands with a test that fails
   without it.
2. `compare` first (it is testable with static fixtures and is the
   product's core value), then `replay`, then `capture` last (exported
   datasets only).
3. Tolerance rules are data, not code: a YAML rules file per job,
   versioned with the capture. An unexplained tolerance is a finding
   to report, not a fix to hide — the report lists every tolerance
   that fired, because "we passed because we ignored 40 columns" is
   exactly the sentence an SI needs to see before cutover.
4. CLI `parityrun <verb>`, `--help` everywhere, exit codes per
   constraint #3. Python 3.10+, std-lib only.
5. Docs: `docs/example-walkthrough.md` from the fixtures; README gains
   the quickstart.
6. CI from the first commit (GitHub Actions: lint, tests, and a
   fixture-driven end-to-end run).

**Acceptance criteria:** the checked-in fixtures run
capture→replay→compare end to end in CI on GitHub runners; the
equivalent twin reports GREEN with zero tolerances fired; the defect
twin reports RED naming the dataset, field, and both values; output is
byte-identical across two runs (determinism test in CI); zero runtime
dependencies; ≥ 40 tests; version 0.1.0 exactly once.

**Explicitly out of scope for Goal 1:** CICS/IMS (online), Db2
migrations, test *generation* (the Locksmith-Loop-shaped AI layer is a
later goal, and only if Goal 1 proves the core), any z/OS live
connection.

---

## Goal 2 — The SI packaging: equivalence reports as contract artifacts

**Why this goal exists.** The buyer is a fixed-price SI; what they sell
their customer is *confidence at cutover*. The harness's output must be
attachable to a contract: an equivalence dossier.

**Work order:**

1. `parityrun dossier` — assembles a single artifact (markdown + JSON
   bundle) per migration wave: coverage table (jobs captured, replayed,
   compared), tolerance ledger (every rule that fired, per dataset),
   verdicts, and a reproducibility statement (harness version, rules
   version, content hashes).
2. Repeat-run stability: `dossier --verify` re-runs compare from
   captured artifacts and asserts byte-identical verdicts — the
   anti-tamper proof an SI can hand an auditor.
3. Template gallery: one-page cover summary (pass counts, risk
   deltas) for non-technical stakeholders; the detail stays in the
   appendix tables.
4. Docs: `docs/si-playbook.md` — how a delivery lead wires parityrun
   into a fixed-price bid (the "we can prove equivalence cheaply"
   argument the research says no competitor can currently make).

**Acceptance criteria:** dossier builds from Goal 1 fixtures in CI;
`--verify` round-trips byte-identically; playbook reviewed against the
evidence case in the README; version 0.2.0 once, with the batch.

---

## Goal 3 — Data-reconciliation slice (GATED — standalone premise killed by V4, 2026-10-04)

**Gate outcome (research track V4):** the *standalone* reconciliation
product exists — **Arbutus Analyzer** (reads native DB2/IMS/ADABAS/
VSAM/ISAM; "RECONCILE THE MIGRATION" decommissioning whitepaper) and
**DataChecks.io** (agent-driven output-parity validation, cloud-DW
scope) — and Next Pathway/AWS embed validation platform-locked.
**The slice as originally drafted is closed.** It survives only if
parityrun's version is all four of: **automated** (Arbutus is
analyst-driven), **statistical-bounds** (sampling with confidence
levels, evalgate's interval machinery), **CI-integrated** (a gate, not
a report), and **vendor-neutral** (any source/target pair). If a
future agent opens this goal, the work order is the four-axes
re-frame + its own kill-pass on those axes first, as version 0.3.0.

---

## Goal 4 — Smaller improvements (pick up anytime)

- `--strict` modes and tolerances lint (flag overlapping/contradictory
  rules).
- A `docs/formats.md` spec of the capture manifest (it must stay
  stable long enough for SIs to build tooling around it).
- Example adapters for common rewritten stacks (a Python target, a
  containerized target) — recipes only, no SDK lock-in.
- Wire the V1 follow-up list (SI fixed-price query, TSRI 2026 depth,
  differential-testing OSS) into the research repo's next pass — this
  repo's findings feed back into the registry per the loop protocol.

---

## Re-verify triggers (any of these → re-run Goal 0 before further work)

- Mechanical Orchard announces a **tool-only SKU, partner program, or
  separately-licensable harness** (as of 2026-10-04: private-offer SaaS,
  harness embedded — see `docs/gate-log.md`).
- BMC/Broadcom ship migration-equivalence features (cross-system
  compare) in their testing products.
- Amazon Q Transform / watsonx Z expose standalone validation
  tooling outside their pipelines.
- Any SI marketing a "parallel-run harness" as a product (search
  refresh hit).

## Shipped goals (append with commit SHA when a goal completes)

| Goal | Shipped in | Notes |
|------|-----------|-------|
| — | — | none yet — Goal 0 is the mandatory first move |
