# Privacy in Federated Learning (with emphasis on federated LLMs / foundation models), 2024-2026

> **Method and provenance note (read first).** These notes come from 16 web searches run on 2026-10-30. Direct page fetching was blocked in this environment: `arxiv.org` and `research.google` failed DNS resolution because they are outside the session's allowed network. The shared web-search budget then ran out before the frameworks and regulation searches could run. So:
> (a) every 2025-2026 paper claim below rests on **search-result snippets and abstracts**, not full-text reads; numbers have not been checked against the PDFs;
> (b) items tagged **[prior knowledge, URL not re-verified]** are well-known foundational papers, repos or legal texts cited from the researcher's background knowledge, with canonical URLs that were not re-fetched in this session. The report writer should treat them as background and spot-check them if possible;
> (c) arXiv IDs of the form 26xx.xxxxx are 2026 preprints. Most are **not yet peer-reviewed** unless a venue is stated.

---

## Attacks: gradient inversion / data reconstruction (incl. LLMs and LoRA), membership inference, attribute inference, memorization/extraction, poisoning/backdoors

### Takeaway
Since 2024, text gradient inversion has gone from approximate, small-batch recovery to **exact full-batch recovery on LLMs**: DAGER at NeurIPS 2024, followed by FedSpy-LLM and TIGER in 2026. Attacks now also target **LoRA/PEFT updates** directly: MineGrad (2026) is a malicious-server attack that reconstructs analytically from LoRA gradients. Membership inference has been specialized to federated/PEFT LLMs (ProjRes 2026, LoRA-Leak 2025, ICP-MIA at NDSS 2026). Verbatim cross-client extraction has been shown in federated LLM fine-tuning (2025). Split-learning LLM pipelines leak prompts and responses through activations (PIDI and PrivPair, 2026). The main counterpoint is one 2025 realism study, which argues that gradient inversion is far less practical against modern, performance-optimized vision models.

### Cited Findings

**A. Gradient inversion / data reconstruction on language models**

- *Background (foundational, pre-2024)* **[prior knowledge, URL not re-verified]**
  - **DLG**, "Deep Leakage from Gradients": Zhu, Liu, Han, NeurIPS 2019. https://arxiv.org/abs/1906.08935
  - **TAG** (Deng et al., EMNLP Findings 2021) and **LAMP** (Balunović, Dimitrov, Jovanović, Vechev, NeurIPS 2022, https://arxiv.org/abs/2202.08827). These first showed that text could be reconstructed from transformer gradients, but only approximately and for small batches.
  - **FILM**, "Recovering Private Text in Federated Learning of Language Models": Gupta et al., NeurIPS 2022, https://arxiv.org/abs/2205.08514. It uses memorization in the *model* together with the gradients to recover sentences even at large batch sizes.
  - **Decepticons** (Fowl et al., ICLR 2023, https://arxiv.org/abs/2201.12675) and **"When the Curious Abandon Honesty"** (Boenisch et al., EuroS&P 2023, https://arxiv.org/abs/2112.02918). Both are malicious-server attacks that modify the model so that individual user text can be read out of the updates.
- **DAGER: Exact Gradient Inversion for Large Language Models.** Petrov, Dimitrov, Baader, Müller, Vechev (ETH Zurich / INSAIT, SRI Lab). NeurIPS 2024, DOI 10.52202/079017-2787.
  - Core idea: it exploits the **low-rank structure of self-attention gradients** and the **discreteness of token embeddings** to test whether a candidate token sequence is in the client batch. It uses exhaustive search for encoders and greedy search for decoders.
  - Threat model: honest-but-curious server, no data prior.
  - Results: exact full-batch recovery up to batch size 128, about 20x faster at the same batch size, about 10x larger batches than prior attacks, and ROUGE-1/2 > 0.99.
  - Caveat: the paper also reports partial or complete failures for GPT-2 at batch sizes 64 and 128. Decoder recovery is limited when batch×sequence approaches the embedding dimension *d*.
  - Sources: [arXiv 2405.15586](https://arxiv.org/abs/2405.15586); [NeurIPS proceedings mirror](https://mlanthology.org/neurips/2024/petrov2024neurips-dager/); [ACM DL](https://dl.acm.org/doi/10.5555/3737916.3740703).
  - Code: the authors' SRI Lab GitHub (`insait-institute/dager-gradient-inversion`) **[prior knowledge, URL not re-verified]**.
- **FedSpy-LLM: Towards Scalable and Generalizable Data Reconstruction Attacks from Gradients on LLMs.** arXiv 2604.06297, 2026 preprint.
  - The title frames it as scaling gradient reconstruction attacks to LLMs and generalizing them across architectures.
  - Its abstract and numbers were not visible in the search snippets. Authors are unverified.
  - Source: [arXiv PDF 2604.06297](https://arxiv.org/pdf/2604.06297).
- **TIGER: Inverting Transformer Gradients via Embedding-Subspace Distance Optimization.** arXiv 2606.18312, 2026.
  - The title indicates an optimization attack based on embedding-subspace distance, which is a successor line to DAGER's subspace test.
  - Details were not visible in the snippets.
  - Source: [arXiv PDF 2606.18312](https://arxiv.org/pdf/2606.18312).
- **MineGrad: Gradient Inversion Attacks on LoRA Fine-Tuning.** arXiv 2608.01521, Aug 2026.
  - Core idea: a malicious server crafts the setup so that **fine-tuning data is embedded in the shared LoRA gradients and can be reconstructed analytically**.
  - Claimed novelty: it works on **both language and vision** tasks, needs no expensive adversarial pretraining on public data, and works **even when the number of training tokens exceeds the LoRA rank**. That removes the assumption earlier PEFT attacks needed.
  - Source: [arXiv PDF 2608.01521](https://arxiv.org/pdf/2608.01521).
  - Implication: it undercuts the folk belief that low-rank adapters are "private by compression".
- **Geminio: Language-Guided Gradient Inversion Attacks in Federated Learning.** arXiv 2411.14937, 2024 (ICCV 2025 per the authors **[venue not re-verified]**).
  - Core idea: an attacker uses a vision-language model and a **natural-language query** to steer a malicious global model, so that gradients preferentially leak the samples matching the attacker's semantic target.
  - Source: [arXiv 2411.14937](https://arxiv.org/pdf/2411.14937).
- **Practical Feasibility of Gradient Inversion Attacks in Federated Learning.** arXiv 2508.19819, 2025.
  - This is a counter-evidence study on **image** FL. It concludes that modern, performance-optimized architectures largely resist meaningful reconstruction under realistic training settings, and that many published successes depend on unrealistic choices.
  - Sources: [arXiv abs](https://arxiv.org/abs/2508.19819); [HTML](https://arxiv.org/html/2508.19819v1).
  - Note: it does **not** cover LLM text inversion, where DAGER-style attacks remain strong.
- **Other inversion work.**
  - "Exploring the Vulnerabilities of FL: A Deep Dive into Gradient Inversion Attacks" (arXiv 2503.11514) is a 2025 systematization: [arXiv](https://arxiv.org/pdf/2503.11514).
  - "Gradient Inversion in Federated Reinforcement Learning" (arXiv 2512.00303): [arXiv](https://arxiv.org/pdf/2512.00303).
  - A curated list covering 2025 inversion work, including PEFT-specific entries and multi-step trajectory inversion (arXiv 2509.22082), accompanies an **IEEE TPAMI 2026** survey by Pengxin Guo et al.: [GitHub Awesome-Gradient-Inversion-Attacks](https://github.com/Pengxin-Guo/Awesome-Gradient-Inversion-Attacks).

**B. Membership inference (MIA) against federated / PEFT LLMs**

- **ProjRes, "Toward Efficient Membership Inference Attacks against Federated Large Language Models: A Projection Residual Approach."** arXiv 2604.21197, 2026.
  - It argues that PEFT (adapters/LoRA) shrinks the uploaded gradients and narrows the classical gradient-based MIA surface. It proposes a projection-residual statistic computed on client updates.
  - Evaluated against **7 baseline MIAs, 4 datasets, and 4 LLMs**.
  - Source: [arXiv HTML](https://arxiv.org/html/2604.21197v1).
- **LoRA-Leak: Membership Inference Attacks Against LoRA Fine-tuned Language Models.** arXiv 2507.18302, 2025. It is a MIA framework specialized to LoRA fine-tuned models. Source: [arXiv](https://arxiv.org/pdf/2507.18302).
- **ICP-MIA, "In-Context Probing for Membership Inference in Fine-Tuned Language Models."** NDSS 2026, arXiv 2512.16292.
  - It probes membership through in-context behaviour rather than shadow fine-tuning.
  - It reports that **higher LoRA rank increases MIA success**, while **4-bit QLoRA appears to reduce it**.
  - Sources: [NDSS PDF](https://www.ndss-symposium.org/wp-content/uploads/2026-f892-paper.pdf); [arXiv](https://arxiv.org/pdf/2512.16292).
- **FISGuard: Defending Against Membership Inference via Fixed Input Subspaces.** arXiv 2608.27836, 2026.
  - It is set in federated LLM fine-tuning with PEFT, where only low-rank or adapter parameters are communicated.
  - It proposes a defense that constrains updates to fixed input subspaces.
  - Source: [arXiv](https://arxiv.org/pdf/2608.27836).
- **Related centralized MIA work used as baselines.**
  - "Membership Inference on LLMs in the Wild" (arXiv 2601.11314): [arXiv](https://arxiv.org/pdf/2601.11314).
  - MIAs against fine-tuned diffusion language models (arXiv 2601.20125): [arXiv](https://arxiv.org/pdf/2601.20125).
  - Dataset-level inference, "Scaling Up Membership Inference" (NAACL Findings 2025): [ACL Anthology](https://aclanthology.org/2025.findings-naacl.234.pdf).
- *Background* **[prior knowledge, URL not re-verified]**
  - Nasr, Shokri, Houmansadr, "Comprehensive Privacy Analysis of Deep Learning: white-box MIAs against centralized and federated learning", IEEE S&P 2019, https://arxiv.org/abs/1812.00910.
  - Carlini et al., "Membership Inference Attacks From First Principles" (LiRA), IEEE S&P 2022, https://arxiv.org/abs/2112.03570.

**C. Attribute / property inference**

- *Background* **[prior knowledge, URL not re-verified]**: Melis, Song, De Cristofaro, Shmatikov, "Exploiting Unintended Feature Leakage in Collaborative Learning", IEEE S&P 2019, https://arxiv.org/abs/1805.04049.
- Recent surveys cover property and attribute inference in FL alongside reconstruction and MIA:
  - "The Federation Strikes Back" (arXiv 2405.03636, rev. Mar 2025; ACM Computing Surveys): [arXiv](https://arxiv.org/abs/2405.03636); [ACM DOI 10.1145/3724113](https://dl.acm.org/doi/10.1145/3724113).
  - "On the Security and Privacy of Federated Learning: A Survey with Attacks, Defenses, Frameworks, Applications, and Future Directions" (arXiv 2508.13730, Aug 2025, more than 200 papers): [arXiv](https://arxiv.org/abs/2508.13730).
- No 2025-2026 attribute-inference paper specific to federated LLMs surfaced in these searches (see Gaps).

**D. Memorization and training-data extraction in federated LLMs**

- *Background*: Ramaswamy et al. (Google), "Training Production Language Models without Memorizing User Data", 2020.
  - It covers the first consumer-scale NWP model trained with FL + **DP-FedAvg** and reports very low unintended memorization, measured with canary/secret-sharer tests.
  - Source: [arXiv 2009.10031](https://arxiv.org/abs/2009.10031).
- *Background*: Thakkar et al., "Understanding Unintended Memorization in Federated Learning".
  - It finds that FedAvg itself (user-level clustering of data) reduces memorization, and DP-FedAvg reduces it further.
  - Source: [ResearchGate](https://www.researchgate.net/publication/342198125_Understanding_Unintended_Memorization_in_Federated_Learning).
- An LLM privacy survey reports that FL alone reduced the number of extracted canaries, and adding DP reduced extraction further, in some cases to zero. Source: [arXiv 2312.06717](https://arxiv.org/pdf/2312.06717).
- Another survey warns that FL does not eliminate risk, because updates can be exploited. It also notes that DP-FL for LLMs has scaling problems: large payloads and a poor signal-to-noise ratio for rare tokens. Source: [arXiv 2310.01424](https://arxiv.org/pdf/2310.01424).
- **"Simple Yet Effective: Extracting Private Data Across Clients in Federated Fine-Tuning of Large Language Models."** arXiv 2506.06060, 2025.
  - It shows **verbatim extraction of other clients' private data** from the federated fine-tuned LLM.
  - Federated models memorize **less than centrally trained ones but still leak partial training data**.
  - Source: [arXiv](https://arxiv.org/pdf/2506.06060).
- **"Loss Landscape Poisoning: Targeted Extraction of Unseen Training Data from LLMs."** arXiv 2606.17110, 2026.
  - It links poisoning to extraction: poisoned data shapes the model so that a *later-seen* target's data becomes extractable.
  - This is directly relevant to collaborative or federated fine-tuning, where some clients may be adversarial.
  - Source: [arXiv](https://arxiv.org/pdf/2606.17110).
- **Privacy-preserving data deduplication for FL of LMs** (EP-MPD; arXiv 2407.08152 / IACR ePrint 2024/1151).
  - Duplicates drive memorization, and this work performs **cross-client deduplication with private set intersection-style cryptography** before federated LM training.
  - Sources: [arXiv](https://arxiv.org/pdf/2407.08152); [ePrint](https://eprint.iacr.org/2024/1151.pdf).

**E. Split learning / split LLM fine-tuning leakage**

- **"A Survey on Split Learning for LLM Fine-Tuning: Models, Systems, and Privacy Optimizations."** arXiv 2604.24468, 2026.
  - It documents that attackers can reconstruct inputs from transmitted "smashed data", that is, activations, through model inversion.
  - It describes **BiSR**, a bidirectional attack that combines forward-activation inversion with backward gradient matching for LLMs.
  - Source: [arXiv HTML](https://arxiv.org/html/2604.24468v1).
- **PIDI, "From Prompts to Responses: Dual-Sided Data Leakage and Defense in Split Large Language Models."** arXiv 2606.14210, 2026.
  - It attacks a head-body-tail split LLM and recovers **both input prompts and generated responses** at inference time.
  - Source: [arXiv](https://arxiv.org/html/2606.14210).
- **PrivPair, "Privacy-Preserving Split Learning for Federated LLM Fine-Tuning."** arXiv 2609.09794, 2026.
  - It shows a "zero-cost decoder": the server simply runs the client's split layers and output head on received activations.
  - **Reconstruction quality increases as fine-tuning proceeds.**
  - It evaluates defenses such as DP on smashed data.
  - Source: [arXiv](https://arxiv.org/html/2609.09794v1).
- **SIRA** (Int. J. Information Security, 2026) shows passive semantic text reconstruction from smashed data in split-learning LM phishing detectors. Source: [Springer](https://link.springer.com/article/10.1007/s10207-026-01311-4).
- *Older*:
  - FORA, a feature-oriented reconstruction attack (CVPR 2024): [CVF](https://openaccess.thecvf.com/content/CVPR2024/papers/Xu_A_Stealthy_Wrongdoer_Feature-Oriented_Reconstruction_Attack_against_Split_Learning_CVPR_2024_paper.pdf).
  - A 2025 taxonomy of split-learning attacks and defenses: [arXiv 2505.05872](https://arxiv.org/pdf/2505.05872).
  - GAN-based split-learning reconstruction (Neural Networks 2025): [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0893608025000292).

**F. Poisoning / backdoors as they interact with privacy defenses**

- *Background* **[prior knowledge, URL not re-verified]**
  - Bagdasaryan et al., "How To Backdoor Federated Learning", AISTATS 2020, https://arxiv.org/abs/1807.00459.
  - Pasquini, Francati, Ateniese, "Eluding Secure Aggregation in Federated Learning via Model Inconsistency", CCS 2022, https://arxiv.org/abs/2110.07714. A malicious server can defeat SecAgg by sending different models to different clients.
- The core tension: SecAgg/HE hide individual updates, which **prevents server-side anomaly detection or robust aggregation**. Model-inconsistency and model-manipulation attacks (Decepticons, Geminio, MineGrad) show that a *malicious* server can turn the global model into a leakage device.
- "Harmful Fine-tuning Attacks and Defenses for Large Language Models: A Survey" (arXiv 2409.18169) covers fine-tuning-time poisoning and safety-alignment removal relevant to federated LLM fine-tuning. Source: [arXiv](https://arxiv.org/pdf/2409.18169).
- FL poisoning survey (background): [arXiv 2306.03397](https://arxiv.org/pdf/2306.03397).

### Inferences
- The threat model has moved from "honest-but-curious server with full gradients" to two settings that matter more for LLMs:
  - **malicious server + PEFT updates**, as in MineGrad and Geminio;
  - **intermediate-activation leakage** in split / offsite-tuning pipelines, as in PIDI and PrivPair.
  So the claim that "only LoRA adapters are shared, hence private" is no longer defensible without DP or cryptographic protection.
- The results conflict by modality. Vision-FL realism studies (2508.19819) suggest inversion is overrated, while text-LLM work (DAGER, FedSpy-LLM) suggests text is *easier* to invert exactly, because tokens are discrete and attention gradients are low-rank. A survey should present the two modalities separately.
- On LoRA rank: ICP-MIA (higher rank means more MIA risk) and MineGrad (an attack that works even when tokens exceed rank) together suggest that rank is a privacy knob but not a sufficient defense.

### Gaps
- FedSpy-LLM, TIGER, MineGrad, ProjRes and PrivPair: author lists, affiliations, quantitative results and code repos could not be verified, because full-text fetching from arxiv.org was blocked.
- No 2025-2026 paper on **attribute/property inference specific to federated LLMs** was found within the search budget.
- Peer-review status of most 2026 preprints is unknown.

---

## Defenses: DP (DP-FedAvg, user-level DP, DP-LoRA in FL, Gboard production DP-FL), secure aggregation, HE, MPC, TEEs, split-learning privacy, federated RAG, federated unlearning

### Takeaway
**User-level DP via DP-FedAvg / DP-FTRL / BLT mechanisms** remains the only defense deployed at consumer scale with formal guarantees. Google's Gboard LMs carry ρ-zCDP 0.2-2 guarantees, and Google now pairs FL with **TEEs ("Confidential Federated Compute")** for externally verifiable DP. For LLMs, the 2025-2026 research front is fixing **LoRA's DP-noise amplification**: FFA-LoRA, FedASK (NeurIPS 2025), LA-LoRA (2026) and DP-FedLoRA. Further fronts are **HE-based aggregation for LoRA** (FedShield-LLM with CKKS), DP and other defenses for split-LLM activations, federated RAG architectures, and early federated LLM unlearning (Oblivionis, hierarchical FU).

### Cited Findings

**A. Differential privacy: production DP-FL (Google Gboard)**

- *Background*
  - DP-FedAvg is from McMahan, Ramage, Talwar, Zhang, "Learning Differentially Private Recurrent Language Models", ICLR 2018, https://arxiv.org/abs/1710.06963 **[prior knowledge, URL not re-verified]**.
  - DP-FTRL is from Kairouz et al., "Practical and Private (Deep) Learning without Sampling or Shuffling", ICML 2021, https://arxiv.org/abs/2103.00039 **[prior knowledge, URL not re-verified]**.
- **Federated Learning of Gboard Language Models with Differential Privacy.** Xu, Zhang, et al. (Google). ACL 2023 Industry.
  - **More than 20 Gboard LMs** were trained and deployed with **ρ-zCDP guarantees, ρ ∈ (0.2, 2)**, using DP-FTRL. Two were also trained with secure aggregation.
  - **All new Gboard LM FL tasks now require DP**, with DP-FTRL recommended.
  - The first production formal-DP model was Spanish next-word prediction, deployed in **Feb 2022**.
  - Sources: [arXiv 2305.18465](https://arxiv.org/pdf/2305.18465); [ACL Anthology](https://aclanthology.org/2023.acl-industry.60.pdf).
- **Private Federated Learning in Gboard.** Google, 2023, arXiv 2306.14793. This is a systems overview of the privacy principles, DP and SecAgg as deployed. Source: [arXiv](https://arxiv.org/pdf/2306.14793).
- **Google Research blog, "Advances in private training for production on-device language models."**
  - FL is the **default** method for training Gboard on-device LMs from user data.
  - It states that the **ε≈10** guarantees of many launched LMs are considered reasonable for ML in practice.
  - Source: [Google Research blog](https://research.google/blog/advances-in-private-training-for-production-on-device-language-models/). The publication date was not visible in the snippets.
- **"A Hassle-free Algorithm for Private Learning in Practice: Don't Use Tree Aggregation, Use BLTs."** Google, arXiv 2408.08868, 2024. It introduces **Buffered Linear Toeplitz (BLT)** matrix-factorization mechanisms as a practical replacement for tree-aggregation DP-FTRL in production FL. Source: [arXiv](https://arxiv.org/pdf/2408.08868).
- **"Efficient Language Model Architectures for Differentially Private Federated Learning."** Google, arXiv 2403.08100, 2024. It reports **live production experiments training an English LM on millions of virtual-keyboard devices** under DP-FL, with architecture choices (e.g., SI-CIFG) tuned for DP. Source: [arXiv](https://arxiv.org/pdf/2403.08100).
- **"Federated Learning in Practice: Reflections and Projections."** Daly, Eichner, Kairouz, McMahan, Ramage, Xu (Google), arXiv 2410.08892, 2024.
  - It argues for refocusing FL on **verifiable privacy properties via TEEs**, rather than on strict on-device computation.
  - Source: [arXiv](https://arxiv.org/pdf/2410.08892).

**B. DP for federated LLM / LoRA fine-tuning**

- *Background* **[prior knowledge, URL not re-verified]**: FFA-LoRA, Sun, Qin, Li, Ding, "Improving LoRA in Privacy-preserving Federated Learning", ICLR 2024, https://arxiv.org/abs/2403.12313.
  - It identified that DP noise is **amplified** through the LoRA product B·A.
  - It proposed **freezing A and training only B**.
  - LA-LoRA cites this as the origin of the noise-amplification observation ([arXiv 2602.19926](https://arxiv.org/html/2602.19926v1)).
- **FedASK, "Differentially Private Federated Low Rank Adaptation Beyond Fixed-Matrix."** Wen et al., **NeurIPS 2025**, arXiv 2507.09990.
  - It names the dilemma: noise on both adapters amplifies noise, while freezing one adapter limits learnability.
  - It proposes a **two-stage (double) sketching pipeline** that updates both A and B under DP.
  - **Caveat:** an independent review (Pith) questions whether its two central theorems hold.
  - Sources: [arXiv](https://arxiv.org/abs/2507.09990); [NeurIPS mirror](https://mlanthology.org/neurips/2025/wen2025neurips-differentially/); [Pith review](https://pith.science/paper/2507.09990).
- **LA-LoRA, "Rethinking LoRA for Privacy-Preserving Federated Learning in Large Models."** arXiv 2602.19926, 2026, OpenReview submission.
  - It formalizes the **semi-quadratic** LoRA update, under which DP noise produces a non-Gaussian quadratic noise term that destabilizes training.
  - Remedy: **alternating local updates of A and B plus a low-pass smoothing filter**.
  - Sources: [arXiv HTML](https://arxiv.org/html/2602.19926v1); [OpenReview](https://openreview.net/forum?id=BPzSV4uw0x).
- **DP-FedLoRA: Privacy-Enhanced Federated Fine-Tuning for on-device LLMs.** arXiv 2509.09097, 2025. It adds Gaussian noise to the LoRA matrices, with guidance on calibrating the privacy budget. Source: [arXiv](https://arxiv.org/pdf/2509.09097).
- **iP-FedLoRA, "Efficient and Differentially Private Federated LLM Fine-Tuning on Heterogeneous Clients."** ACM 2025/26.
  - It uses **matrix-wise DP with sensitivity-aware noise allocation** and rank-compensated regularization for heterogeneous client ranks.
  - Source: [ACM DOI 10.1145/3770855.3817914](https://doi.org/10.1145/3770855.3817914).

**C. Secure aggregation, HE, MPC**

- *Background* **[prior knowledge, URL not re-verified]**
  - Bonawitz et al., "Practical Secure Aggregation for Privacy-Preserving Machine Learning", CCS 2017, https://eprint.iacr.org/2017/281.
  - Bell et al., "Secure Single-Server Aggregation with (Poly)Logarithmic Overhead" (SecAgg+), CCS 2020, https://eprint.iacr.org/2020/704.
- **FedShield-LLM: A Secure and Scalable Federated Fine-Tuned LLM.** arXiv 2506.05640, 2025.
  - It combines **LoRA + unstructured pruning + CKKS fully homomorphic encryption** for encrypted-domain aggregation.
  - It claims to be the first to combine FHE and pruning in federated LLM fine-tuning, and to mitigate leakage in both training and inference.
  - Sources: [arXiv PDF](https://arxiv.org/pdf/2506.05640); [HTML v2](https://arxiv.org/html/2506.05640v2).
- **Secure Aggregation in FL using Multiparty Homomorphic Encryption.** arXiv 2503.00581, Mar 2025.
  - It uses **threshold/multiparty HE**, so the aggregate can be decrypted with only a subset of clients online, which handles dropout.
  - Combining compression with encryption keeps the overhead over baselines small.
  - It is not LLM-specific.
  - Source: [arXiv](https://arxiv.org/abs/2503.00581).
- **PrivTuner, "PrivTuner with Homomorphic Encryption and LoRA: A P3EFT Scheme for Privacy-Preserving Parameter-Efficient Fine-Tuning of AI Foundation Models."** arXiv 2410.00433, 2024. It combines HE with LoRA for privacy-preserving PEFT of foundation models. Source: [arXiv](https://arxiv.org/pdf/2410.00433).
- **SecFFT** (Frontiers in AI, 2025) targets secure federated fine-tuning of LLM semantic knowledge bases. It **encrypts only the high-level, semantically rich parameters** to cut HE cost. A correction was published in Dec 2025. Sources: [Frontiers](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2025.1690950/full); [correction, PMC](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC12750460/).
- Trade-off framing (secondary aggregator): HE adds no noise, so there is no utility loss, but it costs much more computation than pairwise-masking SecAgg. Source: [Emergentmind summary](https://www.emergentmind.com/topics/federated-security-for-llms). This is an aggregator, so lower confidence.

**D. TEEs / confidential computing**

- **Confidential Federated Computations.** Google, 2024.
  - It uses **TEEs + open-sourced, reproducibly built server binaries + a ledger enforcing device-authorized access policies**.
  - Devices pre-authorize which TEE workloads may process their uploads, and only anonymized/DP results are released. The result is **externally verifiable** server-side privacy.
  - Sources: [Google Research pub](https://research.google/pubs/confidential-federated-computations/); code at [GitHub google-parfait/confidential-federated-compute](https://github.com/google-parfait/confidential-federated-compute).
- **Confidential federated analytics in production.** The Gboard new-word discovery blog describes this. Source: [Google Research blog](https://research.google/blog/discovering-new-words-with-confidential-federated-analytics/).
- **Next-generation FL system ("Toward provably private learning from federated data").**
  - Google announced an FL system that uses TEEs to deliver **verifiable DP** while moving computation **server-side** to cut training time.
  - Sources: [Google Research blog](https://research.google/blog/toward-provably-private-learning-from-federated-data/); [Google Research on X](https://x.com/GoogleResearch/status/2106041109944357182).
  - The blog was not fetchable here, so the exact date and ε values are unverified.
- **"Hardening Confidential Federated Compute against Side-channel Attacks."** Google researchers, arXiv 2603.21469, 2026.
  - It finds that some **side channels are inevitable** in any system built on multiple existing confidential VMs (CVMs).
  - An insider could exploit them to **circumvent the DP guarantees**.
  - It proposes hardening measures.
  - Source: [arXiv](https://arxiv.org/pdf/2603.21469).
- Trust caveat: the guarantees depend on AMD/Intel TEE hardware working correctly. Source: [Google Cloud blog](https://cloud.google.com/blog/products/identity-security/verifiable-trust-in-the-ai-era-whats-new-in-confidential-computing).

**E. Split-learning privacy defenses**

- PrivPair (arXiv 2609.09794, 2026) proposes and evaluates defenses for split federated LLM fine-tuning, including DP applied to smashed data, against the zero-cost-decoder attack. Source: [arXiv](https://arxiv.org/html/2609.09794v1).
- PIDI (arXiv 2606.14210) includes a defense for dual-sided leakage. Source: [arXiv](https://arxiv.org/pdf/2606.14210).
- The 2026 split-LLM survey catalogues "privacy optimizations" for split fine-tuning. Source: [arXiv 2604.24468](https://arxiv.org/html/2604.24468v1).

**F. Privacy-preserving federated RAG**

- **Federated RAG: A Systematic Mapping Study.** arXiv 2505.18906, 2025.
  - The first systematic mapping of FedRAG, covering 2020-2025.
  - It reviews architectural patterns, privacy-preserving retrieval and cross-client heterogeneity.
  - Source: [arXiv](https://arxiv.org/abs/2505.18906).
- **Privacy-Preserving Federated Embedding Learning for Localized RAG (FedE4RAG / FedKD).** arXiv 2504.19101, 2025.
  - Federated training of retriever embeddings with adaptive mutual knowledge distillation and dynamic gradient compression.
  - Reported communication reduction of up to **94.89%**.
  - Source: [ResearchGate mirror](https://www.researchgate.net/publication/391246945_Privacy-Preserving_Federated_Embedding_Learning_for_Localized_Retrieval-Augmented_Generation).
- **HyFedRAG.** arXiv 2509.06444, 2025.
  - An edge-cloud federated RAG for heterogeneous, privacy-sensitive medical data.
  - Evaluated on PMC-Patients.
  - Source: [arXiv](https://arxiv.org/abs/2509.06444).
- **PRAG: End-to-End Privacy-Preserving RAG.** arXiv 2604.26525, 2026. Details were not visible. Source: [arXiv](https://arxiv.org/pdf/2604.26525).
- **Related non-federated work.**
  - Privacy-Aware Decoding for RAG leakage (arXiv 2508.03098): [arXiv](https://arxiv.org/pdf/2508.03098).
  - RAG security/privacy survey (arXiv 2606.25533): [arXiv](https://arxiv.org/pdf/2606.25533).
  - Healthcare RAG privacy review (arXiv 2511.11347): [arXiv](https://arxiv.org/pdf/2511.11347).

**G. Machine unlearning in FL / federated LLMs**

- **Oblivionis: A Lightweight Learning and Unlearning Framework for Federated LLMs.** arXiv 2508.08875, 2025. It jointly supports federated LLM fine-tuning and targeted unlearning, for example for right-to-be-forgotten requests. Source: [arXiv](https://arxiv.org/pdf/2508.08875).
- **Hierarchical Federated Unlearning for LLMs.** arXiv 2510.17895, 2025. Details were not visible. Source: [arXiv](https://arxiv.org/pdf/2510.17895).
- **LLM unlearning surveys (not FL-specific).**
  - arXiv 2510.25117 (Oct 2025) reviews more than 180 papers: [arXiv](https://arxiv.org/abs/2510.25117).
  - Artificial Intelligence Review 58:399 (2025): [Springer](https://link.springer.com/article/10.1007/s10462-025-11376-7).
- A curated list including FL unlearning surveys: [GitHub tamlhp/awesome-machine-unlearning](https://github.com/tamlhp/awesome-machine-unlearning).
- **Federated LLM survey.** IEEE Communications Surveys & Tutorials vol. 27 no. 4 (Aug 2025) covers federated LLM fine-tuning, personalization, and privacy attacks and defenses. Source: [PDF](https://www.eng.auburn.edu/~szm0001/papers/COMST-AUG25.pdf).

### Inferences
- The defense landscape is splitting in two:
  - **Industry (Google)** relies on *user-level DP + SecAgg/TEEs* with verifiability. It moves computation server-side inside TEEs, which also makes larger models feasible.
  - **Academia** focuses on *making DP compatible with LoRA*, through freezing, sketching, alternating updates and smoothing.
- Cryptographic aggregation alone (SecAgg/HE) does **not** stop a malicious server that manipulates the model (MineGrad, Geminio, Pasquini et al.) or leakage from the released global model (2506.06060). DP on the released model remains necessary.
- TEE-based FL moves the trust anchor to hardware vendors and side-channel resistance. Google's own 2026 paper concedes that unavoidable side channels exist.

### Gaps
- Exact ε/δ values and model sizes for Google's TEE-based "provably private" FL system are unverified, because the blog fetch was blocked.
- There is no confirmed public evidence of production **user-level DP federated fine-tuning of multi-billion-parameter LLMs**. Gboard models are small on-device LMs. This should be checked.
- MPC-specific federated-LLM systems from 2025-2026 were not surfaced, beyond HE and threshold HE.
- Code repos for FedASK, LA-LoRA, DP-FedLoRA and FedShield-LLM were not verified.

---

## Privacy-utility trade-offs for LLMs; regulatory relevance (GDPR, EU AI Act)

### Takeaway
The cited utility costs fall into three groups:
- In production, ε≈10 or ρ-zCDP 0.2-2 is treated as acceptable for small on-device LMs.
- For LoRA-based federated LLMs, the dominant utility loss is **DP noise amplification through the low-rank product**, which motivates the FFA-LoRA, FedASK and LA-LoRA line.
- Cryptographic approaches trade compute for zero utility loss.

Regulatory material could not be searched within the budget. The GDPR and EU AI Act points below are background only.

### Cited Findings
- Google treats **ε≈10** for launched Gboard LMs as reasonable in practice: [Google Research blog](https://research.google/blog/advances-in-private-training-for-production-on-device-language-models/). More than 20 Gboard LMs are deployed at **ρ-zCDP 0.2-2**: [arXiv 2305.18465](https://arxiv.org/pdf/2305.18465).
- DP-FL for LMs faces payload-size problems and a poor signal-to-noise ratio for rare tokens: [arXiv 2310.01424](https://arxiv.org/pdf/2310.01424).
- LoRA + DP has a structural noise-amplification problem.
  - The semi-quadratic update produces a non-Gaussian quadratic noise term, which severely degrades learning stability: [LA-LoRA](https://arxiv.org/html/2602.19926v1).
  - Freezing one adapter fixes the noise but limits learnability: [FedASK, NeurIPS 2025](https://arxiv.org/abs/2507.09990).
- Rank as a knob: a higher LoRA rank increases MIA success, and 4-bit QLoRA reduces it: [ICP-MIA, NDSS 2026](https://www.ndss-symposium.org/wp-content/uploads/2026-f892-paper.pdf).
- Federation itself gives partial protection: federated LLMs memorize less than central ones but still leak verbatim data ([arXiv 2506.06060](https://arxiv.org/pdf/2506.06060)), and FL + DP can drive canary extraction to zero ([arXiv 2312.06717](https://arxiv.org/pdf/2312.06717)).
- HE (CKKS) aggregation has no noise-induced utility loss but higher compute.
  - FedShield-LLM mitigates the cost with pruning: [arXiv 2506.05640](https://arxiv.org/pdf/2506.05640).
  - SecFFT mitigates it with selective encryption: [Frontiers](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2025.1690950/full).
  - Threshold MHE plus compression keeps overhead small: [arXiv 2503.00581](https://arxiv.org/abs/2503.00581).
- **Policy landscape.** "The Federation Strikes Back" includes a *policy landscape* section that maps FL privacy to regulation: [arXiv 2405.03636](https://arxiv.org/abs/2405.03636); [ACM CSUR](https://dl.acm.org/doi/10.1145/3724113).
- **Regulation (background)** **[prior knowledge, URL not re-verified]**
  - GDPR (Regulation (EU) 2016/679), https://eur-lex.europa.eu/eli/reg/2016/679/oj. Relevant are Art. 5(1)(c) data minimisation, Art. 17 right to erasure (which motivates federated unlearning), Art. 25 data protection by design, and Recital 26 (anonymous data falls outside scope). FL is generally seen as supporting minimisation, but model updates may still be personal data.
  - EU AI Act (Regulation (EU) 2024/1689), https://eur-lex.europa.eu/eli/reg/2024/1689/oj. Relevant are Art. 10 data governance for high-risk systems, which explicitly permits processing special-category data for bias detection subject to "state-of-the-art security and privacy-preserving measures", and the obligations for general-purpose AI model providers (Art. 53), applicable from Aug 2025.
  - The EDPS published a TechDispatch on federated learning in 2025 **[prior knowledge, not verified]**.

### Inferences
- Whether an LLM trained under DP still counts as personal data (GDPR Recital 26 anonymisation) is unresolved, and memorization/extraction results (2506.06060) cut against treating federated LLMs as anonymous without DP.
- Federated unlearning (Oblivionis, hierarchical FU) is the main technical answer to GDPR Art. 17 for federated LLMs. It has not been certified, and the only fully rigorous form remains retraining or DP-based deniability.

### Gaps
- No quantitative perplexity or accuracy-versus-ε tables for federated LLM fine-tuning (for example, Llama-class models with DP-FedLoRA) were retrievable from snippets.
- No 2025-2026 regulatory guidance specific to FL (EDPB/EDPS, ICO) could be retrieved because the search budget was exhausted. The GDPR and AI Act article mappings above are background knowledge and should be checked.

---

## Frameworks / tools (Opacus, TF Privacy, Flower DP/SecAgg, NVIDIA FLARE privacy filters) and open problems

### Takeaway
Open-source tooling now covers DP-SGD (Opacus, TF Privacy, plus Google's `dp_accounting` and JAX-privacy), FL orchestration with DP and SecAgg (Flower, TFF / Google Parfait, NVFlare) and TEE-backed confidential FL (Google Parfait confidential-federated-compute). The search budget ran out before the framework-specific searches, so apart from the Parfait repo, framework details are **background knowledge and unverified in this session**.

### Cited Findings
- **Google Parfait / Confidential Federated Compute.** TEE-hosted binaries for verifiable server-side computation, verified in search: [GitHub google-parfait/confidential-federated-compute](https://github.com/google-parfait/confidential-federated-compute).
- **Background tooling** **[prior knowledge, URL not re-verified]**
  - **Opacus** (Meta): PyTorch DP-SGD with per-sample gradients and an RDP/PRV accountant, https://github.com/pytorch/opacus. Paper: Yousefpour et al., arXiv 2109.12298.
  - **TensorFlow Privacy** (Google): DP-SGD optimizers and the `dp_accounting` library, https://github.com/tensorflow/privacy.
  - **Flower** (flwr): built-in central and local DP wrappers (fixed/adaptive clipping) and **SecAgg+ / LightSecAgg** protocols, plus FlowerTune LLM fine-tuning examples. Docs: https://flower.ai/docs/framework/how-to-use-differential-privacy.html; https://flower.ai/docs/framework/ref-api/flwr.server.workflow.SecAggPlusWorkflow.html.
  - **NVIDIA FLARE**: "privacy filters", i.e. task result filters such as `ExcludeVars`, `PercentilePrivacy` (Shokri-Shmatikov style), `SVTPrivacy`, and homomorphic-encryption filters using TenSEAL/CKKS, plus LLM PEFT/SFT examples. https://nvflare.readthedocs.io/en/main/programming_guide/filters.html; https://github.com/NVIDIA/NVFlare.
  - **FederatedScope-LLM** (Alibaba): https://github.com/alibaba/FederatedScope/tree/llm.
  - **OpenFedLLM** (Ye et al., KDD 2024): https://github.com/rui-ye/OpenFedLLM.
  - **FATE-LLM** (WeBank): https://github.com/FederatedAI/FATE-LLM.
  These are federated LLM fine-tuning frameworks with varying DP support.
- **Curated research lists**
  - [Awesome-Gradient-Inversion-Attacks (TPAMI 2026 survey companion)](https://github.com/Pengxin-Guo/Awesome-Gradient-Inversion-Attacks)
  - [awesome-machine-unlearning](https://github.com/tamlhp/awesome-machine-unlearning)
- **Open problems with sources**
  - (1) DP-compatible PEFT without noise amplification: [LA-LoRA](https://arxiv.org/html/2602.19926v1); [FedASK](https://arxiv.org/abs/2507.09990).
  - (2) Malicious-server attacks on PEFT that bypass SecAgg-style assumptions: [MineGrad](https://arxiv.org/pdf/2608.01521); [Geminio](https://arxiv.org/pdf/2411.14937).
  - (3) Activation leakage in split LLM fine-tuning and inference: [PrivPair](https://arxiv.org/html/2609.09794v1); [PIDI](https://arxiv.org/html/2606.14210).
  - (4) Side channels in TEE-based FL: [arXiv 2603.21469](https://arxiv.org/pdf/2603.21469).
  - (5) Cross-client verbatim extraction from the released global LLM: [arXiv 2506.06060](https://arxiv.org/pdf/2506.06060).
  - (6) Privacy-preserving retrieval and heterogeneity in FedRAG: [arXiv 2505.18906](https://arxiv.org/abs/2505.18906).
  - (7) Verifiable federated LLM unlearning: [Oblivionis](https://arxiv.org/pdf/2508.08875).
  - (8) Realistic evaluation of inversion threats: [arXiv 2508.19819](https://arxiv.org/abs/2508.19819).
  - (9) Poisoning-assisted extraction: [arXiv 2606.17110](https://arxiv.org/pdf/2606.17110).

### Inferences
- Tooling is mature for DP-SGD and DP-FedAvg on small models, and for SecAgg in Flower and TFF. **No mainstream framework offers turnkey, audited user-level DP + SecAgg for multi-billion-parameter federated LoRA.** Practitioners must compose Flower or NVFlare with Opacus-style clipping themselves, and the LoRA noise-amplification results suggest naive composition loses substantial utility.
- Privacy auditing (MIA- and canary-based) of federated LLMs remains largely ad hoc. MIA papers such as ProjRes and ICP-MIA could serve as auditing baselines.

### Gaps
- Searches for Flower, NVFlare, Opacus and TF Privacy feature status in 2025-2026 (current versions, LLM-specific DP or SecAgg support) **were not performed: the web-search budget was exhausted**. All framework claims except the Parfait repo come from background knowledge and need verification.
- No benchmark comparing frameworks on DP federated LLM fine-tuning was found.
