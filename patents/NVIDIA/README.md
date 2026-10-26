# NVIDIA

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed in a search result; `P` = number and title confirmed, assignee inferred from inventors/terminology.

---

## 1. Why NVIDIA matters for your kernel

NVIDIA's public stack (cuDNN, CUTLASS, CuTe, TensorRT, TMA) is where the three-formula split is most explicit: CUTLASS's implicit GEMM is F2 written as an index map (`GEMM_M = N*P*Q`, `GEMM_K = C*R*S`), CuTe's `(Shape, Stride)` is F3, and Hopper's TMA does the F2 gather in hardware (`cuTensorMapEncodeIm2col`). The patents follow the same split:

- **F1** — NVIDIA Research accelerator dataflow patents (Simba-style tiled multi-chip accelerator, SCNN, flexible PE networks).
- **F2** — implicit-GEMM convolution on GPUs ("multi-convolution", "indirectly accessing sample data"), and TMA coordinate-to-address hardware.
- **F3** — shared-memory swizzle written by the TMA unit, warp-group MMA descriptors.

The CuTe layout algebra itself (composition, complement, logical divide) was found only as open documentation and a whitepaper (arXiv 2603.02298), **not** as a patent.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority / filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US11270197B2; cont. US12387089B2; pub. US20200293867A1, US20220076110A1 | Efficient neural network accelerator dataflows | F1 | prov. 62/817,413, 12 Mar 2019; filed 4 Nov 2019 (16/672,918) | granted | V |
| 2 | US10860922B2 (pub. US20200082254A1); US10891538; US10997496; US11847550; early pubs US20180046900A1, US20180046906A1 | Sparse convolutional neural network accelerator | F1 + F3 | 2016-17 | granted | V |
| 3 | US20220083500A1 | Flexible accelerator for a tensor workload | F1 | prov. 15 Sep 2020; filed 9 Jun 2021 | application | P |
| 4 | US10223333B2 (pub. US20160062947A1, app. 14/838,291) | Performing multi-convolution operations in a parallel processing system | F2 | 2014-15 | granted | P |
| 5 | US10255547B2; US12406526B2 | Indirectly accessing sample data to perform multi-convolution operations in a parallel processing system | F2 | 2014-15 family | granted | P |
| 6 | US12141082B2 (pub. US20230289304A1, app. 17/691,276) | Method and apparatus for efficient access to multidimensional data structures and/or other large data blocks | F2 + F3 | filed 10 Mar 2022 | granted 12 Nov 2024 | V |
| 7 | US12693857B2 (pub. US20230289398A1) | Efficient matrix multiply and add with a group of warps | F3 (+F1) | 2022 | granted | P |

---

## 3. Patent details

### F1 — Schedule

#### 3.1 US11270197B2 / US12387089B2 — Efficient neural network accelerator dataflows
- **Assignee:** NVIDIA. **Inventors:** Yakun Shao, Rangharajan Venkatesan, Miaorong Wang, Daniel Smith, William James Dally, Joel Emer, Stephen W. Keckler, Brucek Khailany.
- **Dates:** provisional 62/817,413 (12 Mar 2019); application 16/672,918 filed 4 Nov 2019; continuation granted as US12387089. Publications US20200293867A1 and US20220076110A1.
- **What it covers.** A distributed, **tile-based** DNN accelerator (multiple chips, each with a central PE, a global buffer and many PEs; each PE has a weight buffer, activation buffer and vector MAC units) that combines weights and activations in parallel using **stationary dataflows**. The description claims "flexible and efficient weight and activation tiling at the PE level, chip level and package level". This is the Simba multi-chip-module research accelerator.
- **In three-formula terms (analysis).** F1 at three nested levels: package tile -> chip tile -> PE tile, each with its own stationary choice. The output-tile decision you make (1x3 vs 2x4) is the innermost (PE/register) level of the same hierarchy.
- **Links:** [Google Patents US11270197](https://patents.google.com/patent/US11270197) · [USPTO PDF 11270197](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11270197) · [USPTO PDF 12387089](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12387089) · [Justia 20220076110](https://patents.justia.com/patent/20220076110) · [Google Patents US20200293867A1](https://patents.google.com/patent/US20200293867A1/en)

#### 3.2 US10860922B2 and family — Sparse convolutional neural network accelerator (SCNN)
- **Assignee:** NVIDIA. **Inventors include** Angshuman Parashar (paper authors: Parashar, Rhu, Mukkara, Puglielli, Venkatesan, Khailany, Emer, Keckler, Dally).
- **Family members seen:** US10860922B2 (pub. US20200082254A1), US10891538, US10997496, US11847550, early publications US20180046900A1 and US20180046906A1. Which is the parent was not confirmed.
- **What it covers.** Only non-zero weights and non-zero input activations are sent to the multiplier array; inside a PE, a vector of weights and a vector of activations are multiplied **as a Cartesian product**, and the products are scattered to accumulator banks by computed output coordinates. Loop nests over output-channel tiles with a stationary input held across iterations.
- **In three-formula terms (analysis).** F1: input-stationary, output-channel-tiled loop nest. F2 is *inverted*: instead of computing input addresses from an output pixel, each (weight, activation) pair computes **its output coordinate** `(k, oh = ih - r + pad, ow = iw - s + pad)` and scatters. F3: compressed-sparse storage of weights and activations.
- **Paper:** [SCNN, ISCA 2017, arXiv 1708.04485](https://arxiv.org/abs/1708.04485) · [NVIDIA Research page](https://research.nvidia.com/publication/2017-05_scnn-accelerator-compressed-sparse-convolutional-neural-networks)
- **Links:** [Google Patents US10860922](https://patents.google.com/patent/US10860922) · [USPTO PDF 10891538](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10891538) · [USPTO PDF 10997496](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10997496) · [USPTO PDF 11847550](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11847550) · [Google Patents US20180046900A1](https://patents.google.com/patent/US20180046900A1/en) · [Google Patents US20180046906A1](https://patents.google.com/patent/US20180046906A1/en)

#### 3.3 US20220083500A1 — Flexible accelerator for a tensor workload
- **Assignee:** not shown in results; **inventors** Po-An Tsai, Neal Crago, Angshuman Parashar, Joel Springer Emer, Stephen William Keckler (NVIDIA Research), so `P`. DARPA SDH "Symphony" funding (HR0011-18-3-0007).
- **Dates:** provisional 15 Sep 2020 ("Math accelerator with a flexible network for diverse GEMM and CNN kernels"); filed 9 Jun 2021; published 17 Mar 2022.
- **What it covers.** A tensor accelerator (or FPGA) with a **configurable inter-PE network** and a **flexible-length dot-product unit** that serves GEMM and 2-D/3-D CNN workloads; sliding-window reuse for convolution, and for strided convolutions (stride > 1) it "simply discards rows with no sliding window reuse".
- **In three-formula terms (analysis).** F1 is chosen per layer by configuring the network; the stride>1 rule is an F2 statement: the sliding-window reuse that holds for stride 1 (your two output rows sharing 2 of 3 input rows) breaks for stride 2.
- **Links:** [Justia 20220083500](https://patents.justia.com/patent/20220083500) · [Google Patents US20220083500A1](https://patents.google.com/patent/US20220083500A1/en)

### F2 — Access map

#### 3.4 US10223333B2 — Performing multi-convolution operations in a parallel processing system
- **Inventors:** Sharanyan Chetlur, Bryan Catanzaro (cuDNN authors). Application 14/838,291, published US20160062947A1. Assignee field truncated in results (`P`, NVIDIA by inventors and subject).
- **What it covers.** A "multi-convolution" (a generalized 2-D convolution of a batch of images with a batch of filters) executed as a matrix multiply on a parallel processor, where **each image tile is created in on-chip memory only when needed** — the image matrix is never materialized in DRAM.
- **In three-formula terms (analysis).** This is implicit GEMM: F2 written as `row = (n, p, q)`, `col = (c, r, s)`, element = `x[n, c, p*stride - pad + r, q*stride - pad + s]`, evaluated on the fly per tile. CUTLASS's later public description ("x(NHWC), w(KRSC), y(NPQK)", `GEMM_K = C*R*S`) is the same map.
- **Links:** [Google Patents US10223333](https://patents.google.com/patent/US10223333) · [Google Patents US20160062947A1](https://patents.google.com/patent/US20160062947A1/en) · [Justia 20160062947](https://patents.justia.com/patent/20160062947) · [USPTO PDF 10223333](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10223333)

#### 3.5 US10255547B2 / US12406526B2 — Indirectly accessing sample data to perform multi-convolution operations
- **Inventors:** John Clifton Woolley Jr., John Tran (`P`, NVIDIA).
- **What it covers.** The same multi-convolution, with **precomputed offset values** used to fetch the virtual image-matrix elements indirectly, which speeds up the matrix multiply.
- **In three-formula terms (analysis).** F2 split into a precomputed table plus an add: `addr = base + offset_table[k_index] + pixel_offset`. This is exactly what the oneDNN JIT does when it turns `ki, jj, c` terms into constant displacements — but here as a stored table.
- **Links:** [Justia 10255547](https://patents.justia.com/patent/10255547) · [USPTO PDF 10255547](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10255547) · [Google Patents US12406526](https://patents.google.com/patent/US12406526) · [USPTO PDF 12406526](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12406526)

#### 3.6 US12141082B2 — Efficient access to multidimensional data structures and/or other large data blocks (TMA)
- **Assignee:** NVIDIA Corporation. Application 17/691,276 filed 10 Mar 2022; published US20230289304A1 (14 Sep 2023); granted 12 Nov 2024.
- **Claim 1 (summary).** A parallel processor with **memory access hardware circuits** that **translate a coordinate of a multidimensional data structure into memory addresses** and asynchronously transfer the block between external memory and the processor's shared memory; the circuit determines whether the block is part of a tensor in external memory or non-tensor data. The description spreads a tile's row across all shared-memory banks so it can be read in parallel.
- **In three-formula terms (analysis).** F2 + F3 in hardware: software gives a tile coordinate, the unit applies the global layout (base + strides) and writes the shared-memory layout (with swizzle). The public API of this unit has an **im2col mode** (`cuTensorMapEncodeIm2col`: pixel box corners, channels per pixel, pixels per column, `elementStrides` as traversal strides, `oobFill` zero/NaN for out-of-bounds) — that is your conv access map with padding done by hardware ([CUDA Driver API](https://docs.nvidia.com/cuda/archive/13.0.3/cuda-driver-api/group__CUDA__TENSOR__MEMORY.html)).
- **Links:** [Google Patents US12141082](https://patents.google.com/patent/US12141082) · [USPTO PDF 12141082](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12141082) · [OG notice](https://patentsgazette.uspto.gov/week46/OG/html/1528-2/US12141082-20241112.html)

### F3 — Layout

#### 3.7 US12693857B2 — Efficient matrix multiply and add with a group of warps (Hopper wgmma)
- **Assignee:** not shown in result snippets (`P`, NVIDIA by subject: CUDA warp-level MMA, TMAU). Published application US20230289398A1.
- **What it covers (application abstract).** Several warps collaborate on one result matrix; a state-machine circuit coordinates asynchronous compute units; threads share register files across the group; a **group MMA instruction takes a descriptor** of input data size and format. The description says the **TMAU scrambles (swizzles) data on the way from global to shared memory**, taking the bank layout into account on writes so that later reads are conflict-free, with a per-tensor swizzle pattern (eight-bank example: an unswizzled row read takes eight cycles).
- **In three-formula terms (analysis).** F3 step 4 (XOR swizzle) moved into the copy engine; the MMA descriptor tells the tensor core which swizzled layout to expect. CuTe's `Swizzle<3,4,3>` atoms are the software face of this.
- **Overlap with your kernel.** None on a CPU: there are no software-visible banks (see your file's section 9.1 point 7).
- **Links:** [USPTO PDF 12693857](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12693857) · [Justia 20230289398](https://patents.justia.com/patent/20230289398)

---

## 4. Searched and not found

- **No patent found on the CuTe layout algebra** (Shape/Stride composition, complement, logical divide) or on CUTLASS layout functors. Public: [CuTe layouts doc](https://docs.nvidia.com/cutlass/4.6.0/media/docs/cpp/cute/01_layout.html), CuTe whitepaper arXiv 2603.02298, [PyCuTe](https://github.com/NVlabs/CuTe).
- **No patent found on NHWC/NCHW layout selection** in TensorRT or on the `kCHW32` / `kHWC8` formats as such.
- **No patent found on the Volta/Ampere register fragment layout** (`mma.sync` thread-value maps).
- **No NVDLA convolution-buffer patent surfaced** in these searches; the NVDLA hardware is open source (nvdla.org).

## 5. Search log
- `NVIDIA patent "efficient access to multidimensional data structures" tensor memory accelerator TMA im2col`
- `"Efficient neural network accelerator dataflows" patent NVIDIA Venkatesan Keckler Khailany`
- `NVIDIA patent "Performing multi-convolution operations in a parallel processing system" implicit GEMM`
- `NVIDIA patent shared memory swizzle bank conflict tensor core matrix layout XOR address`
- `NVIDIA patent hierarchical tensor layout shape stride composition layout algebra CuTe`
- `"Sparse convolutional neural network accelerator" US20180046900A1 NVIDIA Parashar Cartesian product`
- `"Efficient matrix multiply and add with a group of warps" patent NVIDIA`
- `NVIDIA patent tensor memory access unit "im2col" convolution "tensor map" descriptor`
- `NVIDIA patent warp matrix multiply accumulate operand fragment register layout`
- `NVIDIA patent compiler selecting tensor memory layout NHWC NCHW TensorRT format selection`
- `US20220083500A1 "Flexible accelerator for a tensor workload" assignee inventors`
