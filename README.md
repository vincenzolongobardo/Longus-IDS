# 🛡️ Longus-IDS
### ML-based Anomaly Detection for DDoS-ACK Attacks in OT/ICS Industrial Networks

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![Scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-orange)](https://scikit-learn.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## Overview

**Longus-IDS** is an Intrusion Detection System designed for industrial OT/ICS environments, targeting DDoS-ACK flooding attacks against PLCs and SCADA systems.

Industrial networks (Industry 4.0) have lost their traditional air-gap isolation due to IT/OT convergence. This exposes critical infrastructure to volumetric attacks that signature-based systems cannot intercept in real time.

Longus-IDS learns the **cyclic, deterministic baseline** of factory traffic and detects micro-deviations caused by an ongoing attack — with **sub-millisecond inference latency**, optimized for Edge deployment.

---

## Key Results

| Model | Weighted Accuracy | Inference Latency | Notes |
|---|---|---|---|
| **Longus-RF (proposed)** | **99.42%** | **1.2 ms** | Best accuracy/speed trade-off |
| XGBoost | 99.24% | 3.8 ms | 3x slower, slight overfitting |
| LightGBM | 98.54% | 1.0 ms | ~1% accuracy drop — unacceptable in industrial settings |

**Test Set validation (101,495 samples):**
- ✅ True DDoS detected: 63,672
- ✅ True Benign classified: 36,701
- ⚠️ False Positives (false alarms): only **18** out of 36,000+ legitimate flows
- ❌ False Negatives (missed attacks): 1,104

---

## How It Works

OT/ICS traffic is highly regular and repetitive. A DDoS-ACK flood injects thousands of empty control packets (ACK-only, no payload), causing an **immediate drop in mean packet length**.

Longus-IDS captures this physical signature through statistical behavioral analysis — not IP addresses or timestamps.

**Top features identified via XAI (Feature Importance):**
1. `Packet Length Mean` — dominant feature
2. `Packet Length Max`
3. `Average Packet Size`
4. `ACK Flag Count`

---

## Architecture & Pipeline

```
Raw CSV (CIC IoT-DIAD 2024)
        │
        ▼
EDA & Data Cleaning
(remove Flow ID, Src IP, Dst IP, Timestamp → prevent short-cut learning)
        │
        ▼
Pearson Correlation Analysis
(remove multicollinear features, r ≥ 0.90)
        │
        ▼
Train/Test Split (80/20, stratified) + StandardScaler
        │
        ▼
Random Forest Classifier
(n_estimators=100, max_depth=10, min_samples_leaf=50)
        │
        ▼
XAI — Feature Importance Analysis
        │
        ▼
Gradio SOC Dashboard (real-time simulation)
```

---

## Dataset

**CIC IoT-DIAD 2024** — Canadian Institute for Cybersecurity, University of New Brunswick.

- `BenignTraffic.pcap_Flow.csv` — normal industrial traffic
- `DDoS-ACK_Fragmentation.pcap_Flow.csv` — DDoS-ACK flooding attack traffic

> Dataset not included in this repository. Download from [CIC official website](https://www.unb.ca/cic/datasets/).

---

## Installation

```bash
git clone https://github.com/YOUR_USERNAME/Longus-IDS.git
cd Longus-IDS
pip install -r requirements.txt
```

Open `longus_ids.ipynb` in Jupyter or Google Colab.

Update the dataset paths in **Cell 0** and **Cell 2** to match your local or Drive file location.

---

## Requirements

See `requirements.txt` for the full list.

Main dependencies:
- `scikit-learn`
- `xgboost`
- `lightgbm`
- `gradio`
- `pandas`, `numpy`
- `matplotlib`, `seaborn`

---

## Future Work

- [ ] Extend detection to MitM and Replay attacks
- [ ] Port Longus-RF to Edge devices (Raspberry Pi / industrial gateways)
- [ ] Evolve from IDS (detection) to IPS (active prevention with firewall drop rules)
- [ ] Test generalization on additional OT datasets (SWaT, WADI, HAI)

---

## Author

**Vincenzo Longobardo**
Bachelor's Degree in Computer Science — Università degli Studi di Napoli "Parthenope"
Thesis: *"Rilevazione di Comportamenti Anomali in Reti Industriali e Sistemi OT mediante Machine Learning"*
Supervisors: Prof. Angelo Ciaramella, Prof. Antonio Agliata — A.Y. 2025/2026

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/YOUR_LINKEDIN)

---

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.
