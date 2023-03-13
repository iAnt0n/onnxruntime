# Graph fusion experiment results
## Supported ViT by parasiting on existing BERT attention fusion
* From the architecture point ViT and BERT are pretty much the same, encoder-only, etc.
![about vit arch](./img/vit-arch.png)

* No notices of attention mask in ViT paper, and from the graphs it is obvious that, unlike BERT, ViT does not use attention mask at all
Needed to make some changes in [attention fusion code](../onnxruntime/python/tools/transformers/fusion_attention.py) (constanly saying that python open source is pure trash)

* Different network graphs can be found [here](./graphs/)

## IAccuracy: first 1000 images from ImageNet dataset
| Model | Accuracy |
| --- | --- |
| HuggingFace Hub PyTorch | 0.798 |
| ORT with no optimizations | 0.798 |
| ORT with full fusion | 0.798 |

## Inference time for first 1000 images from ImageNet dataset
| Model | CPU | GPU |
| --- | --- | --- |
| HuggingFace Hub PyTorch | 150.90612 | ---
| ORT with no optimizations | 112.91931 | 4.03573
| ORT with all possible fusion except Attention | 81.94202 | 3.66351
| ORT with full fusion | 73.34913 | 3.18753

## Above is not very accurate and representative, here is better
### ViT
#### patch16-224
| Model | CPU | GPU |
| --- | --- | --- |
| PyTorch with no optimizations | 102.223 | 11.835 |
| PyTorch with BetterTransformers | - | 4.564 |
| ORT with no optimizations | 81.054 | 3.840 |
| ORT with all possible fusion except Attention | 77.401 | 3.449 |
| ORT with full fusion | 71.982 | 2.966 |
| TensorRT | - | 1.952 | 
| OpenVINO | 90.223 | - |

### BERT
#### batch size 1, sequence length 32
| Model | CPU | GPU |
| --- | --- | --- |
| PyTorch with no optimizations | 45.648 | 13.859 |
| PyTorch with BetterTransformers | - | 8.526 |
| ORT with no optimizations | 19.090 | 2.963 |
| ORT with full fusion | 15.357 | 1.534 |
| TensorRT | - | 1.163 | 

### Google Colab VM
```
Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   46 bits physical, 48 bits virtual
CPU(s):                          12
On-line CPU(s) list:             0-11
Thread(s) per core:              2
Core(s) per socket:              6
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       GenuineIntel
CPU family:                      6
Model:                           85
Model name:                      Intel(R) Xeon(R) CPU @ 2.20GHz
Stepping:                        7
CPU MHz:                         2200.204
BogoMIPS:                        4400.40
Hypervisor vendor:               KVM
Virtualization type:             full
L1d cache:                       192 KiB
L1i cache:                       192 KiB
L2 cache:                        6 MiB
L3 cache:                        38.5 MiB
NUMA node0 CPU(s):               0-11

+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.85.12    Driver Version: 525.85.12    CUDA Version: 12.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA A100-SXM...  Off  | 00000000:00:04.0 Off |                    0 |
| N/A   37C    P0    53W / 400W |      0MiB / 40960MiB |      0%      Default |
|                               |                      |             Disabled |
+-------------------------------+----------------------+----------------------+
```
## Tasks 
- [x] Learn about graph-level neural network optimizations
- [x] Research existing solutions for optimizing ViT model (TLDR; many for text transformers, not so many for vision)
- [x] Adapt ONNX Runtime optimizer for ViT
- [x] Measure inference time
  - [x] HF and ORT on CPU
  - [x] GPU
  - [x] TensorRT
  - [x] BetterTransformers (since it is encoder-only for now and ViT is such)
  - [x] Bonus: OpenVINO
- [x] Parity check
- [x] ORT GitHub contribution
- [ ] Formal report (WIP)
- [ ] Bonus: perf record analysis
