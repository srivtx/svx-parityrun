# parityrun — prove the rewrite behaves like the original

**svx-parityrun** is a vendor-neutral **capture → replay → deterministic-compare**
harness for legacy migrations: record what the production system actually
did, replay the same inputs against the rewritten system, and diff the
outputs byte-for-byte with tolerances you control. First target: **z/OS
JCL batch jobs** (the workload class behind most fixed-price migration
contracts). It is sold — in spirit and eventually in license — to the
**systems integrators who carry delivery risk**, not to agencies and not
to end users.

> **Status: pre-build, verified open (2026-09-30).** This repository is a
> complete, self-contained work order. No product code exists yet. Read
> [`AGENT-GOALS.md`](AGENT-GOALS.md) first — it is the task brief; this
> README is the context. If you are an agent (human or AI) picking this
> up cold, that is exactly the intended entry point.

## Why this is necessary

The SVX research system ran this gap through three dedicated
kill-search passes — R8 (25 queries, 2026-09-30), R13b (2026-09-30),
and V1 (14 queries, 2026-09-30) — looking for the existing solver that
would close it. None was found. The evidence case, with sources:

1. **Migration failure concentrates in validation, not translation.**
   "70% of mainframe exits fail"; "80% of migrations miss deadlines on
   delayed testing" (softwaremodernizationservices.com, Jan 2026;
   openlegacy.com — vendor-claimed, never countered in any pass);
   "80% of core-banking migrations fail… usually due to incomplete or
   incorrect data" (openlegacy.com).
2. **Translation is commoditized; validation is unowned.** IBM watsonx
   Code Assistant for Z claims to "analyze, refactor, transform and
   validate" (venturebeat.com, Aug 2023); Amazon Q Developer Transform
   embeds test-case/test-data generation *inside* its proprietary
   transform pipeline and runtime (repost.aws; docs.aws.amazon.com);
   GitHub Copilot for IBM Z leads the understanding side (Gartner MQ,
   practicallogix.com, Jul 2026). An SI using any other engine gets
   nothing — validation exists only as a feature claim of someone
   else's translator.
3. **The best-funded entrant proves the method but does not sell the
   tool.** Mechanical Orchard's Imogen "rewrites mainframe applications
   with confidence by using real data flows, not just code translation"
   — behavior-capture → generated tests → behavior-matching rewrite
   (mechanical-orchard.com, Jul 8 2026; ThoughtWorks case study Sep 7
   2026: 4 JCL batch jobs → Python/AWS Batch + 3 Db2 migrations). They
   sell delivered modernization. The harness itself is not a product
   anyone can buy.
4. **Buyable test automation stops at the platform boundary.** BMC AMI
   DevX Total Test (ex-Compuware Topaz) and Broadcom's automated
   testing solution cover on-platform z/OS DevOps — unit and functional
   tests inside the mainframe toolchain (bmc.com; docs.broadcom.com;
   Forrester TEI: 33% change-failure reduction, tei.forrester.com) —
   but no cross-system migration-equivalence capability surfaced in
   three passes.
5. **The people who carry the risk are reachable and mid-sized.**
   Government agencies "almost always buy software through channel
   partners" (handbook.opencoreventures.com, Jan 2026); the
   mainframe-modernization services market is led by IBM, Accenture,
   DXC, Cognizant, Deloitte, AWS (credenceresearch.com, Jan 2025) with
   mid-market SIs like Karsun doing the actual delivery (aws.amazon.com,
   May 2025). A fixed-price SI that can *prove* equivalence cheaply
   wins bids and survives cutovers. No procurement moat sits in front
   of selling to them.

The one-sentence necessity argument: **everyone who loses money on
failed migrations already knows testing is the bottleneck, the
hyperscalers' answers only work inside their own pipelines, and the
tool that makes equivalence a deterministic, vendor-neutral artifact
does not exist.**

## What it is — and is not

- **Is:** a harness that records production job inputs/outputs, replays
  them against the rewritten system, and emits deterministic equivalence
  reports (JUnit-style, CI-consumable). "The legacy system as the test
  oracle" (richard-seidl.com, 2010) as a product, 16 years later.
- **Is not:** a COBOL translator, a COBOL-understanding copilot, an
  inventory/dependency mapper, or an agency-facing product. All four of
  those categories are occupied and were explicitly killed in research
  (see the registry's searched-and-closed list).

## Verification status and build gates

The gap was **OPEN at medium-high confidence** as of 2026-09-30 (V1).
Two conditions attach — both are mandatory gates, not advice:

1. **Verify the AWS Marketplace listing "Rhino Agentic Mainframe
   Modernization"** (Mechanical Orchard's Imogen — COBOL/PL/I/JCL/
   Assembler/CICS/IMS → Amazon EKS; aws.amazon.com, undated,
   single-source). If it is a software SKU, the wedge shifts from
   "the harness does not exist" to "vendor-neutral, works-with-any-
   engine" — re-assess before building.
2. Re-run the core kill-search once before ship ("mainframe migration
   equivalence testing tool 2026") — the "parallel run" phrasing was
   structurally unsearchable on our service (6+ consecutive noise
   failures across passes), so absence-of-product rests on 7 phrasings,
   which is strong but not airtight.

Full evidence: [V1 report](https://github.com/srivtx/svx-research/blob/main/research/track-reports/V1-row13-harness-killsearch.md),
[R8 report](https://github.com/srivtx/svx-research/blob/main/research/track-reports/R8-gov-cobol-modernization.md),
[registry row 13](https://github.com/srivtx/svx-research/blob/main/docs/gap-registry.md).

## The SVX family

This is product #3 of the SVX research-to-product system. The sibling
products and the research brain live at:

- [svx-research](https://github.com/srivtx/svx-research) — the research
  brain: the gap registry, track reports, raw evidence, and the loop
  protocol that produced this repository.
- [svx-evalgate](https://github.com/srivtx/svx-evalgate) — product #1
  (gap #1): deterministic eval gating in CI. parityrun inherits its
  house rules: determinism, zero-runtime-dependency core, meaningful
  exit codes, conservative versioning.
- [svx-careops](https://github.com/srivtx/svx-careops) — product #2
  (gap #14): the home-care back-office agent layer.

License: MIT (see [LICENSE](LICENSE)). Version discipline: 0.x until
the harness runs a real JCL capture-replay end to end; no version is
ever bumped by ambition (see `AGENT-GOALS.md`).
