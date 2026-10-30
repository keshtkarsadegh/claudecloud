# Federated LLM Training and Inference on Edge Devices (2024–2026)

> Method note: this research used web-search snippets plus abstracts and HTML pages as the search tool reported them. Page fetching was partly blocked. `www.research.google` failed DNS resolution in this session, so it would have to be added to the session's allowed network domains. The session's shared web-search budget ran out after about 16 queries, so some sub-areas are thin and are listed under Gaps. All numbers below are **self-reported by the paper authors** unless noted. I found no independent replications. Where a link comes from prior knowledge and was not checked in this session, it is marked **[unverified link]**.

## 1. On-device federated fine-tuning of LLMs/SLMs and split (federated) learning between device and edge server

### Takeaway
The field has split into three families for making federated LLM fine-tuning fit on phones and edge boards:
- **Backprop-free / forward-only** methods, such as FwdLLM.
- **Server-assisted or split** designs, such as SplitLoRA-style split FL, Fed MobiLLM side-tuning, and Just Talk Once. These offload most activations or trainable parameters to an edge server.
- **Resource-aware heterogeneous-LoRA** designs, such as FedPipe, HierFedLoRA, HetLoRA, FLoRA and FSLoRA.

Real-phone testbeds are still rare. Most 2025 testbeds use Jetson boards, and only a few use actual smartphones (for example, Huawei nova 9 Pro in Just Talk Once).

### Cited Findings

**Forward-gradient / backprop-free**
- **FwdLLM: Efficient Federated Finetuning of LLMs with Perturbed Inferences.** Mengwei Xu et al. (BUPT), USENIX ATC 2024; arXiv 2308.13894.
  - Core idea: replace backpropagation with "perturbed inference" (forward gradients), so each device's gradient estimate costs about two inferences and stores no intermediate activations.
  - Combined with LoRA/Adapters so that cost scales with the trainable parameters rather than the total parameters.
  - Can run on mobile NPUs, which it reports as 19.5–27.5× faster than computing a backward gradient on CPU.
  - Claims to open the way to FL of billion-parameter LLMs such as LLaMA on COTS phones.
  - Sources: [USENIX ATC'24](https://www.usenix.org/conference/atc24/presentation/xu-mengwei); [arXiv 2308.13894](https://arxiv.org/pdf/2308.13894); [PDF](https://usenix.org/system/files/atc24-xu-mengwei.pdf)

**Server-assisted / side-tuning**
- **Fed MobiLLM: Federated LLM fine-tuning over heterogeneous mobile devices via server-assisted side-tuning.** arXiv 2508.06765, Aug 2025.
  - Trainable parameters sit in a server-side side-network. Devices run only forward passes through a frozen backbone and upload selected intermediate activations, so no device backprop is needed.
  - The paper positions FwdLLM-style forward-gradient methods as the alternative it improves on.
  - Testbed: Jetson TX2, Xavier NX and AGX Xavier (not phones). Models: OPT-125M/350M/1.3B and RoBERTa.
  - Sources: [arXiv 2508.06765](https://arxiv.org/pdf/2508.06765); [HTML](https://arxiv.org/html/2508.06765v1); testbed details per [search summary of the same paper](https://arxiv.org/html/2508.06765v1)

**Split federated learning (SFL) for LLMs**
- **Memory-Efficient Split Federated Learning for LLM Fine-Tuning on Heterogeneous Mobile Devices.** arXiv 2506.02940, Jun 2025.
  - Devices fine-tune LoRA adapters on a subset of lower layers. The server holds the full model and fine-tunes the matching adapters one device at a time (sequentially).
  - Reports a 79% memory reduction and a 6% training-time reduction versus baselines.
  - Caveat: a third-party review says the 79% server-memory figure comes from BERT-base experiments.
  - Sources: [arXiv 2506.02940](https://arxiv.org/pdf/2506.02940); [review](https://www.themoonlight.io/tw/review/memory-efficient-split-federated-learning-for-llm-fine-tuning-on-heterogeneous-mobile-devices)
- **FedsLLM: Efficient Federated Split Learning for LLMs over Communication Networks.** arXiv 2504.14667, Apr 2025.
  - Combines split learning with LoRA.
  - Jointly optimizes subchannel allocation, power control, split point and LoRA rank to minimize training delay.
  - Reports comparable accuracy with much lower client compute.
  - Source: [arXiv 2504.14667](https://arxiv.org/abs/2504.14667v2)
- **SFLAM: Deploying Large AI Models on Resource-Limited Devices with Split Federated Learning.** arXiv 2504.09114.
  - Quantized split federated fine-tuning: embedding on the device, attention and output layers on the server.
  - Experiments are mostly on ViTs, so this is weaker LLM evidence.
  - Source: [arXiv 2504.09114](https://arxiv.org/html/2504.09114v1)
- **Split Fine-Tuning for LLMs in Wireless Networks.** arXiv 2501.09237, Jan 2025.
  - Splits the LLM between mobile devices and an edge server to reduce memory, communication and fine-tuning delay.
  - Only a secondary summary was seen.
  - Source: [review](https://themoonlight.io/review/split-fine-tuning-for-large-language-models-in-wireless-networks)
- **Just Talk Once: Communication-Efficient Split Federated LLM Fine-Tuning on Edge Devices.** arXiv 2609.01457, 2026.
  - Split federated LLM fine-tuning evaluated on a real wireless testbed of heterogeneous edge clients.
  - Three of the five physical clients are Huawei nova 9 Pro smartphones.
  - Source: [arXiv 2609.01457](https://arxiv.org/pdf/2609.01457)
- **Split federated LoRA over wireless networks.** Wang, Zhou, Shi, Letaief (HKUST). IEEE Trans. Wireless Communications 2025; conference version at GLOBECOM 2024.
  - The heavy encoder stays on the edge server; embedding and task modules stay on devices.
  - Sources: [arXiv 2407.02924](https://arxiv.org/html/2407.02924v1); [HKUST poster](https://6g-aoe.hkust.edu.hk/wp-content/uploads/2025/01/Poster_Federated-Low-Rank-Adaptation-for-Large-Language.pdf)

**Resource-aware heterogeneous PEFT (edge servers / devices)**
- **FedPipe: Automated Federated Pipeline for PEFT of LLMs.** Zihan Fang, Zheng Lin, Zhe Chen, Xianhao Chen, Yue Gao, Yuguang Fang. arXiv 2404.06448, 2024.
  - Targets private edge servers with uneven compute and network resources (example: hospitals).
  - Selects which weights to adapt, assigns per-server LoRA ranks and batch sizes, and quantizes the base model to fit each server's memory.
  - The planning problem is an MILP, solved with a heuristic.
  - Reports faster training and higher accuracy than state-of-the-art baselines.
  - Source: [arXiv 2404.06448](https://arxiv.org/html/2404.06448v2)
- **HetLoRA: Heterogeneous LoRA for federated fine-tuning of on-device foundation models.** EMNLP 2024 (Google).
  - Uses different LoRA ranks to match device heterogeneity.
  - Source: [ACL Anthology 2024.emnlp-main.717](https://aclanthology.org/2024.emnlp-main.717)
- **FLoRA.** NeurIPS 2024.
  - Stacking-based aggregation for heterogeneous LoRA ranks. Argues that naive averaging of LoRA A/B matrices is mathematically flawed.
  - Source: [Sony AI page](https://ai.sony/publications/flora-federated-fine-tuning-large-language-models-with-heterogeneous-low-rank-adaptations)
- **FSLoRA: Federated Sketching LoRA.** arXiv 2501.19389.
  - Clients update sketched sub-matrices of the global LoRA modules according to their resources.
  - Source: [papers.cool 2501.19389](https://papers.cool/arxiv/2501.19389)
- **AFLoRA: Adaptive LoRA for resource-efficient federated fine-tuning.** arXiv 2505.24773.
  - Source: [arXiv 2505.24773](https://arxiv.org/html/2505.24773v2)
- **MobileFineTuner: a mobile-native framework for on-device LLM fine-tuning.** arXiv 2512.08211, Dec 2025.
  - Phone-native, single-device fine-tuning.
  - Lists federated/collaborative fine-tuning across phones as future work.
  - Source: [arXiv 2512.08211](https://arxiv.org/pdf/2512.08211)
- **Dongqi Cai (Cambridge/BUPT line of work).** A Jan 2025 talk, "Training LLMs Anywhere: Enabling Large-Scale Decentralized Learning on Your Mobile Devices", covers on-device LLM fine-tuning and cross-device FL, with papers at MobiCom.
  - Source: [Cambridge talk](https://talks.cam.ac.uk/talk/index/226192)

### Inferences
- Device-side memory, not FLOPs, is the binding constraint, because activation memory for backprop is the main cost. That explains why the three dominant 2025 designs (forward-gradient, side-tuning, split) all avoid storing device activations or trainable state.
- Split FL moves the bottleneck to the uplink (activations and gradients every step) and to server sequentiality. Wireless-optimization papers (FedsLLM, Just Talk Once) co-design split point, LoRA rank and radio resources because of this.
- Models evaluated on real devices are still small (≤1.3B in Fed MobiLLM; Gemma-3-270M and Qwen2.5-0.5B in EdgeFlowerTune; see section 3). Federated fine-tuning of 3–8B models on phones is not yet shown on real hardware in the sources found.

### Gaps
- I could not confirm which 2025–2026 MobiCom/MobiSys/SenSys papers do real-phone federated LLM fine-tuning. The ACM DL tables of contents were not reachable.
- I did not retrieve energy or battery numbers for any federated fine-tuning system.
- Candidates to verify (prior knowledge, not checked this session):
  - SplitLoRA (Zheng Lin et al.), [arXiv 2407.00952] **[unverified link]**: https://arxiv.org/abs/2407.00952
  - "Mobile Foundation Model as Firmware" (MobiCom 2024, Yuan et al.) **[unverified link]**: https://arxiv.org/abs/2308.14363
  - OpenFedLLM (KDD 2024) **[unverified link]**: https://arxiv.org/abs/2402.06954
  - pfl-research (Apple's private FL simulation framework) **[unverified link]**: https://arxiv.org/abs/2404.06430

---

## 2. Edge inference: on-device engines, device–edge–cloud collaborative inference, speculative decoding, partitioning, early exit, caching

### Takeaway
On-device inference has moved from CPU/GPU engines (llama.cpp, MLC-LLM) toward NPU-centric and heterogeneous-XPU engines:
- llm.npu: more than 1,000 tok/s prefill.
- HeteroLLM.
- PowerInfer-2: 47B MoE at about 11.7 tok/s on a phone.
- MNN-LLM: up to 25× prefill over llama.cpp on GPU.

Collaborative inference has converged on two patterns. In **speculative decoding**, a device SLM drafts and an edge or cloud LLM verifies (DSSD, edge–cloud early-exit SD, DSD, PicoSpec). In **layer partitioning across edge nodes** (EdgeShard), the model is sharded over several devices.

### Cited Findings

**On-device engines**
- **PowerInfer-2: Fast LLM Inference on a Smartphone.** SJTU IPADS. arXiv 2406.06282 (v1–v3, 2024).
  - Runs TurboSparse-Mixtral-47B on a phone at 11.68 tok/s, claimed as the first 47B LLM served on a smartphone.
  - Speed is highly memory-dependent: 2.13 tok/s with 7 GB of free RAM versus 11.68 tok/s with 19 GB.
  - Speedup: 3.94× average (up to 4.38×) over LLM-in-a-Flash, and 25.4× (up to 29.2×) over llama.cpp. Another version reports 24.6×/27.8×, so the multipliers vary by version.
  - Test phones: OnePlus 12 and Ace 2 (Qualcomm XPUs; 24 GB and 16 GB DRAM).
  - For models that fit in memory, about 40% less memory at speed comparable to llama.cpp and MLC-LLM.
  - Sources: [arXiv v3](https://arxiv.org/abs/2406.06282v3); [HTML v3](https://arxiv.org/html/2406.06282v3); [review](https://liner.com/review/powerinfer2-fast-large-language-model-inference-on-smartphone)
- **llm.npu (earlier mllm-NPU): Fast On-device LLM Inference with NPUs.** Daliang Xu et al. (PKU/BUPT, mllm team). ASPLOS 2025; arXiv 2407.05858.
  - Problem: mobile NPUs need static shapes. Re-preparing the graph per prompt length costs about 11 s for Gemma-2B.
  - Approach:
    - Chunk prompts into fixed-size pieces.
    - Move outlier activations to CPU/GPU.
    - Schedule Transformer blocks across processors by hardware affinity.
  - Results: 22.4× faster prefill and 30.7× energy savings on average, up to 32.8× end-to-end, and more than 1,000 tok/s prefill for a billion-parameter model.
  - Sources: [arXiv 2407.05858](https://arxiv.org/pdf/2407.05858); [HTML v2](https://arxiv.org/html/2407.05858v2)
- **HeteroLLM.** arXiv 2501.14794, 2025.
  - Separate system that also claims more than 1,000 tok/s prefill on Qualcomm hardware through heterogeneous CPU/GPU/NPU execution.
  - Source: [arXiv 2501.14794](https://arxiv.org/pdf/2501.14794v1) (described in the search summary)
- **MNN-LLM: A Generic Inference Engine for Fast LLM Deployment on Mobile Devices.** Alibaba. arXiv 2506.10443, Jun 2025.
  - Tested on a Xiaomi 14.
  - CPU: 8.6× prefill and 2.3× decode versus llama.cpp.
  - GPU (OpenCL): up to 25.3× prefill and 7.1× decode versus llama.cpp; 2.8× and 1.7× versus MLC-LLM.
  - Caveats: self-reported; MLC-LLM used symmetric quantization while the others used asymmetric.
  - Sources: [arXiv 2506.10443](https://arxiv.org/pdf/2506.10443); [HTML](https://arxiv.org/html/2506.10443v1)

**Benchmarks of on-device engines**
- **PalmBench** (arXiv 2410.05315): benchmarks compressed LLMs on mobile with llama.cpp and MLC under standardized settings. [arXiv 2410.05315](https://arxiv.org/pdf/2410.05315)
- **Understanding LLMs in Your Pockets** (arXiv 2410.03613): performance study on COTS phones, llama.cpp on CPU and MLC-LLM on GPU. [arXiv 2410.03613](https://arxiv.org/pdf/2410.03613)
- **Are We There Yet? A measurement study of efficiency for LLM applications on mobile devices.** arXiv 2504.00002. [arXiv 2504.00002](https://arxiv.org/pdf/2504.00002)
- **ExecuTorch.** I found no peer-reviewed or arXiv benchmark with measured numbers. Only vendor comparison pages came up (e.g., Cactus lists 12+ hardware backends), and those carry no measurements. [Cactus comparison](https://cactuscompute.com/compare/mlc-llm-vs-executorch)

**Device–edge–cloud speculative decoding**
- **DSSD: Distributed Split Speculative Decoding.** ICML 2025; arXiv 2507.12000.
  - SLM on the device, LLM on the edge.
  - Partitions the verification phase between device and edge to cut uplink traffic.
  - Sources: [ICML 2025](https://icml.cc/virtual/2025/47046); [arXiv 2507.12000](https://web3.arxiv.org/pdf/2507.12000)
- **Fast and Cost-Effective Speculative Edge-Cloud Decoding with Early Exits.** Venkatesha, Kundu, Panda (Yale). TMLR 2025; arXiv 2505.21594.
  - Draft model on a Jetson Nano, target model on an A100.
  - Up to 35% lower latency versus cloud autoregressive decoding, plus 11% more from preemptive drafting using target-model early exits.
  - 21% speedup in a robot deployment.
  - Sources: [TMLR](https://mlanthology.org/tmlr/2025/venkatesha2025tmlr-fast); [arXiv 2505.21594](https://arxiv.org/abs/2505.21594v1)
- **DSD: distributed speculative decoding for multi-device edge-cloud.** arXiv 2511.21669, Nov 2025.
  - Up to 1.1× speedup and 9.7% higher throughput over SD baselines.
  - Adds an adaptive window control policy and a simulator.
  - Source: [papers.cool 2511.21669](https://papers.cool/arxiv/2511.21669)
- **PicoSpec: pipelined collaborative SD for edge–cloud.** arXiv 2603.19133, Mar 2026.
  - Edge SLM and cloud LLM run concurrently in an asynchronous pipeline.
  - Source: [arXiv 2603.19133](https://arxiv.org/html/2603.19133v2)

**Model partitioning across edge nodes**
- **EdgeShard: Efficient LLM Inference via Collaborative Edge Computing.** PolyU. IEEE Internet of Things Journal, vol. 12, no. 10, pp. 13119–13131, May 2025; arXiv 2405.14371.
  - Jointly selects devices and partitions the model across edge devices and cloud servers, solved with dynamic programming.
  - Up to 50% lower latency and 2× throughput on Llama-2 models in a real testbed.
  - Sources: [arXiv 2405.14371](https://arxiv.org/pdf/2405.14371); [PolyU record](https://research.polyu.edu.hk/en/publications/edgeshard-efficient-llm-inference-via-collaborative-edge-computin/)

### Inferences
- Prefill on the device is close to solved on flagship SoCs thanks to the NPU (more than 1,000 tok/s). Decode stays memory-bandwidth-bound, which is why sparsity/offloading (PowerInfer-2) and device–edge speculative decoding are the active levers.
- Collaborative speculative decoding gains are modest in the reported multi-device setups (DSD about 1.1×; 21–35% latency reduction in edge–cloud early-exit SD). Network RTT and acceptance rate dominate.
- Engine benchmarks are mostly self-reported and use inconsistent quantization. A fair cross-engine benchmark (llama.cpp vs MLC vs MNN vs ExecuTorch vs QNN/LiteRT) on identical hardware is still missing.

### Gaps
- I found no sources in this session on LLM edge caching (KV/prompt/semantic caching at MEC servers), vehicular LLM inference, or early-exit-only LLM systems for edge. The searches ran out.
- I have no verified data for ExecuTorch, Google LiteRT/MediaPipe LLM, or the Qualcomm AI Engine/Genie runtime.
- Candidates to verify: LLMCad (on-device speculative decoding, Xu et al.) **[unverified link]**: https://arxiv.org/abs/2309.04255

---

## 3. Hierarchical FL (device–edge–cloud) for LLMs and wireless/6G-oriented federated LLM work

### Takeaway
The wireless community (IEEE TWC, GLOBECOM, IoT Magazine) frames federated LLM fine-tuning as a joint optimization of split point, LoRA rank, pruning rate and radio resources (bandwidth, power, subchannels). True three-tier device–edge–cloud hierarchical federated LLM training appears mostly as group-based or clustered LoRA aggregation (e.g., HierFedLoRA) rather than as validated three-tier deployments. Realistic-edge benchmarks such as EdgeFlowerTune have started to appear (2026).

### Cited Findings
- **HierFedLoRA: Resource-Efficient Federated Fine-Tuning of LLMs for Heterogeneous Data.** Jun Liu, Yunming Liao, Hongli Xu, Yang Xu (USTC). arXiv 2503.21213, 2025.
  - Hierarchical, group-based LoRA aggregation.
  - 1.6–4.2% accuracy gain and at least 2.1× fine-tuning speedup over strong baselines.
  - The exact topology (whether there is an explicit edge-server tier) was not confirmed from the abstract.
  - Source: [arXiv 2503.21213](https://arxiv.org/pdf/2503.21213)
- **EdgeFlowerTune: Evaluating Federated LLM Fine-Tuning Under Realistic Edge System Constraints.** arXiv 2605.08636, 2026.
  - Benchmarks FedAvg+LoRA, FedProx+LoRA, HeteroLoRA and SplitLoRA with small models (Gemma-3-270M, Qwen2.5-0.5B) under realistic edge constraints.
  - Source: [arXiv 2605.08636](https://arxiv.org/pdf/2605.08636)
- **Federated Intelligence: When Large AI Models Meet Federated Fine-Tuning and Collaborative Reasoning at the Network Edge.** Ni et al. IEEE Internet of Things Magazine 2025; arXiv 2503.21412.
  - Magazine-style overview combining federated fine-tuning with collaborative edge reasoning.
  - Source: [arXiv 2503.21412](https://arxiv.org/html/2503.21412v1)
- **Low-Latency Federated Fine-Tuning for LLMs over Wireless Networks.** Pang et al. arXiv 2602.01024, 2026.
  - Jointly optimizes pruning rates and bandwidth allocation to minimize latency.
  - Source: [arXiv 2602.01024](https://arxiv.org/html/2602.01024v2)
- **A Federated Fine-Tuning Paradigm of Foundation Models in Heterogeneous Wireless Networks.** Wang et al. arXiv 2509.19306, Sep 2025.
  - Switching-based federated fine-tuning for heterogeneous wireless edge devices.
  - Source: [arXiv 2509.19306](https://arxiv.org/html/2509.19306v1)
- **Split federated LoRA over wireless networks** (Wang, Zhou, Shi, Letaief; IEEE TWC 2025): see section 1. [arXiv 2407.02924](https://arxiv.org/html/2407.02924v1)
- **FedsLLM** (joint subchannel, power, split point and LoRA rank optimization): see section 1. [arXiv 2504.14667](https://arxiv.org/abs/2504.14667v2)
- **Surveys**
  - "A Comprehensive Survey of Federated Learning for Edge AI" (Preprints.org, Dec 2025; not peer-reviewed). Covers more than 200 papers, including hierarchical and multi-tier device–edge–cloud FL. Mentions on-device adaptation of foundation models only as outlook. [Preprints 202512.0118](https://www.preprints.org/manuscript/202512.0118/v1)
  - Al-Quraan et al., "Edge-Native Intelligence for 6G Communications Driven by FL" (IEEE TETCI 2023). Predates LLMs. [arXiv 2111.07392](https://arxiv.org/pdf/2111.07392)

### Inferences
- In wireless federated LLM papers, the typical optimization variables are {split layer, LoRA rank, pruning/quantization ratio, bandwidth, transmit power}, and the typical objective is latency or energy under an accuracy constraint. Most are evaluated in simulation with small models.
- Hierarchical aggregation (edge-level LoRA aggregation followed by cloud aggregation) is a natural fit for heterogeneous LoRA ranks (FLoRA/HetLoRA-style stacking), but no source found in this session shows a deployed three-tier LLM FL system.

### Gaps
- I did not find a confirmed IEEE JSAC 2025–2026 paper on federated LLMs. JSAC tables of contents were not searched.
- No dedicated survey of "hierarchical federated LLMs for 6G" was found.
- Candidate survey to verify: "Mobile Edge Intelligence for Large Language Models: A Contemporary Survey" (Qu, Chen, Wei, Lin, Chen, Huang; reportedly IEEE COMST 2025) **[unverified link]**: https://arxiv.org/abs/2407.18921

---

## 4. Industrial deployments (Apple, Google Gboard, Qualcomm, Samsung) and benchmarks

### Takeaway
- **Google** has the most documented production federated-LLM-adjacent pipeline. It trains Gboard LMs with FL plus DP, and in 2025 it uses DP-FL signals from devices to steer LLM-generated synthetic data that fine-tunes the server-side Gboard Proofread LLM. That is federated *learning of signals*, not on-device LLM fine-tuning.
- **Apple** ships an on-device model of about 3B parameters with 2-bit QAT, plus a server PT-MoE model on Private Cloud Compute (device–cloud hybrid inference).
- I could not verify Qualcomm or Samsung federated-LLM work in this session.

### Cited Findings
- **Google Gboard**
  - Federated Learning of Gboard Language Models with Differential Privacy (ACL Industry 2023). [Google Research](https://research.google/pubs/federated-learning-of-gboard-language-models-with-differential-privacy/)
  - Prompt Public LLMs to Synthesize Data for Private On-device Applications (arXiv 2404.04360, 2024). Pretraining on LLM-generated synthetic data gave 19.0% and 22.8% relative improvement in next-word-prediction accuracy over a model trained on a standard public dataset. [arXiv 2404.04360](https://arxiv.org/pdf/2404.04360)
  - "Synthetic and federated: privacy-preserving domain adaptation with LLMs for mobile applications" (Google Research blog, July 2025):
    - DP-FL collects privacy-preserving signals from user data to guide synthetic-data generation, which fine-tunes the Gboard Proofread LLM.
    - A small "buttress" model trained with DP on user data refines the synthetic data.
    - Dozens of FL+DP-trained LMs have replaced older FL-only models.
    - Sources: [Google Research blog](https://www.research.google/blog/synthetic-and-federated-privacy-preserving-domain-adaptation-with-llms-for-mobile-applications/) (snippet only; the page was blocked from fetching); [summary](https://thenote.app/post/en/synthetic-and-federated-privacy-preserving-domain-adaptation-with-llms-for-pu7nqvcxng)
  - Gboard Proofread (ACL 2025 industry). Launched on Pixel 8 and served on TPU v5 in Google Cloud. The tuned PaLM2-XS reached an 85.56% "good ratio" on a human-labeled golden set. [ACL 2025 PDF](https://preview.aclanthology.org/landing_page/2025.acl-long.19.pdf)
  - Improving Gboard LMs via private federated analytics. [Google Research blog](https://www.research.google/blog/improving-gboard-language-models-via-private-federated-analytics/)
- **Apple: Apple Intelligence Foundation Language Models Tech Report 2025.** arXiv 2507.13575.
  - On-device model of about 3B parameters, plus a server model built on a Parallel-Track MoE (PT-MoE) architecture for Private Cloud Compute.
  - The on-device model uses KV-cache sharing to reduce time-to-first-token and 2-bit quantization-aware training.
  - Secondary sources disagree on effective bit-width: one reports mixed 2/4-bit palettization averaging 3.7 bits per weight. Verify against the report.
  - Sources: [arXiv 2507.13575](https://arxiv.org/abs/2507.13575v2); [Apple ML](https://machinelearning.apple.com/research/apple-foundation-models-tech-report-2025); [InfoQ](https://www.infoq.com/news/2025/07/apple-foundation-models-ios26); [Substack (secondary)](https://marklwatson.substack.com/p/apples-hybrid-intelligence-architecture)
- **Benchmarks:** PalmBench, "LLMs in Your Pockets", "Are We There Yet?" and EdgeFlowerTune (see sections 2 and 3).

### Inferences
- Production systems avoid on-device LLM gradient training. Google uses FL/DP for small LMs and for privacy-preserving signals that steer server-side LLM synthetic data. Apple uses on-device inference with a cloud fallback. On-device federated LLM fine-tuning remains a research topic, not a shipped feature, as far as these sources show.
- Apple's device + Private Compute model is a production example of device–cloud collaborative inference, but it routes requests between tiers. It does not use speculative decoding or split execution as far as the sources say.

### Gaps
- Qualcomm (AI Hub, QNN/Genie, on-device LoRA adapters) and Samsung (Gauss / Galaxy AI) federated or on-device LLM papers were not retrieved, because the search budget ran out.
- The details of Apple's private federated learning or federated evaluation for Apple Intelligence were not verified.
- The ε values for Gboard DP-FL in 2025 were not retrieved; the blog was blocked.

---

## 5. Open problems (synthesized from the sources above)

### Takeaway
The main open problems are:
1. Scaling real-device federated fine-tuning beyond about 1B parameters within phone memory, energy and thermal limits.
2. The communication cost and privacy leakage of split-FL activations.
3. Aggregating heterogeneous-rank LoRA correctly.
4. Fair, reproducible engine and system benchmarks.
5. Joint training and serving (personalized adapters plus collaborative inference) over unreliable wireless links.

### Cited Findings
- Device memory and activation storage drive the design of backprop-free and server-assisted methods. [FwdLLM](https://www.usenix.org/conference/atc24/presentation/xu-mengwei); [Fed MobiLLM](https://arxiv.org/pdf/2508.06765)
- Naive LoRA aggregation is mathematically flawed under heterogeneous ranks. [FLoRA](https://ai.sony/publications/flora-federated-fine-tuning-large-language-models-with-heterogeneous-low-rank-adaptations)
- Single-device phone fine-tuning frameworks list federated extension as future work. [MobileFineTuner](https://arxiv.org/pdf/2512.08211)
- On-device inference speed depends strongly on available DRAM: 2.13 versus 11.68 tok/s. [PowerInfer-2](https://arxiv.org/html/2406.06282v3)
- Engine comparisons are confounded by quantization differences. [MNN-LLM](https://arxiv.org/html/2506.10443v1)
- Realistic edge evaluation is only now being standardized. [EdgeFlowerTune](https://arxiv.org/pdf/2605.08636)

### Inferences
- Split FL raises an unresolved privacy question: uploaded activations and gradients can leak inputs. None of the split-FL papers above reported formal privacy guarantees in the snippets seen.
- Energy and thermal throttling over multi-round FL on phones is under-measured. Only llm.npu reports energy, and that is for inference.
- Promising directions:
  - Combining forward-gradient or zeroth-order training with NPU acceleration.
  - Hierarchical aggregation of heterogeneous LoRA at MEC servers.
  - Speculative decoding where the device draft model is itself federatedly personalized.

### Gaps
- Microcontroller and extreme low-resource work is out of scope (covered by another researcher) and was not searched.
- I did not survey attacks or defenses specific to federated LLMs at the edge.
