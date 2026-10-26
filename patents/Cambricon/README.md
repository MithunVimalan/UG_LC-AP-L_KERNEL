# Cambricon Technologies

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Cambricon matters for your kernel

Cambricon (DianNao/Cambricon-X lineage; MLU accelerators) holds a **tensor descriptor** family: an instruction references a **descriptor by identifier**; the descriptor defines the tensor's **shape and data address**; the processor resolves the address and runs the instruction. That is F3 (shape + base + strides) plus the F2 interface (coordinate -> address), the same split as Habana's "Tensor-based memory access" and the Google tensor-traversal unit.

---

## 2. Summary table

| # | Number (family) | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11836491B2; US12112166B2; US12417098B2 (pub. US20240028334A1) | Data processing method and apparatus, and related product for increased efficiency of tensor processing | **F2 + F3** | granted | V |
| 2 | US11886880B2 | Data processing apparatus and related products (descriptor management) | F3 | granted | V |
| 3 | US12346697B2 | Data processing apparatus and related products (tensor data synchronization) | F1 | granted | V |

---

## 3. Patent details

### 3.1 US11836491B2 / US12112166B2 / US12417098B2 — Data processing with tensor descriptors
- **Assignee:** Cambricon (Cambricon Technologies / related entity).
- **What it covers.** When a decoded instruction references a **descriptor by identifier**, a controller **fetches the descriptor content**; the descriptor **defines an N-dimensional tensor's shape and data address**; the processing circuit **determines the address in storage and then executes** the instruction.
- **In three-formula terms (analysis).** The descriptor is F3 (shape = extents, data address = base; strides implied by shape) and the instruction interface is F2 (the op names a tensor and the hardware computes element addresses). Issuing one op over a whole tensor, rather than per-element addresses, is the same abstraction as Habana's Tensor Descriptor Memory and Google's tensor traversal unit.
- **Links:** [USPTO PDF 12112166](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12112166) · [Google Patents US12417098](https://patents.google.com/patent/US12417098) · [USPTO PDF 12417098](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12417098) · [GlobalData/Verdict summary](https://www.verdict.co.uk/cambricon-technologies-in-artificial-intelligence-theme-innovation-strategy-2/)

### 3.2 US11886880B2 — descriptor management
- Register, determine content of, and release descriptors (lifecycle of the F3 descriptors above). [Google Patents US11886880](https://patents.google.com/patent/US11886880)

### 3.3 US12346697B2 — tensor data synchronization
- Synchronisation of tensor data across the processor (F1 ordering). [Google Patents US12346697](https://patents.google.com/patent/US12346697)

---

## 4. Searched and not found
- No Cambricon patent found that fixes a named blocked layout (nChw-style) or a convolution access map with stride/pad; coverage is via the generic tensor-descriptor abstraction.
- Note: the "Lossless tiling in convolution networks" family that surfaced in the Cambricon search is **not** Cambricon's (its references point to Plasticine/Spatial — a reconfigurable-dataflow company, i.e. SambaNova-adjacent). See [`../SambaNova/README.md`](../SambaNova/README.md).

## 5. Search log
- `Cambricon patent neural network processor convolution data layout "fractal" OR tiling instruction tensor Chen DianNao accelerator`
- `Cambricon patent US "data processing method and apparatus" tensor descriptor shape data address increased efficiency tensor processing`
