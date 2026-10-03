# Gate Log — parityrun

Build gates and market-structure findings, newest first. Per
AGENT-GOALS.md, gates are resolved before code; outcomes recorded here,
dated, with sources.

---

## 2026-10-04 — Goal 0 resolved: the Imogen AWS Marketplace question (Gate 1)

**Question (from V1, 2026-09-30):** is the "Rhino Agentic Mainframe
Modernization" / Mechanical Orchard AWS Marketplace presence a software
SKU containing the harness — in which case the wedge re-frames?

**Method:** direct reads (owner session, 2026-10-04) — Mechanical
Orchard's own site (PoC page, Aug 24 2026) and the AWS Marketplace
listing itself via headless browser. Raw captures in the research repo
at `research/raw-search-results/w4-direct/`.

**Findings:**

1. **The listing is real and is the platform, as SaaS:**
   "Imogen by Mechanical Orchard" — AWS Marketplace,
   `prodview-tcry42kis5gxq` — **Delivery method: Software as a Service
   (SaaS); Sold by: Mechanical Orchard; Deployed on AWS: Yes**; pricing
   is custom ("contact info@mechanical-orchard.com for a private
   offer. A free proof of concept using actual code is available").
2. **The harness is embedded, not separable:** listing text — "Imogen
   automates a rigorous testing harness into the process of writing
   modern code to create byte-for-byte equivalence"; "Each new workload
   is tested exhaustively against deterministic and probabilistic tests
   (and ultimately, against real data flows) to confirm that it
   produces identical behavior to the original."
3. **The channel is occupied by partner-shaped offerings:** the
   marketplace also carries "Mainframe Modernization—with Mechanical
   Orchard" (by Thoughtworks, Inc.) and "Mainframe Modernization with
   Perficient & Mechanical Orchard" (by Perficient, Inc. — "uses
   Mechanical Orchard's Imogen platform to automatically refactor
   COBOL, PL/I, JCL, Assembler, CICS, and IMS code into behaviorally
   equivalent Java running on Amazon EKS"; PCI-DSS/HIPAA alignment;
   AWS MAP eligibility).
4. **MO's own funnel is engagement-shaped, and routes AROUND the SI as
   a buyer:** the free-PoC page (Aug 24 2026) — "runs Imogen against a
   real slice of your codebase… we handle the rest in our own
   environment — your code never leaves an MO-controlled cloud
   instance"; "share it with your favorite system integrator." The SI
   is the customer's collaborator, not the tool's buyer.

**Verdict: GATE PARTIALLY FIRES — the row stays OPEN, narrowed.** The
Imogen platform (harness embedded) is now marketplace-procureable, and
three MO-shaped offerings occupy the flagship channel. But the listing
is a private-offer SaaS bundling MO's engine + MO's harness; it is not
vendor-neutral, not engine-agnostic, and not separable. The unowned
slices — exactly the re-frame Goal 0 pre-authorized:

- **Engine-agnostic:** an SI on watsonx Z, Amazon Q Transform, Copilot
  for IBM Z, or its own translation stack gets nothing from Imogen;
- **SI-owned / self-hosted:** "your code never leaves an MO-controlled
  cloud instance" is disqualifying for fixed-price SIs whose contracts
  (or clients) forbid routing code through a third-party SaaS — even
  with Imogen's on-premise orchestration claim, the commercial terms
  are MO's;
- **Non-Java / non-EKS targets:** the marketplace offerings converge
  on "behaviorally equivalent Java running on Amazon EKS" — SIs
  targeting .NET, Go, Python, other clouds, or on-prem are unserved;
- **The dossier as the SI's own contract artifact** (Goal 2) — MO's
  outputs belong to MO's engagement.

**Note on language:** "byte-for-byte equivalence" and "rigorous
testing harness" are now MO's marketing terms — the concept is
market-validated at the highest level. parityrun's positioning must be
explicitly *not* "we also do what Imogen does" but "we do the part
Imogen structurally cannot: your engine, your target, your infra,
your artifact."

**Related V4 findings (2026-10-04, research track V4):**

- **Goal 3's standalone-reconciliation premise is DEAD** — see the
  Goal 3 amendment in AGENT-GOALS.md (Arbutus Analyzer markets
  mainframe-decommissioning reconciliation natively; DataChecks.io
  sells agent-driven output-parity validation, cloud-DW scope; Next
  Pathway/AWS validation is platform-locked). The slice survives only
  on four axes: automated + statistical-bounds + CI-integrated +
  vendor-neutral.
- **TSRI is confirmed NOT a closer** (JANUS Studio = "fully automated
  assessment, transformation"; flagship case had customer-supplied
  testing).
- **No differential/characterization-testing OSS exists to reuse for
  mainframe** (method mature; frameworks exist only in other domains —
  CYNTHIA for ORMs, Kaizen for HPC research). Goal 1 must build from
  first principles.

---

## Next scheduled gates

- Pre-ship kill-search refresh (Goal 0 §2) — "mainframe migration
  equivalence testing tool 2026" + one fresh phrasing, dated at ship
  time.
- Goal 3's own kill-pass if the data-reconciliation slice is ever
  opened (now amended; see AGENT-GOALS.md).
