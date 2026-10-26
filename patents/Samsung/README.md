# Samsung Electronics

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Samsung matters for your kernel

Samsung spans NPU (Exynos, server NPU), processing-in-memory (HBM-PIM), and Winograd. Its most relevant patents are **F2 input-reuse buffers**: load the input feature map into a **reuse buffer at cyclically assigned addresses**, then hand overlapping subsets to the compute units for adjacent outputs — exactly the sliding-window reuse your 2x4 tile exploits (two output rows sharing 2 of 3 input rows). It also holds the **in-DRAM-ALU data-placement** family (dot/outer/tensor-product layouts) and a **Winograd** NPU patent.

---

## 2. Summary table

| # | Number (family) | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11971823B2 (pub. US20240232091A1); cont. US12613804B2 | Computing method and device with data sharing | **F2** | granted | V |
| 2 | US11562220B2; US12118453B2 | Neural processing unit capable of reusing data and method thereof | F2 | granted | V |
| 3 | US12423566B2 | (SRAM-sharing for reconfigurable NPUs; N x N NPU array) | F1 + F3 | granted 23 Sep 2025 | V |
| 4 | US12412079B2 | (Z-first reference NPU for mapping Winograd convolution) | F2 + F3 | granted 9 Sep 2025 | V |
| 5 | US11226816B2; US11782707B2; US12236239B2 | Systems and methods for data placement for in-memory-compute | **F3** | granted | V |

---

## 3. Patent details

### 3.1 US11971823B2 / US12613804B2 — Computing method and device with data sharing
- **Assignee:** Samsung Electronics Co., Ltd. **Inventors include** Channoh Kim, Yoojin Kim.
- **What it covers.** Load input feature map data into a **reuse buffer at addresses assigned in a rotating (cyclic) order**, so overlapping convolution windows share the stored data; the continuation adds multiple **"senders"** that pass overlapping subsets of buffered input to an **executer** for adjacent output positions (an NPU / neural accelerator).
- **In three-formula terms (analysis).** F2 reuse made explicit: for adjacent outputs `ow` and `ow+1`, the windows overlap in `S-1` columns; the cyclic buffer keeps those columns and only loads the one new column. This is the hardware/buffer form of your register reuse.
- **Links:** [USPTO PDF 11971823](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11971823) · [USPTO PDF 12613804](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12613804)

### 3.2 US11562220B2 / US12118453B2 — NPU capable of reusing data
- Reuse **weights** during depth-wise convolution to cut main-memory reads (weight-stationary for depthwise). **Links:** [Google Patents US12118453](https://patents.google.com/patent/US12118453) · [USPTO PDF 11562220](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11562220)

### 3.3 US12423566B2 — SRAM-sharing reconfigurable NPUs
- An **N x N array of NPUs**, each with memory and a convolution MAC circuit storing input and output feature-map values; SRAM shared across the array. F1 (mapping) + F3 (shared storage). [OG notice](https://patentsgazette.uspto.gov/week38/OG/html/1538-4/US12423566-20250923.html)

### 3.4 US12412079B2 — Z-first reference NPU for Winograd convolution
- An NPU supporting both direct and **Winograd** convolution modes, with a **Z-first** reference (traversal/layout) order. F2 (Winograd transforms the access map) + F3 (Z-first layout). [OG notice](https://patentsgazette.uspto.gov/week36/OG/html/1538-2/US12412079-20250909.html)

### 3.5 US11226816B2 / US11782707B2 / US12236239B2 — Data placement for in-memory-compute
- **Inventors:** Krishna T. Malladi, Wenqin Huangfu. HBM with in-DRAM ALUs; the patent gives **dot-product, outer-product and tensor-product data layouts** for operands placed across DRAM banks. Prov. 62/975,577 (12 Feb 2020); US11226816 granted 18 Jan 2022.
- **In three-formula terms (analysis).** F3 chosen to match the in-memory operation: the layout that makes a bank-parallel dot/outer/tensor product conflict-free — the PIM analogue of SambaNova's memory-unit partitioning.
- **Links:** [Google Patents US11226816](https://patents.google.com/patent/US11226816) · [USPTO PDF 11782707](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11782707) · [Justia 12236239](https://patents.justia.com/patent/12236239)

---

## 4. Search log
- `Samsung Electronics patent neural network accelerator convolution "input feature map" reuse buffer tile data layout "NPU" address generation`
- `"Systems and methods for data placement for in-memory-compute" US11226816 assignee inventors` (see also [`../d-Matrix/README.md`](../d-Matrix/README.md) sec. 4)
