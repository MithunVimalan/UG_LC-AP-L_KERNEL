# SambaNova Systems

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred. Inventors: Raghu Prabhakar, Matthew Vilim, Matt Feldman, Yaqi Zhang, Gregory Grohoski, Paul Jordan, et al.

---

## 1. Why SambaNova matters for your kernel

SambaNova's RDU (Reconfigurable Dataflow Unit) is a CGRA of **Pattern Compute Units (PCUs)** and **Pattern Memory Units (PMUs)**. Its compiler patents are an unusually direct match to your three formulas:

- **F2 (access map) in hardware** — "address pattern memory units" generate **address vectors** that data PMUs use to read input pixels, doing an **affine transform** — i.e. `addr = sum_i i_d * stride_d`, your formula (3) over loop indices (1).
- **F3 (layout) as a compiler solve** — "Memory unit partitioning": the compiler **extracts tensor indexing expressions, converts them to logical memory constraints, groups them into concurrent access groups, and picks a partitioning (banking) solution** per group. This is exactly "choose the layout so the schedule's accesses are conflict-free" — your section 9.2 cache-set-conflict problem, solved at compile time for on-chip SRAM banks.
- **US11645057B2 "Systems and methods for memory layout determination and conflict resolution"** is a SambaNova member of this family (it surfaced unattributed in the Google search; it belongs here).

---

## 2. Summary table

| # | Number (family) | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11645057B2 | Systems and methods for memory layout determination and conflict resolution | **F3** | granted | V |
| 2 | US12093551B2 (parent US11709611B2); cont. pub. US20250013375A1 | Memory unit partitioning solutions for reconfigurable dataflow computing systems | **F3** | granted | V |
| 3 | US12461778B2 | (CGR processor with PCUs and PMUs executing a dataflow graph) | F1 | granted 4 Nov 2025 | V |
| 4 | (pub. 2024; Prabhakar, Vilim, Feldman, Zhang) | affine transform using address pattern memory units | **F2** | application | V |
| 5 | US12468635B2 | Duplication of tensors for memory allocation in a reconfigurable data processor | F3 | granted | V |
| 6 | US20240069770A1 | Multiple contexts for a memory unit in a reconfigurable data processor | F3 | application | V |

---

## 3. Patent details

### F3 — Layout as a banking/partitioning solve

#### 3.1 US12093551B2 / US20250013375A1 — Memory unit partitioning solutions
- **What it covers.** A parser reads source code, **extracts tensor indexing expressions**, converts them to **logical memory constraints**, **groups them into concurrent access groups**, and selects a **partitioning (bank) solution** for each group so concurrent accesses hit different banks. Parent US11709611B2; continuation US20250013375A1.
- **In three-formula terms (analysis).** This solves F3 from F1 + F2: given the schedule's concurrent accesses (the indexing expressions), find a banked layout with no conflict. It is the compile-time, SRAM-bank analogue of the GPU XOR swizzle and of your "pad the row pitch by one line to break the 4096-byte set-conflict pattern".
- **Links:** [Google Patents US12093551](https://patents.google.com/patent/US12093551) · [Justia 20250013375](https://patents.justia.com/patent/20250013375)

#### 3.2 US11645057B2 — Memory layout determination and conflict resolution
- A compiler analysis/transformation pass that **determines required physical layouts** from a dataflow graph and **detects and resolves conflicts** between producer and consumer layouts (memory-layout functions generate expected producer/consumer layouts from the operation types).
- **In three-formula terms (analysis).** F3 propagation + conflict resolution across the graph — the same job as oneDNN Graph layout propagation and your "output `nChw8c` must be the next layer's `mimo` input".
- **Links:** [Google Patents US11645057](https://patents.google.com/patent/US11645057) · [USPTO PDF 11645057](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11645057)

### F2 — Access map in hardware

#### 3.3 Address pattern memory units (affine transform)
- **Inventors:** Raghu Prabhakar, Matthew Vilim, Matt Feldman, Yaqi Zhang. A statically reconfigurable dataflow processor performs an **affine transform** using **address PMUs** (a memory arranged as a vector of L banks plus L data PMUs); **address PMUs write address vectors that data PMUs use to read input pixels**.
- **In three-formula terms (analysis).** F2/F3 split in hardware: address PMUs compute `addr(i) = base + sum_d i_d*stride_d` (your offset formula), data PMUs fetch. The conv window for an output tile is one such address-vector program.
- **Links:** [Justia, Raghu Prabhakar](https://patents.justia.com/inventor/raghu-prabhakar?page=2)

### F1 — Schedule

#### 3.4 US12461778B2 — CGR processor with PCUs and PMUs
- A coarse-grained reconfigurable processor with **pattern compute units and pattern memory units configured to execute a dataflow graph** — the place-and-route schedule. [OG notice](https://patentsgazette.uspto.gov/week44/OG/html/1540-1/US12461778-20251104.html)

#### 3.5 US12468635B2 — Duplication of tensors for memory allocation
- A compiler **creates copies of a tensor** to satisfy differing read/write access patterns (so producer and consumer layouts need not match). F3. [Google Patents US12468635](https://patents.google.com/patent/US12468635)

#### 3.6 US20240069770A1 — Multiple contexts for a memory unit
- A PMU holds multiple addressing contexts (several layouts live in one memory unit). [Justia 20240069770](https://patents.justia.com/patent/20240069770)

---

## 4. Searched and not found
- No single SambaNova patent found that names convolution stride/pad directly; the access map is covered generically via affine address PMUs.

## 5. Search log
- `SambaNova Systems patent "reconfigurable" dataflow "pattern memory unit" tensor "memory layout" address generator loop nest compiler mapping`
- (cross-referenced from the Google search that surfaced US11645057B2)
