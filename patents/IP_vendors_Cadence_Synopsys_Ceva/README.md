# Semiconductor IP vendors: Cadence (Tensilica), Synopsys (ARC), Ceva

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). These three licence NPU/DSP IP (Cadence Tensilica Vision/NNA, Synopsys ARC EV/NPX, Ceva NeuPro/SensPro). This folder is a **partial result**: the product lines are well documented but a focused assignee search returned mostly adjacent patents, so the specific layout/schedule patents were not pinned down here.

---

## 1. Why they matter for your kernel

These vendors ship the convolution engines inside a large share of edge SoCs. Their public material shows the same three formulas:
- **Cadence Tensilica Vision P6 / C5 / NNA** — MAC-bound CNN DSPs; the compiler tiles feature maps and packs weights for the vector width. [eeNews: Tensilica DSPs for neural networks](https://www.eenewseurope.com/en/embedded-neural-networks-cadences-latest-dsp-target/)
- **Synopsys ARC EV6x / NPX6** — vision processors with CNN engines; MetaWare NN compiler does layer tiling and weight compression. [eeJournal: Synopsys EV6x](https://www.eejournal.com/article/further-cnn-ip-integration)
- **Ceva NeuPro / SensPro** — the CDNN compiler lays out weights and tiles activations for the vector units.

All three are **licensors**, so their layouts are consumed through a vendor compiler (the "keep layout a planner output" camp), which is why little is exposed as a fixed public format.

---

## 2. Status

| Vendor | Patents pinned here | Note |
|---|---|---|
| Cadence Design Systems (Tensilica) | none confirmed in these searches | search returned the unrelated "Lossless tiling" family (assignee not Cadence) |
| Synopsys | none confirmed | one multicore-NN patent US11853866 surfaced (assignee unconfirmed) |
| Ceva | none confirmed | — |

## 3. Searched and not found / next steps
A dedicated assignee search is needed: `assignee:"Cadence Design Systems"`, `assignee:"Synopsys"`, `assignee:"Ceva"` on Google Patents / USPTO, with terms "convolution", "tiling", "weight", "vector". The "Lossless tiling in convolution networks" family that recurs in these searches is **not** theirs (its references point to Plasticine/Spatial = SambaNova-adjacent; see [`../SambaNova/README.md`](../SambaNova/README.md)).

## 4. Search log
- `Ceva OR Synopsys OR Cadence patent neural network processor convolution tensor data layout tiling accelerator DSP vector`
- `Synopsys patent ARC EV OR Cadence Tensilica patent convolution neural network "data layout" OR tiling accelerator tensor DSP imaging US assignee`
- `Cadence patent "Tensilica" OR "neural network" convolution "data" "memory" tiling vector DSP US granted assignee Cadence Design Systems`
