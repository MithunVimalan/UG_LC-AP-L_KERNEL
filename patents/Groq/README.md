# Groq

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed; `P` = assignee inferred.

Portfolio size (third-party counts, differing dates): 62 US grants all-time to 31 Dec 2025 ([Patent Leaderboard](https://www.patentleaderboard.com/company/groq)); 108 documents incl. applications ([Patsnap](https://discovery.patsnap.com/company/groq/)). Assignee list: [Justia, Groq Inc](https://patents.justia.com/assignee/groq-inc?page=2).

---

## 1. Why Groq matters for your kernel

Groq's Tensor Streaming Processor (TSP / LPU) has no caches, no arbiters and no hardware interlocks: **the compiler fixes the schedule to the cycle**. So Groq's patents put F1 in the compiler (instruction flows and data flows "scheduled to intersect at a specific resource at a defined time"), and the conv access map F2 in a **flattened input stream + expanded kernel** construction — Groq's own version of im2col, derived from the observation that conv "repeatedly reads overlapping input values". The Jonathan Ross family (priority 19 Nov 2018) is the most direct F2 patent family found for any company in this study.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority | Status | V |
|---|---|---|---|---|---|---|
| 1 | US11455370 | Flattened input stream generation for convolution with expanded kernel | **F2** | prov. 62/769,444, 19 Nov 2018 (family) | granted 27 Sep 2022 | V |
| 2 | US11204976; US11809514B2 | Expanded kernel generation | F2 + F3 | same family | granted | V |
| 3 | US11301546; US11537687 (pub. US20200160226A1); CN119166964A; EP4550215 | Spatial locality transform of matrices | F2 + F3 | prov. 62/769,444, 19 Nov 2018 | granted (CN pending) | V |
| 4 | US11645226B1; US12222894B2 | Compiler operations for tensor streaming processor | **F1** | — | granted | V |
| 5 | US12271339B2 | Instruction format and instruction set architecture for tensor streaming processor | F1 | — | granted 8 Apr 2025 | V |
| 6 | US11568275B1 | Processor compiler | F1 | — | granted | P |
| 7 | US11360934B1 | Tensor streaming processor architecture | F1 + F3 | — | granted | V |
| 8 | US11263129B1; US11243880B1; US20240320185A1 | Processor architecture; Deterministic memory for tensor streaming processors | F3 | — | granted | V |
| 9 | US12340300 | Streaming processor architecture | F3 | — | granted 24 Jun 2025 | V |
| 10 | US12277444B2 (pub. US20230161621A1) | Software-defined tensor streaming multiprocessor for large-scale machine learning | F1 | — | granted 15 Apr 2025 | V |
| 11 | US12260118 | Deterministic near-compute memory for deterministic processor and enhanced data movement between memory units and processing units | F3 | — | granted | P |
| 12 | US12688075 | Graph partitioning and implementation of large models on tensor streaming processors | F1 | — | granted (Jul 2026 per Autobound) | P |

---

## 3. Patent details

### F2 — Access map

#### 3.1 US11455370 — Flattened input stream generation for convolution with expanded kernel
- **Assignee:** Groq. **Inventor:** Jonathan Alexander Ross. Granted 27 Sep 2022.
- **Independent claim 1 (summary).** A method of computing a convolution: an **input stream generator** receives the kernel and the input tensor, **splits the input tensor into kernel-sized tiles**, and **flattens each tile into a vector**; an **expanded kernel generator** expands the kernel; a **multiply-add unit** convolves the flattened vectors with the expanded kernel vectors.
- **Dependent claims 2-5.** Padding: pad so the output size equals the input size; **pad each dimension to a whole multiple of the kernel dimension**; pad the trailing edge with zeros.
- **In three-formula terms (analysis).** The input is re-indexed by kernel-sized tiles: `tile = (ih / R, iw / S)`, `in_tile = (ih % R, iw % S)` — a div/mod split of the *spatial* axes by the *kernel* size. Every output pixel's window then overlaps at most 4 neighbouring tiles (2-D), and the expanded kernel holds the kernel shifted to every in-tile offset, so each output is a dot product of a few flattened tiles with a few expanded-kernel vectors. This replaces the per-output `ih = oh - pad + r` gather (F2) with a fixed tile-neighbourhood product plus an F3 re-tiling of the input.
- **Overlap with your kernel.** Your kernel computes the window directly from loop counters (`ih = oh + kh - 1`, `iw = 3b + jj + ki - 1`) with no re-tiling of the input by kernel size and no expanded kernel. Different construction.
- **Links:** [USPTO PDF 11455370](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11455370)

#### 3.2 US11204976 / US11809514B2 — Expanded kernel generation
- **Assignee:** Groq. Same specification: "A method comprises receiving a kernel used to convolve with an input tensor", tile and flatten, then build the **expanded kernel** (the kernel replicated at shifted offsets) that pairs with the flattened tiles.
- **In three-formula terms (analysis).** F3 for the weights: the weight layout is chosen so that F2 becomes a regular product — the counterpart of `Ohwi8o` being "packed in kernel read order".
- **Links:** [Google Patents US11809514](https://patents.google.com/patent/US11809514) · [USPTO PDF 11809514](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11809514) · [Justia 11204976](https://patents.justia.com/patent/11204976) · [USPTO PDF 11204976](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11204976)

#### 3.3 US11301546 / US11537687 — Spatial locality transform of matrices
- **Assignee:** Groq, Inc. **Inventor:** Jonathan Alexander Ross. Pub. US20200160226A1 (21 May 2020); both grants claim provisional 62/769,444 (19 Nov 2018). Chinese CN119166964A (pending); EP counterpart EP4550215.
- **Background stated.** Convolution is expensive "when a kernel repeatedly reads overlapping input values".
- **EP abstract.** Receive an input tensor for convolution with a kernel; **divide the input tensor into tiles each the size of the kernel**; **flatten values in each tile into flattened input vectors**.
- **Links:** [USPTO PDF 11537687](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11537687) · [USPTO PDF 11301546](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11301546) · [Golden US11537687](https://golden.com/wiki/US_Patent_11537687_Spatial_locality_transform_of_matrices-5ZG58N8) · [CN119166964A](https://patents.google.com/patent/CN119166964A/zh) · [EP4550215 document](https://data.epo.org/publication-server/rest/v1.2/patents/EP4550215NWA3/document.html)

### F1 — Schedule

#### 3.4 US11645226B1 / US12222894B2 — Compiler operations for tensor streaming processor
- **Assignee:** Groq Inc. **Inventors:** Dennis Charles Abts, Jonathan Alexander Ross, John Thompson, Gregory Michael Thorson.
- **What it covers.** The compiler **schedules each instruction flow and its data flows to intersect at a specific resource at a defined time**, and schedules transport along lanes organised as **functional slices of tiles**.
- **In three-formula terms (analysis).** F1 as a cycle-exact space-time schedule: "loop counter -> (unit, cycle)" rather than "loop counter -> output pixel" only. Because there are no caches, F2 and F3 must be known at compile time too.
- **Links:** [USPTO PDF 11645226](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11645226) · [Google Patents US12222894](https://patents.google.com/patent/US12222894) · [USPTO PDF 12222894](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12222894)

#### 3.5 US12271339B2 — Instruction format and ISA for tensor streaming processor
- Claim 1: a **compiler** that allocates functional resources and data transport lanes, schedules data flows through specific units, and schedules instruction flows so each executes a specific instruction on specific data **at a defined time**. **Links:** [OG notice](https://patentsgazette.uspto.gov/week14/OG/html/1533-2/US12271339-20250408.html) · [USPTO PDF 12271339](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12271339)

#### 3.6 US11360934B1 — Tensor streaming processor architecture
- The base TSP architecture patent: the chip is divided into functional units organised as functional slices (per the family text), with a 320-lane programming abstraction in which each tile works on 16 lanes in SIMD (stated in the related US12260118). The slice names MEM / VXM / MXM / SXM come from Groq's ISCA 2020 paper, not from the snippets seen here. **Links:** [Google Patents US11360934B1](https://patents.google.com/patent/US11360934B1/en)

#### 3.7 Others (F1)
- US11568275B1 Processor compiler — [Google Patents](https://patents.google.com/patent/US11568275)
- US12277444B2 Software-defined tensor streaming multiprocessor — [Google Patents](https://patents.google.com/patent/US12277444) · [OG notice](https://patentsgazette.uspto.gov/week15/OG/html/1533-3/US12277444-20250415.html)
- US12688075 Graph partitioning and implementation of large models on TSPs — [USPTO PDF](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12688075)

### F3 — Layout / memory organisation
- US11263129B1 / US11243880B1 Processor architecture; US20240320185A1 Deterministic memory for TSPs — [US11263129B1](https://patents.google.com/patent/US11263129B1/en) · [US11243880B1](https://patents.google.com/patent/US11243880B1/en) · [US20240320185A1](https://patents.google.com/patent/US20240320185A1/en)
- US12340300 Streaming processor architecture — switch units perform **transposition, permutation, shifting and rotation** of data elements (the SXM): layout changes done in-stream rather than in memory. [Justia 12340300](https://patents.justia.com/patent/12340300)
- US12260118 Deterministic near-compute memory — 320 lanes, 16-lane tiles. [USPTO PDF](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12260118)

---

## 4. Searched and not found
- No Groq patent found that names "superlane" layout or a per-MEM-slice tensor address formula.

## 5. Search log
- `Groq patent tensor streaming processor compiler scheduled instructions deterministic "stream" superlane memory slices data layout`
- `Groq Inc patent convolution "tensor" "stride" OR "address" "memory" "vector" layout transposition permute`
- `Groq patent "Flattened input stream generation for convolution with expanded kernel" claims`
- `Groq patent "Spatial locality transform of matrices" US20200160226A1 claims memory layout`
- `US11645226B1 "Compiler operations for tensor streaming processor" Groq claims schedule`
- `patents.justia.com assignee Groq Inc patents list`
