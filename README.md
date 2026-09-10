# IoT Lateral Movement Detection: A Baseline Study

## Overview

This project investigates the effectiveness of behavioral anomaly detection in identifying lateral movement attacks across heterogeneous consumer IoT ecosystems. Using an unsupervised Isolation Forest model trained exclusively on benign network traffic, we evaluate detection performance across four distinct attack categories of increasing subtlety — from high-volume Denial of Service attacks to identity-based Man-in-the-Middle attacks.

**Core finding:** Traffic-behavior-based anomaly detection performs well on high-volume, "loud" attacks but degrades substantially on subtler, identity-based attacks — providing empirical support for the need to combine behavioral detection with zero-trust identity verification in IoT security architectures.

## Motivation

As consumer IoT ecosystems grow increasingly interconnected — wearables, smartphones, smart speakers, and smart home hubs frequently communicating across different vendor platforms — a single compromised device can become a pivot point for lateral movement across an entire personal network. Most existing IoT security research focuses on industrial or single-vendor IoT environments; this project specifically investigates detection challenges in **heterogeneous, cross-vendor consumer IoT** settings, an area that remains comparatively underexplored.

## Dataset

This project uses the [**CICIoT2023**](https://www.unb.ca/cic/datasets/iotdataset-2023.html) dataset, developed by the Canadian Institute for Cybersecurity (CIC) at the University of New Brunswick. CICIoT2023 was generated from a realistic smart home testbed of 105 heterogeneous IoT devices, capturing over 47 million labeled network flows across 33 distinct attack types.

For this baseline study, four attack categories were evaluated against benign traffic:

| Attack Category | Type | Description |
|---|---|---|
| DoS-UDP Flood | Volumetric | High-volume denial of service via UDP flooding |
| Recon-PortScan | Reconnaissance | Port scanning to identify open services |
| VulnerabilityScan | Reconnaissance | Scanning for known device/software vulnerabilities |
| MITM-ArpSpoofing | Identity-based | ARP cache poisoning to impersonate device identity |

## Methodology

1. **Feature preparation:** Categorical features (e.g., protocol type) were one-hot encoded; missing values were imputed as zero.
2. **Model:** An unsupervised **Isolation Forest** was trained exclusively on benign traffic to learn a baseline of "normal" network behavior, without exposure to any attack data during training.
3. **Evaluation:** For each attack category, the trained model was tested against a held-out, class-balanced set of benign and attack traffic to avoid distortion from class imbalance.
4. **Metrics:** Precision, recall, and F1-score were calculated for each attack category using scikit-learn's `classification_report`.

## Results

| Attack Type | Attack Recall | Attack Precision | Overall Accuracy |
|---|---|---|---|
| DoS-UDP Flood | 1.00 | 0.99 | 0.99 |
| Recon-PortScan | 0.58 | 0.96 | 0.70 |
| VulnerabilityScan | 0.47 | 0.91 | 0.71 |
| MITM-ArpSpoofing | 0.29 | 0.86 | 0.62 |

![Performance chart showing declining recall across attack types](results-chart.png)

### Key Observations

- **DoS-UDP Flood** was detected with near-perfect recall (1.00), consistent with its high-volume, statistically obvious traffic signature.
- **Recon-PortScan** and **VulnerabilityScan** — both reconnaissance-style attacks designed to blend in with normal traffic — showed substantially reduced recall (0.58 and 0.47, respectively).
- **MITM-ArpSpoofing** exhibited the weakest detection performance (recall = 0.29). This is a critical finding: ARP spoofing is fundamentally an **identity impersonation attack**, not a traffic-volume or pattern anomaly. Behavioral anomaly detection has limited visibility into device identity claims, since it evaluates *how* traffic behaves rather than *who* a device claims to be.

## Implications

These results provide direct empirical evidence that **behavioral anomaly detection alone is insufficient** for identifying identity-based lateral movement attacks in IoT ecosystems. This motivates the core architecture proposed in the broader research direction of this project: an AI-driven detection framework combined with **zero-trust identity verification**, to address the specific blind spot demonstrated by the MITM-ArpSpoofing results above.

## Future Work

- **Zero-trust identity layer:** Incorporate device identity verification (e.g., certificate- or token-based authentication) to directly address the identity-impersonation blind spot identified in this study.
- **Blockchain-based identity ledger:** Explore a lightweight, hash-chained device identity record to support decentralized trust verification across vendor ecosystems.
- **Advanced modeling:** Evaluate more sophisticated architectures (e.g., autoencoders, graph-based behavioral models) to determine whether detection of subtle reconnaissance and identity-based attacks can be improved beyond this baseline.
- **Automated mitigation:** Extend the detection framework toward automated response — e.g., recommending or enforcing device isolation upon high-confidence attack detection — as a pathway toward real-time prevention.
- **Cross-vendor heterogeneity:** Extend evaluation to more explicitly heterogeneous, cross-vendor device combinations, better reflecting real-world consumer IoT ecosystems.

## Repository Structure

```
├── notebooks/
│   └── iot_anomaly_detection.ipynb    # Full analysis notebook
├── results_chart.png                   # Performance comparison chart
└── README.md
```

## Tech Stack

- Python 3.12
- pandas, NumPy
- scikit-learn (Isolation Forest, classification metrics)
- Jupyter Notebook

## Dataset Citation

Neto, E. C. P., Dadkhah, S., Ferreira, R., Zohourian, A., Lu, R., & Ghorbani, A. A. (2023). CICIoT2023: A real-time dataset and benchmark for large-scale attacks in IoT environment. *Sensors*, 23(13), 5941. https://www.unb.ca/cic/datasets/iotdataset-2023.html

## Author

**Yewande Goodness Hassan**
LinkedIn: [linkedin.com/in/hassanyewande](https://www.linkedin.com/in/hassanyewande/)

This project is part of ongoing research into AI-driven, zero-trust security frameworks for heterogeneous consumer IoT ecosystems.
