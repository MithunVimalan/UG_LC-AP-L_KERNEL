# Rockchip (RKNN / RK3588 NPU)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Rockchip matters for your kernel

Rockchip's RK3588/3576 NPU exposes its **native matmul layouts publicly in a header** (`rknn_matmul_api.h`): A int8 `(K/16, M, 16)`, fp16 `(K/8, M, 8)`; B int8 `(N/32, K/32, 32, 32)`; C `(N/4, M, 4)`; and the feature layout `NC1HWC2` with `C2 = 16` bytes of channel data (8 fp16 / 16 int8). These are F3 formulas of the same family as your `nChw8c`, with the block set by the MAC cell caching 16 bytes of weight.

**No Rockchip patent surfaced** in these searches; the layouts are public in the RKNN toolkit header and third-party reverse engineering. This folder is a stub pending a dedicated Rockchip assignee search.

---

## 2. Public (non-patent) layout material (F3) — prior art
| Item | Source |
|---|---|
| A/B/C native matmul layouts (block 16/32); `NC1HWC2`, `C2 = 16` | [rknn_matmul_api.h](https://github.com/airockchip/rknn-toolkit2/blob/master/rknpu2/runtime/Linux/librknn_api/include/rknn_matmul_api.h) |
| "Each MAC cell caches 1x1x16 weight bytes" (16 int8 / 8 fp16) | [jas-hacks RK3588 reverse engineering](http://jas-hacks.blogspot.com/2024/02/rk3588-reverse-engineering-rknn.html) (third-party) |

Derived offsets (from the header, not a patent):
```
A int8:  off = (k//16)*M*16 + m*16 + k%16
B int8:  off = (n//32)*(K/32)*1024 + (k//32)*1024 + (n%32)*32 + k%32
NC1HWC2: off = (((n*C1 + c//C2)*H + h)*W + w)*C2 + c%C2,  C2 = 16
```

## 3. Searched and not found
- No Rockchip-assigned patent surfaced for data layout / convolution. A follow-up assignee search ("Rockchip Electronics", "Fuzhou Rockchip") is the next step.

## 4. Search log
- `Hailo Technologies patent OR Rockchip patent neural network accelerator data layout convolution dataflow structured US assignee`
