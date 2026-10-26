# IBM (International Business Machines)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed in a search result; `P` = assignee inferred (here: same shared NNPA disclosure as a confirmed IBM patent).

---

## 1. Why IBM matters for your kernel

IBM's relevant portfolio is the **z16/z17 Telum "Neural Network Processing Assist" (NNPA)** family, filed in 2021. About a dozen patents share one long disclosure, and that disclosure defines a **blocked tensor layout of exactly the oneDNN `nChw{X}c` type**, with explicit floor/mod index formulas and padding. Most claims in the family are about the instruction (functions, parameter blocks, exceptions, data conversion), but one family member claims **reformatting a tensor into sub-tensors that start on memory-unit boundaries** — that is F3.

The second IBM item is a classic: **US5953531, compiler selection of loop block/tile sizes** under cache and TLB constraints — F1 in the purest form (expired by age).

The IBM Research AI accelerator (VLSI 2018: PE array, two 8 KB L0 scratchpads, 2 MB shared scratchpad, weight- and output-stationary modes; [RealWorldTech](https://www.realworldtech.com/?p=4687)) was found only as an article, not as a patent, in these searches.

---

## 2. The IBM NNPA "4D-feature" layout (from the shared disclosure)

The disclosure maps a row-major generic 4-D tensor `[E4][E3][E2][E1]` to a 5-D tensor:

```
[E4] [ceil(E1/64)] [E3] [ceil(E2/32)*32] [64]
element [e4][e3][e2][e1]  ->  [e4][floor(e1/64)][e3][e2][e1 mod 64]
```

"A resulting tensor can be represented as a 4D-tensor of, e.g., 64-element vectors or a 5D-tensor"; pad elements "are ignored for the input tensors and unpredictable for output tensors"; "the resulting tensor may be larger than the generic tensor". A companion **4D-kernel** layout "re-arranges the elements of a 4D-tensor to reduce the number of memory accesses and data gathering steps when executing ... a convolution".

**Analysis — what this is in your notation.** With `E4 = N, E3 = H, E2 = W, E1 = C` (NHWC order), the layout is

```
off = (((n*ceil(C/64) + c/64)*H + h)*Wp + w)*64 + c%64,      Wp = roundup(W, 32)
```

i.e. **`nChw64c` with W padded to 32**. With a 2-byte NNP data type, one 64-element vector is 128 bytes, and 32 of them are 4 KB — one page per `(n, c/64, h, w/32)` block (derived). This is the same recipe as oneDNN `nChw16c` (split C, pad, channel block innermost) with a 64-wide block and an added W-padding rule.

---

## 3. Summary table

| # | Number (family) | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11669331B2 (app. 17/350,393; pub. US20220405101A1) | Neural network processing assist instruction | F3 (disclosure) | granted | V |
| 2 | US12639396 (pub. US20220405348A1) | Reformatting of tensors to provide sub-tensors | **F3 (claims)** | granted | P |
| 3 | US12694294 (pub. US20220405598A1) | Concatenating input and output tensors for use in recurrent neural networks | F3 | granted | P |
| 4 | US12236338 | Single function to perform combined matrix multiplication and bias add operations | (F3 disclosure) | granted | P |
| 5 | US11269632 | Data conversion to/from selected data type with implied rounding mode | (F3 disclosure) | granted | P |
| 6 | US11797270 | Single function to perform multiple operations with distinct operation parameter validation | (F3 disclosure) | granted | P |
| 7 | US11675592 | Instruction to query for model-dependent information | (F3 disclosure) | granted | P |
| 8 | US11693692; US12008395 | Program event recording storage alteration processing for a neural network accelerator instruction | (F3 disclosure) | granted | P |
| 9 | US11734013 | Exception summary for invalid values detected during instruction execution | (F3 disclosure) | granted | P |
| 10 | US12079658 | Detection of invalid machine-specific data types during data conversion | (F3 disclosure) | granted | P |
| 11 | US5953531A | Method of, system for, and computer program product for minimizing loop execution time by optimizing block/tile sizes | **F1** | granted, expired by age | V |

---

## 4. Patent details

### 4.1 US11669331B2 — Neural network processing assist instruction
- **Assignee:** International Business Machines Corp. **Inventors:** Laith M. AlBarakat, Jonathan D. Bradbury, Timothy Slegel, Simon Weishaupt, Anthony Saporito, Cedric Lichtenau. App. 17/350,393; pub. US20220405101A1.
- **What it covers.** One architected machine instruction implementing a **query function** and many **non-query tensor functions**, executed on the general-purpose processor or on a co-processor that **shares main memory** (no pinning, no I/O). The description carries the 4D-feature / 4D-kernel layouts above.
- **Links:** [Google Patents US11669331](https://patents.google.com/patent/US11669331) · [USPTO PDF 11669331](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11669331)

### 4.2 US12639396 — Reformatting of tensors to provide sub-tensors  (F3 in the claims)
- **Abstract.** "A tensor of a first select dimension is reformatted to provide one or more sub-tensors of a second select dimension", **each sub-tensor starting on a memory-unit boundary**, with the data rearranged to fit. Purpose: split 4-D tensors into **2-D sub-tensors** that are easier to address and can be **loaded or stored in one operation**. The number of pages of the 2-D tensor follows from the 4-D size via **ceil functions** for rows and elements per row.
- **In three-formula terms (analysis).** F3 step 2 + 3 (split + pad to a page) with the page as the alignment unit — the same idea as your checklist item "make the channel-block stride a multiple of 16 floats so each block plane starts on a line boundary", at page granularity.
- **Links:** [USPTO PDF 12639396](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12639396) · [Justia 20220405348](https://patents.justia.com/patent/20220405348)

### 4.3 US12694294 — Concatenating input and output tensors for use in recurrent neural networks
- Reformatting so the inputs/outputs of RNN gates are concatenated in the blocked layout. **Links:** [USPTO PDF 12694294](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12694294) · [Justia 20220405598](https://patents.justia.com/patent/20220405598)

### 4.4 Other NNPA family members (same layout disclosure, claims elsewhere)
[US12236338](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12236338) · [US11269632](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11269632) · [US11797270](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11797270) · [US11675592](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11675592) · [US11693692](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11693692) · [US12008395](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12008395) · [US11734013](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11734013) · [US12079658](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12079658)

Patent-strategy note (analysis): a layout *disclosed* in a patent but *claimed* nowhere becomes prior art against later filings by anyone, from its publication date (Dec 2022 for the US20220405xxx publications).

### 4.5 US5953531A — Minimizing loop execution time by optimizing block/tile sizes  (F1)
- **Assignee:** IBM (per Justia).
- **What it covers.** Determine block (tile) sizes for a loop nest by **estimating the memory cost per iteration** and choosing the sizes that minimize it subject to **a maximum block size, a maximum effective cache size (ECS), and a maximum effective TLB size (ETS)**; alternatively search the iteration space iteratively. Prior-art discussion cites Ferrante et al. on counting distinct cache lines touched by a loop nest.
- **In three-formula terms (analysis).** F1 chosen from F2: the cost model counts the distinct lines the tile's access map touches — the same calculation as your working-set estimate (4 input rows x 512 B, one output row 4 KB, weights 864 B, all inside 48 KB L1).
- **Status.** A 1990s US patent; long expired by its 20-year term. Free prior art.
- **Links:** [Google Patents US5953531](https://patents.google.com/patent/US5953531) · [Justia 5953531](https://patents.justia.com/patent/5953531) · [USPTO PDF 5953531](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/5953531)

---

## 5. Searched and not found
- No IBM patent surfaced on the **RaPiD / AIU PE-array dataflow** or on **scratchpad tiling** for convolution.
- No IBM patent surfaced on **hardware im2col / conv address generation**.

## 6. Search log
- `IBM patent "reformatting of tensors to provide sub-tensors" neural network processing assist NNPA 4D feature tensor`
- `International Business Machines patent neural network processing assist instruction data layout format "4D-feature tensor" 64 elements`
- `"Neural network processing assist instruction" US11669331 International Business Machines inventors Lichtenau`
- `IBM patent "4D-kernel" layout convolution tensor rearrange reduce memory accesses`
- `IBM patent convolution dataflow accelerator "loop" tiling schedule "processing element" array AI core RaPiD`
- `US5953531A "minimizing loop execution time by optimizing block/tile sizes" assignee IBM`
- `International Business Machines patent compiler "loop tiling" OR "loop blocking" cache tile size selection`
- `IBM patent convolution im2col address generation systolic array "input feature map" stride padding`
