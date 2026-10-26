# SiMa.ai (SiMa Technologies, Inc.)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed; `P` = assignee inferred.

Assignee list: [Justia, SiMa Technologies Inc](https://patents.justia.com/assignee/sima-technologies-inc). Inventor lists: [Reed Kotler](https://patents.justia.com/inventor/reed-kotler), [Nishit Shah](https://patents.justia.com/inventor/nishit-shah?page=2).

---

## 1. Why SiMa.ai matters for your kernel

SiMa's MLSoC (edge) has a machine learning accelerator (MLA) built from **Tiles** (processing elements) running **statically scheduled instructions**. Its portfolio is almost entirely **F1**: compiler allocation of layers to Tile groups, deterministic phases, conflict-free data routing between Tiles, instruction streaming. One patent is **F2/F1 for convolution** (decompose a multi-kernel convolution into matrix multiplies, US11488066), and one title is explicitly **layout-based** data transfer between Tiles (US12260253).

---

## 2. Summary table

| # | Number | Title | F | Filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US11488066B2 (app. 16/854,803; pub. US20210326750A1) | Efficient convolution of multi-channel input samples with multiple kernels | **F2** + F1 | 2020 | granted | V |
| 2 | US11734605B2 | Allocating computations of a machine learning network in a machine learning accelerator | F1 | — | granted | V |
| 3 | US11734549B2 | Avoiding data routing conflicts in a machine learning accelerator | F1 | filed 1 May 2020 | granted | V |
| 4 | US11886981 | (inter-processor data transfer with compiler-generated static schedule; exact title not captured) | F1 | filed 17 Apr 2020 | granted | V |
| 5 | US12260253 | Layout-based data transfer between synchronized, interconnected processing elements | F1 + F3 | — | granted (Mar 2025) | V |
| 6 | US12067465 | Instruction streaming for a machine learning accelerator | F1 | — | granted Aug 2024 | V |
| 7 | US11989581 | Software managed memory hierarchy | F1 | — | granted | V |
| 8 | US12026510 | Using cycle counts to serve compute elements (full title not captured) | F1 | — | granted | V |
| 9 | US12657444 | Machine learning network implemented by statically scheduled instructions | F1 | — | granted | P |
| 10 | US20210303987A1 | Power reduction for machine learning accelerator | — | — | application | P |

---

## 3. Patent details

### 3.1 US11488066B2 — Efficient convolution of multi-channel input samples with multiple kernels
- **Assignee:** SiMa Technologies Inc (original). **Inventors:** Nishit Shah, Srivathsa Dhruvanarayan. App. 16/854,803; pub. US20210326750A1.
- **What it covers.** **Convolutions of an input sample with multiple kernels are decomposed into matrix multiplications**; each output value is a **partial product for one output pixel and one kernel**, and the compiler allocates the matrix multiplications among multiple Tiles.
- **In three-formula terms (analysis).** F2 written as a matrix product per kernel tap: for each tap `(r, s)`, `Y[:, k] += X_shift(r,s)[:, c] * W[r, s][c, k]` — the "sum over shifted 1x1 convolutions" form of conv, where the shift is the access map `ih = oh + r - pad`. F1 then distributes these products over Tiles.
- **Overlap with your kernel.** Your inner loop is the same sum over taps (`kh, ki`) of broadcast-input x weight-vector products. The patent's claims concern the decomposition plus Tile allocation on SiMa's MLA; read the claims if you document your kernel as "conv decomposed into per-tap matrix multiplies".
- **Links:** [Google Patents US11488066](https://patents.google.com/patent/US11488066) · [USPTO PDF 11488066](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11488066) · [Justia 20210326750](https://patents.justia.com/patent/20210326750)

### 3.2 US11734605B2 — Allocating computations of an ML network in an MLA
- **Inventors:** Reed Kotler, Nishit Shah. The compiler **allocates instructions to groups of Tiles so different groups implement different layers**, sizes the groups with a computation metric, and runs work in **deterministic phases**. A related Kotler/Shah patent schedules **partial networks across several layers** so intermediate outputs stream Tile-to-Tile without external-memory round trips (layer fusion).
- **Links:** [Google Patents US11734605](https://patents.google.com/patent/US11734605) · [Justia 11734605](https://patents.justia.com/patent/11734605)

### 3.3 US11734549B2 / US11886981 / US12260253 — data routing and transfers between Tiles
- The compiler generates **data transfer instructions** with a **static schedule** so concurrent transfers between processing elements neither collide nor stall; US12260253 makes the transfers **layout-based** (the data layout determines the transfer pattern between synchronized PEs).
- **Links:** [Google Patents US11734549](https://patents.google.com/patent/US11734549) · [Justia, Reed Kotler](https://patents.justia.com/inventor/reed-kotler) · [Justia issue list 25 Mar 2025](https://patents.justia.com/patents-by-issue-date/2025/03/25?page=85)

### 3.4 US12067465 — Instruction streaming for an MLA
- **Inventor:** Subba Rao Venkata Kalari. Instructions are split into **blocks fetched from off-chip memory**; each block holds deterministic instructions on a static schedule plus a count of non-deterministic memory operations. [Golden US12067465](https://golden.com/wiki/US_Patent_12067465_Instruction_streaming_for_a_machine_learning_accelerator-4BPP4GW)

### 3.5 US11989581 — Software managed memory hierarchy
- Off-chip -> on-chip transfers check whether on-chip memory still holds **unconsumed data** before overwriting it (software double-buffering). [Golden US11989581](https://golden.com/wiki/US_Patent_11989581_Software_managed_memory_hierarchy-R3RGKK5)

---

## 4. Searched and not found
- No SiMa patent found on a named **tensor storage layout** or on **hardware conv address generation** (stride/pad/halo).

## 5. Search log
- `SiMa Technologies patent machine learning accelerator MLA tensor tiling convolution data layout "SiMa"`
- `SiMa Technologies patent "machine learning accelerator" tiles mosaic compiler "convolution" partition data "processing elements"`
- `US11488066 "Efficient convolution of multi-channel input samples with multiple kernels" assignee SiMa claims`
- `"sima technologies inc" patents justia "machine learning accelerator" data transfer tiles list`
