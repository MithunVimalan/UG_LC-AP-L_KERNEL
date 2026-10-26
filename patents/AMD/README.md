# AMD (incl. ATI Technologies and Xilinx)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed in a search result; `P` = assignee inferred.

---

## 1. Why AMD matters for your kernel

Two very different AMD portfolios touch the chain:
- **AMD / ATI (GPU side).** The one clear layout patent family is the 2018 **"Tiling format for convolutional neural networks"**: convert filters and images from linear layout into a tiled layout so a SIMD unit reads adjacent memory. That is F3 in exactly your sense (`nchw` -> blocked). AMD's public software (MIOpen `NCHWc8`, Composable Kernel transform chains, CK-Tile XOR swizzle) is open source, and no AMD patent on the CK transform chain or LDS swizzle surfaced.
- **Xilinx (FPGA / Versal AI Engine side).** A strong F2 cluster: **image preprocessors** that turn rows of an image into parallel streams of conv-window samples for a matrix multiplier or systolic array (hardware im2col with stride and dilation), and an F1 patent on **three-level static block scheduling** of a network onto a systolic array.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority / filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US10762392B2; US11494592B2; US11900253B2; CN111353575A | Tiling format for convolutional neural networks | F3 | CN priority 20 Dec 2018 | granted | V (AMD / ATI) |
| 2 | US10460416 (parent, app. 15/786,244); US10984500B1 (cont.) | Inline image preprocessing for convolution operations using a matrix multiplier on an integrated circuit | F2 | 17 Oct 2017 | granted (10984500: 20 Apr 2021) | V (Xilinx) |
| 3 | US11386644B2 (pub. US20190114499A1); EP3698313B1 | Image preprocessing for generalized image processing | F2 | 17 Oct 2017 | granted 12 Jul 2022; EP granted 20 Dec 2023 | V (Xilinx) |
| 4 | US11194490B1 (app. 15/956,594) | Data formatter for convolution | F2 + F3 | 2018 | granted | V (Xilinx) |
| 5 | US12061990B2 (pub. US20190114548A1, app. 15/786,434); EP3698295A1 | Static block scheduling in massively parallel software defined hardware systems | F1 | 17 Oct 2017 | granted | V (Xilinx) |
| 6 | US10789402B1 | Compiler and hardware abstraction layer architecture for a neural network accelerator | F1 | 2019 | granted | V (Xilinx) |

---

## 3. Patent details

### F3 — Layout

#### 3.1 US10762392B2 / US11494592B2 / US11900253B2 — Tiling format for convolutional neural networks
- **Assignees:** Advanced Micro Devices, Inc. and ATI Technologies ULC (US10762392); Advanced Micro Devices (US11900253). **Inventors:** Nima Osqueizadeh, Paul Blinzer (US10762392); Song Zhang, Jiantan Liu, Hua Zhang, Min Yu (US11900253). Chinese priority filed 20 Dec 2018 (CN111353575A).
- **What it covers.** Converting **filter and image data from a linear memory layout into a tiled layout** so that the data elements used by one convolution filter operation sit in **adjacent memory locations**. The description uses block notation such as **"4C18H34W"** (a block of 4 channels x 18 rows x 34 columns) and maps each thread of the layout to **one channel of a SIMD unit** (with other SIMD widths, e.g. 32 or 128 channels, as alternatives).
- **In three-formula terms (analysis).** This is F3 step 2 + 3 of your vendor file (split + pad) with a 3-D block: `(c/4, h/18, w/34, c%4, h%18, w%34)`. Note the spatial block (18 x 34) is larger than the output tile, so it carries a **halo**: an 18-row input block feeds 16 output rows of a 3x3 conv (18 = 16 + 3 - 1), and 34 = 32 + 3 - 1. That is F2 (the conv window) baked into the F3 block size — the same arithmetic as your "2x4 tile gives a 4 x 6 window".
- **Overlap with your kernel.** Direct: a channel-blocked, halo-carrying input layout. Your current input is plain `nchw` (C = 3), and your output `nChw8c` blocks channels only, with no spatial blocking. Read the claims before adopting a spatially blocked input format with built-in halo.
- **Links:** [Google Patents US11900253](https://patents.google.com/patent/US11900253) · [Justia 10762392](https://patents.justia.com/patent/10762392) · [USPTO PDF 10762392](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10762392) · [USPTO PDF 11494592](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11494592) · [USPTO PDF 11900253](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11900253) · [CN111353575A](https://patents.google.com/patent/CN111353575A/en) · [Justia AMD assignee list](https://patents.justia.com/assignee/advanced-micros-devices-inc?page=98)

### F2 — Access map

#### 3.2 US10460416 / US10984500B1 — Inline image preprocessing for convolution using a matrix multiplier (Xilinx)
- **Assignee:** Xilinx, Inc. **Inventor (10984500):** Ashish Sirasao. Parent application 15/786,244 -> US10460416; continuation -> US10984500, issued 20 Apr 2021.
- **What it covers.** A **preprocessor circuit** between memory and a matrix multiplier: memory banks, multiplexer circuitry, two sets of registers, and control circuitry that **generates memory addresses** and steers data into **streams of image samples**, supporting **strided and dilated** access, so the multiplier sees im2col rows without the image matrix ever being stored.
- **In three-formula terms (analysis).** Hardware F2: for each output pixel the circuit emits the `R*S*C` samples `x[c, oh*stride - pad + r*dil, ow*stride - pad + s*dil]`, reusing overlapping rows across neighbouring outputs from its registers (the sliding-window reuse your 2x4 tile exploits in registers).
- **Links:** [USPTO PDF 10984500](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10984500) · [Google Patents US10984500B1](https://patents.google.com/patent/US10984500B1/en) · [USPTO PDF 10460416](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10460416) · [Justia 10460416](https://patents.justia.com/patent/10460416) · [Google Patents US10460416](https://patents.google.com/patent/US10460416)

#### 3.3 US11386644B2 — Image preprocessing for generalized image processing (Xilinx)
- **Assignee:** Xilinx. **Inventor:** Elliott Delaye. Priority 17 Oct 2017 (US 15/786,267); issued 12 Jul 2022; EP3698313B1 granted 20 Dec 2023.
- **What it covers.** A first buffer stores **several rows of image data**; an **interconnect network** connects each **shift register** to several buffer locations; a control circuit loads and shifts the registers to output **multiple streams of image samples** in parallel.
- **In three-formula terms (analysis).** A line-buffer + shift-register implementation of the conv window: rows `ih = oh - pad + r` are held in the buffer, and shifting walks `iw = ow - pad + s`. This is the hardware twin of keeping 4 input rows live for a 2-row output tile.
- **Links:** [Google Patents US11386644](https://patents.google.com/patent/US11386644) · [USPTO PDF 11386644](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11386644) · [Justia 20190114499](https://patents.justia.com/patent/20190114499) · [EP3698313B1](https://data.epo.org/gpi/EP3698313B1)

#### 3.3b US11106968B1 / US10411709B1 / US11704536B1 — traversing / dividing input feature maps; convolution circuitry (Xilinx)
- **Assignee:** Xilinx, Inc. **Inventors (11106968):** Ehsan Ghasemi, Elliott Delaye, Ashish Sirasao. App. 15/989,075.
- **US11106968** stores adjacent IFM elements in a buffer and traverses them with **height and width traversal circuits** (each a counter + offset adder) and an **address generator** that computes `addr = width_traversal + input_width * height_traversal`, producing a deliberately **non-sequential** address order. **US10411709** divides a 3-D IFM into sub-maps; **US11704536** is convolution circuitry.
- **In three-formula terms (analysis).** F2 as nested counters with strength-reduced address adders — the hardware form of `off = ... + (oh + kh - 1)*W + (ow + kw - 1)`, same as Google's adder-based tensor traversal unit.
- **Links:** [Google Patents US11106968](https://patents.google.com/patent/US11106968) · [USPTO PDF 11106968](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11106968) · [Google Patents US10411709](https://patents.google.com/patent/US10411709) · [Google Patents US11704536](https://patents.google.com/patent/US11704536)

#### 3.4 US11194490B1 — Data formatter for convolution (Xilinx)
- **Assignee:** Xilinx Inc. **Inventors:** Ravi Sunkavalli, Victor J. Wu, Poching Sun. Application 15/956,594.
- **What it covers.** Memory circuit + data upload circuitry + **data formatting circuitry** + systolic array. The formatter **reads subsets of a multi-dimensional data set and arranges their elements into data streams output in parallel**; the systolic array computes the output feature map from those streams and the filter values.
- **In three-formula terms (analysis).** F2 + an on-the-fly F3: the formatter converts the stored layout into the stream order the systolic array's schedule needs.
- **Links:** [Google Patents US11194490](https://patents.google.com/patent/US11194490) · [Justia 11194490](https://patents.justia.com/patent/11194490) · [USPTO PDF 11194490](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11194490)

### F1 — Schedule

#### 3.5 US12061990B2 — Static block scheduling in massively parallel software defined hardware systems (Xilinx)
- **Assignee:** Xilinx Inc. **Inventors:** Yongjun Wu, Jindrich Zejda, Elliott Delaye, Ashish Sirasao. Application 15/786,434 (pub. US20190114548A1), priority 17 Oct 2017; EP3698295A1.
- **What it covers.** Statically scheduling a neural network on a massively parallel hardware system at **three levels**: the upper level models the layers to fix the order of functions; the intermediate level builds **systolic arrays from repeated processes**; the lower level has the compiler **map the array's operations onto parts of the hardware**.
- **In three-formula terms (analysis).** F1 as a hierarchy of schedules (layer order -> systolic array -> PE assignment), fixed at compile time.
- **Links:** [Google Patents US12061990](https://patents.google.com/patent/US12061990) · [Justia 12061990](https://patents.justia.com/patent/12061990) · [USPTO PDF 12061990](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12061990) · [EP3698295A1](https://data.epo.org/gpi/EP3698295A1)

#### 3.6 US10789402B1 — Compiler and hardware abstraction layer architecture for a neural network accelerator (Xilinx)
- **Assignee:** Xilinx (original). Compiler + HAL for a NN accelerator; snippet gave no tiling detail.
- **Link:** [Google Patents US10789402](https://patents.google.com/patent/US10789402)

---

## 4. Public (non-patent) AMD material that defines the same formulas

| Formula | AMD public source | Content |
|---|---|---|
| F2 + F3 | [Composable Kernel transforms](https://github.com/ROCm/composable_kernel/blob/develop/include/ck/tensor_description/multi_index_transform.hpp) | `PassThrough`, `Pad`, `Embed`, `UnMerge`, `Merge`, `Xor` — conv as four composed transforms |
| F3 | [CK-Tile LDS bank-conflict blog](https://rocm.blogs.amd.com/software-tools-optimization/lds-bank-conflict/README.html) | XOR swizzle `K0' = K0 ^ (M % ...)`; padding called "hard to tune" |
| F2 + F3 | [AI Engine buffer descriptors, UG1079](https://docs.amd.com/r/en-US/ug1079-ai-engine-kernel-coding/Tiling-Parameters-and-Buffer-Descriptors), [UG1603](https://docs.amd.com/r/en-US/ug1603-ai-engine-ml-kernel-graph/Tiling-Parameters-and-Buffer-Descriptors) | DMA buffer descriptors with 1-D/2-D addressing, iterations, chaining; tiling parameter objects; memory tiles address up to 4 dimensions; **zero padding only on memory tiles, dims 0-2** |
| F2 | [AIE-ML multi-dimensional addressing](https://docs.amd.com/r/en-US/ug1603-ai-engine-ml-kernel-graph/Multi-dimensional-Addressing-in-AI-Engine-Kernels) | tensor descriptors and tensor buffer streams, built on 3-D abstractions |

Because these are published, they also act as prior art against later filings by others.

## 5. Searched and not found
- No AMD patent found on **implicit GEMM / im2col on GPU**, on **MFMA operand layouts**, or on the **LDS XOR swizzle**.
- No AMD patent found on **compiler tile-size selection** for GEMM/conv.

## 6. Search log
- `"Tiled format for convolutional neural networks" patent Advanced Micro Devices assignee US`
- `Advanced Micro Devices patent convolution implicit GEMM GPU LDS "matrix multiply" convolution input "image to column"`
- `Xilinx patent "Inline image preprocessing for convolution operations using a matrix multiplier" US10984500B1`
- `Xilinx patent AI Engine array tensor tiling DMA buffer descriptor multi-dimensional address generation`
- `AMD patent "matrix multiplication" "wavefront" register layout matrix core MFMA operand data layout swizzle LDS`
- `US11194490B1 "Data formatter for convolution" Xilinx assignee claims`
- `Xilinx patent "Image preprocessing for generalized image processing" US11386644`
- `Advanced Micro Devices patent machine learning workload "tiling" schedule "matrix multiplication" "tile size"`
- `Xilinx patent neural network compiler "tiling" "loop" schedule FPGA DPU convolution`
- `"Static block scheduling in massively parallel software defined hardware systems" patent assignee Xilinx`
