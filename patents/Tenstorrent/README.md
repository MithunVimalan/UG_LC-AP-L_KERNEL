# Tenstorrent

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred. Assignee list: [Justia, Tenstorrent Inc](https://patents.justia.com/assignee/tenstorrent-inc).

---

## 1. Why Tenstorrent matters for your kernel

Tenstorrent's **tile layout is fully public** and is a clean F3 example: a **32x32 tile** subdivided into **four 16x16 faces**, each face a contiguous block, each tile on its own page, tensors aligned to tile boundaries ([Metalium tiles doc](https://docs.tenstorrent.com/tt-metal/latest/tt-metalium/tt_metal/advanced_topics/tiles.html)). The engine natively multiplies 16x16, so one tile = four engine operands. The patents found are about **data movement over the NoC and dataflow-graph execution** (F1), not about the tile layout as such — the layout lives in open docs and acts as prior art.

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US12248430B2 | (multicore processor; data movement among cores over NoC) | F1 | granted 11 Mar 2025 | V |
| 2 | US12210478B2 | (executing an application data-flow graph across computational nodes) | F1 | granted | V |
| 3 | US20250252075A1 (CA3319147A1) | Network on chip with data movement processing cores | F1 | application | V |

---

## 3. Patent details

### 3.1 US12248430B2 — multicore processor with NoC data movement
- **Assignee:** Tenstorrent Inc. App. filed 14 Sep 2022; granted 11 Mar 2025. Cores, pipelines, routers and **network interface units** move data over a **network-on-chip** layer.
- **In three-formula terms (analysis).** F1 at the chip-mesh level: which core computes which tile and how tiles stream between cores — the chip-level schedule around the 32x32 tile operand.
- **Links:** [Justia, Tenstorrent Inc](https://patents.justia.com/assignee/tenstorrent-inc)

### 3.2 US12210478B2 — executing an application data-flow graph
- Programmable controllers run instructions **asynchronously in response to events** exchanged between controllers, NIUs and endpoints, to execute a dataflow graph across nodes. F1.

### 3.3 US20250252075A1 — Network on chip with data movement processing cores
- Inventor Ivan Matosevic; CA counterpart CA3319147A1 lists assignee **Tenstorrent USA Inc** (name varies by filing). F1. [Justia, Ivan Matosevic](https://patents.justia.com/inventor/ivan-matosevic) · [CA3319147A1](https://eureka.patsnap.com/patent/CA3319147A1)

---

## 4. Public (non-patent) layout material — prior art for F3
- Tile = 32x32, four 16x16 faces, each face contiguous; `offset = tile_id*1024 + face*256 + (r%16)*16 + (c%16)`: [Metalium tiles](https://docs.tenstorrent.com/tt-metal/latest/tt-metalium/tt_metal/advanced_topics/tiles.html)
- Engine natively multiplies 16x16; tile = four engine operands: [ttnn Tensor docs](https://docs.tenstorrent.com/tt-metal/latest/ttnn/ttnn/tensor.html)
- NoC, unpacker/packer (L1 <-> source/dest registers): [Metalium guide](https://github.com/tenstorrent/tt-metal/blob/main/METALIUM_GUIDE.md), [pybuda terminology](https://docs.tenstorrent.com/pybuda/latest/terminology.html)

## 5. Searched and not found
- No Tenstorrent patent found that claims the 32x32 tile / 16x16 face layout or convolution access map; these are public docs.

## 6. Search log
- `Tenstorrent patent tensor tile 32x32 face layout "Tensix" data movement NOC compiler patent assignee`
- `Tenstorrent Inc patent assignee justia convolution tensor matrix "network on chip" data movement OR scheduling`
