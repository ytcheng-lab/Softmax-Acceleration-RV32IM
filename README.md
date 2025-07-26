# Fast Softmax on RV32IM Without FPU

## 📌 Summary
> **Achieved up to 58% cycle reduction** for softmax execution on RV32IM platforms by replacing floating-point exponential with lightweight software approximations.  
> This project implements a fast, FPU-free softmax tailored for edge AI inference on integer-only RISC-V SoCs.

## Motivation

The softmax function is commonly used in classification tasks and transformer-based models such as MobileBERT.  
However, on RV32IM systems (no FPU), computing `exp(x)` is expensive in both latency and instruction count.

To address this, this project investigates and implements:
- Exponential approximations (e.g., polynomial/Horner’s method)
- Lookup table (LUT) based methods
- Accuracy vs. performance trade-offs

## Results

- Input: 512 random `float32` values in range `[0, 10)`
- Platform: RISC-V Spike + pk (RV32IMZf toolchain)
- Cycle count: ~**58% reduction** (vs glibc `expf`)
- Max absolute error: **0.0003**

### Performance Comparison
| Method                | Cycle Count | Time Reduction | Max Absolute Error |
| --------------------- | ----------- | -------------- | -----------------  |
| glibc expf (baseline) | 1,265,689   | 0%             | 0                  |
| Taylor3 (no LUT)      | 386,938     | 69.43%         | 0.0132             |
| Taylor3 + LUT         | 533,376     | 57.86%         | 0.0003             |

## System Overview

This project replaces the original softmax exponential with an efficient software approximation.

### Exponential Breakdown

> *(tbd: Insert diagram illustrating exponential decomposition: shift + exp + sum + normalize)*

## Design Features

| Feature                  | Description                                               |
|--------------------------|-----------------------------------------------------------|
| No FPU required          | Compatible with RV32IM-only cores                         |
| Software-only exp(x)     | Uses LUTs and/or polynomial approximations                |
| Benchmarked on Spike     | Measures real cycle count using `mcycle` CSR             |
| Build-friendly           | One-step `make` simulation and benchmarking               |

## Project Structure

- `docs/` # Full technical report (`full-report.md`) and concept slides (softmax_rv32im_concept_slides.pdf)  
- `src/`  # Source code (`main.c`, `algo.c`, `exp_approx.c`)  
- `run/`  # Makefile, simulation runner, and outputs  
- `README.md` # This document  

## Build & Run

To compile and simulate:
```bash
cd run
make all
```

## Documentation
- Full Report — Implementation details and performance analysis
- Concept Slides — Visual explanation of design
- RISC-V toolchain setup (Spike, pk, compiler): [Setup Guide (HackMD)](https://hackmd.io/Ouj3SnvZTQ-1iiaWfvpPMQ)

## Next Step(s)
- Profile other functions (e.g., GELU, tanh) under similar constraints

## License
MIT License
