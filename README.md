
# PULP-NN: Replicating Optimized QNN Inference on Ultra-Low-Power RISC-V Clusters

This repository documents a partial replication and evaluation of the **PULP-NN library** as described in Garofalo et al. (2020), focusing on enabling **Quantized Neural Network (QNN)** inference on **Parallel Ultra-Low-Power (PULP)** multi-core RISC-V processors, specifically the GAP-8 platform using GVSOC.

---

## Project Summary

Modern IoT systems need always-on AI at the edge without the luxury of cloud connectivity or high power. This project explores **hardware-software co-design techniques** for QNN inference using:
- 8-bit quantization for weights/activations
- SIMD MAC operations
- Multi-core RISC-V clusters (1, 4, and 8 cores)

We tested **MobileNetV1** (generated with DORY) in a simulated GAP-8 environment, evaluated **speedup**, **MACs-per-cycle**, and **memory optimization** using **double buffering**.

---

##  Key Experiments Conducted

| Experiment | Objective | Tools/Methods |
|-----------|-----------|---------------|
|  Hello from FC | Toolchain & SDK sanity check | PULP-SDK + GVSOC |
|  Vector Multiply Benchmark | Scalar x vector profiling | `pi_perf` performance counters |
|  Vector Multiply w/ Double Buffering | Test memory I/O overlap | DMA + asynchronous writes |
|  QNN Inference (MobileNetV1) | End-to-end layer execution | DORY-generated code, 1–8 cores |

---

## 🔍 Results Summary

| Cores Used | Cycles | MACs | MACs/Cycle | Speedup |
|------------|--------|------|------------|---------|
| 1          | 125,672,299 | 186,400,768 | 1.48 | 1× |
| 4          | 36,042,845  | 186,400,768 | 5.17 | 3.49× |
| 8          | 21,429,362  | 186,400,768 | 8.69 | 5.86× |

- Speedup was **near-linear** (especially from 1 to 4 cores).
- MACs-per-cycle increased dramatically with core count.
- Double buffering significantly reduced idle CPU time.

---

## 🏗 Repo Structure

| Directory / File | Description |
|------------------|-------------|
| `src/`           | All core kernel implementations |
| `include/`       | Public headers (`pulp_nn_kernels.h`, `pulp_nn_utils.h`) |
| `tests/`         | All test programs (`hello`, `vector_mul`, `dory_nn`) |
| `dory/`          | DORY-generated C code for QNN layers |
| `build/`         | Intermediate build outputs |
| `report/`        | Final PDF report detailing this replication |

---

## ⚙️ How to Reproduce

### 1. Clone the Paper Version
```bash
git clone https://github.com/pulp-platform/pulp-nn.git
cd pulp-nn
git checkout paper_version
```

### 2. Install Compatible SDK and Toolchain
- Use pulp-sdk at commit `c3bfcf42ee09dd5aa99ce138a0fcecb7318c01e8`
- Ensure legacy headers like `rt/rt_api.h` are available

### 3. Build and Simulate
```bash
source configs/pulp-open.sh
make all
cd tests/dory_nn
make run platform=gvsoc
```

---

## 📚 Academic Context

This work replicates the experimental setup of:

**Garofalo et al.**, 2020.  
_"PULP-NN: accelerating quantized neural networks on parallel ultra-low-power RISC-V processors."_  
Phil. Trans. R. Soc. A. 378 (2020): 20190155  
DOI: [10.1098/rsta.2019.0155](http://doi.org/10.1098/rsta.2019.0155)

Our implementation validates key performance improvements in QNN inference through:
- Lightweight parallelism
- SIMD acceleration
- Memory I/O optimization


---

## 📎 Future Work

- Extend to **sub-byte quantization (1-4 bit)** from newer PULP-NN branches
- Evaluate **energy efficiency vs. STM32 benchmarks**
- Port project to **physical GAP-8 boards**
