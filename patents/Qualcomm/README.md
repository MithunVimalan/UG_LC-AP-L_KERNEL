# Qualcomm (incl. the Reservoir Labs polyhedral-compiler portfolio)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed in a search result; `P` = assignee inferred.

---

## 1. Why Qualcomm matters for your kernel

Qualcomm holds two portfolios that hit the chain from opposite ends:

1. **Hexagon NPU patents (own filings).** Depth-first (layer-fused) tile scheduling to keep data in tightly coupled memory (TCM) — F1 — and, most relevant to you, **US12165237B2: a blocked memory storage format for the 3-channel RGB first layer** — F3 for exactly your `C = 3` case.
2. **Reservoir Labs polyhedral compiler patents (acquired).** Reservoir Labs built R-Stream, a polyhedral compiler whose mapper performs "joint parallelism, locality, contiguity, vectorization and data layout (JPLCVD) affine scheduling". Google Patents shows the assignment chain **Reservoir Labs -> Significs and Elements, LLC -> Qualcomm Technologies, Inc. -> Qualcomm Incorporated**. These patents are the closest thing in the whole study to a patent on your three-map model itself: the polyhedral model *is* schedule + access relation + data layout as separate maps. Several are now **expired (fee-related)** per Google Patents — check status.

Qualcomm's public HMX manual names the activation unit a **crouton**: a 3-D block of spatial-x, spatial-y and input channel; FP16 crouton = 32 (spatial) x 32 (input channel) = 2 KB ([HMX PRM V81](https://docs.qualcomm.com/doc/80-N2040-62/80-N2040-62_REV_AA_Qualcomm_Hexagon_V81_HMX_Programmers_Reference_Manual.pdf)). No patent naming "crouton" was found.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority / filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | **US12165237B2**; EP4587997A1; WO2024058894A1 | Memory storage format for supporting machine learning acceleration | **F3** | filed 16 Sep 2022 (17/946,753) | granted 10 Dec 2024 | V |
| 2 | US11487998B2 | Depth-first convolution in deep neural networks | F1 | — | granted 1 Nov 2022 | V |
| 3 | US12450486B2 | Depth-first deep convolutional neural network inference | F1 | prov. Dec 2019; filed 14 Dec 2020 | granted (OG 21 Oct 2025) | V |
| 4 | US11556798B2 (app. 16/905,541) | Optimizing machine learning model performance | F1 | — | granted 17 Jan 2023 | V |
| 5 | US10789055B2 (pub. US20170097815A1); cont. US11537373B2 | Systems and methods for scalable hierarchical polyhedral compilation | F1 + F2 | prov. 62/237,265, 5 Oct 2015 | 10789055: expired (fee) per Google | V (Reservoir -> Qualcomm) |
| 6 | US8572595B1 | Methods and apparatus for aggressive scheduling in source code compilation | F1 | — | granted | P (Reservoir) |
| 7 | US10180828B2 | Systems and methods for power optimization of processors | F1 + F3 | — | granted | P (Reservoir; text describes R-Stream) |
| 8 | US8930926 | (polyhedral parallelizer and locality optimizer; DOE-funded) | F1 | — | granted | P (Reservoir) |
| 9 | US8661422B2 | Methods and apparatus for local memory compaction | **F3** | — | granted | V (Reservoir) |
| 10 | US9830133B1; cont. US11200035 (from 13/712,659, filed 12 Dec 2012) | Methods and apparatus for automatic communication optimizations in a compiler based on a polyhedral representation | F2 | prov. 2011 | 9830133 expired per Google | V (Reservoir) |
| 11 | US10698669; US11500621 | Methods and apparatus for data transfer optimization | F2 | — | granted | P (Reservoir) |
| 12 | US10496304; US11068178B2 | Systems and methods for minimizing communications | F1 + F2 | — | granted | P (Reservoir) |

---

## 3. Patent details

### F3 — Layout

#### 3.1 US12165237B2 — Memory storage format for supporting machine learning acceleration  **(closest to your first layer)**
- **Assignee:** Qualcomm Incorporated. **Inventors:** Colin Beaton Verrilli, Natarajan Vaidhyanathan, Matthew Simpson, Geoffrey Carlton Berry, Sandeep Pande.
- **Family:** US 17/946,753 filed 16 Sep 2022, granted 10 Dec 2024; PCT/US2023/030397 = WO2024058894A1; EP23768699.3 = EP4587997A1 (published 23 Jul 2025).
- **Problem stated.** "The first layer of many computer vision ML networks is potentially limited to three channels (e.g., red, green, and blue (RGB)). Unfortunately, preparation for processing the first layer ... results in a large allocation of tightly coupled memory (TCM)." (I.e., padding 3 channels up to the NPU's channel block wastes TCM — the same 62 % waste your vendor file computed for `kHWC8` with 3 channels.)
- **What it claims (summary).** Assign addresses to the pixels across the three channels, store them in a **blocked format** (memory blocks arranged along a spatial axis or a channel axis; NCHW vs NHWC contrasted), and run inference using those addresses.
- **In three-formula terms (analysis).** F3 for a `C = 3` input: instead of `nchw` (your choice) or channel-padded `NC8hw`, a block that packs the 3 channels with spatial neighbours so the block is dense. The patent addresses the same question your kernel answered with oneDNN's `flat` branch (`jcp.ic < simd_w` -> plain `nchw` input).
- **Overlap with your kernel (technical note, not a legal opinion).** Your input stays in plain `nchw`, read by scalar broadcast; you do not re-block the 3 channels. That is the oneDNN-documented approach, public since at least 2016. If you ever design a **packed RGB block** for the first layer (e.g., interleave 3 channels with spatial pixels to fill 8 lanes), read this patent's claims first.
- **Links:** [Google Patents US12165237](https://patents.google.com/patent/US12165237) · [Justia 12165237](https://patents.justia.com/patent/12165237) · [USPTO PDF 12165237](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12165237) · [EP4587997A1](https://patents.google.com/patent/EP4587997A1/ko) · [Inventor list, Sandeep Pande](https://patents.justia.com/inventor/sandeep-pande)

#### 3.2 US8661422B2 — Methods and apparatus for local memory compaction (Reservoir Labs)
- **Original assignee:** Reservoir Labs. **Inventors:** Richard A. Lethin, Allen K. Leung, Benoit J. Meister, Nicolas T. Vasilache, David E. Wohlford.
- **What it covers.** A compiler **re-indexes the data a tile touches** so it can be stored compactly in local memory, using **Hermite factorizations, linear programming, or a triangular-base prism** approach.
- **In three-formula terms (analysis).** This is F3 derived from F1 + F2: given the tile (schedule) and the access functions, compute the image of the tile under the access map (the input window — your "3 x 5 window for a 1x3 tile") and build a new, dense local layout for just that image. It is the formal version of "the output tile decides how much input you take".
- **Links:** [Google Patents US8661422](https://patents.google.com/patent/US8661422) · [USPTO PDF 8661422](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/8661422) · [HPEC 2006 R-Stream slides](https://archive.ll.mit.edu/HPEC/agendas/proc06/Day2/21_Schweitz_Pres.pdf)

### F1 — Schedule

#### 3.3 US11487998B2 — Depth-first convolution in deep neural networks
- **Assignee:** Qualcomm (original). **Inventors:** Rexford Alan Hill, Sruthikesh Surineni, Adrienne Milner, Vito Bica. Granted 1 Nov 2022.
- **What it covers.** Start the convolution of layer 2 **before layer 1 has finished**, as soon as a **strip** of layer-1 outputs exists — a strip-wise, layer-fused schedule.
- **In three-formula terms (analysis).** F1 across layers: the loop over output rows of layer *L+1* is interleaved with the loop over output rows of layer *L*; F2 tells you how many rows of layer *L* each strip of *L+1* needs (`rows = strip + R - 1` for stride 1), which sets the strip buffer size.
- **Links:** [Google Patents US11487998](https://patents.google.com/patent/US11487998) · [Inventor list, Vito Bica](https://patents.justia.com/inventor/vito-bica)

#### 3.4 US12450486B2 — Depth-first deep convolutional neural network inference
- **Assignee:** Qualcomm. **Inventors:** Piero Zappi, Jin Won Lee, Christopher Lott, Rexford Alan Hill. Provisional Dec 2019; filed 14 Dec 2020; in the 21 Oct 2025 Official Gazette. Claims a method on a device with a processor, on-chip memory and off-chip memory.
- **Links:** [USPTO PDF 12450486](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12450486) · [OG notice](https://patentsgazette.uspto.gov/week42/OG/html/1539-3/US12450486-20251021.html)

#### 3.5 US11556798B2 — Optimizing machine learning model performance
- **Assignee:** Qualcomm (original). **Inventor:** Meghal Varia. Application 16/905,541; granted 17 Jan 2023.
- **Claims (summary).** Analyse the network to pick a **depth-first cut point**; run **depth-first traversal** on that portion, storing weights and outputs in local memory; run **layer-based traversal** on the rest. Claim 2: the cut point depends on whether combined weight + output size fits local memory capacity. Claim 3: the local memory may be **tightly-coupled memory**.
- **In three-formula terms (analysis).** F1 chosen by a memory-capacity test — the multi-layer version of your L1 working-set check (section 9.2 item 10 of your vendor file).
- **Links:** [Google Patents US11556798](https://patents.google.com/patent/US11556798) · [USPTO PDF 11556798](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11556798) · [IPIQ](https://ipiq.io/patents/300319)

#### 3.6 US10789055B2 / US11537373B2 — Scalable hierarchical polyhedral compilation (Reservoir Labs -> Qualcomm)
- **Inventors:** Muthu Manikandan Baskaran, Benoit J. Meister, Benoit Pradelle. App. 15/285,810 (pub. US20170097815A1); provisional 62/237,265 (5 Oct 2015); DARPA contract HR011-12-C-0123. Continuation US11537373B2 (granted 2022 to Qualcomm Technologies per Justia). US10789055: "Expired – Fee Related" per Google Patents.
- **What it covers.** Polyhedral compilation for **multi-level hierarchical processors and memories**: a **focalization** operator temporarily reduces the polyhedral dimensions handled at each memory level, and a **defocalization** operator restores them.
- **In three-formula terms (analysis).** F1 as an affine schedule applied level by level (DRAM tile -> L2 tile -> L1 tile -> register tile), with F2 kept as affine access relations throughout. This is exactly "swap formula (1), keep (2) and (3)" in a compiler.
- **Links:** [Google Patents US10789055](https://patents.google.com/patent/US10789055) · [USPTO PDF 10789055](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10789055) · [USPTO PDF 11537373](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11537373) · [Justia 11537373](https://patents.justia.com/patent/11537373) · [Inventor list, Baskaran](https://patents.justia.com/inventor/manikandan-baskaran) · [Inventor list, Meister](https://patents.justia.com/inventor/benoit-j-meister)

#### 3.7 US8572595B1 — Aggressive scheduling in source code compilation (Reservoir Labs)
- Polyhedral affine scheduling for parallelism and locality. **Links:** [Google Patents US8572595](https://patents.google.com/patent/US8572595)

#### 3.8 US10180828B2 — Systems and methods for power optimization of processors (Reservoir Labs)
- **What it says.** R-Stream "can perform exact dependence analysis and state-of-the-art polyhedral transformations through its **joint parallelism, locality, contiguity, vectorization, and data layout (JPLCVD) affine scheduling framework**"; the affine scheduler finds **permutable loops that can be tiled**, and **tile sizes are chosen so that the tile's data footprint fits in fast memory**.
- **In three-formula terms (analysis).** All three formulas solved jointly: schedule (affine + tiling), access (exact dependence analysis on access relations), layout (data layout as part of the same optimisation).
- **Links:** [Google Patents US10180828](https://patents.google.com/patent/US10180828) · [USPTO PDF 10180828](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10180828)

#### 3.9 US8930926 — polyhedral parallelizer and locality optimizer (Reservoir Labs, DOE funded)
- Listed on OSTI; title not captured in the result. **Link:** [OSTI 1167014](https://www.osti.gov/biblio/1167014)

### F2 — Access map / data movement

#### 3.10 US9830133B1 / US11200035 — Automatic communication optimizations in a compiler based on a polyhedral representation (Reservoir Labs)
- **Inventors:** Muthu Baskaran, Richard Lethin, Benoit Meister, Nicolas Vasilache. US11200035 is a continuation of 13/712,659 (filed 12 Dec 2012), provisional 2011; Justia lists Reservoir Labs as assignee for 11200035 (Qualcomm transfer not confirmed for this number).
- **What it covers.** Optimise source code for reduced-communication execution on a second apparatus with local memory for data reuse: generate the DMA / copy code between global and local memory from the polyhedral access relations.
- **In three-formula terms (analysis).** F2 used to compute *what to copy*: the set `{input element | exists tile point p, access(p) = element}`, i.e. the window, as a polyhedron.
- **Links:** [Google Patents US9830133](https://patents.google.com/patent/US9830133) · [Justia 11200035](https://patents.justia.com/patent/11200035)

#### 3.11 US10698669 / US11500621 — Methods and apparatus for data transfer optimization
- Transfers between master and local memory (Reservoir line; inventor Allen K. Leung). **Links:** [USPTO PDF 10698669](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10698669) · [USPTO PDF 11500621](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11500621) · [Inventor list, Leung](https://patents.justia.com/inventor/allen-k-leung)

#### 3.12 US10496304 / US11068178B2 — Systems and methods for minimizing communications
- Same compiler line (`P`). **Links:** [USPTO PDF 10496304](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10496304) · [Google Patents US11068178](https://patents.google.com/patent/US11068178)

---

## 4. Public Qualcomm material (non-patent) on the same formulas
- HMX crouton (FP16 32 x 32, 2 KB) and tiles: [HMX Programmer's Reference Manual V81](https://docs.qualcomm.com/doc/80-N2040-62/80-N2040-62_REV_AA_Qualcomm_Hexagon_V81_HMX_Programmers_Reference_Manual.pdf)
- HVX 128-byte vectors: [HVX PRM](https://docs.qualcomm.com/doc/80-N3040-23/topic/v75-hvx-prm-introduction.html)
- Qualcomm on polyhedral compilation for the AI stack: [Qualcomm OnQ, May 2023](https://www.qualcomm.com/news/onq/2023/05/applying-advanced-compilation-technology-to-the-ai-stack) — "a search for good choices of reshaping of the polyhedra to the PEs of the machine"
- Hexagon-MLIR compiler paper: [arXiv 2602.19762](https://arxiv.org/abs/2602.19762)

## 5. Searched and not found
- No Qualcomm patent found that names the **crouton** layout or HMX tile layout.
- No Qualcomm patent found on **hardware address generation for conv** (stride/pad); hits were other assignees (see Ceremorphic, Arm, others in their folders).
- The deal terms for the Reservoir Labs asset transfer were not found; only the assignment chain on the patents.

## 6. Search log
- `Qualcomm patent convolution tensor data layout "crouton" OR "chunk" vector tightly coupled memory HVX depth 32`
- `Qualcomm patent neural network "loop tiling" OR "tiling" schedule compiler accelerator convolution`
- `Qualcomm patent "depth-first" convolution tiling layer fusion memory neural network`
- `Qualcomm Incorporated patent address generation convolution stride padding hardware accelerator im2col`
- `Qualcomm patent "tensor" "memory layout" OR "data layout" neural network processor tightly coupled memory`
- `Reservoir Labs acquired Qualcomm polyhedral compiler R-Stream patents`
- `"US11556798" Qualcomm depth-first cut point layer-based traversal Meghal Varia`
- `EP4587997A1 "Memory storage format for supporting machine learning acceleration" Qualcomm US application`
- `US10789055B2 "scalable hierarchical polyhedral compilation" claims Reservoir Labs Qualcomm`
- `Reservoir Labs patent "local memory compaction" OR "data layout" polyhedral`
- `"Methods and apparatus for automatic communication optimizations in a compiler based on a polyhedral representation" US11200035`
