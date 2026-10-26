# d-Matrix

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed; `P` = assignee inferred.

Assignee list: [Justia, d-MATRIX CORPORATION](https://patents.justia.com/assignee/d-matrix-corporation).

---

## 1. Why d-Matrix matters (and where it does not)

d-Matrix (Santa Clara; Corsair accelerator) builds **digital in-memory compute (DIMC)** chiplets: matrix multiplies happen inside SRAM arrays, with **block floating point** (now the OCP Microscaling / MX formats) natively supported ([d-Matrix technical white paper](https://d-matrix.ai/pdf/d-Matrix-WhitePaper-Technical-FINAL.pdf), [technology page](https://www.d-matrix.ai/technology/)). Its granted claims found here are about **transformer workloads on DIMC chiplets** (QK^T dot product, softmax on a SIMD unit), which is F1 at the operator-mapping level.

**No d-Matrix patent on convolution address generation (F2) or on a tensor storage layout (F3) was found.** In a DIMC design, "layout" is mostly "which weights live in which SRAM array", a placement decision rather than an address formula.

---

## 2. Summary table

| # | Number (family) | Title | F | Filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US11847072B2 (app. 17/538,923) | AI accelerator apparatus using in-memory compute chiplet devices for transformer workloads | F1 | 30 Nov 2021 | granted | V |
| 2 | US12271321B2 (cont. of 17/538,923); family pubs US20240160586A1, US20250036582A1 (which pub belongs to which grant not confirmed) | same title | F1 | 24 Oct 2023 | granted 8 Apr 2025 | V |
| 3 | US12260223B2 | (continuation-in-part of the same family) | F1 | — | granted 25 Mar 2025 | V |
| 4 | US12353985B2 | (server system, same platform; different inventors) | F1 | — | granted 8 Jul 2025 | V |

---

## 3. Patent details

### 3.1 US11847072B2 / US12271321B2 — AI accelerator apparatus using in-memory compute chiplet devices for transformer workloads
- **Assignee:** d-MATRIX CORPORATION, Santa Clara, CA. **Inventors:** Sudeep Bhoja, Siddharth Sheth.
- **Family:** 17/538,923 filed 30 Nov 2021 -> US11847072; continuation filed 24 Oct 2023 -> US12271321 (granted 8 Apr 2025); CIP -> US12260223; related server-system patent US12353985.
- **What it covers.** A chiplet-based accelerator in which **DIMC devices** accelerate transformer computations; a dependent claim has a DIMC device **accelerate the Q·K^T dot product followed by softmax**, and another adds a **SIMD device for the softmax**.
- **In three-formula terms (analysis).** F1 at the operator level: which device runs which part of attention, and in what order. No per-element access or layout formula is claimed in the snippets seen.
- **Links:** [Google Patents US11847072](https://patents.google.com/patent/US11847072) · [USPTO PDF 11847072](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11847072) · [OG 12271321](https://patentsgazette.uspto.gov/week14/OG/html/1533-2/US12271321-20250408.html) · [OG 12260223](https://patentsgazette.uspto.gov/week12/OG/html/1532-4/US12260223-20250325.html) · [Google Patents US12353985](https://patents.google.com/patent/US12353985) · [OG 12353985](https://patentsgazette.uspto.gov/week27/OG/html/1536-2/US12353985-20250708.html) · [Google Patents US20250036582A1](https://patents.google.com/patent/US20250036582A1/en) · [Justia 20240160586](https://patents.justia.com/patent/20240160586)

---

## 4. Related patents by others (not d-Matrix) found in the same searches
- **Samsung** — US11226816B2 / US11782707B2 / US12236239B2 "Systems and methods for data placement for in-memory-compute" (HBM with in-DRAM ALUs; dot-product, outer-product and tensor-product data layouts). See [`../Samsung/README.md`](../Samsung/README.md).
- **IBM** — US12456043B2, compute-in-memory tile ([OG notice](https://patentsgazette.uspto.gov/week43/OG/html/1539-4/US12456043-20251028.html)).
- "Tiled in-memory computing architecture" US12536118 — assignee not shown ([USPTO PDF](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12536118)).

## 5. Search log
- `d-Matrix Corporation patent digital in-memory compute matrix tensor data layout "d-Matrix" granted`
- `d-Matrix patent "in-memory compute" matrix multiplication block floating point "data" layout chiplet transformer US11847072 claims`
- `patents.justia.com assignee "d-Matrix Corporation" patents in-memory compute tensor`
- `d-Matrix patent US11847072 "AI accelerator apparatus using in-memory compute chiplet devices for transformer workloads" inventors`
- `"Patents Assigned to d-MATRIX CORPORATION" justia matrix tile weights layout "in-memory compute" block floating point`
