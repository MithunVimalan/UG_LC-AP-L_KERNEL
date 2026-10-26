# Apple

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred. Assignee list: [Justia, Apple Inc](https://patents.justia.com/assignee/apple-inc).

---

## 1. Why Apple matters for your kernel

Apple's Neural Engine (ANE) patents (many by Ponan Kuo and others) describe the **multiply-accumulate array and its input/kernel feed** rather than a named storage format. The relevant F-coverage:
- **F1/F2** — "Multi dimensional convolution in neural network processor": MAC circuits convolve input with a kernel, with **accumulator batches holding several output channels' depth planes** (an output-channel-blocked schedule).
- **F3** — Apple's public ANE guidance fixes a layout: activations as **(B, C, 1, S)** channels-first, last axis **aligned to 64 bytes** (MPSImage stores 4 channels per texture slice, `slice = img*ceil(C/4) + c/4`). These appear as docs/ML-research, not as a patent found here.

Apple is a **mild layout competitor**: ANE prefers channels-first with a 64-byte-aligned last axis, the opposite minor axis from your channels-last `nChw8c`.

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11853868B2 | Multi dimensional convolution in neural network processor | F1 + F2 | granted | V |
| 2 | US11989640B2 | Scalable neural network processing engine | F1 | granted | V |
| 3 | US11487846B2 | Performing multiply and accumulate operations in neural network processor | F1 | granted | V |
| 4 | US11580353B2 | Neural network processor for handling differing datatypes | F1 + F3 | granted | V |
| 5 | US12106206B2 | Multi-operational modes of neural engine circuit | F1 | granted | V |
| 6 | US12229657B2 | Multi-mode planar engine for neural processor | F1 | granted | V |

---

## 3. Patent details

### 3.1 US11853868B2 — Multi dimensional convolution in neural network processor
- **Assignee:** Apple Inc. A neural engine circuit with **multiply-add circuits that convolve input data with a kernel**, plus **accumulator batches that hold output depth planes for several output channels**.
- **In three-formula terms (analysis).** F1: the schedule computes multiple output channels at once (your 8-channel `nChw8c` accumulator, generalised). F2: the MAC feed reads the conv window per output.
- **Links:** [Google Patents US11853868](https://patents.google.com/patent/US11853868) · [USPTO PDF 11853868](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11853868)

### 3.2 US11989640B2 — Scalable neural network processing engine
- MAC on input data using **stored kernel data**, then post-processing; input/output may be single- or multi-channel. F1. [USPTO PDF 11989640](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11989640)

### 3.3 US11580353B2 — NN processor for handling differing datatypes
- Input buffer + **kernel extract circuit** + MAC supporting INT8 and FP16. The kernel extract circuit is an F3/F2 element (pulls the right weights for the array). [Justia 11580353](https://patents.justia.com/patent/11580353)

### 3.4 US11487846B2 / US12106206B2 / US12229657B2
- MAC operations; multi-operational modes; multi-mode planar engine (schedule variations). [Google Patents US11487846](https://patents.google.com/patent/US11487846) · [Google Patents US12106206](https://patents.google.com/patent/US12106206) · [OG 12229657](https://patentsgazette.uspto.gov/week07/OG/html/1531-3/US12229657-20250218.html)

---

## 4. Public (non-patent) ANE / MPS layout material (F3) — also prior art
- ANE tensor format (B, C, 1, S) channels-first; last axis aligned to 64 bytes: [Apple ML Research, Deploying Transformers on the ANE](https://machinelearning.apple.com/research/neural-engine-transformers)
- MPSImage: 4 channels per slice, `slice = img*ceil(C/4) + c/4`: [MPSImage docs](https://developer.apple.com/documentation/metalperformanceshaders/mpsimage)

## 5. Searched and not found
- No Apple patent found that names the (B,C,1,S) ANE layout or the MPSImage 4-channel slice format; those are docs.

## 6. Search log
- `Apple patent neural engine ANE convolution data "channels" tensor layout "4 channels" OR interleaved memory format MPS image accelerator`
- `Apple Inc patent neural engine convolution "kernel" "input" data "stored" layout "plurality of channels" multiply accumulate array shift`
