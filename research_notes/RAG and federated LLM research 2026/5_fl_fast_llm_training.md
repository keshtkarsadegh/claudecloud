# Efficient Federated and Low-Communication Distributed LLM Training (2024–2026): Communication, Computation, Convergence

> Method note: These notes come from web-search result snippets gathered on 2026-10-30. Direct page fetching from arxiv.org failed: the host would not resolve in this sandbox, so arxiv.org must be allowed in the session's network settings before pages can be fetched. The session's web-search budget also ran out before every claim could be checked. Numbers marked "(snippet)" come from search-engine summaries of the primary page and should be checked against the PDF before publication. Items I know from prior knowledge but could not re-verify are listed under **Gaps**, not as findings.

## Q1. Communication-efficient training: DiLoCo family, Prime Intellect, DeMo/Psyche, Photon, compression, gossip

### Takeaway
DiLoCo-style local-SGD with an outer Nesterov optimizer is now the standard approach for cross-datacenter and over-the-internet LLM pretraining. Research from 2025–2026 follows four directions:
- **Scaling laws** show DiLoCo matching or beating data parallelism at 4–10B parameters with more than 100× less communication (NeurIPS 2025).
- **Overlap and streaming** of synchronization with compute (Streaming DiLoCo, eager updates).
- **Extreme pseudo-gradient compression** with top-k, 2-bit quantization and error feedback (SparseLoCo, MuLoCo), which made the permissionless Covenant-72B run possible in 2026.
- **Removing global all-reduce** through gossip and partial averaging (NoLoCo, GASLoC).

Headline "×" figures are not comparable across papers. Some measure communication volume, some per-step throughput and some sync latency, and the baselines differ.

### Cited Findings

**DiLoCo scaling and variants (Google / Google DeepMind)**
- **Scaling Laws for DiLoCo.** "Communication-Efficient Language Model Training Scales Reliably and Robustly: Scaling Laws for DiLoCo". Authors: Zachary Charles, Gabriel Teston, Lucio Dery, Keith Rush, Nova Fallen, Zachary Garrett, Arthur Szlam, Arthur Douillard (Google). NeurIPS 2025, arXiv:2503.09799. — [arXiv](https://arxiv.org/pdf/2503.09799); [NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/hash/99acb4c087266e80b547aed79247266b-Abstract-Conference.html); [Google Research](https://research.google/pubs/communication-efficient-language-model-training-scales-reliably-and-robustly-scaling-laws-for-diloco/)
  - The paper derives scaling laws for evaluation loss and optimal hyperparameters under a fixed compute budget. Well-tuned DiLoCo scales better than data-parallel (DP) training as model size grows, and can beat DP even at small sizes. — [HF papers](https://huggingface.co/papers/2503.09799)
  - DiLoCo also has a larger optimal batch size than DP. The laws predicted that DiLoCo would beat DP at 4B and 10B parameters while cutting total communication by more than 100×. — [arXiv HTML](https://arxiv.org/html/2503.09799v1)
- **Streaming DiLoCo.** "Streaming DiLoCo with overlapping communication: Towards a Distributed Free Lunch". Douillard et al. (Google), arXiv:2501.18512, January 30, 2025. — [arXiv](https://arxiv.org/abs/2501.18512v1)
  - It synchronizes subsets (fragments) of parameters on a staggered schedule rather than all at once, which lowers peak bandwidth. It overlaps worker compute with sync communication, which raises the tolerated latency. It also quantizes the exchanged outer gradients. — [arXiv HTML](https://arxiv.org/html/2501.18512v1); [papers.cool](https://papers.cool/arxiv/2501.18512)
  - The paper recommends at most 5 steps of overlap, because quality drops with longer overlap. — [Factored Gossip DiLoCo, arXiv:2606.22768](https://arxiv.org/pdf/2606.22768)
- **Eager Updates.** "Eager Updates For Overlapped Communication and Computation in DiLoCo". Satyen Kale, Arthur Douillard, Yanislav Donchev. arXiv:2502.12996 (2025). — [arXiv](https://arxiv.org/abs/2502.12996)
  - Each worker applies its own local outer gradient as a proxy while the all-reduce for the global outer gradient is still in flight. The authors report this is competitive with standard DiLoCo in low-bandwidth settings. — [arXiv PDF](https://arxiv.org/pdf/2502.12996)
- **Factored Gossip DiLoCo** (arXiv:2606.22768, 2026). This is an independent re-evaluation. It reports that Streaming DiLoCo's overlap yields negligible compute-utilization gains over DiLoCo in its target low-bandwidth regime, and it proposes a gossip-based variant to reduce blocking communication. — [arXiv](https://arxiv.org/pdf/2606.22768) (snippet)

**Prime Intellect: OpenDiLoCo, INTELLECT-1, INTELLECT-2**
- **INTELLECT-1 Technical Report.** Prime Intellect, arXiv:2412.01152 (December 2024). It describes a 10B model trained across continents with DiLoCo plus int8-quantized pseudo-gradients. — [arXiv](https://arxiv.org/pdf/2412.01152); [release blog](https://primeintellect.ai/blog/intellect-1-release)
  - **Sync frequency and compression.** The run synchronized every 100 inner steps and quantized pseudo-gradients to int8. The blog claims a 400× bandwidth reduction against DP. — [PI blog](https://primeintellect.ai/blog/intellect-1-release)
  - **Conflicting reduction figure.** The arXiv report instead claims "up to 2000×" from int8 combined with reduced sync frequency. The two documents do not reconcile the 400× and 2000× figures. — [arXiv HTML](https://arxiv.org/html/2412.01152v1)
  - **What int8 alone contributes.** int8 versus fp32 by itself is a 4× payload reduction. — [arXiv](https://arxiv.org/abs/2412.01152v1)
  - **Overhead.** The outer all-reduce took under 1 minute, which is 1–2% of total training time. Bandwidth utilization improved up to 40× over the OpenDiLoCo release. — [PI blog](https://primeintellect.ai/blog/intellect-1-release)
  - **Loss impact.** The blog reports that int8 pseudo-gradients had no visible effect on the loss curves. — [PI blog](https://primeintellect.ai/blog/intellect-1)
  - Weights are on Hugging Face. — [HF](https://huggingface.co/PrimeIntellect/INTELLECT-1-fp32)
- **OpenDiLoCo** is Prime Intellect's open-source reproduction and scaling of DiLoCo. Its first run went past 1B parameters. — [PI blog](https://primeintellect.ai/blog/intellect-1-release)
- **INTELLECT-2.** "INTELLECT-2: A Reasoning Model Trained Through Globally Decentralized Reinforcement Learning". Prime Intellect, arXiv:2505.07291 (May 2025). It is a 32B model and the first globally distributed RL run at that scale. — [arXiv HTML](https://arxiv.org/html/2505.07291v1); [release blog](https://primeintellect.ai/blog/intellect-2-release)
  - **prime-rl.** The framework decouples rollout generation, training and weight broadcast so the run tolerates unreliable networks. It uses two-step asynchrony to overlap communication with computation. — [arXiv](https://arxiv.org/html/2505.07291v1); [PI blog](https://primeintellect.ai/blog/intellect-2-release)
  - **Supporting components.** SHARDCAST distributes weights to inference workers. TOPLOC verifies rollouts produced by untrusted workers. — [Moonlight review](https://www.themoonlight.io/en/review/intellect-2-a-reasoning-model-trained-through-globally-decentralized-reinforcement-learning)
  - **Results.** The model improves over QwQ-32B on math and code, with a slight drop on IFEval. — [HF model card](https://huggingface.co/PrimeIntellect/INTELLECT-2/blob/refs%2Fpr%2F9/README.md)
  - **Utilization.** A secondary write-up reports 83% compute utilization globally and 96% within the US. I did not verify this against the primary source. — [learnopoly (secondary)](https://learnopoly.com/primeinintellect-liberates-intellect-2-a-32b-reasoning-model-formed-via-learning-to-strengthen-asynchronous-distributed/)
- **INTELLECT-3** is reported to have been trained on a centralized 512-GPU cluster rather than permissionless compute. This rests on single secondary sources and is unverified against Prime Intellect's own materials. — [implicator.ai](https://www.implicator.ai/prime-intellects-intellect-3-open-source-ambition-meets-centralized-reality/); [BlockEden](https://blockeden.xyz/blog/2026/03/13/templar-covenant-72b-bittensor-largest-decentralized-llm-pretraining/)

**Nous Research: DeMo, DisTrO, Psyche**
- **DeMo.** "DeMo: Decoupled Momentum Optimization". Bowen Peng et al. (Nous Research), arXiv:2411.19870, ICLR 2026. — [ICLR 2026 proceedings](https://proceedings.iclr.cc/paper_files/paper/2026/hash/17790fc9b4828206f99a0dea35da3657-Abstract-Conference.html); [arXiv v2](https://arxiv.org/html/2411.19870v2); [ML Anthology](https://mlanthology.org/iclr/2026/peng2026iclr-demo/)
  - **Method.** Each node keeps a local, un-synchronized momentum. Only the fast-moving components are extracted, using a DCT followed by top-k, and only those are communicated. The residual stays in the momentum buffer and acts as error feedback. — [ICLR abstract](https://proceedings.iclr.cc/paper_files/paper/2026/hash/17790fc9b4828206f99a0dea35da3657-Abstract-Conference.html)
  - **Results.** On 300M and 1B models it sends up to 85× less data per GPU than AdamW-DDP at comparable loss and accuracy. — [ICLR abstract](https://proceedings.iclr.cc/paper_files/paper/2026/hash/17790fc9b4828206f99a0dea35da3657-Abstract-Conference.html)
- **Psyche** is Nous's decentralized training network. It implements DisTrO/DeMo in a Rust system with P2P networking. — [Nous Psyche](https://nousresearch.com/nous-psyche/)
  - Its first run, **Consilience**, targets a 40B MLA-architecture model on 20T tokens, with base and annealed releases planned. — [Nous Psyche](https://nousresearch.com/nous-psyche/); [HyperAI (secondary)](https://hyper.ai/en/stories/5a7df889896b6787efb2fb44d83f9ca6)
  - Covenant-72B's paper describes Consilience as using whitelisted participants. — [Covenant-72B arXiv](https://arxiv.org/pdf/2603.08163)

**Templar / Covenant AI: SparseLoCo, MuLoCo, Covenant-72B**
- **SparseLoCo.** "Communication Efficient LLM Pre-training with SparseLoCo" (v1 title: "Overcoming the Communication-Performance Tradeoff in LLM Pretraining"). Amir Sarfi, Benjamin Thérien, Joel Lidin, Eugene Belilovsky (Templar / Mila). arXiv:2508.15706, NeurIPS 2025 (workshop/poster listing). — [arXiv](https://arxiv.org/abs/2508.15706); [NeurIPS](https://neurips.cc/virtual/2025/124278)
  - **Method.** Top-k sparsification is combined with 2-bit quantization of DiLoCo pseudo-gradients, reaching 97–99% sparsity. DiLoCo's outer momentum is replaced by a single error-feedback accumulator, which the authors argue approximates outer momentum locally. — [arXiv HTML v2](https://arxiv.org/html/2508.15706v2)
  - **Ablations.** Top-k selection beats random-k, and 2-bit quantization costs almost nothing in quality. — [arXiv HTML v1](https://arxiv.org/html/2508.15706v1)
  - **Scale and code.** It outperforms DiLoCo on dense models from 178M to 2B and on an MoE model (645M total, 273M active). Code: [github.com/tplr-ai/SparseLoCo](https://github.com/tplr-ai/SparseLoCo). — [alphaXiv](https://alphaxiv.org/abs/2508.15706)
  - **Version caveat.** A 2026 v3 exists. The figures above are from the snippets and were not checked against v3.
- **MuLoCo.** "MuLoCo: Muon is a practical inner optimizer for DiLoCo". Thérien, Huang, Rish, Belilovsky. arXiv:2505.23725 (2025). — [papers.cool](https://papers.cool/arxiv/2505.23725); [Moonlight review](https://www.themoonlight.io/review/muloco-muon-is-a-practical-inner-optimizer-for-diloco)
  - Using Muon as the inner optimizer makes DiLoCo more robust to aggressive compression. With 2-bit quantization and error feedback, MuLoCo matches or beats uncompressed DiLoCo while communicating 8× less. — [papers.cool](https://papers.cool/arxiv/2505.23725)
- **Covenant-72B.** "Covenant-72B: Pre-Training a 72B LLM with Trustless Peers Over-the-Internet". arXiv:2603.08163 (March 2026). It describes permissionless participation on Bittensor Subnet 3, unlike the whitelisted INTELLECT-1 and Consilience runs. — [arXiv](https://arxiv.org/pdf/2603.08163)
  - **Run (secondary reports).** The run completed on March 10, 2026, with more than 70 nodes. It scored 67.1 zero-shot MMLU and reportedly beat LLaMA-2-70B and LLM360 K2 under identical evaluation. — [BlockEden](https://blockeden.xyz/blog/2026/03/13/templar-covenant-72b-bittensor-largest-decentralized-llm-pretraining/); [KuCoin news](https://www.kucoin.com/news/flash/bittensor-subnet-completes-largest-decentralized-llm-pre-training-with-covenant-72b); [simplytao](https://simplytao.ai/blog/covenant-72b-the-largest-decentralized-llm-training-run)
  - **Communication (secondary).** SparseLoCo gives more than 146× compression against dense gradients. Per-round communication was about 70 s, against an estimated 8.3 min for INTELLECT-1. — [BlockEden](https://blockeden.xyz/blog/2026/03/13/templar-covenant-72b-bittensor-largest-decentralized-llm-pretraining/)
  - **Governance (single source).** Covenant AI reportedly left Bittensor on April 10, 2026. — [ownyourmind.ai](https://ownyourmind.ai/tokenomics/templar-bittensor-covenant-exit/)

**Photon: federated pretraining (Flower Labs / Cambridge)**
- "Photon: Federated LLM Pre-Training". Lorenzo Sani, Alex Iacob, Zeyu Cao, Royson Lee, Bill Marino, Yan Gao, Wanru Zhao, Dongqi Cai, Zexi Li, Xinchi Qiu, Nicholas D. Lane (Flower Labs / Univ. of Cambridge). MLSys 2025, arXiv:2411.02908. — [arXiv](https://arxiv.org/pdf/2411.02908); [MLSys 2025](https://proceedings.mlsys.org/paper_files/paper/2025/hash/185087ea328b4f03ea8fd0c8aa96f747-Abstract-Conference.html); [Flower blog](https://flower.ai/blog/2025-05-09-photon)
  - It is a cross-silo FL system for end-to-end pretraining of models up to 7B. The authors report better perplexity than centralized pretraining and roughly 2× faster convergence than DiLoCo. — [MLSys](https://proceedings.mlsys.org/paper_files/paper/2025/hash/185087ea328b4f03ea8fd0c8aa96f747-Abstract-Conference.html)
  - They also report 35% lower wall-time than baseline distributed methods while communicating 64× to 512× less. A key ingredient is small client batches with high learning rates, which federated averaging tolerates well. — [arXiv](https://arxiv.org/pdf/2411.02908)
  - One MLSys poster page shows the placeholder name "SystemX" in place of "Photon", which looks like a page error. — [MLSys poster](https://mlsys.org/virtual/2025/poster/3001)

**Gossip and no-all-reduce methods**
- **NoLoCo.** "NoLoCo: No-all-reduce Low Communication Training Method for Large Models". Gensyn, arXiv:2506.10911 (2025). — [HF papers](https://huggingface.co/papers/2506.10911); [Gensyn blog](https://blog.gensyn.ai/noloco-training-large-models-with-no-all-reduce/)
  - **Method.** Global all-reduce is replaced by pairwise partial weight averaging with a random peer, embedded in a Nesterov-momentum variant.
  - **Results.** About 10× lower sync latency than DiLoCo-style all-reduce (experiments with up to 1,000 internet GPUs) and about 4% faster convergence to the same validation loss, on models from 125M to 6.8B. — [Gensyn research](https://www.gensyn.ai/research/noloco-training-large-models-with-no-all-reduce)
- **GASLoC.** "Unifying Local Communications and Local Updates for LLM Pretraining", arXiv:2606.11081 (2026). It is gossip-based and claims to clearly outperform DiLoCo under heterogeneous bandwidth. I did not verify its numbers. — [arXiv](https://arxiv.org/pdf/2606.11081); [takara summary](https://tldr.takara.ai/p/2606.11081)

**Decentralized-cluster frameworks**
- **DiLoCoX.** "DiLoCoX: A Low-Communication Large-Scale Training Framework for Decentralized Cluster". 0G Labs et al., arXiv:2506.21263 (2025). It pretrains a 107B model over 1 Gbps links by combining pipeline parallelism, a dual optimizer policy, one-step-delay overlap of communication and local training, and adaptive gradient compression. — [arXiv](https://arxiv.org/pdf/2506.21263); [arXiv HTML](https://arxiv.org/html/2506.21263v1)
  - **Throughput.** It reports 3,728 tokens/s for DiLoCoX against 10.4 tokens/s for AllReduce on Qwen1.5-107B. That is 357× higher throughput than AllReduce and 1.35× higher than CocktailSGD. — [arXiv HTML](https://arxiv.org/html/2506.21263v1)
  - **Quality cost.** AllReduce still reaches lower loss (3.90 against 4.20). The 357× is a throughput ratio, not a time-to-target-loss ratio. — [arXiv HTML](https://arxiv.org/html/2506.21263v1)
  - **Ablations.** Throughput drops to 2,197 tokens/s without overlap and to 1,168 tokens/s without compression. — [arXiv HTML](https://arxiv.org/html/2506.21263v1)
  - 0G Labs' press release repeats these claims. It is promotional material. — [The Defiant PR](https://thedefiant.io/news/press-releases/0g-labs-achieves-breakthrough-in-decentralized-ai-training-with-100-billion-parameters)
- **DES-LOC.** "DES-LOC: Desynced Low Communication Adaptive Optimizers for Training Foundation Models", arXiv:2505.22549 (2025). It syncs parameters and Adam optimizer states at different frequencies. I could not retrieve its quantitative claims (see Gaps). — [arXiv](https://arxiv.org/pdf/2505.22549)
- **Memory-efficient decentralized pretraining on distributed GPUs**, arXiv:2602.11543 (2026). It appeared in search results, but I did not read beyond the listing. — [arXiv](https://arxiv.org/pdf/2602.11543)
- **Survey.** "Beyond A Single AI Cluster: A Survey of Decentralized LLM Training", arXiv:2503.11023 (2025). — [arXiv](https://arxiv.org/pdf/2503.11023)

### Inferences
- **The leading recipe** in 2025–2026 combines several pieces: DiLoCo outer loop (H ≈ 50–500 inner steps); Nesterov or error-feedback outer update; pseudo-gradient compression (int8, then 2-bit plus top-k); overlap of sync with compute (one-step delay, streaming fragments). Muon as the inner optimizer appears to make compression more forgiving.
- **Error feedback is the unifying idea** across DeMo, SparseLoCo and MuLoCo. SparseLoCo shows that the error-feedback buffer can replace outer momentum altogether.
- **Compute versus wall-clock.** The NeurIPS 2025 scaling-law paper is the strongest evidence that DiLoCo is not just "worse but cheaper": at a fixed compute budget it can match or beat DP. Most other papers report communication savings or throughput, not equal-quality wall-clock time, so speedup claims should be normalized to time-to-target-loss when comparing.
- **Scale progression of over-the-internet runs.** 10B (INTELLECT-1, late 2024) → 32B RL (INTELLECT-2, 2025) → 40B (Consilience, in progress per Nous) → 72B permissionless (Covenant-72B, March 2026). DiLoCoX's 107B run was on a decentralized cluster, not permissionless.

### Gaps
- **Streaming DiLoCo.** I could not fetch the abstract to confirm its exact headline figure, which I recall from prior knowledge as roughly 100× less bandwidth at similar quality for billion-scale models. Verify at https://arxiv.org/abs/2501.18512.
- **DES-LOC.** Quantitative communication reduction and venue are unverified.
- **Original DiLoCo and OpenDiLoCo papers.** I did not re-verify their arXiv entries this session. From prior knowledge: DiLoCo is Douillard et al., arXiv:2311.08105, with a reported 500× communication reduction on C4; OpenDiLoCo is Jaghouar, Ong, Hagemann, arXiv:2407.07852. Both need checking.
- **Other 2025–2026 DiLoCo-family work** I know of but did not verify, so it is not listed as findings: DeepMind "Decoupled DiLoCo"/async variants, HALoS, Pier, CocktailSGD follow-ups, and Pluralis "protocol learning"/compressed model-parallel training. Verify individually.
- **Covenant-72B primary-paper details.** Tokens trained, the exact compression factor and peer-validation ("Gauntlet") specifics come only from secondary sources.
- **Psyche Consilience status** as of October 2026 (finished or not, final quality) was not confirmed.

## Q2. Parameter-efficient federated fine-tuning for speed: LoRA variants, FedSA-LoRA, freezing, zeroth-order (FedKSeed, FedMeZO)

### Takeaway
Federated LoRA research has moved from "just FedAvg the adapters" to fixing a known problem: averaging A and B separately does not equal averaging the product BA. The main responses are:
- **Exact or residual aggregation:** FedEx-LoRA, LoRA-FAIR.
- **Stacking for heterogeneous ranks:** FLoRA.
- **Selective sharing:** FedSA-LoRA shares only A. FFA-LoRA freezes A.

The selective-sharing approach also roughly halves upload size. Zeroth-order methods sit at the extreme end of communication cost. FedKSeed exchanges only seeds and scalars, under 18 KB per round, more than 1000× less, at the price of many more steps and sensitivity to hyperparameters.

### Cited Findings
- **FedSA-LoRA.** "Selective Aggregation for Low-Rank Adaptation in Federated Learning". Pengxin Guo et al., ICLR 2025, arXiv:2410.01463. — [ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/f53a37f820d5be5930415d964f4a0187-Abstract-Conference.html); [arXiv](https://arxiv.org/pdf/2410.01463)
  - **Observation.** The A matrices learn general knowledge and the B matrices learn client-specific knowledge.
  - **Method.** Both are trained locally, but only A is uploaded and aggregated, so B stays personalized. The paper extends this to FedSA-rsLoRA and FedSA-VeRA. Code is public. — [ICLR abstract](https://proceedings.iclr.cc/paper_files/paper/2025/hash/f53a37f820d5be5930415d964f4a0187-Abstract-Conference.html)
  - **Main baseline.** FFA-LoRA freezes A and aggregates only B. — [ICLR paper PDF](https://proceedings.iclr.cc/paper_files/paper/2025/file/f53a37f820d5be5930415d964f4a0187-Paper-Conference.pdf)
- **FedEx-LoRA.** "Exact Aggregation for Federated and Efficient Fine-Tuning of (Large Language / Foundation) Models". Raghav Singhal, Kaustubh Ponkshe, Praneeth Vepakomma (MBZUAI). ACL 2025 (oral), arXiv:2410.09432. — [arXiv v4](https://arxiv.org/abs/2410.09432v4); [ACL Anthology](https://preview.aclanthology.org/setup/2025.acl-long.67); [MBZUAI repository](https://irep.mbzuai.ac.ae/items/7659ad10-d974-4552-9eb1-5689e894116f)
  - It keeps standard averaging of A and B but folds the residual error, avg(BA) − avg(B)·avg(A), into the frozen base weights, which makes the aggregate update exact.
  - It mainly assumes clients share the same rank. — [arXiv HTML](https://arxiv.org/html/2410.09432v4)
- **FLoRA.** "FLoRA: Federated Fine-Tuning Large Language Models with Heterogeneous Low-Rank Adaptations". Ziyao Wang et al., NeurIPS 2024. — [NeurIPS 2024](https://proceedings.neurips.cc/paper_files/paper/2024/hash/28312c9491d60ed0c77f7fff4ad86dd1-Abstract.html); [Sony AI](https://ai.sony/publications/FLoRA-Federated-Fine-Tuning-Large-Language-Models-with-Heterogeneous-Low-Rank-Adaptations)
  - It uses stacking-based, "noise-free" aggregation of client adapters with different ranks. The authors argue earlier averaging was mathematically incorrect.
- **LoRA-FAIR.** "LoRA-FAIR: Federated LoRA Fine-Tuning with Aggregation and Initialization Refinement", arXiv:2411.14961. — [arXiv](https://arxiv.org/pdf/2411.14961)
  - It corrects both aggregation bias and client "initialization lag".
  - It is designed for equal ranks. Its heterogeneous-rank extension uses HetLoRA-style zero-padding and truncation.
  - In its experiments FLoRA failed to converge, which the authors attribute to FLoRA adding updates directly to the base model.
- **raFLoRA.** "Preventing Rank Collapse in Federated Low-Rank Adaptation", arXiv:2602.13486 (2026). It argues that HetLoRA's zero-padding causes aggregation bias and rank collapse, and proposes rank-partitioned aggregation. — [arXiv HTML](https://arxiv.org/html/2602.13486)
- **EcoLoRA** (EMNLP 2025). A communication-efficient federated LoRA method that reports up to 79% less communication time and up to 65% less total training time. — [search snippet via arXiv:2504.14667 results](https://arxiv.org/abs/2504.14667v2) (snippet; primary EcoLoRA URL not retrieved)
- **FedKSeed.** "Federated Full-Parameter Tuning of Billion-Sized Language Models with Communication Cost under 18 Kilobytes". Zhen Qin, Daoyuan Chen, Bingchen Qian, Bolin Ding, Yaliang Li, Shuiguang Deng (Zhejiang Univ. / Alibaba). ICML 2024, arXiv:2312.06353. — [PMLR](https://proceedings.mlr.press/v235/qin24a.html); [arXiv](https://arxiv.org/pdf/2312.06353)
  - **Method.** Zeroth-order full-parameter tuning over a finite pool of K random seeds. Only seeds and scalar projected gradients are exchanged. K seeds are encoded by a single 4-byte seed. — [arXiv v5](https://arxiv.org/html/2312.06353v5)
  - **Cost.** Under 18 KB per round, more than 1000× less communication, and inference-level memory.
  - **FedKSeed-Pro** adds probability-differentiated seed sampling: fewer seeds for the same accuracy and faster sync to the latest global model.
  - **Code** is in the FederatedScope repository, FedKSeed branch. — [arXiv](https://arxiv.org/pdf/2312.06353)
- **FedMeZO.** "On the Convergence of Zeroth-Order Federated Tuning for Large Language Models". Zhenqing Ling et al., arXiv:2402.05926 (KDD 2024 per prior knowledge, not verified). — [arXiv](https://arxiv.org/abs/2402.05926v3)
  - It gives the first convergence analysis of MeZO combined with FL for LLMs. Experiments report faster convergence than first-order FedAvg with much lower GPU memory. It also proposes a theory-informed personalized learning-rate rule.
  - A later survey notes that FedMeZO uses LoRA to cut communication and that FedKSeed then improves on it with finite seeds. — [Survey arXiv:2508.16261](https://arxiv.org/pdf/2508.16261)
- **Zeroth-order FL with adaptive momentum** (ACL 2026, Chen et al.). It targets the unstable convergence of zeroth-order federated tuning. — [ACL 2026](https://aclanthology.org/2026.acl-long.1851.pdf) (snippet)
- **Resource-constrained zeroth-order federated fine-tuning.** "Efficient Zero-Order Federated Finetuning of Language Models for Resource-Constrained Devices", arXiv:2502.10239. This is device-oriented and out of scope. — [arXiv](https://arxiv.org/html/2502.10239v2)
- **Survey.** "On the Evolution of Federated Post-Training Large Language Models: A Model Accessibility View", arXiv:2508.16261 (2025). — [arXiv](https://arxiv.org/pdf/2508.16261)

### Inferences
- **Per-round traffic.**
  - Full fine-tuning: O(d) floats.
  - LoRA: O(r(m+n)) per layer.
  - FedSA or FFA: about half of LoRA.
  - Zeroth-order seed methods: O(K) scalars, which is kilobytes.
  For wall-clock speed in cross-silo settings with good links, LoRA-family methods usually win because zeroth-order methods need many more steps. Zeroth-order methods win when links are very slow or uploads are metered.
- **Exactness of aggregation** (FedEx-LoRA, FLoRA, LoRA-FAIR) affects convergence speed as much as it affects final accuracy. Biased aggregation behaves like extra client drift.
- **Conflicting evidence.** The literature disagrees on FLoRA's robustness (FLoRA's authors against LoRA-FAIR's). I found no neutral benchmark that settles it. FlowerTune (Q4) is the closest common testbed.

### Gaps
- No verified quantitative speedup numbers for FedSA-LoRA, FedEx-LoRA or LoRA-FAIR. The snippets gave qualitative claims only.
- I did not gather prompt/adapter-tuning-specific federated LLM papers from 2025–2026, or layer-freezing and progressive-unfreezing schemes (e.g., FedPT, layer-dropping), because the search budget ran out.
- The EcoLoRA primary arXiv link was not retrieved.
- FedMeZO's venue (KDD 2024) and quantitative speedups are not verified.

## Q3. Faster convergence: adaptive optimizers, momentum, drift correction, asynchrony, client selection

### Takeaway
In LLM-scale low-communication training, the "outer optimizer" is the main lever for convergence.
- **Classic DiLoCo** uses an AdamW inner optimizer with a Nesterov outer step.
- **MuLoCo** swaps the inner optimizer for Muon and gains robustness to compression.
- **SparseLoCo** replaces outer momentum with error feedback.
- **DES-LOC** desynchronizes the optimizer states.
- **NoLoCo** embeds gossip averaging in Nesterov momentum.

Asynchrony is most mature in RL post-training (prime-rl's two-step async). For federated fine-tuning, the evidence on straggler and asynchrony handling is thin, so this section is weak and needs follow-up.

### Cited Findings
- **Outer and inner optimizer choices.**
  - **MuLoCo.** Muon as the inner optimizer tolerates 2-bit quantization with error feedback and matches or beats uncompressed DiLoCo at 8× less communication. — [papers.cool](https://papers.cool/arxiv/2505.23725)
  - **SparseLoCo.** An error-feedback accumulator replaces global outer momentum and acts as a local approximation of it. — [arXiv](https://arxiv.org/abs/2508.15706)
  - **DeMo.** Decoupled local momentum combined with DCT/top-k exchange of fast components. — [ICLR 2026](https://proceedings.iclr.cc/paper_files/paper/2026/hash/17790fc9b4828206f99a0dea35da3657-Abstract-Conference.html)
  - **NoLoCo.** A Nesterov-momentum variant with partial pairwise averaging, about 4% faster convergence to the same validation loss than DiLoCo. — [HF papers](https://huggingface.co/papers/2506.10911)
- **Hyperparameters and batch size.**
  - DiLoCo's optimal global batch size is larger than DP's, and it is more robust at scale, according to fitted scaling laws for loss and hyperparameters. — [NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/hash/99acb4c087266e80b547aed79247266b-Abstract-Conference.html)
  - Photon attributes faster convergence (about 2× over DiLoCo) partly to small local batches with high learning rates under federated averaging. — [arXiv:2411.02908](https://arxiv.org/pdf/2411.02908)
- **Asynchrony and overlap.**
  - **Eager updates.** The local outer gradient stands in while the global all-reduce is in flight. — [arXiv:2502.12996](https://arxiv.org/abs/2502.12996)
  - **Streaming DiLoCo.** Overlap is limited to about 5 steps before quality degrades. — [arXiv:2606.22768](https://arxiv.org/pdf/2606.22768)
  - **DiLoCoX.** A one-step-delay overlap raised throughput from 2,197 to 3,728 tokens/s at 107B. — [arXiv HTML](https://arxiv.org/html/2506.21263v1)
  - **INTELLECT-2.** Asynchronous decoupling of rollout, training and broadcast for globally distributed RL. — [arXiv:2505.07291](https://arxiv.org/html/2505.07291v1)
- **Stragglers in federated LLM fine-tuning.** Resource heterogeneity across edge servers produces very different per-client training times and a severe straggler problem at aggregation. — [arXiv:2404.06448](https://arxiv.org/pdf/2404.06448)
- **Zeroth-order convergence.**
  - FedMeZO gives convergence theory and a personalized learning-rate rule. — [arXiv:2402.05926](https://arxiv.org/abs/2402.05926v3)
  - FedKSeed warns that an overly large learning rate can cause divergence. — [arXiv:2312.06353](https://arxiv.org/pdf/2312.06353)
  - Adaptive momentum is applied to zeroth-order FL tuning. — [ACL 2026](https://aclanthology.org/2026.acl-long.1851.pdf)

### Inferences
- The inner/outer decomposition, as in FedOpt, has become the main design space. Recent gains come from rethinking the outer state (momentum against error feedback) rather than from classic drift-correction terms such as SCAFFOLD or FedProx. I found no 2025–2026 LLM-scale evidence that SCAFFOLD-style control variates beat a well-tuned DiLoCo outer Nesterov step.
- Async overlap gives limited returns: about 5 steps for Streaming DiLoCo, and Factored Gossip DiLoCo found negligible utilization gains. Overlap matters most when sync time is comparable to compute time per round.

### Gaps
- I found no verified 2025–2026 paper specifically on **client selection** for federated LLM training, or on **FedAdam/FedYogi-style server optimizers** benchmarked at LLM scale. The search budget was exhausted.
- I found no verified fully asynchronous federated LLM fine-tuning method (staleness-aware) with quantitative speedups.
- From prior knowledge, unverified: Google's "asynchronous local-SGD" LLM work (Liu et al., 2024) and "DiPaCo" (Douillard et al., 2024) are relevant. Verify before citing.

## Q4. Systems: split/pipeline federated training, FlowerTune, FedML, throughput benchmarks

### Takeaway
The main 2025 public benchmark for federated LLM fine-tuning is **FlowerTune** (NeurIPS 2025 Datasets and Benchmarks). It covers 4 domains and 26 base models and includes system metrics for communication and memory. For pretraining, the systems are Photon (Flower), prime/prime-rl (Prime Intellect), Psyche (Nous), Templar's SparseLoCo stack and DiLoCoX (0G). Split federated LoRA work mostly targets edge or wireless settings, which other researchers cover.

### Cited Findings
- **FlowerTune.** "FlowerTune: A Cross-Domain Benchmark for Federated Fine-Tuning of Large Language Models". Yan Gao et al., with Nicholas Lane and Lingjuan Lyu among the authors (Flower Labs and collaborators). NeurIPS 2025 Datasets and Benchmarks, arXiv:2506.02961. — [arXiv](https://arxiv.org/pdf/2506.02961); [NeurIPS](https://proceedings.neurips.cc/paper_files/paper/2025/hash/563991b5c8b45fe75bea42db738223b2-Abstract-Datasets_and_Benchmarks_Track.html); [Leaderboard](https://flower.ai/benchmarks/llm-leaderboard)
  - **Domains and datasets.**
    - General NLP: alpaca-gpt4.
    - Finance: fingpt-sentiment-train.
    - Medical: medical-flashcards.
    - Code: code-alpaca-20k.
    Each dataset is sharded to simulate institutions. — [arXiv HTML](https://arxiv.org/html/2506.02961v2)
  - **Scope.** 26 base models are compared. The template is model-, method- and aggregation-agnostic, with tools for communication-overhead and memory metrics. — [arXiv](https://arxiv.org/pdf/2506.02961)
- **Photon** is built on Flower: a cross-silo federated pretraining system up to 7B. — [Flower blog](https://flower.ai/blog/2025-05-09-photon)
- **prime-rl, SHARDCAST and TOPLOC** form Prime Intellect's open-source stack for async decentralized RL. — [arXiv:2505.07291](https://arxiv.org/html/2505.07291v1)
- **Psyche** is Rust-based with P2P networking and DisTrO/DeMo. — [Nous](https://nousresearch.com/nous-psyche/)
- **SparseLoCo** code. — [GitHub tplr-ai/SparseLoCo](https://github.com/tplr-ai/SparseLoCo) (per [alphaXiv](https://alphaxiv.org/abs/2508.15706))
- **DiLoCoX** combines pipeline parallelism with DiLoCo, overlap and adaptive compression for 107B over 1 Gbps. — [arXiv:2506.21263](https://arxiv.org/pdf/2506.21263)
- **Split federated learning combined with LoRA** (FedsLLM / SflLLM), arXiv:2504.14667 (2025). It jointly optimizes split point, LoRA rank, subchannel and power allocation to minimize training delay, with comparable accuracy and lower client compute. This is wireless/edge-oriented, so it is mentioned only briefly. — [arXiv v2](https://arxiv.org/abs/2504.14667v2)
- **Automated federated pipeline for parameter-efficient fine-tuning of LLMs**, arXiv:2404.06448. It addresses stragglers via automated per-client configuration. — [arXiv](https://arxiv.org/pdf/2404.06448)
- **SplitLoRA** (2024) is a split-learning plus FL framework for parameter-efficient LLM fine-tuning. Only a secondary summary was found. — [PromptLayer summary](https://www.promptlayer.com/research-papers/splitlora-a-split-parameter-efficient-fine-tuning-framework-for-large-language-models)

### Inferences
- No common throughput benchmark exists for decentralized pretraining. Each team reports a different metric: Prime Intellect reports sync-time share and utilization, DiLoCoX tokens/s, NoLoCo sync latency and Covenant per-round communication seconds. A tokens-per-second-per-GPU at fixed target loss under specified bandwidth and latency would be the natural standard. It does not exist yet.

### Gaps
- **FedML / TensorOpera**, **FederatedScope-LLM** and **OpenFedLLM**: I found no 2025–2026 primary sources this session. From prior knowledge, OpenFedLLM (Ye et al., KDD 2024, arXiv:2402.06954) and FederatedScope-LLM (Kuang et al., KDD 2024, arXiv:2309.00363) are the main frameworks. These are unverified here.
- I found no quantitative throughput comparisons from the FlowerTune leaderboard (e.g., communication MB per round for winning entries).
- I found no verified pipeline-parallel-over-internet systems such as SWARM follow-ups or Pluralis.

## Q5. Open problems

### Takeaway
The cited work points to these open problems:
- **Fair comparison metrics:** time-to-target-loss at a given bandwidth.
- **Scaling laws for compressed and gossip variants:** only plain DiLoCo has them.
- **Throughput versus final-loss trade-offs:** DiLoCoX's 357× throughput came with worse loss.
- **Trust and verification in permissionless settings.**
- **Heterogeneous LoRA aggregation:** results conflict across papers.
- **Asynchrony limits:** overlap gains saturate quickly.
- **Communication bottleneck in RL post-training:** it shifts to weight broadcast.

### Cited Findings
- **Throughput versus quality.** DiLoCoX's AllReduce baseline reaches lower loss (3.90 against 4.20) despite 357× lower throughput, so the time-to-quality trade-off is unresolved. — [arXiv HTML](https://arxiv.org/html/2506.21263v1)
- **Limits of overlap and asynchrony.** Streaming DiLoCo degrades beyond 5 overlap steps, and its overlap gives negligible utilization gains in low-bandwidth regimes according to an independent 2026 study. — [arXiv:2606.22768](https://arxiv.org/pdf/2606.22768)
- **Scaling-law coverage.** Scaling laws exist for DiLoCo up to 10B validation. — [NeurIPS 2025](https://papers.neurips.cc/paper_files/paper/2025/hash/99acb4c087266e80b547aed79247266b-Abstract-Conference.html)
  - SparseLoCo was tested only up to 2B dense and a 645M MoE in the paper. — [alphaXiv](https://alphaxiv.org/abs/2508.15706)
  - NoLoCo was tested only up to 6.8B. — [Gensyn](https://www.gensyn.ai/research/noloco-training-large-models-with-no-all-reduce)
- **Trust and verification.** Permissionless training (Covenant-72B) contrasts with the whitelisted INTELLECT-1 and Consilience runs. INTELLECT-2 needed TOPLOC to verify untrusted rollouts. — [arXiv:2603.08163](https://arxiv.org/pdf/2603.08163); [arXiv:2505.07291](https://arxiv.org/html/2505.07291v1)
- **Sustainability of decentralized runs.** Reports say INTELLECT-3 used centralized compute and that Covenant AI left Bittensor. Both rest on single secondary sources. — [implicator.ai](https://www.implicator.ai/prime-intellects-intellect-3-open-source-ambition-meets-centralized-reality/); [ownyourmind.ai](https://ownyourmind.ai/tokenomics/templar-bittensor-covenant-exit/)
- **Federated LoRA aggregation.** Heterogeneous-rank aggregation is unresolved: FLoRA fails in LoRA-FAIR's setup, and zero-padding causes rank collapse according to raFLoRA. — [arXiv:2411.14961](https://arxiv.org/pdf/2411.14961); [arXiv:2602.13486](https://arxiv.org/html/2602.13486)
- **Zeroth-order stability.** Zeroth-order federated tuning is sensitive to the learning rate and perturbation scale. — [arXiv:2312.06353](https://arxiv.org/pdf/2312.06353); [ACL 2026](https://aclanthology.org/2026.acl-long.1851.pdf)
- **Reporting inconsistency.** INTELLECT-1's own blog and report give different reduction figures (400× against 2000×). — [blog](https://primeintellect.ai/blog/intellect-1-release); [arXiv](https://arxiv.org/html/2412.01152v1)

### Inferences
- The most valuable next contributions are likely:
  1. Unified scaling laws covering compression ratio, sync interval H and replica count M jointly.
  2. A standard bandwidth- and latency-parameterized benchmark that reports time-to-target-loss.
  3. Theory for why error feedback can replace outer momentum.
  4. Combining federated, non-IID, privacy-constrained data, as in Photon, with the extreme compression developed for the decentralized-compute (IID) setting. Most DiLoCo-family results assume IID shards, and Photon is one of the few that frames the problem as cross-silo FL.
- MoE models and RL post-training are under-explored for low-communication methods. SparseLoCo has one small MoE result, and INTELLECT-2 is RL-only with async broadcast.

### Gaps
- No verified 2026 position paper or survey of open problems was found beyond the 2025 survey (arXiv:2503.11023). The open problems above are synthesized from individual papers.
- Privacy (differential privacy or secure aggregation) combined with compression for federated LLMs was not researched, because it is out of scope and the search budget ran out.
