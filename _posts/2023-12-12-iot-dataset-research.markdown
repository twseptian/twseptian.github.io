---
layout: post
title:  "IoT Dataset Research: Curating Realistic Traffic for Security Analysis"
date:   2023-12-12 20:00:19 +0700
author: twseptian
pin: true
categories: [Infosec, IoT, Research]
tags: [iot, infosec, dataset]
---

## Introduction
This article outlines my work in researching Internet of Things (IoT) security and producing curated datasets for intrusion detection, protocol analysis, and machine-learning experiments. The focus is on practical, reproducible methods for capturing and labeling traffic that reflects real deployment conditions. Links to the underlying records are provided for verification and reuse.

## Scope and Objectives
- Build a **minimal but representative IoT testbed** (sensors/actuators, gateways, mixed radios).
- Capture **normal**, **attack**, and **mixed** scenarios to support supervised and unsupervised studies.
- Provide **documented feature tables** and clear methodology notes for reproducibility.
- Publish through persistent repositories (e.g., Zenodo) with stable identifiers.

## Methodology
1. **Requirements & Threat Model**  
   Select protocol families (Wi-Fi, ZigBee/XBee, CoAP, MQTT) and DoS/misuse patterns relevant to constrained devices.

2. **Testbed Assembly**  
   Combine commodity boards, gateways, and low-power radios. Keep topology simple, documented, and repeatable.

3. **Scenario Design**  
   - *Normal*: routine telemetry, command, and control flows.  
   - *Attack*: controlled floods/misuse aligned to protocol behavior.  
   - *Mixed*: realistic blends to observe detector drift and false positives.

4. **Traffic Capture**  
   Record raw PCAPs with timestamps and device identifiers. Maintain capture notes (topology, firmware, run IDs).

5. **Feature Engineering**  
   Extract packet/session attributes into structured tables; optionally apply dimensionality reduction for ML tasks. Retain a mapping from features to raw traces for auditability.

6. **Documentation & Publication**  
   Provide abstracts, scenario descriptions, feature dictionaries, and access guidance. Where necessary, restrict raw traces while publishing sanitized derivatives.

## Dataset Portfolio (selected)
- **TCP FIN Flood and Zbassocflood IoT Dataset**  
  Normal, attack, and mixed scenarios across Wi-Fi and ZigBee (XBee), with feature-extraction details.  
  Zenodo: https://zenodo.org/records/4431541

- **Ping Flood Attack Pattern Recognition on IoT Networks**  
  Labeled traffic and clustering results (e.g., K-Means) for ICMP flood behavior on Wi-Fi.  
  Zenodo: https://zenodo.org/records/4436208

- **UDP Flood Attack Pattern on IoT Networks**  
  DoS-oriented UDP traffic captures suitable for detector benchmarking.  
  Zenodo: https://zenodo.org/records/4436127

- **Constrained Application Protocol (CoAP) Dataset**  
  Message-level CoAP exchanges for analyzing constrained application behavior and misuse.  
  Zenodo: https://zenodo.org/records/4436043

- **Message Queue Telemetry Transport (MQTT) Dataset**  
  Publish/subscribe patterns, session characteristics, and anomalous messaging scenarios.  
  Zenodo: https://zenodo.org/records/4436172

> Some records restrict raw files. Follow the request instructions on the respective Zenodo pages.

## Engineering Notes
- **Realism over complexity**: A small, heterogeneous lab generates more useful signals than synthetic traffic alone.  
- **Explicit labels**: Separate normal/attack/mixed runs simplify evaluation and reduce labeling errors.  
- **Feature dictionaries**: Document each attribute (name, type, units, derivation) to support replication and peer review.  
- **Provenance**: Keep run sheets (date, topology, firmware, parameters) to defend result integrity.

## Governance and Ethics
- Capture data **only** on systems under control or with explicit authorization.  
- Exclude human-sensitive payloads; sanitize metadata where applicable.  
- Prefer **restricted access** to raw traces if they expose device secrets; publish derived, anonymized features publicly.  
- Provide clear **citation guidance** and versioning for downstream users.

## Reuse Guidelines
- Benchmark IDS rules (Snort/Suricata) and ML classifiers on the labeled scenarios.  
- Compare protocol-specific indicators (e.g., Wi-Fi vs. ZigBee) to assess detector portability.  
- Cite dataset DOIs and include version numbers in methods sections.  
- Share improvements (e.g., new features or labels) with change logs for traceability.

## Conclusion
Well-designed IoT datasets bridge the gap between controlled experiments and operational realities. The collections above are structured to be auditable, reusable, and suitable for comparative studies. Feedback and collaboration are welcome—especially contributions that extend features, scenarios, or validation protocols.