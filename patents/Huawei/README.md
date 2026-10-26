# Huawei (Ascend / DaVinci, HiSilicon)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed; `P` = assignee inferred.

---

## 1. Why Huawei matters for your kernel

Huawei's Ascend (DaVinci architecture, Cube unit) is one of the most **layout-explicit** vendors. Its public format `NC1HWC0` is oneDNN `nChw{C0}c` with `C0 = 16` for fp16/fp32 and `C0 = 32` for int8, tied to the Cube unit that processes 32-byte x 32-byte data; weights use `FRACTAL_Z`, feature maps `FRACTAL_NZ`. The key patent, **"Operation accelerator, processing method, and related device" (US12306901B2)**, claims the `NC1HWC0` structure and a **matrix converter** (hardware im2col) that is decoupled from the MAC unit — F2 + F3 together.

---

## 2. Summary table

| # | Number (family) | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US12306901B2 (pub. WO2020073211A1); CN116710912A | Operation accelerator, processing method, and related device | **F2 + F3** | granted | V |
| 2 | US11238130B2 | Signal processing method and apparatus (fractal matrix multiplication) | F3 | granted | V |
| 3 | US11783163B2 | Hardware accelerator for IM2COL operation | F2 | granted | P (assignee not confirmed) |
| 4 | US12210871B2 | Model processing method and apparatus | F1 | granted | P |
| 5 | WO2023071658A1 | AI model processing / operation method and apparatus (operator compilation, minimum computing unit) | F1 | application | V |
| 6 | WO2023102678A1; US11275671B2 (family) | Adaptive buffer management to support dynamic tensor shape in DNN applications | F1 + F3 | application / granted | P |

---

## 3. Patent details

### 3.1 US12306901B2 — Operation accelerator, processing method, and related device  (the key one)
- **Assignee:** Huawei. Published as WO2020073211A1; Chinese family CN116710912A ("matrix multiplier and control method").
- **What it covers.** A **matrix converter** turns the input feature map into matrix form for the MAC array. The description: an **NC1HWC0 data structure** "solves a problem of low efficiency in processing small matrices in a TPU architecture by splitting a matrix converted from the input dataset into small matrices", and the **matrix conversion is decoupled from the MAC computation unit**. Claims cover **C0 consecutive channels per row vector** ("the C0 pieces of data are ... corresponding to an element in an input feature map and ... on C0 consecutive channels in a C direction"); a read instruction's **read stride matches the convolution kernel's slide stride** (per the WO description).
- **In three-formula terms (analysis).**
  - F3: `NC1HWC0` = `off = ((n*C1 + c/C0)*H + h)*W*C0 + w*C0 + c%C0` with `C1 = ceil(C/C0)` — identical in shape to oneDNN `nChw16c` (your output format family), block `C0 = 16` (fp16/fp32) or 32 (int8).
  - F2: the matrix converter reads, for each output, the window `x[c, oh*s - pad + r, ow*s - pad + s']` with the read stride tied to the conv stride — hardware im2col feeding the Cube unit.
- **Overlap with your kernel.** `NC1HWC0` with `C0 = 16` is the fp32/fp16 twin of your `nChw8c` output (block 8 on AVX2, 16 on AVX-512). The access-map read with stride-matched reads is the hardware version of your `iw = 3b + jj + ki - 1`.
- **Links:** [Google Patents US12306901B2](https://patents.google.com/patent/US12306901B2) · [USPTO PDF 12306901](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12306901) · [WO2020073211A1](https://patents.google.com/patent/WO2020073211A1/en) · [CN116710912A](https://patents.google.com/patent/CN116710912A/zh)

### 3.2 US11238130B2 — Signal processing method and apparatus
- **Assignee:** Huawei. Mentions **fractal matrix multiplication** (the `FRACTAL_Z` / `FRACTAL_NZ` family that pairs with `NC1HWC0`). **Link:** [Google Patents US11238130](https://patents.google.com/patent/US11238130)

### 3.3 US11783163B2 — Hardware accelerator for IM2COL operation
- A hardware im2col accelerator (assignee not confirmed in the snippet; `P`). **Link:** [USPTO PDF 11783163](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11783163)

### 3.4 WO2023071658A1 — AI model processing / operation method and apparatus
- **Assignee:** Huawei. Compiler with a sparse tool, graph optimisation module and **operator compilation module**; the **"minimum computing unit"** is the scale of matrix operation the accelerator does at once (the Cube tile) — F1/operator-mapping. **Link:** [WO2023071658A1](https://patents.google.com/patent/WO2023071658A1/zh)

### 3.5 US12210871B2 — Model processing method and apparatus
- Model-level processing/scheduling (`P`). **Link:** [Google Patents US12210871](https://patents.google.com/patent/US12210871)

### 3.6 WO2023102678A1 / US11275671B2 — Adaptive buffer management for dynamic tensor shapes
- Buffer management (Unified Buffer) for dynamically-shaped tensors; related to dynamic tiling. **Link:** [WO2023102678A1](https://patents.google.com/patent/WO2023102678A1/en)

---

## 4. Public (non-patent) Huawei material on the formulas (also prior art)
- `NC1HWC0` and `C0` rule (16 for fp16/fp32, 32 for int8), Cube unit 32 B x 32 B: [Ascend ATC tool docs](https://www.hiascend.com/document/detail/en/canncommercial/850/devaids/atctool/atctool_0006.html), [CANN API C0 rule](https://www.hiascend.com/doc_center/source/zh/canncommercial/700/operatordev/Ascendcopdevg/atlasascendc_api_07_0146.html)
- `FRACTAL_Z` for weights ("C1HW, N1, N0, C0"), `FRACTAL_NZ` for feature maps: Ascend operator-dev docs
- CATLASS tensor layout abstraction (CuTe-shaped fractal layouts): [DeepWiki CATLASS](https://deepwiki.com/ascend-catlass/catlass/2.2-tensor-layout-abstraction-(tla))
- Open-source Ascend `im2col` op: [cann/ops-math im2col](https://gitcode.com/cann/ops-math/blob/master/conversion/im2col/op_host/im2col_def.cpp)

## 5. Searched and not found
- No separate Huawei patent isolated on the **fractal (FRACTAL_Z/NZ) shapes** as such; they appear as public docs and inside US11238130 / the CATLASS library.
- No Huawei tiling-strategy patent for AI Core clearly isolated (WO2023071658 is the closest, at operator-compilation level).

## 6. Search log
- `Huawei patent img2col convolution matrix "operation accelerator" read stride convolution kernel sliding Ascend cube WO2020073211A1`
- `Huawei Technologies patent data format NC1HWC0 OR "fractal" matrix format conversion convolution neural network processor C0 16`
- `Huawei patent tensor tiling scheduling operator compiler "tiling" strategy AI core buffer neural network "loop" Ascend CANN`
