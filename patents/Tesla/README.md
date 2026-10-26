# Tesla

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred. Assignee list: [Justia, Tesla Inc](https://patents.justia.com/assignee/tesla-inc).

---

## 1. Why Tesla matters for your kernel

Tesla's FSD / Dojo accelerator team (Peter Bannon, Emil Talpes, William McGee, Kevin Hurd) holds a tight family on a **hardware data formatter** feeding a computational (systolic) array. The key idea is a **non-consecutive gather**: the formatter assembles a compute operand from **two subsets that are each contiguous in memory but not adjacent to each other** — which is exactly the conv access map (a window is contiguous within a row but jumps by the row pitch between rows). That is F2 + F3 in hardware.

---

## 2. Summary table

| # | Number (family) | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11157441B2 (app. 15/920,173, 13 Mar 2018); cont. US11681649B2; US12216610B2 (app. 18/335,944) | Computational array microprocessor system using non-consecutive data formatting | **F2 + F3** | granted | V |
| 2 | US11157287B2 | Computational array microprocessor system with variable latency memory access | F1 | granted | V |
| 3 | US11893393B2 | Computational array microprocessor system with hardware arbiter managing memory requests | F1 | granted | V |

---

## 3. Patent details

### 3.1 US11157441B2 / US11681649B2 / US12216610B2 — Computational array using non-consecutive data formatting
- **Assignee:** Tesla, Inc. **Inventors:** Emil Talpes, William McGee, Peter Joseph Bannon. Family: 15/920,173 (filed 13 Mar 2018) -> US11157441 (26 Oct 2021); -> 17/451,984 -> US11681649; -> 18/335,944 -> US12216610 (4 Feb 2025).
- **What it covers.** A computational array plus a **hardware data formatter** that gathers groups of values for parallel processing; the formatter can gather **two subsets that are each contiguous in memory without the subsets being adjacent to each other**.
- **In three-formula terms (analysis).** F2 for a conv row pair: subset A = row `ih` columns `[iw, iw+W0)`, subset B = row `ih+1` same columns — each contiguous, separated by the row pitch. The formatter builds the operand without an im2col copy. This is the hardware twin of your "read 4 distinct input rows for a 2-row output tile", and of the row-pitch stride in your offset formulas.
- **Overlap with your kernel.** Conceptually identical to how your kernel strides between rows; the claims are for a dedicated gather-formatter in front of a systolic array.
- **Links:** [Google Patents US11157441](https://patents.google.com/patent/US11157441) · [Google Patents US12216610](https://patents.google.com/patent/US12216610) · [OG 12216610](https://patentsgazette.uspto.gov/week05/OG/html/1531-1/US12216610-20250204.html) · [Justia, Peter Joseph Bannon](https://patents.justia.com/inventor/peter-joseph-bannon)

### 3.2 US11157287B2 — variable latency memory access; US11893393B2 — hardware arbiter
- The schedule/memory side of the same array: tolerate variable-latency memory, and an **arbiter managing memory requests** so the array stays fed. F1. **Links:** [Google Patents US11157287](https://patents.google.com/patent/US11157287) · [USPTO PDF 11893393](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11893393)

---

## 4. Searched and not found
- No Tesla patent found that fixes a named tensor storage layout (Dojo's configurable 8/16-bit vector formats are described in Hot Chips material, not a patent found here).

## 5. Search log
- `Tesla patent "Dojo" OR "neural network accelerator" convolution data array SRAM tensor layout Bannon Talpes systolic`
- `Tesla Inc patent "computational array" "data formatter" machine learning accelerator vector matrix processor Bannon`
