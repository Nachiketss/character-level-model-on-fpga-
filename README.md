# FPGA-Based Character-Level MLP Name Generator

An **INT8-quantized character-level neural network accelerator** implemented on a **Xilinx Artix-7 FPGA**. The project demonstrates an end-to-end AI hardware workflow, from model training and quantization in PyTorch to HLS-based accelerator design, FPGA deployment, and hardware/software integration using MicroBlaze and AXI.

## Project Overview

This project implements a character-level **Multilayer Perceptron (MLP)** capable of generating character sequences resembling names.

The neural network was initially trained in **PyTorch**, quantized to **INT8**, and then implemented as a hardware accelerator using **Vitis HLS**. The accelerator was integrated into a MicroBlaze-based FPGA system using AXI interfaces and deployed on a **Nexys A7-100T** development board.

The project explores the trade-offs between **machine-learning accuracy, hardware resource utilization, memory efficiency, and timing performance**.

## System Architecture

```text
                 ┌──────────────────┐
                 │   PyTorch Model  │
                 │    Training      │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │   INT8           │
                 │   Quantization   │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │    Vitis HLS     │
                 │  MLP Accelerator │
                 └────────┬─────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │      AXI Interface    │
              └───────────┬───────────┘
                          │
              ┌───────────▼───────────┐
              │       MicroBlaze      │
              │     Soft Processor    │
              └───────────┬───────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │   UARTLite       │
                 │   Serial Output  │
                 └──────────────────┘
                          │
                          ▼
                    Generated Names
```

## Hardware Platform

* **FPGA:** Xilinx Artix-7 XC7A100T
* **Development Board:** Digilent Nexys A7-100T
* **Processor:** MicroBlaze soft processor
* **Accelerator:** Vitis HLS-generated MLP IP
* **Interconnect:** AXI
* **UART:** AXI UARTLite
* **Clock:** Vivado Clocking Wizard
* **Reset:** Processor System Reset
* **FPGA Tool:** Vivado 2025.1
* **HLS Tool:** Vitis HLS

## Machine Learning Model

The model is a character-level MLP trained to predict the next character in a sequence.

### Training

The neural network was trained using **PyTorch**.

| Metric               | Result |
| -------------------- | -----: |
| Training Loss        | 2.1248 |
| Validation Loss      | 2.1693 |
| Random Baseline      |  3.296 |
| Number of Parameters | 11,897 |

The validation loss remains close to the training loss, indicating limited overfitting.

## INT8 Quantization

To reduce memory requirements and improve hardware efficiency, the trained floating-point model was quantized to **8-bit integer representation**.

The weights were scaled using:

```text
w_q = clip(round(w × 127 / max(|w|)), -127, 127)
```

The average quantization error across layers was **less than 0.008**.

### Benefits

* Approximately **4× reduction in parameter memory**
* Lower hardware storage requirements
* More efficient FPGA implementation
* Maintained model behavior with minimal quantization error

## Hardware Accelerator

The neural-network forward pass was implemented in **C/C++ using Vitis HLS** and synthesized into FPGA hardware.

The generated accelerator was integrated into a Vivado block design and controlled by the MicroBlaze processor.

### Hardware/Software Co-Design

The system uses MicroBlaze for software-level control while the computationally intensive neural-network inference is performed by the FPGA accelerator.

```text
MicroBlaze
    │
    │ AXI
    ▼
MLP Accelerator
    │
    ▼
Neural Network Inference
```

This approach allows software flexibility while accelerating the primary computation in dedicated FPGA hardware.

## FPGA Implementation Results

The accelerator was successfully synthesized, implemented, and integrated into the FPGA system.

### Resource Utilization

| Resource        | Usage |
| --------------- | ----: |
| LUTs            | 1,320 |
| Flip-Flops      | 1,246 |
| BRAM            |     4 |
| LUT Utilization |   ~2% |
| FF Utilization  |   ~1% |

The relatively low resource utilization leaves substantial FPGA resources available for additional functionality or larger accelerator architectures.

## Timing Results

The synthesized accelerator achieved:

| Metric           |         Result |
| ---------------- | -------------: |
| Target Frequency |        100 MHz |
| Achieved Fmax    | **144.94 MHz** |
| WNS              |   **2.925 ns** |
| TNS              |       **0 ns** |

The design successfully achieved timing closure and exceeded the original 100 MHz target frequency.

## FPGA Deployment

The complete system was deployed on the **Nexys A7-100T** board.

The final hardware platform included:

* MicroBlaze processor
* MLP accelerator
* AXI interconnect
* AXI UARTLite
* Clocking Wizard
* Processor System Reset
* FPGA memory and supporting infrastructure

The generated bitstream was successfully built and the hardware platform was exported for software development.

## Example Output

The MicroBlaze software communicates with the accelerator and generates character sequences through the trained model.

The generated outputs produced plausible name-like sequences and were consistent with the behavior observed from the original floating-point PyTorch model.

Example:

```text
Generated names:
[Example outputs can be added here]
```

## Tools & Technologies

### Machine Learning

* Python
* PyTorch
* INT8 Quantization

### FPGA / Hardware

* Xilinx Artix-7
* Nexys A7-100T
* Vitis HLS
* Vivado 2025.1
* MicroBlaze
* AXI
* AXI UARTLite

### Hardware Description / Design

* C/C++
* SystemVerilog / Verilog
* FPGA IP integration
* Hardware/software co-design

## Key Technical Takeaways

This project provided hands-on experience across the complete AI hardware development flow:

1. Training a machine-learning model in PyTorch
2. Evaluating model performance
3. Applying INT8 quantization
4. Designing an HLS-based neural-network accelerator
5. Integrating custom IP into a Vivado design
6. Connecting hardware and software through AXI
7. Using MicroBlaze for processor-based control
8. Generating and deploying an FPGA bitstream
9. Analyzing FPGA resource utilization
10. Performing timing analysis and achieving timing closure

## Future Improvements

Potential extensions of this project include:

* Increasing model size and network complexity
* Exploring different quantization schemes
* Implementing additional parallelism and pipelining
* Optimizing memory access and data movement
* Comparing HLS and RTL implementations
* Adding additional FPGA communication interfaces
* Benchmarking hardware inference latency against software inference
* Exploring larger AI models and more complex FPGA accelerator architectures

## Repository Structure

```text
fpga-mlp-name-generator/
│
├── training/
│   ├── train.py
│   └── quantization.py
│
├── hls/
│   ├── mlp.cpp
│   └── mlp.h
│
├── microblaze/
│   └── main.c
│
├── vivado/
│   └── README.md
│
├── results/
│   ├── resource_utilization.md
│   └── timing_results.md
│
├── docs/
│   └── architecture.png
│
└── README.md
```

## Author

**Nachiket Shirgur**

M.S. Electrical Engineering
Stevens Institute of Technology

Interested in **FPGA design, AI hardware, embedded systems, digital design, and hardware/software co-design**.

