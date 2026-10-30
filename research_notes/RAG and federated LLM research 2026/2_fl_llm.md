# Federated Learning (FL) + Large Language Models (LLMs): 2024–2026 survey notes

**Method note (read first).** These notes come from about 15 web searches run on 2026-10-30. Direct page fetching failed: `arxiv.org` did not resolve in this cloud session. The shared web-search budget then ran out before every item could be verified. So most facts below come from search-result snippets and abstracts as the search tool returned them, not from full-text reading. Experimental numbers are the authors' own claims unless noted otherwise. Anything I recall from background knowledge but could not source is in **Gaps** and is flagged as unverified. The author lists I could not confirm are marked "authors: not verified".

---

## Key Question 1: Federated fine-tuning, instruction tuning, alignment, pretraining, prompt tuning, and LLMs helping FL

### Takeaway
Most FL+LLM work is federated parameter-efficient fine-tuning (PEFT), and almost all of it uses LoRA, growing out of FedIT (Zhang et al.). Since 2025 the field has spread into three newer areas:
- **Federated preference alignment:** FedPDPO, and the first convergence theory for distributed DPO (2026).
- **Federated / low-communication pretraining:** Photon (MLSys 2025) and the DiLoCo family (Streaming DiLoCo, DiLoCo scaling laws, DiLoCoX, Decoupled DiLoCo).
- **Black-box / zeroth-order federated prompt tuning:** FedBPT (KDD'24), FedOne (ICML'25).

### Cited Findings

**A. Federated instruction tuning (foundational)**
- **FedIT, "Towards Building the Federated GPT: Federated Instruction Tuning"**
  - Authors: Jianyi Zhang and 8 co-authors (Duke, Microsoft Research, Adobe, Amazon).
  - Versions: arXiv 2305.05644 (May 2023, revised Jan 2024) and a NeurIPS 2023 FL workshop version. Sources: [arXiv](https://arxiv.org/abs/2305.05644v2), [mlanthology](https://mlanthology.org/neuripsw/2023/zhang2023neuripsw-building-a).
  - Claim: it is the first FL-based instruction tuning of LLMs. Clients download a global LLM and compute local updates on their own instructions. — [arXiv HTML](https://arxiv.org/html/2305.05644v2)
  - Result: in GPT-4 auto-evaluation, FedIT beats centralized training that uses only limited local instructions. — [deepai summary](https://deepai.org/publication/towards-building-the-federated-gpt-federated-instruction-tuning)
  - Code: "Shepherd" framework at github.com/JayZhang42/FederatedGPT-Shepherd. — [sigport poster](https://sigport.org/node/7387)
  - Mechanics: FedIT averages the A and B LoRA matrices separately. This is the baseline that later "exact aggregation" work criticises. — [arXiv 2509.26399](https://arxiv.org/pdf/2509.26399)

**B. Federated LoRA/PEFT methods.** The aggregation details are in Key Question 3.
- **FLoRA, "Federated Fine-Tuning LLMs with Heterogeneous Low-Rank Adaptations"** (NeurIPS 2024, arXiv 2409.05976)
  - Replaces averaging with **stacking**. The server concatenates local B matrices horizontally and local A matrices vertically.
  - This works because the sum of K LoRA products equals the product of the stacked matrices. Aggregation is therefore noise-free, supports heterogeneous ranks, and the global rank equals the sum of client ranks.
  - Sources: [NeurIPS PDF](https://proceedings.neurips.cc/paper_files/paper/2024/file/28312c9491d60ed0c77f7fff4ad86dd1-Paper-Conference.pdf), [arXiv](https://arxiv.org/abs/2409.05976)
- **FedSA-LoRA** (ICLR 2025, arXiv 2410.01463)
  - Finding: A matrices learn general knowledge, while B matrices encode client-specific knowledge.
  - Method: both are trained, but only A is sent to the server. B stays local, which gives personalization.
  - Sources: [ICLR 2025 paper](https://arxiv.org/pdf/2410.01463), summary in [arXiv 2509.26399](https://arxiv.org/pdf/2509.26399)
- **FedEx-LoRA, "Exact Aggregation for Federated and Efficient Fine-Tuning of Foundation Models"** (ACL 2025 long, oral, pp. 1316–1336; arXiv 2410.09432; also listed at ICLR 2025)
  - Averaging LoRA factors produces inexact updates. FedEx-LoRA adds a residual error term to the frozen base weight so the aggregate becomes exact, at minimal overhead.
  - Code: github.com/RaghavSinghal10/fedex-lora (CERT-Lab).
  - Sources: [ACL Anthology](https://aclanthology.org/2025.acl-long.67/), [ICLR virtual](https://iclr.cc/virtual/2025/33698), [GitHub](https://github.com/RaghavSinghal10/fedex-lora)
- **Fed-SB, "A Silver Bullet for Extreme Communication Efficiency…"** (arXiv 2502.15436)
  - A federated version of LoRA-SB: only a small r×r matrix R is trained.
  - Plain averaging of R is therefore *exact*, and nothing extra has to be communicated (unlike FedEx-LoRA's residual).
  - Reports 45–90× fewer parameters than LoRA. — [arXiv PDF](https://arxiv.org/pdf/2502.15436)
- **LoRA-FAIR** (arXiv 2411.14961)
  - Fixes server-side aggregation bias and client-side "initialization lag".
  - Experiments appear to be mainly computer vision; the authors list going beyond CV datasets as future work. — [arXiv PDF](https://arxiv.org/pdf/2411.14961)
- **FlexLoRA, "Federated Fine-tuning of LLMs under Heterogeneous Tasks and Client Resources"** (NeurIPS 2024, arXiv 2402.11505; Alibaba FederatedScope team)
  - Problem: the "buckets effect", where the least-resourced clients hold back everyone else.
  - Method: build a full-size LoRA weight from client updates, then use SVD to give each client an adapter at its own rank.
  - Compatibility: plugs into FedAvg, FedIT and SLoRA.
  - Gains: about +2.14% on average, up to +2.72%, rising with the share of high-rank clients.
  - Code: FederatedScope `FlexLoRA` branch.
  - Sources: [arXiv](https://arxiv.org/abs/2402.11505), [NeurIPS poster](https://neurips.cc/virtual/2024/poster/94124), [GitHub branch](https://github.com/alibaba/FederatedScope/tree/FlexLoRA), [review](https://www.themoonlight.io/en/review/federated-fine-tuning-of-large-language-models-under-heterogeneous-tasks-and-client-resources)
- **FedKRSO** (arXiv 2602.03019, 2026): communication- and memory-efficient federated fine-tuning of LLMs. Only the title was confirmed. — [arXiv PDF](https://arxiv.org/pdf/2602.03019)
- **FedKSeed:** zeroth-order full-parameter federated tuning using a finite set of random seeds, which cuts transmission to a few thousand bytes. — [survey arXiv 2409.15723](https://arxiv.org/html/2409.15723)
  - Surveys disagree on how to classify it: one places it under black-box/inference-only prompt tuning. — [arXiv 2508.16261](https://arxiv.org/html/2508.16261)

**C. Federated alignment / RLHF / DPO**
- **FedPDPO, "Federated Personalized Direct Preference Optimization for LLM Alignment"** (arXiv 2603.19741, March 2026)
  - Problem: plain DPO degrades in FL under non-IID preferences, and its implicit rewards generalize poorly.
  - Method: a personalized federated DPO framework.
  - Result: up to +4.80% average accuracy over baselines. — [arXiv](https://arxiv.org/abs/2603.19741)
- **"Distributed Direct Preference Optimization"** (arXiv 2605.20696, 2026): claims the first theoretical convergence and complexity analysis of DPO in both federated and decentralized settings. It cites FedPDPO as concurrent empirical work. — [arXiv PDF](https://arxiv.org/pdf/2605.20696)
- **"Pareto-Lenient Consensus for Efficient Multi-Preference LLM Alignment"** (arXiv 2604.05965): a related multi-preference consensus approach. Only the title was confirmed. — [arXiv PDF](https://arxiv.org/pdf/2604.05965)
- **Earlier framework support:** OpenFedLLM (2024) already included "federated value alignment" next to federated instruction tuning. — [arXiv 2402.06954](https://arxiv.org/pdf/2402.06954)
- **FedLLM-Bench** (NeurIPS 2024 D&B) has one federated preference-alignment dataset. — [NeurIPS PDF](https://proceedings.neurips.cc/paper_files/paper/2024/file/c8cdab0e890c59255c27977072fdb0f0-Paper-Datasets_and_Benchmarks_Track.pdf)
- **Industry (weak evidence):** a PatSnap patent blog describes a Google federated-RLHF patent in which on-device local reward models send scores to a server. Treat it as a pointer, not as evidence. — [PatSnap blog](https://www.patsnap.com/resources/blog/articles/rlhf-vs-dpo-in-llm-fine-tuning-60-patent-analysis/)

**D. Federated / low-communication pretraining**
- **Photon: Federated LLM Pre-Training** (MLSys 2025; arXiv 2411.02908; Lorenzo Sani, Alex Iacob, Zeyu Cao, …, Nicholas D. Lane; Cambridge / Flower Labs)
  - Results:
    - Trains models of up to 7B parameters in federated mode.
    - 35% lower wall time than baseline distributed training while communicating 64×–512× less.
    - Reaches target perplexity about 2× faster than DiLoCo (η=0.1) across three client counts.
  - Architecture: a Photon Aggregator, Photon LLM Nodes, and a Photon Data Source per client.
  - Code: github.com/relogu/photon.
  - Sources: [MLSys proceedings](https://proceedings.mlsys.org/paper_files/paper/2025/hash/185087ea328b4f03ea8fd0c8aa96f747-Abstract-Conference.html), [arXiv HTML](https://arxiv.org/html/2411.02908v1), [Flower blog](https://flower.ai/blog/2025-05-09-photon), [GitHub](https://github.com/relogu/photon)
- **Position paper, "The Future of Large Language Model Pre-training is Federated"** (arXiv 2405.10853; same group). — [arXiv](https://arxiv.org/html/2405.10853)
- **Streaming DiLoCo with overlapping communication** (Google DeepMind, arXiv 2501.18512)
  - Method: synchronizes subsets of parameters in sequence, which overlaps communication with computation, lowers peak bandwidth and reduces the bits exchanged.
  - Results: in 1B/10B/100B simulations it beats vanilla DiLoCo, with the gains growing with scale. A secondary source says bandwidth drops by about two orders of magnitude.
  - Sources: [emergentmind](https://www.emergentmind.com/papers/2501.18512), [Import AI 398](https://jack-clark.net/2025/02/03/import-ai-398-deepmind-makes-distributed-training-better-ai-versus-the-intelligence-community-and-another-chinese-reasoning-model/)
- **"Communication-Efficient LM Training Scales Reliably and Robustly: Scaling Laws for DiLoCo"** (arXiv 2503.09799, March 2025)
  - DiLoCo scales predictably with model size and, when well tuned, scales better than data-parallel training, even at small model sizes.
  - Its optimal batch size is larger. — [arXiv PDF](https://arxiv.org/pdf/2503.09799)
- **INTELLECT-1** (Prime Intellect): a 10B model trained with OpenDiLoCo, scaled to a 14M-token batch size. — [samsja on X](https://x.com/samsja19/status/1900648803147149610), [Import AI](https://jack-clark.net/2025/02/03/import-ai-398-deepmind-makes-distributed-training-better-ai-versus-the-intelligence-community-and-another-chinese-reasoning-model/)
- **DiLoCoX** (arXiv 2506.21263): low-communication training of models above 100B parameters on decentralized clusters. — [arXiv PDF](https://arxiv.org/pdf/2506.21263)
- **"Decoupled DiLoCo for Resilient Distributed Pre-training"** (arXiv 2604.21428, 2026). — [arXiv PDF](https://arxiv.org/pdf/2604.21428)
- **FoMoE, "Breaking the Full-Replica Barrier with a Federation of MoEs"** (arXiv 2606.19025, 2026): only the title was confirmed. It points toward federated pretraining in which clients do not hold full model replicas. — [arXiv PDF](https://arxiv.org/pdf/2606.19025)
- **Governance angle:** "Does Distributed Training Undermine Compute Governance?" (arXiv 2605.29359). — [arXiv HTML](https://arxiv.org/html/2605.29359)

**E. Federated prompt tuning (incl. black-box)**
- **FedBPT / Fed-BBPT, "Efficient Federated Prompt Tuning for Black-Box Large Pre-trained Models"** (arXiv 2310.03123; KDD'24)
  - Treats the LLM as a black-box inference API and optimizes prompts locally with gradient-free (zeroth-order) methods. — [arXiv HTML](https://arxiv.org/html/2310.03123)
- **FedOne, "Query-Efficient Federated Learning for Black-box Discrete Prompt Learning"** (ICML 2025, arXiv 2506.14929)
  - Activating a single client per round gives the best query efficiency against cloud LLM APIs. — [ICML poster](https://icml.cc/virtual/2025/poster/45310), [arXiv PDF](https://arxiv.org/pdf/2506.14929)
- **FDPT** (ICCV 2025): federated discrete prompt tuning for black-box vision-language models, using zeroth-order optimization and aiming to reduce inversion-attack risk. — [CVF PDF](https://openaccess.thecvf.com/content/ICCV2025/papers/Wu_FDPT_Federated_Discrete_Prompt_Tuning_for_Black-Box_Visual-Language_Models_ICCV_2025_paper.pdf)
- **Federated black-box prompt tuning system on the edge** (ACM MobiCom'24 companion, DOI 10.1145/3636534.3698856). Edge aspect: mention only. — [ACM DL](https://dl.acm.org/doi/10.1145/3636534.3698856)

**F. LLMs helping FL (synthetic data, LLM-side knowledge transfer)**
- **FedMKT, "Federated Mutual Knowledge Transfer for Large and Small Language Models"** (COLING 2025; arXiv 2406.02224; WeBank/FATE group, authors not verified)
  - Setup: a server LLM and heterogeneous client SLMs exchange knowledge in both directions through selective logit distillation. Only LoRA adapters are trained.
  - Tokenizer mismatch is handled with minimum-edit-distance (MinED) token alignment.
  - Claim: the server LLM reaches performance comparable to fine-tuning directly on client data.
  - Limitation: it relies on a public dataset.
  - Sources: [ACL Anthology PDF](https://aclanthology.org/2025.coling-main.17.pdf), [arXiv HTML](https://arxiv.org/html/2406.02224), [review](https://www.themoonlight.io/en/review/fedmkt-federated-mutual-knowledge-transfer-for-large-and-small-language-models), [later critique, arXiv 2602.18749](https://arxiv.org/html/2602.18749)
- **DPSDA-FL, "Synthetic Data Aided FL Using Foundation Models"** (FL@FM-IJCAI'24 workshop; Springer, Mar 2025; Univ. Manchester)
  - Differentially private foundation-model synthetic data makes non-IID client data more homogeneous.
  - On CIFAR-10: up to +26% class recall and +9% accuracy.
  - Note: it uses image synthesis, not LLM text.
  - Sources: [Springer](https://link.springer.com/chapter/10.1007/978-3-031-82240-7_8), [arXiv](https://arxiv.org/pdf/2407.05174)
- **Titles confirmed, contents not verified:**
  - LanFL, DP FL with LLMs using synthetic samples (arXiv 2410.19114). — [arXiv PDF](https://arxiv.org/pdf/2410.19114)
  - Concordia, self-improving synthetic tables for federated LLMs (arXiv 2605.09855). — [arXiv PDF](https://arxiv.org/pdf/2605.09855)
  - "Federated Learning Meets LLMs: Feature Extraction From Heterogeneous Clients" (arXiv 2510.00065). — [arXiv PDF](https://www.arxiv.org/pdf/2510.00065)
  - Federated reasoning distillation with learnability-aware data allocation (arXiv 2602.18749, 2026). — [arXiv HTML](https://arxiv.org/html/2602.18749)

### Inferences
- The field has moved past FedIT-style "FedAvg on LoRA" in two directions. One is *correctness of aggregation* (FLoRA, FedEx-LoRA, Fed-SB). The other is *personalization through asymmetric sharing* (FedSA-LoRA). Their 2025 venues (ICLR, ACL, NeurIPS) suggest these are now the standard baselines a new federated PEFT paper must beat.
- Federated alignment is clearly behind federated SFT. The first convergence theory for federated DPO only appeared in 2026 (arXiv 2605.20696), and personalized/pluralistic federated DPO (FedPDPO) is very recent. Federated RLHF with PPO/GRPO-style online RL looks under-explored in the sources I found. That reading is based on absence of evidence.
- Federated *pretraining* has merged with the "low-communication distributed training" literature (DiLoCo and its descendants). Photon is the explicitly FL-framed system, while the DiLoCo work is datacenter/decentralized-framed. The difference is mostly whether data is siloed, not the algorithm: both are local-SGD with an outer optimizer.

### Gaps
- Not verified for lack of fetch/search budget, though background knowledge suggests these exist:
  - FedSA-LoRA's author list (I believe Pengxin Guo et al.) and its code repo.
  - Key numeric results for FLoRA, FedSA-LoRA and FedEx-LoRA.
  - SLoRA, FedBiOT, FedPETuning (ACL Findings 2023), FedPrompt, FFA-LoRA (ICLR 2024; freezes A, trains B, privacy-motivated), RoLoRA, FedRA, FedDPA, FedDAT.
  - Federated RLHF papers such as FedRLHF and "Towards Federated RLHF with Aggregated Client Preference" (FedBis/FedBiscuit, ICLR 2025).
  - Recent federated GRPO / RL-for-reasoning work.
  The report writer should not cite any of these from these notes.
- The authors and results of the 2026 papers SeFoRA, PreLort, "Rethinking Factor Sharing in Federated LoRA" and FedPA-LoRA were seen only as titles (see KQ3).
- I found no reliable source on federated GRPO/RLVR for reasoning models (2025–2026).

---

## Key Question 2: Frameworks and benchmarks (FederatedScope-LLM, OpenFedLLM, Flower/FlowerTune, FedML/FedLLM, NVIDIA FLARE, FATE-LLM)

### Takeaway
- **OpenFedLLM** (with FedLLM-Bench) is the most research-oriented codebase.
- **FlowerTune** (NeurIPS 2025 D&B) is the main public, ongoing cross-domain *leaderboard*.
- **FATE-LLM** and **NVIDIA FLARE** target industrial deployment.
- **FederatedScope-LLM** hosts the Alibaba methods (e.g., FlexLoRA).
- Flower and FLARE now interoperate.

### Cited Findings
- **OpenFedLLM** (arXiv 2402.06954; Rui Ye et al., SJTU group, authors not fully verified)
  - Supports 7 FL algorithms, federated instruction tuning and federated value alignment, 8 training datasets, and 30+ evaluation metrics.
  - Headline result: Llama2-7B fine-tuned with any FL algorithm beats GPT-4 by a significant margin on a financial benchmark.
  - Source: [arXiv PDF](https://arxiv.org/pdf/2402.06954)
- **FedLLM-Bench** (NeurIPS 2024 Datasets & Benchmarks)
  - Follow-up to OpenFedLLM, described as the first *realistic* FedLLM benchmark.
  - Contents: 3 federated instruction-tuning datasets and 1 federated preference-alignment dataset.
  - Code: github.com/rui-ye/FedLLM-Bench.
  - Sources: [NeurIPS PDF](https://proceedings.neurips.cc/paper_files/paper/2024/file/c8cdab0e890c59255c27977072fdb0f0-Paper-Datasets_and_Benchmarks_Track.pdf), [GitHub](https://github.com/rui-ye/FedLLM-Bench)
- **FederatedScope-LLM** (Alibaba)
  - Built on the FederatedScope backend; supports FedPEFT and model personalization.
  - Per 2024 comparison tables: instruction tuning yes, value alignment no; 1 aggregation method, 3 datasets, 3 metrics; base model LLaMA-7B.
  - Sources: [survey arXiv 2406.12844](https://arxiv.org/pdf/2406.12844), [survey arXiv 2404.15381](https://arxiv.org/pdf/2404.15381)
- **FATE-LLM** (arXiv 2310.10049; WeBank)
  - "Industrial grade" framework on top of FATE, with components FATE-Flow, Eggroll and OSX. Focus is PEFT for training efficiency.
  - Per 2024 comparison tables: 1 aggregation method, 1 dataset, 4 metrics.
  - Sources: [arXiv PDF](https://arxiv.org/pdf/2310.10049), [survey arXiv 2406.12844](https://arxiv.org/pdf/2406.12844)
- **FlowerTune, "A Cross-Domain Benchmark for Federated Fine-Tuning of LLMs"** (arXiv 2506.02961; NeurIPS 2025 Datasets & Benchmarks; first author Yan Gao, Flower Labs)
  - Scope: four domains (general NLP, finance, medical, coding), each with its own federated instruction-tuning datasets and metrics.
  - Pipelines: separate end-to-end pipelines for fine-tuning and for evaluation, plus tools to measure communication and memory.
  - Model size: under 14B in the paper; the public site's cap is 13B.
  - Timeline: the leaderboard launched in October 2024 with a Mistral-7B baseline. Entries now include models such as Qwen2.5-7B-Instruct, and submissions remain open.
  - Sources: [arXiv](https://arxiv.org/abs/2506.02961), [NeurIPS PDF](https://papers.nips.cc/paper_files/paper/2025/file/563991b5c8b45fe75bea42db738223b2-Paper-Datasets_and_Benchmarks_Track.pdf), [Flower blog](https://flower.ai/blog/2024-10-16-flowertune-llm-leaderboard/), [leaderboard](https://flower.ai/benchmarks/llm-leaderboard), [NLP board](https://flower.ai/benchmarks/llm-leaderboard/nlp/)
- **Flower LLM apps:** Flower Hub examples use 🤗 PEFT LoRA, the Flower Simulation Engine and FedAvg. — [Flower app](https://flower.ai/apps/zexili/flwr-nlp)
- **"Towards the Next Frontier of LLMs, Training on Private Data: A Cross-Domain Benchmark for Federated Fine-Tuning"** (arXiv 2605.13936, 2026)
  - A newer federated PEFT benchmark for sensitive domains that cites FlowerTune as related work. — [arXiv PDF](https://arxiv.org/pdf/2605.13936)
- **NVIDIA FLARE**
  - "Empowering FL for Massive Models with NVIDIA FLARE" (arXiv 2402.07792): supports PEFT and full SFT of foundation LLMs. — [arXiv PDF](https://arxiv.org/pdf/2402.07792)
  - NVFlare Day 2024 lists p-tuning, SFT and PEFT recipes. — [NVIDIA PDF](https://developer.download.nvidia.com/assets/Clara/flare/NVFLARE_DAY_2024_Main_Presentation.pdf)
- **Flower × FLARE integration** (arXiv 2407.00031): Flower ServerApps and ClientApps run natively on the FLARE runtime without code changes. — [arXiv PDF](https://arxiv.org/pdf/2407.00031), [NVIDIA blog](https://developer.nvidia.com/blog/supercharging-the-federated-learning-ecosystem-by-integrating-flower-and-nvidia-flare/)
- **FedLLM-Factory** (GitHub, boyi-liu): a community codebase that implements 15+ federated fine-tuning methods. — [GitHub](https://github.com/boyi-liu/FedLLM-Factory)

### Inferences
- For a PhD researcher choosing a testbed:
  - FlowerTune is the de facto comparable leaderboard, being peer-reviewed at NeurIPS 2025 D&B and open to submissions.
  - OpenFedLLM / FedLLM-Bench is the easiest place to prototype new aggregation or alignment algorithms, because it already includes alignment.
  - FedLLM-Factory is useful for running many LoRA-aggregation baselines.
- The 2024 framework comparison tables (counts of algorithms, datasets and metrics) are probably out of date by late 2026.

### Gaps
- **FedML / FedLLM (TensorOpera):** search returned nothing, so I have no current status or citations.
- **Current FlowerTune rankings:** I have no snapshot of the top entries or which aggregation methods win.
- **Release-level details:** I did not check FATE-LLM 2.x or NVFlare 2.5/2.6 feature lists (e.g., any 2025–2026 additions for LLM alignment).

---

## Key Question 3: Data/system heterogeneity, non-IID effects, LoRA aggregation noise, model heterogeneity

### Takeaway
The central technical problem in federated LoRA is that the average of products is not the product of averages:

mean_k(B_k A_k) ≠ mean_k(B_k) · mean_k(A_k)

Plain averaging of the factors therefore injects aggregation noise, and the error grows with non-IID data and with heterogeneous ranks. Three families of fix have emerged:
- **Exact aggregation:** FLoRA stacking, FedEx-LoRA's residual, Fed-SB training only R.
- **Asymmetric or selective sharing:** FedSA-LoRA shares only A.
- **Rank-heterogeneity reconciliation:** zero-padding (HetLoRA), SVD redistribution (FlexLoRA), self-pruning and sparsity-weighting (Byun et al.), nested/prefix ranks (PreLort), folding (Fed-PLoRA), and sketching (SeFoRA).

### Cited Findings
- **Why averaging fails:** existing methods "rely on traditional federated averaging of LoRA adapters, resulting in inexact updates" (FedEx-LoRA). — [ACL 2025](https://aclanthology.org/2025.acl-long.67/)
- **FLoRA** argues that averaging creates "mathematically inaccurate aggregation noise" and fixes it with stacking. — [arXiv](https://arxiv.org/abs/2409.05976)
- **Byun et al., "Towards Federated Low-Rank Adaptation of LMs with Rank Heterogeneity"** (arXiv 2406.17477; NAACL 2025): zero-padding aggregation dilutes the information carried by high-rank clients, which destabilizes training. — [arXiv](https://arxiv.org/abs/2406.17477)
- **LoRA-A², "Towards Robust and Efficient Federated LoRA with Heterogeneous Clients"** (arXiv 2410.22815; ACL 2025)
  - Method: alternating freezing of A and B plus adaptive rank selection.
  - Claim: stays robust under extreme data heterogeneity and at low ranks. — [arXiv](https://arxiv.org/abs/2410.22815)
- **HetLoRA vs FlexLoRA vs Fed-PLoRA:**
  - HetLoRA handles mixed ranks by zero-padding and truncation; FlexLoRA uses SVD redistribution and beats HetLoRA on zero-shot generalization to unseen clients. — [arXiv 2402.11505](https://arxiv.org/abs/2402.11505), [Liner review](https://liner.com/review/federated-finetuning-of-large-language-models-under-heterogeneous-language-tasks)
  - **Fed-PLoRA, "Heterogeneous Federated Fine-Tuning with Parallel One-Rank Adaptation"** (ICLR 2026) argues that HetLoRA's truncation loses information, whereas Fed-PLoRA folds high-rank components and so keeps it. This comes from a secondary paper-notes site. — [papernotes](https://en.papernotes.org/ICLR2026/llm_safety/heterogeneous_federated_fine-tuning_with_parallel_one-rank_adaptation/)
- **FedHL, "FL for Heterogeneous Low-Rank Adaptation via Unbiased Aggregation"** (arXiv 2505.18494, 2025). — [arXiv PDF](https://arxiv.org/pdf/2505.18494)
- **FLoRIST, "Singular Value Thresholding for Efficient and Accurate Federated Fine-Tuning of LLMs"** (arXiv 2506.09199): SVD-based thresholding after aggregation. — [arXiv PDF](https://arxiv.org/pdf/2506.09199)
- **"Communication-Efficient and Accurate Approach for Aggregation in Federated LoRA"** (arXiv 2509.26399): uses FedIT, FLoRA and FedSA-LoRA as baselines. — [arXiv PDF](https://arxiv.org/pdf/2509.26399)
- **FedLoRA-Optimizer** (arXiv 2510.11274). — [arXiv PDF](https://arxiv.org/pdf/2510.11274)
- **Ravan, "Multi-Head Low-Rank Adaptation for Federated Fine-Tuning"** (arXiv 2506.05568). — [arXiv PDF](https://arxiv.org/pdf/2506.05568)
- **ILoRA** (arXiv 2511.16069, Nov 2025): names three problems, namely instability caused by initialization, rank incompatibility combined with aggregation error, and client drift under non-IID data. — [arXiv](https://arxiv.org/abs/2511.16069)
- **"Rethinking LoRA for Data Heterogeneous FL: Subspace and State Alignment"** (arXiv 2602.01746, 2026). — [arXiv PDF](https://arxiv.org/pdf/2602.01746)
- **"Preventing Rank Collapse"** (arXiv 2602.13486, 2026). This was seen only as a reference, so its exact title was not confirmed. — [cited in arXiv 2608.09742](https://arxiv.org/html/2608.09742v1)
- **PreLort, "Prefix-Nested LoRA for Federated Fine-Tuning under Rank Heterogeneity"** (arXiv 2606.15963, June 2026)
  - Makes ranks compatible during training as well as at aggregation.
  - Each rank segment is averaged only over the clients that contribute to it. — [arXiv PDF](https://arxiv.org/pdf/2606.15963)
- **Title-only 2026 papers (Aug 2026):**
  - SeFoRA, "Sketch-Aggregated Federated LoRA with Heterogeneous Client Ranks" (arXiv 2608.10144). — [arXiv PDF](https://arxiv.org/pdf/2608.10144)
  - "Rethinking Factor Sharing in Federated LoRA: A Rank-Aware Adaptive Approach" (arXiv 2608.09742). This revisits FedSA-LoRA-style A-only sharing. — [arXiv HTML](https://arxiv.org/html/2608.09742v1)
  - FedPA-LoRA, "Product-Aligned Framework for Mitigating Aggregation and Initialization Errors in Heterogeneous Federated LoRA" (arXiv 2608.15381). — [arXiv PDF](https://arxiv.org/pdf/2608.15381)
- **FediLoRA** (arXiv 2509.06984): heterogeneous LoRA for *multimodal* federated fine-tuning with missing modalities, using dimension-wise weighted aggregation. — [arXiv HTML](https://arxiv.org/html/2509.06984v1)
- **Model heterogeneity (different architectures or tokenizers):**
  - FedMKT does mutual LLM↔SLM distillation with MinED token alignment (COLING 2025). — [ACL PDF](https://aclanthology.org/2025.coling-main.17.pdf)
  - FedADP proposes unified aggregation across heterogeneous architectures (arXiv 2505.06497), but it is not LLM-specific. — [arXiv PDF](https://arxiv.org/pdf/2505.06497)
- **Non-IID preferences:** FedPDPO reports that DPO degrades in FL under non-IID preference data and adds personalization to cope. — [arXiv](https://arxiv.org/abs/2603.19741)

### Inferences
- The sheer number of 2026 federated-LoRA aggregation papers (PreLort, SeFoRA, FedPA-LoRA, Rethinking Factor Sharing, Preventing Rank Collapse, ILoRA) suggests the problem is not settled. No single method is accepted as dominant.
- Two design choices trade off against each other:
  - *Exact* aggregation (FLoRA's rank grows with K; FedEx-LoRA sends a residual) costs communication or memory.
  - Cheap aggregation (FedSA-LoRA, Fed-SB) restricts either expressivity or global consensus.
  This is a natural axis for positioning new work.
- Recent titles keep pairing "initialization error" with "aggregation error" (LoRA-FAIR, ILoRA, FedPA-LoRA). This suggests the field now treats the client-side restart point after aggregation as part of the same problem as the server-side product mismatch.

### Gaps
- **No head-to-head numbers:** I found no independent comparison of FLoRA, FedEx-LoRA, FedSA-LoRA, Fed-SB and the rank-heterogeneous methods on one benchmark. FlowerTune or FedLLM-Factory could supply one, but I did not see published tables.
- **Model heterogeneity beyond distillation:** no 2025–2026 sources were found for clients holding different base LLMs while sharing adapters (e.g., cross-architecture LoRA transfer).
- **System heterogeneity** (stragglers, memory limits) appeared only implicitly through rank heterogeneity. It was left mostly to other researchers per the scope.

---

## Key Question 4: Survey papers and open problems

### Takeaway
Several surveys from 2024–2026 cover FL+LLM. The most authoritative recent ones are IEEE COMST (Aug 2025), Machine Learning (Springer, 2026), and an arXiv survey dedicated to federated fine-tuning (2503.12016). Recurring open problems:
- Compute cost on heterogeneous devices.
- Lack of standardized benchmarks.
- Correct and efficient adapter aggregation under heterogeneity.
- Federated alignment.
- Pretraining at scale.
- Privacy/security. This last one is out of scope here and only mentioned.

### Cited Findings
- **"Toward Federated Large Language Models: Motivations, Methods, and Future Directions"** (IEEE Communications Surveys & Tutorials, vol. 27, no. 4, Aug 2025, p. 2733; Auburn-affiliated authors, names not verified)
  - Covers the full lifecycle, from pretraining through fine-tuning to applications, plus threats and security. — [PDF](https://www.eng.auburn.edu/~szm0001/papers/COMST-AUG25.pdf)
- **"Federated Learning for Large Language Models: Opportunities, Challenges, and Open Research Directions"** (Machine Learning, Springer, 2026, DOI 10.1007/s10994-026-07142-7)
  - Challenges it lists include the high compute cost of billion-parameter training on heterogeneous devices and the lack of standardized benchmarks. — [Springer](https://link.springer.com/article/10.1007/s10994-026-07142-7)
- **"A Survey on Federated Fine-tuning of Large Language Models"** (arXiv 2503.12016, 2025). — [arXiv PDF](https://arxiv.org/pdf/2503.12016)
- **"On the Evolution of Federated Post-Training LLMs: A Model Accessibility View"** (arXiv 2508.16261, 2025)
  - Organizes federated post-training by how much model access clients have: white-box, gray-box, black-box.
  - Rates federated black-box prompt tuning through inference APIs as the most parameter-efficient and privacy-preserving option. — [arXiv HTML](https://arxiv.org/html/2508.16261)
- **"Federated Foundation Language Model Post-Training Should Focus on Open-Source Models"** (arXiv 2505.23593, 2025): a position paper. Only the title is confirmed; its argument was not verified. — [arXiv PDF](https://arxiv.org/pdf/2505.23593)
- **"A Survey on Foundation Models for Personalized Federated Intelligence"** (arXiv 2505.06907, 2025). — [arXiv PDF](https://arxiv.org/pdf/2505.06907)
- **"Federated and edge learning for large language models"** (Information Fusion, 2025): edge/privacy focus, mention only. — [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1566253524006183)
- **"Federated Large Language Models: Current Progress and Future Directions"** (arXiv 2409.15723, 2024). — [arXiv HTML](https://arxiv.org/html/2409.15723)
- **"Synergizing Foundation Models and Federated Learning: A Survey"** (arXiv 2406.12844, 2024). — [arXiv PDF](https://arxiv.org/pdf/2406.12844)
- **"Advances and Open Challenges in Federated Foundation Models"** (arXiv 2404.15381, 2024). — [arXiv PDF](https://arxiv.org/pdf/2404.15381)
- **"Recent Advances in Federated Learning Driven LLMs: A Survey on Architecture, Performance, and Security"** (arXiv 2406.09831). — [arXiv PDF](https://arxiv.org/pdf/2406.09831)
- **"Integration of LLMs and Federated Learning"** (Patterns, 2024; arXiv 2307.08925). — [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S2666389924002708), [arXiv](https://arxiv.org/pdf/2307.08925)
- **"Distributed LLMs and Multimodal LLMs: A Survey"** (arXiv 2503.16585). — [arXiv PDF](https://arxiv.org/pdf/2503.16585)

### Inferences
These open problems are synthesized from the cited works, not quoted from them:
1. **Aggregation of low-rank adapters.** Exact, communication-cheap and rank-heterogeneous aggregation is still open, as the stream of 2026 papers in KQ3 shows.
2. **Federated alignment theory and practice.** DPO convergence theory only appeared in 2026, and there is little sign of federated online RL or GRPO.
3. **Benchmark standardization.** The Springer 2026 survey flags it as missing, even though FlowerTune and FedLLM-Bench exist.
4. **Federated pretraining beyond about 10B parameters on truly siloed, non-IID data.** Photon reached 7B. The DiLoCo-family scaling evidence comes mostly from datacenter or decentralized settings with IID shards.
5. **Model accessibility.** Should federated post-training assume open weights, or black-box APIs (FedBPT, FedOne)? The 2508.16261 survey and the 2505.23593 position paper frame this as a field-level choice.

Out of scope, mentioned only:
- **Privacy:** DP, attacks on LoRA updates, inversion (cf. FDPT).
- **Agentic FL**, **edge/on-device FL** (MobiCom'24 black-box prompt system), and **training speed**.

### Gaps
- **Full taxonomies and "open problems" sections:** I could not read these for the COMST 2025, Springer 2026 and 2503.12016 surveys because arxiv.org fetching was blocked and the search budget ran out. The open problems listed above are partly my own synthesis.
- **Survey authors:** not verified for most of the surveys above.
- **Possible newer surveys:** none from 2026 beyond the Springer one could be confirmed.
