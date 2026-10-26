# Amazon (AWS Annapurna Labs — Inferentia, Trainium, Neuron compiler)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed (Amazon Technologies, Inc.); `P` = assignee inferred (terminology: "state buffer", "PE array", NeuronCore).

---

## 1. Why Amazon matters for your kernel

Amazon's accelerator portfolio (inventors Ron Diamant, Jeffrey T. Huynh, Vignesh Vivekraja, Haichen Li, Hongbin Zheng, Kun Xu and others) is the **densest F2 portfolio on systolic-array convolution** in this study. Every non-trivial conv variant has its own family, each one a different way of writing the access map `ih = oh*stride - pad + r*dil`:

- **stride** -> "Memory operation for systolic array": which input elements to fetch and **how many to skip** per weight element, from the stride;
- **dilation** -> "Dilated convolution using systolic array": coordinates that fall in the **zero-padding region**, partial sums per stride location;
- **transposed conv** -> "Transposed convolution using systolic array": input subsets chosen from the weight's coordinates in a **180-degree rotated** weight array and the stride;
- **DMA** -> "Tensorized DMA descriptors": multi-dimensional **stride + repetition count** per dimension.

On the Trainium side the on-chip **state buffer (SBUF)** is a software-managed scratchpad of 128 partitions, one per systolic-array row ([arXiv 2608.21555](https://arxiv.org/abs/2608.21555) describes it) — so F3 is a compiler decision, and Amazon has compiler patents on **layout analysis** and **state-buffer reshaping**.

---

## 2. Summary table

| # | Number (family) | Title | F | App. | Status | V |
|---|---|---|---|---|---|---|
| 1 | US11501145B1; cont. US12026607B1 | Memory operation for systolic array | **F2** | — | granted | V |
| 2 | US11379555B2 (app. 16/457,503; pub. US20200410036A1); cont. US11816559B2; WO2020264264A1 | Dilated convolution using systolic array | **F2** | 16/457,503 | granted | V |
| 3 | US11681902; US11954583B2; WO2021061566A1 | Transposed convolution using systolic array | **F2** | — | granted | V |
| 4 | US11550736B1; US11983128B1 (app. 18/067,109) | Tensorized direct memory access descriptors; Multidimensional and multiblock tensorized DMA descriptors | F2 + F3 | — | granted | V |
| 5 | US10884707B1 (app. 16/455,201; pub. US20200409664A1); US11347480 | Transpose operations using processing element array | F3 | — | granted | V |
| 6 | US11741350B2 (app. 16/698,461); US12198041B2 | Efficient utilization of processing element array | F1 | — | granted | V |
| 7 | US12182688 (family) | (hierarchical partitioning; extra rows for under-utilizing convolutions) | F1 | — | granted | P |
| 8 | US12417082B1 | Frontier node-based data layout analysis framework | **F3** (+F1) | — | granted | P |
| 9 | US11494321 | State buffer memloc reshaping | F3 | — | granted | P |
| 10 | US12664430B1 | Acceleration of neural networks with stacks of convolutional layers | F1 | — | granted | P |

---

## 3. Patent details

### F2 — Access map

#### 3.1 US11501145B1 / US12026607B1 — Memory operation for systolic array
- **Assignee:** Amazon Technologies. **Inventors include** Ron Diamant, Jeffrey T. Huynh.
- **What it covers.** A neural network accelerator **loads one weight element into the systolic array** and, from **stride-based parameters**, extracts **how many input elements to fetch and how many to skip** from memory for that weight element; the background describes the filter traversing the image "in multiple strides".
- **In three-formula terms (analysis).** This is the weight-stationary form of F2: fix the tap `(c, r, s)` (loaded into the array), then the inputs it touches are the strided set `x[c, oh*stride - pad + r, ow*stride - pad + s]` for all outputs — a regular pattern of "fetch 1, skip stride-1". Your kernel does the dual: fix the output tile, loop over taps.
- **Links:** [Google Patents US11501145](https://patents.google.com/patent/US11501145) · [Justia 11501145](https://patents.justia.com/patent/11501145) · [Google Patents US12026607](https://patents.google.com/patent/US12026607)

#### 3.2 US11379555B2 / US11816559B2 — Dilated convolution using systolic array
- **Assignee:** Amazon Technologies Inc. **Inventors:** Jeffrey T. Huynh, Ron Diamant. App. 16/457,503; pub. US20200410036A1; PCT WO2020264264A1.
- **What it covers.** Dilated convolution on a systolic array: a controller computes **start addresses and fetch counts** for input elements from a stride pattern; a claim excerpt refers to "the first subset of the coordinates being in the **zero-padding region**"; **non-zero partial sums for different stride locations** are kept in a column summation buffer per stride location.
- **In three-formula terms (analysis).** F2 with dilation `d`: `iw = ow*stride - pad + s*d`. For a fixed tap, the touched inputs form a lattice; coordinates outside `[0, W)` are the padding region and are skipped rather than fetched as zeros.
- **Links:** [Google Patents US11379555](https://patents.google.com/patent/US11379555) · [Justia 20200410036](https://patents.justia.com/patent/20200410036) · [Google Patents US11816559](https://patents.google.com/patent/US11816559) · [WO2020264264A1](https://patents.google.com/patent/WO2020264264A1/en)

#### 3.3 US11681902 / US11954583B2 — Transposed convolution using systolic array
- **Assignee:** Amazon. **Inventors (US11954583):** Jeffrey T. Huynh, Vignesh Vivekraja.
- **Claim summary (US11954583).** Select **input subsets based on weight coordinates in a 180-degree rotated weight array**, load weight elements into the systolic array, stream the matching input subsets to compute partial sums. US11681902's abstract: fetch input subsets based on the **transposed convolution's stride** and the weight's coordinates in the rotated array.
- **In three-formula terms (analysis).** Transposed conv inverts F2: `oh = ih*stride - pad + r`, so for each tap the contributing inputs are those with `(oh + pad - r) % stride == 0`. Rotating the kernel by 180 degrees turns it back into an ordinary correlation over a zero-inserted input, without materialising the zeros.
- **Links:** [Justia 11681902](https://patents.justia.com/patent/11681902) · [Google Patents US11954583](https://patents.google.com/patent/US11954583) · [WO2021061566A1](https://patents.google.com/patent/WO2021061566A1/en)

#### 3.4 US11550736B1 / US11983128B1 — Tensorized DMA descriptors
- **Assignee:** Amazon Technologies, Inc. **Inventors (US11983128):** Kun Xu, Ron Diamant, Ilya Minkin, Mohammad El-Shabani, Raymond S. Whiteside, Uday Shilton Udayaselvam. App. 18/067,109.
- **What it covers.** DMA circuitry builds **tensorized memory descriptors** whose addresses have **multi-dimensional strides**, each striding dimension with **a stride and a repetition count**; tensorized **header** descriptors carry the striding of one dimension each; tensorized **template** descriptors cover data blocks sharing a striding pattern without a uniform stride (multi-block).
- **In three-formula terms (analysis).** F3 as a descriptor: `addr = base + sum_d i_d * stride_d`, `0 <= i_d < count_d` — the same dot product, issued by DMA. A conv input window for one output tile (your 4 x 6 window) is one such descriptor.
- **Links:** [Google Patents US11983128](https://patents.google.com/patent/US11983128) · [Justia 11983128](https://patents.justia.com/patent/11983128) · [Google Patents US11550736](https://patents.google.com/patent/US11550736) · [USPTO PDF 11550736](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11550736)

### F3 — Layout

#### 3.5 US10884707B1 / US11347480 — Transpose operations using processing element array
- **Assignee:** Amazon Technologies Inc. **Inventors:** Haichen Li, Ron Diamant, Jeffrey T. Huynh, Yu Zhou, Se Jong Oh. App. 16/455,201; pub. US20200409664A1.
- **What it covers.** Use the PE array itself (multiplying by an identity) to transpose data, i.e. a layout change done by the compute engine instead of by DMA.
- **Links:** [Google Patents US10884707](https://patents.google.com/patent/US10884707) · [Justia 20200409664](https://patents.justia.com/patent/20200409664) · [USPTO PDF 11347480](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11347480)

#### 3.6 US12417082B1 — Frontier node-based data layout analysis framework (`P`)
- **What it covers.** A compiler **groups loop nests of a compute-flow graph into connected components**, finds a **compatible data layout for each component**, then generates machine instructions and allocates memory from those layouts (PE array + state buffer terminology).
- **In three-formula terms (analysis).** F3 chosen jointly for all loop nests that share a tensor — the graph-level version of "your `nChw8c` output must be what the next layer's `mimo` input expects".
- **Links:** [Google Patents US12417082](https://patents.google.com/patent/US12417082) · [USPTO PDF 12417082](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12417082)

#### 3.7 US11494321 — State buffer memloc reshaping (`P`)
- Reduce DMA operations in the generated instruction code by **reshaping memory locations in the state buffer**. **Link:** [USPTO PDF 11494321](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11494321)

### F1 — Schedule

#### 3.8 US11741350B2 / US12198041B2 — Efficient utilization of processing element array
- **Assignee:** Amazon Technologies Inc. **Inventors include** Jeffrey T. Huynh, Ron Diamant, Hongbin Zheng. App. 16/698,461; pub. US20210158132A1.
- **What it covers (description).** Rows of the PE array **share input data**: the input is **read from memory once and replicated across rows** by an input selector circuit, raising array utilisation and lowering memory bandwidth; when the **convolution stride is greater than one**, the input is **partitioned into blocks loaded into different rows**.
- **Relevance (analysis).** A layer with few input channels (like your `C = 3`) fills few PE rows; replicating the input so that rows hold different taps is the systolic-array fix — the same problem oneDNN's `flat` branch and Qualcomm US12165237B2 address from the layout side. (The related US12182688 family, inventor list incl. Huynh, adds instructions to process input matrices in parallel across more rows when a conv under-utilises the array.)
- **Links:** [Google Patents US11741350](https://patents.google.com/patent/US11741350) · [Justia 11741350](https://patents.justia.com/patent/11741350) · [Google Patents US12198041](https://patents.google.com/patent/US12198041)

#### 3.9 US12182688 family / US12664430B1
- Hierarchical partitioning with extra rows for under-utilizing convolutions (inventor list includes Huynh) — [Justia, Jeffrey T. Huynh](https://patents.justia.com/inventor/jeffrey-t-huynh)
- Acceleration of neural networks with stacks of convolutional layers — [Google Patents US12664430B1](https://patents.google.com/patent/US12664430B1/en)

---

## 4. Search log
- `"Transposed convolution using systolic array" US11681902 OR US11954583 assignee Amazon inventors`
- `US11379555B2 "Dilated convolution using systolic array" assignee inventors Amazon OR Google`
- `"Multidimensional and multiblock tensorized direct memory access descriptors" US11983128 assignee`
- `Amazon Technologies patent convolution systolic array "padding" OR "stride" input data "memory" address Diamant Huynh`
- `"Transpose operations using processing element array" US10884707 assignee; "Efficient utilization of processing element array" US11741350`
- `Amazon Technologies patent neural network compiler "data layout" OR "memory layout" tensor accelerator "state buffer"`
- `US12417082B1 "Frontier node-based data layout analysis framework" assignee Amazon`
