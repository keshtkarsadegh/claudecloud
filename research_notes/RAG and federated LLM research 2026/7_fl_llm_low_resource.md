# Federated LLM Training and Inference on Low-Resource and Resource-Heterogeneous Devices (2024–2026)

**Method and reliability notes (read first).**
- Direct page fetching from arxiv.org was **blocked** in this environment (DNS failure on `arxiv.org`). The session's shared web-search budget also ran out after 11 searches. Findings below come from search-result snippets and the summaries the search engine returned, not from full-text reads. Numbers are the authors' own claims as surfaced in those snippets, and they were not checked against the PDFs.
- Items marked **[PK]** come from the researcher's prior knowledge of well-known papers (cutoff mid-2026). Their arXiv IDs are given as I remember them but were **not re-verified in this session**. The report writer should treat [PK] items as lower-confidence and check them before final citation.
- Scope: this file covers the resource-constraint angle. Device–edge–cloud split, wireless scheduling and network architecture belong to another researcher and appear only in passing (CooperLLM, MobiLLM, wireless KD).

---

## 1. Memory-efficient federated fine-tuning (QLoRA-in-FL, zeroth-order / forward-only, layer freezing / partial training, sparse sub-models, offloading)

### Takeaway
Two families dominate. (a) **Backprop-free (zeroth-order/forward-only) federated tuning** (FwdLLM, FedKSeed, FedMeZO, FedSPZO, Meerkat, ZorBA, CooperLLM) brings client memory down to roughly inference level and communication down to KBs, at the cost of many forward passes and an accuracy gap versus first-order LoRA. (b) **First-order PEFT with compression/offloading** (federated QLoRA variants such as HAFLQ and FedQuad, layer-allocation methods FedRA/FedBiOT/offsite-tuning, and server-assisted side-tuning such as MobiLLM and Fed MobiLLM) keeps first-order accuracy but needs quantized backbones, activation reduction, or a helper server. As of late 2026 no method dominates on all of accuracy, memory, energy and latency on truly low-end hardware.

### Cited Findings

**Zeroth-order / forward-only (BP-free)**
- **FwdLLM: Efficient Federated Finetuning of LLMs with Perturbed Inferences.** Mengwei Xu et al. (BUPT and collaborators), **USENIX ATC 2024**. Core idea: BP-free training ("perturbed inferences") combined with PEFT, with perturbation and compute loads allocated across devices so it suits mobile NPUs that can only run inference. Authors report up to 3 orders of magnitude faster convergence than prior BP-free baselines and a **4.6× reduction in memory footprint**. — [USENIX ATC'24 page](https://www.usenix.org/conference/atc24/presentation/xu-mengwei); [PDF](https://www.usenix.org/system/files/atc24-xu-mengwei.pdf)
- **FwdLLM+: Accelerating Forward-Only FedLLM with Low-Rank Perturbations**, IEEE TMC 2026 (journal extension). Reports up to **151× faster training** and a **93% memory reduction** versus vanilla BP-based FedLLM. — [IEEE CSDL](https://www.computer.org/csdl/journal/tm/2026/08/11429542/2eOD933hNSw)
- **FedKSeed: Federated Full-Parameter Tuning of Billion-Sized LMs with Communication Cost under 18 Kilobytes.** Zhen Qin et al. (Zhejiang Univ. / Alibaba), **ICML 2024 (PMLR v235)**. Core idea: zeroth-order optimisation over a finite pool of K random seeds. Clients exchange only (seed, scalar-gradient) pairs, so full-parameter tuning costs **<18 KB per round** and memory equals inference. The paper contrasts itself with FwdLLM, which it says can need several hundred GB of communication for a ~300M model. That figure is a competitor's claim. Code: FederatedScope (see the paper). — [PMLR](https://proceedings.mlr.press/v235/qin24a.html); [arXiv 2312.06353](https://arxiv.org/html/2312.06353v2); [ICML poster](https://icml.cc/virtual/2024/poster/33581)
- **FedMeZO: On the Convergence of Zeroth-Order Federated Tuning for LLMs.** Zhenqing Ling et al., arXiv 2402.05926 (KDD 2024 per [PK]). Applies MeZO in FL, gives convergence theory, and reports faster convergence than FedAvg while cutting GPU memory to near inference level. — [arXiv 2402.05926](https://arxiv.org/abs/2402.05926v3)
- **FedSPZO: Efficient Zeroth-Order Federated Finetuning of LMs on Resource-Constrained Devices**, arXiv 2502.10239 (Feb 2025). Split-perturbation ZO with different numbers of perturbations for different network blocks. Reports up to **3× lower computation** than other ZO-FL methods on RoBERTa-large, OPT-1.3B and **LLaMA-3.2-3B**. Positioned as a LoRA alternative for memory-tight, bandwidth-limited IoT clients. Authors concede it still trails first-order LoRA in accuracy. — [arXiv 2502.10239](https://arxiv.org/abs/2502.10239)
- **Meerkat: Mitigating Non-IID Drift in ZO Federated LLM Fine-Tuning with Transferable Sparsity**, arXiv 2506.03337 (Jun 2025). Sparse ZO that updates only a transferable, very sparse parameter subset, which helps with non-IID drift and lowers communication. — [arXiv 2506.03337](https://arxiv.org/html/2506.03337v1)
- **ZorBA: ZO Federated Fine-tuning of LLMs with Heterogeneous Block Activation**, arXiv 2603.04436 (2026). Combines ZO with per-client heterogeneous block activation for heterogeneous devices. Only the reference list surfaced in search, so no results are verified. — [arXiv 2603.04436](https://arxiv.org/pdf/2603.04436)
- **CooperLLM: Cloud–Edge–End Cooperative Federated Fine-tuning for LLMs via ZOO-based Gradient Correction**, arXiv 2601.12917 (Jan 2026). Mobile devices run ZO locally and the cloud corrects the ZO updates with gradient knowledge from cloud-side backprop. It targets the slow convergence and accuracy gap of pure ZO. It belongs to the edge–cloud architecture area and is mentioned here only briefly. — [arXiv 2601.12917](https://arxiv.org/html/2601.12917)
- **Prada: Black-Box LLM Adaptation with Private Data on Resource-Constrained Devices**, arXiv 2503.14932 (2025). Adapts a black-box LLM using private on-device data with a small local proxy. Related to the proxy-model approaches below. — [arXiv 2503.14932](https://arxiv.org/pdf/2503.14932)
- **Trade-off from a competing paper:** forward-only methods bring activation memory to inference level but **need multiple forward passes per update, which raises on-device compute**. — [Fed MobiLLM, arXiv 2508.06765](https://arxiv.org/pdf/2508.06765)

**Quantized LoRA (QLoRA-style) in FL**
- **HAFLQ: Heterogeneous Adaptive Federated LoRA Fine-tuned LLM with Quantization**, arXiv 2411.06581 (rev. May 2025). Salience-driven block-wise adaptive quantization of the frozen backbone, with heterogeneous rank and adaptive aggregation. Reports a **31% memory reduction** on text classification. The authors limit generalisability to that task. — [arXiv 2411.06581](https://arxiv.org/html/2411.06581v2)
- **FedQuad**, arXiv 2506.01001 (Jun 2025). Adapts LoRA depth per device and **quantizes activations** to cut activation memory, which on small devices often dominates over weight memory. — [arXiv 2506.01001](https://export.arxiv.org/abs/2506.01001)
- **Quantized LoRA for model and data privacy** (Zhu et al.), arXiv 2406.10976. The server ships quantized LoRA and backbone to clients, which lowers download cost and protects server IP. The focus is privacy rather than client memory. — [arXiv 2406.10976](https://arxiv.org/html/2406.10976v1)
- **Flower FlowerTune practical recipe:** Qwen2.5-7B-Instruct with **4-bit quantization + LoRA rank 8 + FedProx** runs in simulation on a single GPU. This shows 4-bit QLoRA as the de facto baseline in federated LLM leaderboards. — [Flower app](https://flower.ai/apps/mrs83/flowertune_qwen2.5-7b-instruct-medical)
- Background: **QLoRA** (NF4 4-bit backbone + LoRA) fine-tunes a 65B model in under 48 GB. — [cited in arXiv 2408.13010](https://arxiv.org/pdf/2408.13010); original QLoRA [PK]: [arXiv 2305.14314](https://arxiv.org/abs/2305.14314)

**Layer allocation, partial training, emulators/offsite-tuning**
- **FedRA: A Random Allocation Strategy for Federated Tuning to Unleash the Power of Heterogeneous Clients**, **ECCV 2024**, arXiv 2311.11227. Each round, each client gets a random subset of layers (with adapters) sized to its capacity. It works even when **no client can hold the full model**, and every layer still learns from many clients, unlike fixed depth-pruning. — [arXiv 2311.11227](https://arxiv.org/html/2311.11227); [ECCV PDF](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/06476.pdf)
- **FedBiOT: LLM Local Fine-tuning in FL without Full Model**, **KDD 2024** (Alibaba FederatedScope team). The server compresses and prunes the LLM into an "emulator" sized to client resources. Clients train only lightweight adapters on the emulator and never receive the full model, which protects IP and cuts memory. — [ResearchGate](https://www.researchgate.net/publication/383419623_FedBiOT_LLM_Local_Fine-tuning_in_Federated_Learning_without_Full_Model); [OpenReview](https://openreview.net/pdf?id=i5da6iedW8)
- **Offsite-tuning family (FedOT, FATE-LLM):** the model is split into a trainable adapter and a frozen, layer-dropped emulator. — [Model-accessibility survey, arXiv 2508.16261](https://arxiv.org/pdf/2508.16261)
- **Caveat from FedProxy (arXiv 2604.19015, 2026):** offsite-tuning-based methods **consistently underperform centralized fine-tuning**. FedProxy instead uses proxy SLMs with heterogeneity-aware fusion. — [arXiv 2604.19015](https://arxiv.org/pdf/2604.19015)

**Offloading / side-tuning (server-assisted)**
- **MobiLLM: Enabling LLM Fine-Tuning on the Mobile Device via Server-Assisted Side Tuning**, arXiv 2502.20421 (ACM DL 10.1145/3737902.3768351). The phone keeps the frozen backbone (forward only) and offloads backprop of a trainable **side network** to a server. Reports **4.487 GB** device memory for OPT-1.3B. This is single-device, not federated. — [arXiv 2502.20421](https://arxiv.org/abs/2502.20421); [ACM](https://dl.acm.org/doi/10.1145/3737902.3768351)
- **Fed MobiLLM**, arXiv 2508.06765 (2025). The federated version for heterogeneous mobile devices: clients do forward passes only, and side-networks are trained and aggregated at the server. — [arXiv 2508.06765](https://arxiv.org/pdf/2508.06765)
- **PAE MobiLLM**, arXiv 2507.01216. Privacy-aware additive side-tuning: data and labels stay local and heavy operations go to an edge server. — [arXiv 2507.01216](https://arxiv.org/html/2507.01216)

### Inferences
- The memory floor is set by the **frozen backbone weights**, and on low-end phones also by **activations/KV**. ZO and side-tuning remove activation and optimizer memory but not weight memory. Running FL fine-tuning on 2–4 GB devices therefore likely needs ZO or side-tuning **combined with** 4-bit or lower backbones (HAFLQ/FedQuad direction).
- The ZO literature has moved from "does it converge" (FedMeZO, 2024) to fixing **sample efficiency and non-IID drift** (FedSPZO, Meerkat, ZorBA) and to **hybrid ZO + cloud first-order correction** (CooperLLM). This suggests pure on-device ZO is still too slow or inaccurate to stand alone.
- The emulator/offsite family trades accuracy for IP protection and memory. The FedProxy finding suggests the field is moving toward proxy-SLM approaches.

### Gaps
- FwdLLM's absolute memory numbers (GB per model) were not retrievable. Only the 4.6× ratio was.
- No found paper reports wall-clock ZO-FL training **on a physical low-end phone or Pi** with energy (J/round) measured. Most ZO-FL results are GPU simulations.
- **Ferret** (first-order federated full-parameter tuning with shared randomness, [PK] arXiv 2409.06277) and **Photon** (federated LLM pre-training, [PK] arXiv 2411.02908, MLSys 2025) were not verified in this session.
- I found no strong 2025–2026 evidence on **2-bit/ternary backbones inside FL fine-tuning**.

---

## 2. Resource-heterogeneous FL for LLMs (heterogeneous LoRA ranks, width/depth sub-models, large→small KD, federated SLMs)

### Takeaway
Heterogeneous-rank LoRA is now its own sub-field with a known taxonomy. **Zero-pad + truncate** (HetLoRA) is biased. **SVD redistribution of the full product** (FlexLoRA) is accurate but costs server compute. **Stacking** (FLoRA) gives exact aggregation, but rank and communication grow with the number of clients. **Sketching / product-space / dimension-wise** schemes (FSLoRA, FedPA-LoRA, FediLoRA, HeLoRA) are 2025–2026 refinements. Separately, **LLM↔SLM mutual distillation** (FedMKT, FedCoLLM, FedProxy, FLoKD) lets weak clients hold only an SLM.

### Cited Findings
**Heterogeneous LoRA ranks**
- **HetLoRA** (Cho et al., Google; *Heterogeneous LoRA for Federated Fine-tuning of On-Device Foundation Models*, EMNLP 2024 per [PK], arXiv 2401.06432 [PK]). Clients use different ranks, zero-padded to the max rank for aggregation and then truncated. Uses **sparsity-weighted aggregation** and self-pruning of local ranks. — [ResearchGate](https://www.researchgate.net/publication/386195300_Heterogeneous_LoRA_for_Federated_Fine-tuning_of_On-Device_Foundation_Models); mechanism summarised in [FedPA-LoRA arXiv 2608.15381](https://arxiv.org/pdf/2608.15381)
- **FlexLoRA** (Bai et al., NeurIPS 2024 per [PK], arXiv 2402.11505 [PK]). Aggregates full-size products ΔW=BA, then uses **SVD** to redistribute rank-specific factors to each client. — mechanism per [FedPA-LoRA arXiv 2608.15381](https://arxiv.org/pdf/2608.15381) and [FSLoRA arXiv 2501.19389](https://arxiv.org/html/2501.19389v4)
- **FLoRA** (Wang et al., NeurIPS 2024 per [PK], arXiv 2409.05976 [PK]). **Stacks** client factors for noise-free exact aggregation. The global rank grows with the sum of client ranks, which raises communication and adapter size. — [FedPA-LoRA](https://arxiv.org/pdf/2608.15381)
- **FedSA-LoRA** (ICLR 2025 per [PK]): aggregates only **A** globally and keeps **B** local, since A captures general knowledge and B is client-specific. — [FedPA-LoRA](https://arxiv.org/pdf/2608.15381)
- **FSLoRA: Federated Sketching LoRA**, arXiv 2501.19389. Server-side global LoRA; clients update **sketched sub-matrices** whose size they choose by capacity. Head-to-head reported averages: **FlexLoRA 77.90 vs FLoRA 72.81** (normal rank distribution) and **75.59 vs 73.43** (heavy-tailed). — [arXiv 2501.19389](https://arxiv.org/html/2501.19389v4)
- **HeLoRA: LoRA-heterogeneous Federated Fine-tuning for Foundation Models**, ACM TOIT 2025 (DOI 10.1145/3723877). — [ACM DL](https://dl.acm.org/doi/10.1145/3723877)
- **FediLoRA**, arXiv 2509.06984 (2025). Dimension-wise reweighting for heterogeneous ranks plus missing modalities in multimodal FL, avoiding "information dilution" during aggregation. — [arXiv 2509.06984](https://arxiv.org/pdf/2509.06984)
- **FedPA-LoRA**, arXiv 2608.15381 (Aug 2026). Aggregates in a shared **product space**, keeps client-specific factors, and **decouples local compute rank from communication rank**. — [arXiv 2608.15381](https://arxiv.org/pdf/2608.15381)
- **Surveys:** *Federated Low-Rank Adaptation for Foundation Models: A Survey*, **IJCAI 2025** — [IJCAI](https://www.ijcai.org/proceedings/2025/1196.pdf); *A Survey on Federated Fine-tuning of LLMs*, arXiv 2503.12016, which groups heterogeneity as cross-client versus intra-model/layer — [arXiv 2503.12016](https://arxiv.org/pdf/2503.12016); *Federated LLMs: Current Progress and Future Directions*, arXiv 2409.15723 — [arXiv](https://arxiv.org/pdf/2409.15723); curated list **awesome-fm-fl** — [GitHub](https://github.com/lishenghui/awesome-fm-fl)

**Width/depth sub-models**
- FedRA (random layer allocation, ECCV 2024) and FedBiOT (pruned emulators per client, KDD 2024) are the main depth/width-scaled designs (see §1). FedQuad adapts **LoRA depth** per device. ZorBA activates **heterogeneous blocks** per client in ZO. — [FedRA](https://arxiv.org/html/2311.11227); [FedQuad](https://export.arxiv.org/abs/2506.01001); [ZorBA](https://arxiv.org/pdf/2603.04436)

**Large→small knowledge distillation / federated SLMs**
- **FedMKT: Federated Mutual Knowledge Transfer for Large and Small Language Models**, Tao Fan et al. (WeBank/HKUST, FATE team), **COLING 2025**, arXiv 2406.02224. Server LLM and client SLMs (possibly heterogeneous architectures and tokenizers) exchange knowledge through **minimum-edit-distance token alignment** and **selective mutual distillation** on a public dataset. Both LLM and SLMs improve. — [ACL Anthology](https://aclanthology.org/2025.coling-main.17/); [arXiv 2406.02224](https://arxiv.org/abs/2406.02224)
- **FedCoLLM: A Parameter-Efficient Federated Co-tuning Framework for Large and Small Language Models** (Fan et al., 2024). Clients tune LoRA on SLMs, and the server performs mutual KD between the aggregated SLM and the LLM on auxiliary data. The selling point is client compute efficiency. — [ResearchGate](https://www.researchgate.net/publication/385920407_FedCoLLM_A_Parameter-Efficient_Federated_Co-tuning_Framework_for_Large_and_Small_Language_Models)
- **FedProxy**, arXiv 2604.19015 (Apr 2026). Clients fine-tune **proxy SLMs**, with heterogeneity-aware fusion into the LLM. Reports beating offsite-tuning baselines. — [arXiv 2604.19015](https://arxiv.org/pdf/2604.19015)
- **FLoKD: Adaptive KD for Federated Low-Rank LLM over Wireless Networks**, arXiv 2609.13580 (Sep 2026). Uses FedMKT as a baseline and criticises its unselective use of public data. — [arXiv 2609.13580](https://arxiv.org/html/2609.13580)
- **Communication-aware KD for federated LLM fine-tuning over wireless**, arXiv 2509.01750. Logit-based federated KD with roughly **50% lower inter-client communication** (wireless-side detail is out of scope). — [arXiv 2509.01750](https://arxiv.org/html/2509.01750v2)

### Inferences
- For truly low-end clients (≤4 GB RAM), the realistic heterogeneous-FL design is **SLM-on-device + LLM-on-server with mutual KD** (FedMKT/FedCoLLM/FedProxy), not LoRA on the shared LLM. Rank heterogeneity only helps clients that can already hold the backbone.
- Rank-heterogeneous aggregation is converging on **product-space (ΔW) aggregation with decoupled communication rank** (FlexLoRA → FSLoRA → FedPA-LoRA). This also suits bandwidth-limited clients.

### Gaps
- No standard benchmark compares HetLoRA/FlexLoRA/FLoRA/FSLoRA/FedPA-LoRA under *measured device memory* rather than nominal rank. FSLoRA's numbers are its own experiments.
- I found no verified results for federated **pre-training of SLMs** on low-end hardware (Photon [PK] targets GPU clusters).
- Tokenizer and architecture mismatch between client SLMs (e.g., SmolLM vs Qwen vs Gemma) is handled only by FedMKT-style token alignment. I found no systematic study.

---

## 3. Compression for inference on low-resource devices (4/2/1-bit quantization, pruning, distillation, tiny LLMs, MCU/TinyML inference)

### Takeaway
On-device inference for 0.1–3B models is mature on phones, Pi and Jetson (4-bit weight-only quantization plus llama.cpp, MLC, NPU stacks). **Native 1.58-bit models (BitNet b1.58 2B4T + bitnet.cpp)** are the most significant 2025 step for CPU-only, energy-limited devices. **True MCU-class LLM inference** is still mostly limited to BERT-scale or ~10–30M-parameter "micro LMs".

### Cited Findings
- **BitNet b1.58 2B4T** (Microsoft Research, technical report 2025, [PK] arXiv 2504.12285): a natively trained ternary-weight 2B model trained on 4T tokens, with open weights and GPU/CPU inference code. The report claims a much lower memory footprint, energy use and decoding latency than comparable full-precision models. — [HF papers search](https://www.huggingface.co/papers?q=BitNet). Third-party comparison table (**not verified against the paper**): **0.4 GB** non-embedding memory, **29 ms/token** CPU decode, **~0.028 J/inference (estimated)**, versus LLaMA-3.2-1B at 2 GB/48 ms/0.258 J and Qwen2.5-1.5B at 2.6 GB/65 ms/0.347 J. Average benchmark score ~54.2 versus 55.2 for Qwen2.5-1.5B. — [HF Space bitnet-cpp-explorer](https://huggingface.co/spaces/knoxel/bitnet-cpp-explorer/blob/main/app.py); [emelia.io blog](https://emelia.io/hub/bitnet-1bit-llm-cpu-inference)
- **bitnet.cpp** kernels (third-party summary): **1.37–5.07× speedup and 55.4–70.0% energy reduction on ARM** (Apple M-series, Raspberry Pi), and **2.37–6.17× speedup with 71.9–82.2% energy reduction on x86**. Microsoft reportedly advises against commercial use without further testing. — [emelia.io](https://emelia.io/hub/bitnet-1bit-llm-cpu-inference). Original bitnet.cpp paper [PK]: "1-bit AI Infra", arXiv 2410.16144.
- **On-Device Qwen2.5: Efficient LLM Inference with Model Compression and Hardware Acceleration**, arXiv 2504.17376 (2025). AWQ-style weight compression plus an FPGA accelerator for Qwen2.5 on an edge SoC. — [arXiv 2504.17376](https://arxiv.org/pdf/2504.17376)
- **T-MAN**, arXiv 2511.11248: end-to-end **low-bit LLM inference on NPUs via unified table lookup**. — [arXiv 2511.11248](https://arxiv.org/pdf/2511.11248). **ShadowNPU**, arXiv 2508.16703: NPU-centric on-device LLM inference co-design. — [arXiv 2508.16703](https://arxiv.org/pdf/2508.16703). **TierKV**, arXiv 2609.21172: long-context on-device LLMs via multi-tier KV caching, aimed at KV memory on phones. — [arXiv 2609.21172](https://arxiv.org/pdf/2609.21172)
- **MCU-class:** **MCUBERT: Memory-Efficient BERT Inference on Commodity Microcontrollers**, arXiv 2410.17957 (2024) is BERT-scale on Cortex-M. Numbers were not retrieved. — [arXiv 2410.17957](https://arxiv.org/pdf/2410.17957). **Micro Language Models Enable Instant Responses**, arXiv 2604.19642 (2026): **8M–30M-parameter** micro-LMs produce the first 4–8 words on-device while the cloud completes the reply. The 28M model on **Orange Pi** gives **45 ms TTFT** and 4 words in 55 ms. — [arXiv 2604.19642](https://arxiv.org/html/2604.19642v1)
- **FPGA tiny-LLM data points:** HLSTransform reaches 57.11 tok/s on a 110M model (VU9P FPGA), and **TinyLlama-1.1B runs at 0.588 tok/s on a PYNQ-Z1** board. — [Hardware-perspective survey arXiv 2410.04466](https://arxiv.org/html/2410.04466v3)
- **Energy of SLMs on edge boards:** *Characterizing and Understanding Energy Footprint and Efficiency of SLMs on Edges*, arXiv 2511.11624. **Jetson Orin Nano (GPU) gave the lowest energy per inference** among the edge platforms tested, which included Pi-class CPUs. — [arXiv 2511.11624](https://arxiv.org/html/2511.11624)
- **Tiny/small open models** commonly used as FL clients or on-device students ([PK], not re-verified this session): TinyLlama-1.1B ([arXiv 2401.02385](https://arxiv.org/abs/2401.02385)), MobileLLM 125M/350M (Meta, ICML 2024, [arXiv 2402.14905](https://arxiv.org/abs/2402.14905)), Phi-3-mini 3.8B ([arXiv 2404.14219](https://arxiv.org/abs/2404.14219)), SmolLM2 135M/360M/1.7B ([arXiv 2502.02737](https://arxiv.org/abs/2502.02737)), Qwen2.5 0.5B/1.5B ([arXiv 2412.15115](https://arxiv.org/abs/2412.15115)), Gemma 3 1B ([arXiv 2503.19786](https://arxiv.org/abs/2503.19786)). Key compression tools [PK]: AWQ (MLSys 2024, [arXiv 2306.00978](https://arxiv.org/abs/2306.00978)), llama.cpp GGUF k-quants ([GitHub](https://github.com/ggml-org/llama.cpp)), BitNet ([GitHub](https://github.com/microsoft/BitNet)).

### Inferences
- In federated settings, the **client model used for local inference/personalization will be an SLM (≤2B) at 4-bit or native ternary**. The FL design question is then how adapters trained on such a quantized backbone aggregate. Few FL papers train against ternary or 2-bit backbones (gap below).
- Native low-bit models (BitNet) shift the bottleneck to **activations and KV cache**, which is why work like TierKV and activation quantization (FedQuad) matters for low-RAM devices.

### Gaps
- Searches for 2-bit on-device benchmarks (Pi/phone, llama.cpp, SmolLM/Qwen) could not run because the search budget was exhausted. No verified 2-bit numbers are included.
- The BitNet 2B4T memory/latency/energy figures above come from secondary sources. The primary technical report could not be fetched.
- No verified example was found of **LLM (≥100M) autoregressive generation on a Cortex-M-class MCU**. MCU work remains BERT-scale or micro-LM on application-class SBCs.

---

## 4. Low-bandwidth communication, energy-aware FL and real-device measurements

### Takeaway
Communication can already be reduced to KBs per round (FedKSeed seed-sharing; sparse ZO such as Meerkat; LoRA-only exchange; logit KD). **Energy is the least-studied axis.** Physical-power measurements exist for classic FL on Raspberry Pi and for SLM *inference* on Jetson/Pi, but I found **no peer-reviewed study measuring wall-plug energy of federated LLM fine-tuning on Pi, Jetson, phones or MCUs**.

### Cited Findings
- **FedKSeed: <18 KB/round** for full-parameter tuning of billion-scale LLMs. — [PMLR](https://proceedings.mlr.press/v235/qin24a.html)
- **FedSPZO** explicitly targets bandwidth-limited IoT clients over wireless links. — [arXiv 2502.10239](https://arxiv.org/abs/2502.10239)
- **Logit-based KD** cuts inter-client communication by ~50%. — [arXiv 2509.01750](https://arxiv.org/html/2509.01750v2)
- **FL on Raspberry Pi 4: A Comprehensive Power Consumption Analysis** (ACM UCC 2023, DOI 10.1145/3603166.3632545). INA219 measurements show **communication raised power 8–32% above idle**, and CNN training averaged **~3.3 W (~32% above idle)**. The models are CNN/RNN, not LLMs. — [ACM DOI](https://doi.org/10.1145/3603166.3632545)
- **EcoFL: A System-Level Energy-Aware Benchmark for IoT Edge Intelligence** (JLPEA 2026, DOI 10.3390/jlpea16030024). Energy is **estimated with a TDP-based model** under emulated Pi 4 and Jetson Nano profiles. The authors state that hardware power-monitor validation is an essential next step. — [MDPI DOI](https://doi.org/10.3390/jlpea16030024)
- **SLM energy on edge** (arXiv 2511.11624): Jetson Orin Nano had the lowest J/inference. — [arXiv 2511.11624](https://arxiv.org/html/2511.11624)
- **Energy-aware DL on resource-constrained hardware survey (2025):** MLPerf Power covers the Jetson family but has **limited coverage of MCUs and ultra-low-power devices**. — [PDF](https://anil.recoil.org/papers/2025-dl-rcn.pdf)
- **Testbeds:** *Where is the Testbed for my Federated Learning Research?* (arXiv 2407.14154) surveys physical FL testbeds, including heterogeneous edge boards. — [arXiv 2407.14154](https://arxiv.org/pdf/2407.14154)
- **RISC-V / micro-electronics:** *Deploying Large AI Models on Micro-electronics with RISC-V: FL for Energy Monitoring and Robotics* (Springer 2025) targets Pi/Jetson-Nano-class constraints. Only the abstract was seen. — [Springer](https://link.springer.com/chapter/10.1007/978-981-96-6932-5_31)
- **Real-device LLM fine-tuning memory:** MobiLLM measures **4.487 GB** on-device for OPT-1.3B side-tuning. — [arXiv 2502.20421](https://arxiv.org/abs/2502.20421). FwdLLM evaluated on real mobile SoCs/NPUs (per its ATC paper). — [USENIX](https://www.usenix.org/conference/atc24/presentation/xu-mengwei)

### Inferences
- Because ZO needs many forward passes, **communication savings may be offset by an on-device compute and energy increase**. No paper found quantifies the net J per accuracy point against LoRA-FL on the same hardware. This is a key evaluation gap.
- The communication-reduction literature is ahead of energy-measurement work. Claims of feasibility on "low-resource devices" usually rest on GPU simulations with memory accounting.

### Gaps
- No measured J/round or battery-drain data for federated LLM fine-tuning on phones, Pi or Jetson was found.
- I found nothing addressing **intermittent connectivity / low-connectivity regions** specifically for FedLLM (asynchronous, delay-tolerant FedLLM). The search budget ran out before this could be checked.
- Low-communication distributed-training methods such as DiLoCo / Streaming DiLoCo ([PK] arXiv 2311.08105, 2501.18512) were not verified this session.

---

## 5. Open problems

### Takeaway
The main open issues are: (1) closing the ZO-vs-first-order accuracy and time-to-accuracy gap on real low-end hardware; (2) aggregation when backbones are quantized *differently* per client (4/2/1.58-bit) and ranks differ; (3) activation/KV memory as the binding constraint; (4) honest energy accounting on physical devices; (5) tokenizer- and architecture-heterogeneous SLM federations; (6) robustness of these schemes under non-IID data, stragglers and intermittent links.

### Cited Findings
- FedSPZO's authors admit ZO still **trails first-order LoRA** in accuracy. — [arXiv 2502.10239](https://arxiv.org/abs/2502.10239)
- Forward-only methods need **multiple forward passes per update** (compute/energy overhead). — [arXiv 2508.06765](https://arxiv.org/pdf/2508.06765)
- Offsite/emulator methods **underperform centralized fine-tuning**. — [arXiv 2604.19015](https://arxiv.org/pdf/2604.19015)
- Non-IID drift is a specific failure mode of ZO-FL. — [Meerkat arXiv 2506.03337](https://arxiv.org/html/2506.03337v1)
- Rank-heterogeneous aggregation still trades bias (HetLoRA zero-padding) against communication growth (FLoRA stacking) against server SVD cost (FlexLoRA), and FedPA-LoRA proposes decoupling. — [arXiv 2608.15381](https://arxiv.org/pdf/2608.15381)
- HAFLQ's memory gains are reported **only on text classification**. — [arXiv 2411.06581](https://arxiv.org/html/2411.06581v2)
- Energy benchmarks for FL rely on **TDP emulation** rather than hardware monitors. — [EcoFL](https://doi.org/10.3390/jlpea16030024); MCU coverage in power benchmarks is limited. — [survey PDF](https://anil.recoil.org/papers/2025-dl-rcn.pdf)
- FedMKT depends on a shared public dataset without judging sample relevance. — [FLoKD arXiv 2609.13580](https://arxiv.org/html/2609.13580)

### Inferences
- A promising, under-explored combination: **native-ternary (BitNet-style) SLM client + ZO or side-tuned adapters + seed-based communication (FedKSeed) + SLM↔LLM distillation at the server**. Each piece exists separately, but no paper found combines them on physical low-end hardware.
- Rigorous benchmarks should report **peak RSS, J/round, rounds-to-target, and bytes/round on physical devices** (Pi 5, Jetson Orin Nano, mid/low-tier Android). Current papers mostly report GPU-simulated memory.

### Gaps
- Not covered because of the search budget: privacy/robustness trade-offs specific to ZO-FL (e.g., DP + ZO), federated RLHF/DPO on low-resource clients, and federated multimodal SLMs on edge boards beyond FediLoRA.
- Code repositories were not verified for most papers. Known ones: FederatedScope (FedKSeed, FedBiOT), FATE-LLM (FedMKT, FedCoLLM), Flower FlowerTune (see the links above), and microsoft/BitNet. The rest should be checked by the writer.
