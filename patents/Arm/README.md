# Arm (incl. Apical)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Arm matters for your kernel  (high relevance)

Arm is the **CPU + NPU IP** behind a huge fraction of edge inference, and uniquely it **publishes a general blocked-weight offset formula in a public header** (Arm Compute Library `OHWIo{IB}i{BB}`), which is the clearest statement of your F3 anywhere (see your vendor file section 5.1). Its NEON/SVE/SME GEMM packing (`CpuGemmTranspose1xWKernel`, KleidiAI) is the direct analogue of your `Ohwi8o` weight packing with `W = 16/element_size` (one q-register of B per k step). Its Ethos-U NPU layout `NHCWB16` is a channel-blocked format with the channel block **outside** W (streaming order).

**Most of Arm's three-formula knowledge is public (ACL, KleidiAI, Vela are open source), not patented.** The patents found are about the NPU hardware (parameters on chip, configuration), not the layout formulas — so the open headers are the prior-art record, and the patents are narrower.

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11995532B2 | Systems and devices for configuring neural network circuitry | F1 | granted | V (Arm, ex-Apical) |
| 2 | US11275996B2 | Systems and devices for formatting neural network parameters | **F3** | granted | P (Arm) |
| 3 | US11501150B2 | Neural network architecture | F1 | granted | P (Arm) |
| 4 | (US patent, filed 19 Dec 2019, granted 21 Feb 2023) | Hardware accelerator for artificial neural networks (finetuned + fixed weights) | F3 | granted | V (Arm Limited) |

---

## 3. Patent details

### 3.1 US11995532B2 — Systems and devices for configuring neural network circuitry
- **Assignee:** ARM Ltd (originally Apical Limited). **Inventor:** Daren Croxford. App. 16/210,389. Metadata keywords: parameters, multiplication circuits, **slices**, reduced-dimension data.
- **In three-formula terms (analysis).** Configuration of the MAC slices and parameter feed — F1 (how the array is set up per layer).
- **Links:** [Google Patents US11995532](https://patents.google.com/patent/US11995532)

### 3.2 US11275996B2 — Systems and devices for formatting neural network parameters
- **What it covers (F3).** Formatting (re-laying-out) the network parameters for the accelerator — the weight-packing step, Arm's equivalent of `Ohwi8o` / `OHWIo`. **Link:** [Google Patents US11275996](https://patents.google.com/patent/US11275996)

### 3.3 Hardware accelerator for ANNs (finetuned + fixed weights)
- **Assignee:** Arm Limited. Filed 19 Dec 2019, granted 21 Feb 2023. A communication bus interface, memory, controller and >=1 PE; claims focus on **finetuned weights stored alongside fixed weights** (F3 storage). Inventor Chuteng Zhou among Arm ML inventors. [Justia, Chuteng Zhou](https://patents.justia.com/inventor/chuteng-zhou)

---

## 4. Public (non-patent) Arm material — the real F1/F2/F3 record (and prior art)
This is where Arm's three formulas actually live, openly:

| Formula | Source | Content |
|---|---|---|
| **F3 (general formula)** | [ACL CoreTypes.h](https://github.com/ARM-software/ComputeLibrary/blob/main/arm_compute/core/CoreTypes.h) | `OHWIo{interleave}i{block}` six-digit offset: `value = x5*H*W*I'*IB + x4*W*I'*IB + x3*I'*IB + x2*IB*BB + x1*BB + x0` |
| F3 | [ACL data_layout.dox](https://github.com/ARM-software/ComputeLibrary/blob/main/docs/user_guide/data_layout.dox) | "NHWC: native layout ... best performance"; NCHW legacy |
| F3 (GEMM pack) | [CpuGemmTranspose1xWKernel.h](https://github.com/ARM-software/ComputeLibrary/blob/main/src/cpu/kernels/CpuGemmTranspose1xWKernel.h) | transpose in chunks of `1xW`, `W = 16/element_size` (4 fp32, 8 fp16) = one NEON q-register of B per k step |
| F3 (pack+quant) | [KleidiAI microkernel_names.md](https://github.com/ARM-software/kleidiai/blob/main/docs/microkernel_names.md), [vscale.md](https://github.com/ARM-software/kleidiai/blob/main/docs/vscale.md) | block shape in the kernel name `p<W>x<H>`, `BD = KR/SR`; SVE/SME `vs` scales with vector length |
| F3 (NPU) | [Vela tensor.py](https://github.com/nxp-imx/ethos-u-vela/blob/HEAD/ethosu/vela/tensor.py) | `NHCWB16`: `off = e*(n*H*W*C' + h*W*C' + (c/16)*W*16 + w*16 + c%16)`, `C' = roundup(C,16)` — channel block outside W (streaming) |
| F2 (Mali) | [direct_convolution.cl](https://github.com/ARM-software/ComputeLibrary/blob/main/src/core/CL/cl_kernels/nhwc/direct_convolution.cl) | M0/N0/K0 output tiling of the work |

These open sources predate and document the layouts, so they are strong prior art against later filings by others.

## 5. Searched and not found
- No Arm patent found that claims the ACL `OHWIo` formula, the KleidiAI pack, or the Ethos `NHCWB16` layout — they are published open source.

## 6. Search log
- `Arm Limited patent convolution neural network accelerator data layout "weight" format OR "tensor" tiling Ethos NPU brick address`
- `Arm Ltd patent Ethos-U "weight" compression OR "feature map" DMA "NHCWB16" brick tile neural network accelerator data format justia`
- `Arm Limited patent US11727259 OR US10504022 ... "topological scheduling" EP3839834` (US11727259/US10504022 are **Google's**, not Arm's — see [`../Google/README.md`](../Google/README.md))
- `Arm Limited patent US11995532 "configuring neural network circuitry" claims`
