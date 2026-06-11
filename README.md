# WCDMA Digital Communication System — LabVIEW

![Block Diagram](Documentation/Block_Diagram.png)

---

## Overview

A complete end-to-end **WCDMA (Wideband Code Division Multiple Access)** digital communication system implemented in **LabVIEW**, covering the full baseband processing chain from raw bit generation to data recovery. Performance is evaluated through BER vs. SNR analysis with real-time constellation visualization.

The system supports both **software simulation** (AWGN channel model) and **USRP hardware** deployment.

---

## System Architecture

```
[PRBS Source] → [Conv. Encoder] → [Spreader] → [QPSK Mod + RRC] → [USRP Tx / AWGN]
                                                                           |
[Recovered Bits] ← [Viterbi Decoder] ← [Despreader] ← [QPSK Demod + MF] ← [USRP Rx]
```

| Stage | LabVIEW Block | Parameters |
|---|---|---|
| Data Source | MT Generate Bits (PN) | LFSR Order 12, Fibonacci |
| Channel Coding | MT Convolutional Code | Rate 1/3, K = 3 |
| Spreading | MT Spread Symbols | PN Order 5 (31 chips) |
| Modulation | MT Modulate PSK | M = 4 (QPSK) |
| Pulse Shaping / Matched Filter | MT Generate Filter Coefficients | RRC, α = 0.35 — shared Tx & Rx |
| Channel | MT Add AWGN | Adjustable Eb/N0 (dB) |
| Demodulation | MT Demodulate PSK | M = 4, matched RRC coefficients |
| Despreading | MT Despread Symbols | Sync depth = 31 bits |
| Decoding | MT Convolutional Decoder | Rate 1/3, K = 3, Viterbi (Hard) |

A single `MT Generate Filter Coefficients.vi` block generates the RRC coefficients and feeds them to both the modulator (Tx) and demodulator (Rx), forming a matched filter pair. Their cascade (RRC × RRC = Raised Cosine) satisfies the Nyquist zero-ISI criterion and maximizes SNR at the decision point.

---

## Performance Results

| SNR = 0 dB | SNR = 10 dB | SNR = 20 dB | SNR = 30 dB |
|:---:|:---:|:---:|:---:|
| ![SNR0](Plots/Constellation_Diagram_At_SNR_0.png) | ![SNR10](Plots/Constellation_Diagram_At_SNR_10.png) | ![SNR20](Plots/Constellation_Diagram_At_SNR_20.png) | ![SNR30](Plots/Constellation_Diagram_At_SNR_30.png) |
| Heavily scattered — high BER | Partially clustered — moderate BER | Clusters forming — low BER | Tight clusters — negligible BER |

Higher SNR → tighter constellation → lower BER → reliable data recovery.

---

## Repository Structure

```
WCDMA-LabVIEW/
├── Simulations/              # Core end-to-end simulation VI
├── USRP_Implementation/      # USRP Tx/Rx hardware integration VIs
├── Plots/                    # Constellation diagrams & BER curves
├── Documentation/            # Technical report & block diagram
└── README.md
```

---

## Getting Started

**Prerequisites:**
- LabVIEW 2020 or later
- LabVIEW Communications Design Suite (for MT blocks)
- *(Optional)* USRP hardware + NI-USRP driver

**Simulation:**
1. Clone the repository and open LabVIEW.
2. Open the main VI in `/Simulations`.
3. Set the desired **Eb/N0 (dB)** on the front panel.
4. Run the VI and observe the constellation graph and BER monitor in real time.

**USRP Hardware:**
1. Connect your USRP devices and ensure the NI-USRP driver is installed.
2. Open the VI in `/USRP_Implementation`.
3. Configure carrier frequency, gain, and sample rate, then run Tx and Rx simultaneously.

---

## Author

**Donna Ahmed Mohamed**  
Electronics & Communication Engineering — Helwan University  
[LinkedIn](https://www.linkedin.com/in/donna-ahmed-)

---

*Developed as part of a Digital Communications project — National Telecommunication Institute (NTI) & Helwan University.*
