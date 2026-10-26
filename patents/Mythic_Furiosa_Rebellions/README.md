# Other edge/inference startups: Mythic, FuriosaAI, Rebellions

Partial result. Legend and method: see [`../README.md`](../README.md). These three were searched but returned mostly product material, not pinned patents.

---

## 1. Mythic (analog compute-in-memory)
- **Architecture (public).** Analog Matrix Processor: tiles, each an analog compute engine that **stores weights and performs matrix multiplications in-memory** (resistive crossbar), plus local SRAM and a SIMD unit ([Mythic technology](https://www.mythic.ai/technology), [NextPlatform](https://www.nextplatform.com/2018/08/23/a-mythic-approach-to-deep-learning-inference/)).
- **Related patent (assignee unconfirmed):** "Efficient data layout and alignment for wide-vector accelerator systems" (US12450056) describes an **Aligner Logic Unit** doing **segment rotation and sub-segment shift** before data reaches an analog crossbar — an F2/F3 alignment step. Assignee not shown; verify before attributing to Mythic. [USPTO PDF 12450056](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12450056)
- **F-relevance.** In analog CIM, "layout" = which weights are programmed into which crossbar row/column (F3 as placement); the access map (F2) is the input-to-crossbar routing. No Mythic-assigned layout patent was pinned here.

## 2. FuriosaAI (RNGD / Warboy; Tensor Contraction Processor)
- No FuriosaAI patent surfaced in these searches. Public: Furiosa's RNGD is built around a **Tensor Contraction Processor** that treats the primitive as tensor contraction rather than fixed matmul — a different F1/F2 factoring. A dedicated assignee search ("FuriosaAI", "Furiosa") is the next step.

## 3. Rebellions (ATOM / REBEL)
- No Rebellions patent surfaced. Public: ATOM is a transformer-and-CNN inference NPU. A dedicated assignee search ("Rebellions Inc") is the next step.

## 4. Searched and not found / next steps
- Assignee searches needed: `assignee:"Mythic"`, `assignee:"FuriosaAI"` / `"Furiosa"`, `assignee:"Rebellions"` on Google Patents / USPTO.

## 5. Search log
- `Mythic analog patent OR FuriosaAI patent OR Rebellions patent neural network accelerator convolution data layout tensor matrix`
