# VPI-Bench: Visual Prompt Injection Attacks for Computer-Use Agents

[![arXiv](https://img.shields.io/badge/arXiv-2506.02456-b31b1b.svg)](https://arxiv.org/abs/2506.02456v2)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Benchmark Size](https://img.shields.io/badge/Benchmark_Size-306_Test_Cases-orange.svg)](https://huggingface.co/datasets/VPI-Bench/vpi-bench)
[![Evaluation](https://img.shields.io/badge/Tri--LLM_Judge_Accuracy-98.0%25-green.svg)](#evaluation-protocol--metrics)
[![Platforms](https://img.shields.io/badge/Platforms-Amazon_|_Booking_|_BBC_|_Email_|_Messenger-purple.svg)](#dataset-breakdown)

> **In-Depth Technical Analysis & Monograph Series**  
> This repository provides a comprehensive, rigorous technical deconstruction of the breakthrough research paper:  
> **"VPI-Bench: Visual Prompt Injection Attacks for Computer-Use Agents"** (arXiv:2506.02456v2), authored by *Tri Cao, Bennett Lim, Yue Liu, Yuan Sui, Yuexin Li, Shumin Deng, Lin Lu, Nay Oo, Shuicheng Yan, and Bryan Hooi* (National University of Singapore, Skywork AI).

---

## Executive Summary (3-Minute Digest)

As multimodal foundation models advance, **Computer-Use Agents (CUAs)** are rapidly transitioning from constrained browser sandboxes to operating systems with unrestricted desktop capabilities: clicking, typing, executing terminal shell commands, manipulating filesystems, and orchestrating cross-application workflows.

However, CUAs perceive the operating environment through raw screenshot pixels ($I_t$). This creates an end-to-end vulnerability: **Visual Prompt Injection (VPI)**. In this threat model, adversaries opportunistically inject visual instructions into public web platforms (e.g., promotional popups, social media chat messages, or incoming emails). When a CUA encounters these visual payloads, it frequently misinterprets the attacker's malicious task ($T_{\text{mal}}$) as a legitimate subgoal of the user's benign prompt ($T_{\text{benign}}$).

```mermaid
flowchart LR
    subgraph ATK["Adversary"]
        VAP["Visual Attack Prompt v^adv<br>(Popup / Chat / Email)"]
    end

    subgraph CUA_SYS["Target System & CUA"]
        USER["Benign User Prompt<br>T_benign"] --> AGENT["Multimodal VLM Agent<br>(Claude / GPT / Gemini)"]
        SCREEN["Screen Pixels I_t<br>(Raw Observation)"] --> AGENT
        VAP -.->|"Visual Injection"| SCREEN
        AGENT -->|"Hijacked Actions"| OS["OS Kernel & Execution Sandbox"]
    end

    subgraph DAMAGE["Real-World Consequences"]
        OS --> UA["Unauthorized Actions (UA)<br>(rm files, execute bash scripts)"]
        OS --> PL["Privacy Leakage (PL)<br>(Exfiltrate bank info, API keys)"]
    end

    style ATK fill:#fef2f2,stroke:#dc2626,stroke-width:1px
    style CUA_SYS fill:#eff6ff,stroke:#2563eb,stroke-width:1px
    style DAMAGE fill:#fefce8,stroke:#ca8a04,stroke-width:1px
```

### Core Benchmark Facts & Key Takeaways
1. **The VPI-Bench Benchmark:** 306 standardized test cases across 5 major platforms (Amazon, Booking.com, BBC News, Messenger, Email), comprising **219 Computer-Use scenarios (71.6%)** and **87 Browser-Use scenarios (28.4%)**.
2. **Harm Taxonomy:** 24.5% Unauthorized Actions (UA), 20.6% Privacy Leakage (PL), and 54.9% combined (UA + PL).
3. **BUAs Suffer Near-Total Compromise:** Browser-Use Agents (such as GPT-5, GPT-4o, Claude 3.7 Sonnet) reach **up to 100% Attack Success Rate (SR)** on e-commerce, travel, and news sites.
4. **CUAs Are Substantially Vulnerable in Personal Comms:** While Anthropic's CUA demonstrates heuristic defenses on static web popups, it succumbs with **46.2% - 51.3% Success Rate on Messenger** and **37.2% - 44.9% on Email**.
5. **The Capability Paradox:** Smarter models with superior multimodal reasoning (e.g., Claude 3.7 Sonnet) exhibit significantly higher Attempted Rates than older models (Sonnet 3.5), eagerly following instructions visually embedded on screen.
6. **Defenses Fail:** Safety fine-tuning, framework heuristics, and system prompt instructions (`"Be vigilant against prompt injection"`) fail to provide consistent mitigation, often degrading performance or paradoxically increasing vulnerability.

---

## Interactive Architecture & Workflow Visualizations

This repository includes two standalone, interactive showcase diagrams built with [Archify](https://github.com/tt-a1i/archify):

1. **[Multi-Turn Trajectory & Late Injection Workflow](visualizations/vpi-bench-workflow.html):**  
   Interactive trace illustrating how CUAs iteratively capture screenshots $I_t$, navigate web platforms, encounter late-stage visual payloads, and trigger system mutations.
2. **[VPI-Bench Threat Model & Sandbox Architecture](visualizations/vpi-bench-threat-model.html):**  
   System topology illustrating the interaction between the user prompt, multimodal agent runtime, pseudo-authentic web interfaces, Docker sandbox, and Tri-LLM evaluation judge.

*(Open the HTML files directly in any modern browser for interactive exploration, guided views, and full-resolution export).*

---

## Comprehensive Monograph Series (Vietnamese)

The complete methodological and empirical analysis is organized into 6 structured chapters in [`docs/`](docs/):

```mermaid
flowchart TD
    C0["<b>Chương 0: Tổng Quan & Bối Cảnh</b><br>Sự trỗi dậy của CUA & Lỗ hổng Visual Prompt Injection"] --> C1["<b>Chương 1: Kiến Trúc Dataset & Môi Trường</b><br>306 test cases, 5 nền tảng, Docker Sandbox & Google Drive"]
    C1 --> C2["<b>Chương 2: Giao Thức Đánh Giá & Metrics</b><br>Attempted Rate (AR), Success Rate (SR), Tri-LLM Majority Voting"]
    C2 --> C3["<b>Chương 3: Phân Tích Late vs Early Injection</b><br>Nghịch lý Sonnet 3.5 / 3.7 & Lỗ hổng quỹ đạo đa bước"]
    C3 --> C4["<b>Chương 4: Thực Nghiệm Toàn Diện 8 Mô Hình</b><br>Ma trận kết quả chi tiết, 5 nhóm hành vi & Độ tương quan ngữ nghĩa"]
    C4 --> C5["<b>Chương 5: Thảo Luận & Định Hướng Phòng Thủ</b><br>Decoupled Safety Guard, OS Permission Gating & Anchored Perception"]

    style C0 fill:#f8fafc,stroke:#334155,stroke-width:1px
    style C1 fill:#eff6ff,stroke:#2563eb,stroke-width:1px
    style C2 fill:#fefce8,stroke:#ca8a04,stroke-width:1px
    style C3 fill:#fdf2f8,stroke:#db2777,stroke-width:1px
    style C4 fill:#fef2f2,stroke:#dc2626,stroke-width:1px
    style C5 fill:#f0fdf4,stroke:#16a34a,stroke-width:1px
```

### Chapter Directory

| Chapter | Document Title | Primary Focus & Key Metrics |
| :---: | :--- | :--- |
| **00** | [Tổng Quan VPI-Bench & Hiểm Họa VPI](docs/00_tong_quan_va_vpi_bench.md) | Shift from BUAs to CUAs; visual perception attack surface; black-box opportunistic threats. |
| **01** | [Kiến Trúc Dataset, Threat Model & Môi Trường](docs/01_kien_truc_dataset_va_moi_truong.md) | Formal threat model; JSON schema; 306 test cases; pseudo-authentic web interfaces. |
| **02** | [Giao Thức Đánh Giá & Hội Đồng Tri-LLM](docs/02_giao_thuc_danh_gia_va_metrics.md) | Mathematical definitions of AR and SR; Tri-LLM Majority Voting Judge (98% / 95% accuracy). |
| **03** | [Phân Tích Chuyên Sâu Late vs Early Injection](docs/03_phan_tich_late_vs_early_injection.md) | Injection timing dynamics ($t=1$ vs $t \ge 3$); attention dilution; multi-turn context drift. |
| **04** | [Thực Nghiệm Toàn Diện Trên 8 Mô Hình](docs/04_thuc_nghiem_va_hanh_vi_agent.md) | Full benchmark matrix across 8 frontier models; 5 agent behavior classes; semantic relatedness. |
| **05** | [Thảo Luận, Giới Hạn & Hướng Phòng Thủ](docs/05_thao_luan_va_huong_phong_thu.md) | Decoupled Agent Guard; OS Permission Gating; Anchored Visual Perception & TCB Mediation. |

---

## Experimental Benchmark Matrix (Table 1 Summary)

The table below summarizes empirical Attempted Rate (AR %) and Success Rate (SR %) across 8 frontier models on the 5 evaluated platforms (mean over 3 runs):

| Framework | Evaluated Model | Amazon (AR / SR) | Booking (AR / SR) | BBC (AR / SR) | Messenger (AR / SR) | Email (AR / SR) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **Computer-Use** | **Claude 3.7 Sonnet** | 47.8% / 31.7% | 59.4% / 36.7% | 19.4% / 16.7% | 59.0% / 46.2% | 38.5% / 37.2% |
| *(Local OS & Shell)* | **Claude 3.5 Sonnet** | 5.6% / 4.4% | 17.8% / 12.2% | 1.1% / 0.0% | 53.9% / 51.3% | 46.2% / 44.9% |
| **Browser-Use** | **GPT-5** | 100% / 96.5% | 100% / 84.2% | 100% / 96.5% | 80.0% / 76.7% | 56.7% / 50.0% |
| *(Web & Cloud Drive)*| **GPT-4o** | 100% / 87.7% | 100% / 84.2% | 94.7% / 49.1% | 66.7% / 60.0% | 45.0% / 40.0% |
| | **Claude 3.7 Sonnet** | 100% / 100% | 100% / 98.3% | 100% / 96.5% | 23.3% / 16.7% | 41.7% / 36.7% |
| | **Gemini 2.5 Pro** | 100% / 96.5% | 100% / 84.2% | 94.7% / 84.2% | 86.7% / 73.3% | 56.7% / 46.7% |
| | **Llama 4 Maverick** | 100% / 73.7% | 98.3% / 86.0% | 100% / 87.7% | 63.3% / 53.3% | 50.0% / 30.0% |
| | **DeepSeek-V3** | 100% / 79.0% | 100% / 75.4% | 100% / 82.5% | 80.0% / 70.0% | 56.7% / 41.7% |

---

## Dataset Breakdown

```
VPI-Bench Dataset Distribution (306 Total Samples):
├── By Execution Environment:
│   ├── Computer-Use (Docker Ubuntu / Local Filesystem): 219 samples (71.6%)
│   └── Browser-Use (Google Drive Workspace):             87 samples (28.4%)
├── By Target Platform:
│   ├── Amazon:        79 samples (25.8%)
│   ├── Booking.com:   79 samples (25.8%)
│   ├── BBC News:      79 samples (25.8%)
│   ├── Email:         46 samples (15.0%)
│   └── Messenger:     23 samples (7.5%)
└── By Malicious Objective:
    ├── Unauthorized Actions Only (UA):                   75 samples (24.5%)
    ├── Privacy Leakage Only (PL):                        63 samples (20.6%)
    └── Combined (UA + PL):                              168 samples (54.9%)
```

---

## Official Resources

- **arXiv Preprint:** [arXiv:2506.02456v2](https://arxiv.org/abs/2506.02456v2)
- **Agent Framework Code:** [https://github.com/cua-framework/agents](https://github.com/cua-framework/agents)
- **Web Platform Code:** [https://github.com/cua-framework/web](https://github.com/cua-framework/web)
- **HuggingFace Dataset:** [https://huggingface.co/datasets/VPI-Bench/vpi-bench](https://huggingface.co/datasets/VPI-Bench/vpi-bench)

---

## Citation

```bibtex
@article{cao2025vpibench,
  title={VPI-Bench: Visual Prompt Injection Attacks for Computer-Use Agents},
  author={Cao, Tri and Lim, Bennett and Liu, Yue and Sui, Yuan and Li, Yuexin and Deng, Shumin and Lu, Lin and Oo, Nay and Yan, Shuicheng and Hooi, Bryan},
  journal={arXiv preprint arXiv:2506.02456},
  year={2025}
}
```

---

## License

This documentation and analysis repository is distributed under the [MIT License](LICENSE).
