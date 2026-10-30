# Federated Learning x Agentic AI (LLM agents and multi-agent systems), 2024-2026

> **Method note (read first).** Direct page fetching of arxiv.org was blocked in this environment (DNS resolution failed), and the session's shared web-search budget ran out after 18 searches. Every claim below therefore comes from **search-result snippets** (arXiv abstract pages, ICLR virtual/proceedings pages, GitHub READMEs, OpenReview, aggregators such as alphaXiv, Hugging Face Papers, and Lacuna). I could not read full papers or result tables. Treat numbers as abstract-level claims. **Peer-review status:** [PR] means accepted at a peer-reviewed venue, [WS] means a workshop paper, and [PP] means an arXiv preprint with no confirmed venue. Most of this area is still preprints.

## 1. Federated training / fine-tuning of LLM agents (federated agent RL, self-evolution, in-context, skills, tool-use)

### Takeaway
The field moved from federating plain instruction tuning (FedIT) to federating agent-specific artifacts. These include RL policies trained on local trajectories (FedAgent, ICLR 2026), filtered-trajectory LoRA updates (Fed-SE), natural-language "knowledge compendiums" (FICAL), structured skill-library edits (FederatedSkill, FedSkillGit), and typed tool-routing artifacts (SYNAPSE). By late 2026 a unifying problem formulation exists: "Federated Agent Optimization" (Qiang Yang et al.). Its central observation is that agent capability lives in policy, memory, tools, reward, and skills, not only in weights. Only FedAgent is confirmed peer-reviewed at a main venue.

### Cited Findings
**Unifying framing / surveys**
- **Federated Agent Optimization (FAO)**, arXiv 2610.01195 (Oct 2026), 9 authors led by Qiang Yang [PP]. It is a problem-formulation paper, not a classic survey. It studies how distributed agents can improve together "through controlled information exchange while keeping raw data, complete trajectories, and private knowledge local". Each client agent has five federatable components: policy, memory, tools, reward, and structured knowledge (ontologies, rules, explicit skills). FAO is posed as a multi-objective problem trading off agent utility, privacy leakage, and communication cost. The paper says validation and the privacy/evaluation challenges remain open — [arXiv abs](https://arxiv.org/abs/2610.01195); [PDF](https://arxiv.org/pdf/2610.01195); [alphaXiv](https://www.alphaxiv.org/abs/2610.01195)
- FAO reports that for memory, tool use, rules, and skills "the first federated methods have appeared, most of them as preprints". It also says methods exchanging text or rules rely on keeping raw trajectories local, and **none applies a formal privacy mechanism** (e.g., DP) — [arXiv HTML 2610.01195](https://arxiv.org/html/2610.01195v1)
- FAO describes FedAgent as RL on local trajectories followed by parameter averaging. It also cites FedRLHF, which keeps feedback and reward functions on clients while federating policies — [arXiv HTML 2610.01195](https://arxiv.org/html/2610.01195v1)
- Background survey: "Federated Large Language Models: Current Progress and Future Directions", arXiv 2409.15723 [PP]. It covers federated fine-tuning and prompt learning and flags federated agents as an emerging direction — [arXiv](https://arxiv.org/abs/2409.15723)
- "Federated reasoning LLMs: a survey", *Frontiers of Computer Science* (journal) [PR] — [journal page](https://journal.hep.com.cn/fcs/EN/10.1007/s11704-025-50480-3) (seen in search results only; contents not verified)

**Federated RL for LLM agents (parameter-sharing)**
- **FedAgent / "Federated Agent Reinforcement Learning"**, by Canyu Chen, Kangyu Zhu, Zhaorun Chen, Zhanhui Zhou, Shizhe Diao, Yiping Lu, Tian Li, Manling Li, and Dawn Song. **ICLR 2026** [PR]. It proposes a decentralized training paradigm for agents learning collaboratively across clients without sharing local data. It also introduces the **FedAgentGym** benchmark: four types of LLM agents, two scenarios (WebShop, ALFWorld), three decentralized setting variations, and three heterogeneity challenges. The abstract claims performance "comparable to centralized training" and robustness to heterogeneity — [ICLR virtual page](https://iclr.cc/virtual/2026/10019292); [ICLR proceedings PDF](https://proceedings.iclr.cc/paper_files/paper/2026/file/efcf4c92de26fdd19b64ce553636d422-Paper-Conference.pdf)
- **Code:** `sunblaze-ucb/FedAgent` ("A Library for Decentralized Agent Learning"). It implements FedAvg with optional FedProx, plus federated GRPO/PPO trainers built on **verl**, with GiGPO integrated. The README claims a Best Paper Award at the AAAI'26 TrustAgent Workshop and an Outstanding Paper Award at the AAAI'26 PerFM Workshop [WS] — [GitHub](https://github.com/sunblaze-ucb/FedAgent). A mirror/fork (`avan2139/FedAgent`) lists a related title, "Is Decentralized LLM Agent RL Robust to Heterogeneity?", which may be a retitled or companion version (unverified) — [GitHub](https://github.com/avan2139/FedAgent)
- **FedMOA: Federated GRPO for Personalized Reasoning LLMs under Heterogeneous Rewards**, arXiv 2602.00453 [PP]. It does federated RL (GRPO) with client-specific rewards. It is relevant to federated agent RL, but the snippet does not show whether tool use is covered — [arXiv PDF](https://arxiv.org/pdf/2602.00453)

**Federated self-evolution (trajectory-based)**
- **Fed-SE: Federated Self-Evolution for Privacy-Constrained Multi-Environment LLM Agents**, arXiv 2512.08870 (Dec 2025), by Xiang Chen, Yuling Shi, Qizhen Lan, Yuchao Qiu, Min Wang, Xiaodong Gu, and Yanfu Yan [PP]. Locally, agents are fine-tuned on filtered, high-return trajectories. Globally, updates are aggregated in a low-rank subspace to cut communication. A later version is retitled "...for Cross-Environment Knowledge Transfer in Privacy-Constrained LLM Agents" — [arXiv abs](https://arxiv.org/abs/2512.08870); [arXiv HTML](https://arxiv.org/html/2512.08870)
- Fed-SE results conflict across sources. HF Papers reports a **~18% gain in average task success** over federated baselines across five heterogeneous environments — [HF Papers](https://huggingface.co/papers/2512.08870). The arXiv HTML reports **~10% over FedIT** with Qwen2.5-7B — [arXiv HTML](https://arxiv.org/html/2512.08870). These probably reflect different baselines or versions.

**Parameter-free / in-context federated agent learning (text-artifact exchange)**
- **FICAL: Federated In-Context LLM Agent Learning**, arXiv 2412.08054 (Dec 2024), by Panlong Wu, Kangshuo Li, Junbao Nan, and Fangxin Wang (CUHK-Shenzhen) [PP]. Clients send **natural-language "knowledge compendiums"** instead of weights. A Knowledge Compendium Generation (KCG) module (DeepSeek-v2) produces tool-use knowledge, and the server merges these into a global compendium stored in a vector DB. Agents retrieve relevant parts at query time (a RAG-based tool-learning module) — [arXiv](https://arxiv.org/abs/2412.08054); [Moonlight review](https://www.themoonlight.io/en/review/federated-in-context-llm-agent-learning)
- FICAL communication is reported (by a secondary survey) as **0.078 MB** of tool knowledge vs **26,000 MB** for parameter-exchange baselines. I have not verified this against the original — [arXiv PDF 2412.08054](https://arxiv.org/pdf/2412.08054); [FAO HTML](https://arxiv.org/html/2610.01195)
- **SYNAPSE: Federated Tool Routing via Typed Compendium Artifacts**, arXiv 2602.00911 [PP]. It targets federated tool routing across organizations running frozen, possibly heterogeneous LLMs, with no shared data or weights — [arXiv](https://arxiv.org/abs/2602.00911)
- **Typed Federated Artifacts for the Agentic Web: Sharing Tool-Routing Knowledge Across Frozen, Heterogeneous LLM Agents**, arXiv 2609.06815, by Abhijit Chakraborty, Ni Trieu, and Vivek Gupta (ASU) [PP]. A typed federated artifact is a schema-validated object whose fields each have a role, type, and validation rule, meant to make per-field privacy, conflict resolution, and cross-model transfer well-defined. It critiques FICAL's free-text compendiums. **Results:** each compendium arm improves GPT-4o per-step tool-call accuracy by **≥6.7 points on τ-bench retail**, which the authors attribute to format rather than federated experience. **Negative result:** on a math proxy and StableToolBench, a **TF-IDF classifier beat every LLM routing arm** — [arXiv abs](https://arxiv.org/abs/2609.06815); [arXiv HTML v1](https://arxiv.org/html/2609.06815v1). A third-party review notes the benchmark cannot measure routing to unlabeled tools — [Pith](https://pith.science/paper/2609.06815)

**Federated skill evolution**
- **FederatedSkill: Federated Learning for Agentic Skill Evolution**, arXiv 2606.03143 (Jun 2026), by Jingbo Yang, Guanyu Yao, Yang Zhang, Ramana Rao Kompella, Gaowen Liu, and Shiyu Chang (UCSB with Cisco co-authors) [PP]. Clients exchange **structured edits to local skill libraries** instead of raw traces. A server-side "evolution agent" merges edits into a **personalized** skill set per client rather than one global average. Evaluated on 20 agent task families, it reports **up to +44.4% success rate and -37.5% compute** vs self-evolving baselines — [arXiv](https://arxiv.org/abs/2606.03143)
- FAO reports FederatedSkill uploads at about **99-281 KB per task family** over a full run — [FAO HTML](https://arxiv.org/html/2610.01195v1)
- **FedSkillGit: Federated Skill Evolution via Version Control**, arXiv 2610.21348 (Oct 2026) [PP]. It builds on FederatedSkill and replaces full-document updates with versioned commits — [arXiv](https://arxiv.org/abs/2610.21348)
- **EvolveNet: Collaborative Harness Evolution for Agent Self-Improvement**, arXiv 2608.04968 [PP]. Appeared in the same search cluster; contents not verified — [arXiv PDF](https://arxiv.org/pdf/2608.04968)

**Domain application**
- **FedEHR-Agents: Federated Agentic Optimization for Automated EHR Modeling**, arXiv 2608.27856 [PP]. Listed only on an aggregator; contents not verified — [awesomepapers](https://awesomepapers.io/ai-agents/papers/2608.27856)

### Inferences
- The field is clearly splitting into two paradigms. (a) **Weight-space federation** (FedAgent, Fed-SE, FedMOA) uses open-weight models, RL or SFT on local trajectories, and LoRA or low-rank aggregation. (b) **Artifact-space federation** (FICAL, SYNAPSE/Typed Artifacts, FederatedSkill, FedSkillGit) works with frozen or closed LLMs and exchanges text, skills, or schema-typed objects at KB-scale communication. Paradigm (b) fits API-model agents better and is growing fastest in 2026.
- Artifact-space methods replace FedAvg with an **LLM-as-aggregator** (summarizer or evolution agent). This creates new research questions: aggregation faithfulness, poisoning of the merge agent, and personalization vs consensus.
- The Typed Artifacts negative result (TF-IDF beating LLM routing) suggests that some reported "federation gains" may come from format or retrieval effects. Ablations that separate federated experience from format are needed.
- Privacy in nearly all these methods is "data stays local". Formal guarantees (DP, secure aggregation) on trajectories or text artifacts are largely absent, per FAO.

### Gaps
- I could not read full papers, so per-benchmark numbers for FedAgent on WebShop/ALFWorld, the exact Fed-SE baselines, and FederatedSkill's task families are unverified.
- I found no dedicated paper on **federated tool-use learning with DP guarantees**, and no federated work on **reward models for agents** beyond FedRLHF (cited by FAO; details not retrieved).
- The affiliation and venue of the `avan2139/FedAgent` variant are unclear.

## 2. Agents across organizational silos with privacy (federated MAS, decentralized agents, federated RAG, federated memory)

### Takeaway
"Federated MAS" is now a recognized concept, distinct from FL: agents converse across trust boundaries rather than exchanging gradients. Work splits into four strands. The first is privacy-enhancing agent communication (EPEAgent). The second is FL used to train safety guards for MAS (federated graph learning). The third is federated RAG (a systematic review exists at EMNLP 2025 Findings). The fourth is cross-principal agent memory (AIM, MemPrivacy, Federated Recommender Agents). Apart from the federated RAG review, nearly all of this is preprint-stage.

### Cited Findings
- **Privacy-Enhancing Paradigms within Federated Multi-Agent Systems (EPEAgent)**, by Zitong Shi, Wan et al. (Emory, Carl Yang's group), arXiv Mar 2025 [PP]. It defines "Federated MAS" and names three core challenges: heterogeneous privacy protocols among agents, multi-party conversation structure, and dynamic conversation networks. EPEAgent intervenes at the RAG and context-retrieval stages so each agent receives only the data it needs (minimal disclosure) — [Semantic Scholar](https://www.semanticscholar.org/paper/Privacy-Enhancing-Paradigms-within-Federated-Shi-Wan/fb4446f32cfda955229d14824ed82581f6288a5e); [Emory PDF](http://www.cs.emory.edu/~jyang71/files/epeagents.pdf)
- **Privacy-Preserving Topology-Guided Safety for LLM-Based Multi-Agent Systems via Federated Graph Learning**, arXiv 2609.02967 [PP]. It treats training MAS safety guards as a **graph FL** problem: operators keep raw episodes local and share only model updates. It evaluates same-domain and cross-domain silos and reports that cross-domain federation **matches centralized pooling** — [arXiv PDF](https://arxiv.org/pdf/2609.02967); [HTML](https://arxiv.org/html/2609.02967v1)
- **Federated RAG systematic review**, *Findings of EMNLP 2025* [PR]. It covers privacy-preserving retrieval and cross-silo latency, and cites **RAGRoute** (reduces redundant cross-silo queries) and **FedE4RAG** (homomorphic encryption for retriever privacy) — [ACL Anthology PDF](https://aclanthology.org/2025.findings-emnlp.388.pdf)
- **Distributed Agent Reasoning Across Independent Systems With Strict Data Locality**, arXiv 2511.16292 [PP]. It uses HMAC-based tokens so organizations can coordinate without exposing identities. Each org keeps its own storage and policies, and coordination runs through short natural-language exchanges — [arXiv PDF](https://arxiv.org/pdf/2511.16292)
- **Federated Recommender Agents**, arXiv 2610.39289 [PP]. Interaction histories and personal memories stay on the client, and agents share reasoning as natural-language knowledge without parameter updates — [arXiv](https://arxiv.org/abs/2610.39289)
- **AIM: A Privacy-Aware Interoperable Memory Framework for Multi-Agent Multi-User LLM Systems**, arXiv 2609.12320 [PP]. Each memory is labeled private or public, and private memories are never compared across users, which limits cross-user leakage — [arXiv HTML](https://arxiv.org/html/2609.12320)
- **MemPrivacy: Privacy-Preserving Personalized Memory Management for Edge-Cloud Agents**, arXiv 2605.09530 [PP] — [arXiv PDF](https://arxiv.org/pdf/2605.09530) (contents not verified beyond title)
- "Memory as Distributed State in Multi-Agent LLM Systems" (ResearchGate) argues that privacy-preserving shared memory is underdeveloped. It notes that no benchmark covers private-vs-shared memory classification or memory integrity under concurrent updates — [ResearchGate](https://www.researchgate.net/publication/415461413_Memory_as_Distributed_State_in_Multi-Agent_LLM_Systems)
- A survey on long-term memory security ("Mnemonic Sovereignty") argues that exposing memory across principals *increases* attack surface and that poisoned memory can propagate across agents — [arXiv HTML 2604.16548](https://arxiv.org/html/2604.16548v1)
- **SplitAgent: A Privacy-Preserving Distributed Architecture for Enterprise-Cloud Agent Collaboration**, arXiv 2603.08221 [PP]. It argues that MCP and A2A assume full trust and data sharing between participants, which motivates a split enterprise/cloud design — [arXiv HTML](https://arxiv.org/html/2603.08221)
- **PrivAct: Internalizing Contextual Privacy Preservation via Multi-Agent Preference Training**, arXiv 2602.13840 [PP]. It is adjacent work: it trains privacy norms into MAS rather than using FL — [arXiv PDF](https://arxiv.org/pdf/2602.13840)

### Inferences
- Two different notions of "federated agents" coexist. **(i) FL-for-agents** shares parameters or artifacts to improve agents (Section 1). **(ii) Federated MAS** has agents collaborate at inference time across silos, with privacy enforced by disclosure control. A PhD-level survey should keep these distinct. EPEAgent explicitly argues that (ii) is not FL.
- Federated RAG is the most mature sub-strand (it has a peer-reviewed systematic review) and is the natural substrate for agent-level cross-silo retrieval. Agent-specific federated RAG (agents choosing which silo to query, e.g., RAGRoute-style routing) overlaps with tool routing (SYNAPSE).

### Gaps
- I could not retrieve quantitative results for EPEAgent or for the federated graph learning safety paper.
- No authoritative source found yet on **federated memory with formal privacy guarantees**. The sources say this is open.
- Specific federated-RAG systems (e.g., Vector Institute FedRAG library, C-FedRAG) were not covered within the search budget.

## 3. LLM agents orchestrating / automating FL (client selection, HPO, FL system synthesis)

### Takeaway
A fast-growing reverse direction uses LLM agents as the FL control plane. The flagship results are **Helmsman** (ICLR 2026; agent teams synthesize complete FL systems, with the AgentFL-Bench benchmark) and **FedAgentBench** (agents automating real-world medical-imaging FL across 201 datasets). Other work targets client selection, aggregation, and HPO. The evidence shows LLM agents can automate routine FL stages but struggle on interdependent, implicit-goal tasks, and token cost scales with the number of clients.

### Cited Findings
- **Helmsman: Autonomous Synthesis of Federated Learning Systems via Collaborative LLM Agents**, by Haoyuan Li et al. (TU Eindhoven), arXiv 2510.14512. Listed at **ICLR 2026** [PR] on ML Anthology. It runs in three phases: (1) interactive human-in-the-loop planning, (2) modular code generation by supervised agent teams, and (3) autonomous evaluation and refinement in a sandboxed FL simulator. It introduces **AgentFL-Bench**: 16 tasks across 5 areas (data heterogeneity, communication efficiency, personalization, active learning, continual learning). Generated solutions are reported as "competitive with, and often superior to" hand-crafted baselines. **Code:** github.com/haoyuan-l/Helmsman — [ML Anthology](https://mlanthology.org/iclr/2026/li2026iclr-helmsman/); [arXiv PDF](https://arxiv.org/pdf/2510.14512); [alphaXiv overview](https://www.alphaxiv.org/overview/2510.14512v1); [AgentFL-Bench listing](https://awesomepapers.io/ai-agents/datasets/agentfl-bench)
- **FedAgentBench: Towards Automating Real-world Federated Medical Image Analysis with Server-Client LLM Agents**, by Pramit Saha, Joshua Strong, Divyanshu Mishra, Cheng Ouyang, and J. Alison Noble (Oxford), arXiv 2509.23803 (Sep 2025). Submitted to ICLR 2026; acceptance unconfirmed, so treat as [PP]. Agents at each hospital and at the server handle client selection, orchestration, preprocessing, label harmonization, and FL-algorithm selection from a **registry of 40 FL algorithms** (FedAvg, FedProx, SCAFFOLD, Ditto, ...), across **201 datasets / 6 imaging modalities**. It evaluates **14 open-source and 10 proprietary LLMs**: GPT-4.1 and DeepSeek-V3 automate many stages, but complex interdependent, implicit-goal tasks remain hard — [arXiv PDF](https://arxiv.org/pdf/2509.23803v1); [alphaXiv](https://www.alphaxiv.org/ja/abs/2509.23803); [Lacuna](https://lacuna.tiptreesystems.com/work/fedagentbench-towards-automating-real-world-federated-medical-image-analysis/wrk_e7284a8bcb4266a6070a84df10c95959)
- **Agentic Federated Learning: The Future of Distributed Training Orchestration**, arXiv 2604.04895 [PP]. It compares random selection, a raw-LLM selector, and a tool-calling agent for client selection. The **raw LLM performed best**, but its token cost grows with client count and **at 50 clients exceeded the agent's**. It also warns that agents can misjudge client utility and repeatedly pick ineffective devices — [arXiv PDF](https://arxiv.org/pdf/2604.04895); [HTML](https://arxiv.org/html/2604.04895v1)
- **FL-MAESTRO: Multi-Agent LLM Orchestration for Resource-...** (title truncated in snippet), arXiv 2608.20518 [PP]. Three specialist LLM agents each own one decision dimension, a coordinator merges their analyses into one per-round decision, and a **non-LLM feasibility check** vets it — [arXiv PDF](https://arxiv.org/pdf/2608.20518)
- **Agentic Federated Learning: Rule-Based Client and Server Agents for Adaptive Training**, arXiv 2609.35914 [PP]. A server orchestrator agent handles client selection, aggregation-strategy choice, and quality-aware weighting, while light client agents tune hyperparameters and assess update quality. Note the agents here are rule-based, not LLMs — [arXiv HTML](https://arxiv.org/html/2609.35914v1)
- **FedAgent-HPO: Agentic Hyperparameter Optimization for Personalized FL** (OpenReview). Dual agents: one generates personalized client/server configurations from local history and peer signals, the other adapts the search space. Reports **up to +8.8% accuracy under non-IID data**. **Withdrawn from ICLR 2026**, so not peer-reviewed — [OpenReview](https://openreview.net/forum?id=q78jTz1JDy)
- Non-LLM baseline: a DDQN RL agent for client selection in federated LLM fine-tuning — [ResearchGate](https://www.researchgate.net/publication/392240808_Reinforcement_learning_Agent_for_Client_Selection_in_Federated_LLMs)
- "Towards AI Agents for Selecting Architectural Patterns in FL" (CEUR-WS Vol-4080) [WS] — [CEUR PDF](https://ceur-ws.org/Vol-4080/paper2.pdf)

### Inferences
- The dominant design pattern is **LLM proposes, deterministic checker verifies** (FL-MAESTRO's feasibility check, Helmsman's sandboxed simulator). This mirrors the broader trend toward verifiable agentic workflows.
- The cost-scaling finding in 2604.04895 implies that LLM-in-the-loop per-round decisions do not scale to cross-device FL (thousands of clients). Plausible niches are cross-silo FL (around 10-100 institutions) or hierarchical summarization of client statistics.
- Helmsman's AgentFL-Bench and FedAgentBench are the two main benchmarks for "agents doing FL engineering". FedAgentGym (Section 1) is the benchmark for "FL training agents".

### Gaps
- Quantitative Helmsman results per task were not retrieved. One aggregator claims it produced working FL systems for all 16 tasks, which I could not verify.
- The final venue of FedAgentBench is unconfirmed.

## 4. Personal on-device agents learning collaboratively; security/privacy of federated agents

### Takeaway
Evidence here is thin. The search budget ran out before a dedicated query on on-device agents, and I found **no confirmed paper specifically on cross-device FL of personal LLM agents** (e.g., phone assistants). Related work covers edge-cloud memory privacy (MemPrivacy) and personalization (FederatedSkill's per-client skill sets; FedMOA's heterogeneous rewards). On security, threats include poisoning of federated LLM fine-tuning, memory poisoning that propagates across agents, protocol-level exploits, and the absence of formal privacy mechanisms in artifact-exchange methods.

### Cited Findings
- **Graph Representation Learning Augmented Model Manipulation on Federated Fine-Tuning of LLMs**, arXiv 2605.07961 [PP]. An adversarial model-manipulation attack on federated LLM fine-tuning. It applies directly to FedAgent-style weight-space agent federation — [arXiv](https://arxiv.org/abs/2605.07961)
- FAO: methods exchanging text or rules keep raw trajectories local, but **none applies a formal privacy mechanism** — [FAO HTML](https://arxiv.org/html/2610.01195)
- FICAL itself acknowledges that in-context learning can leak user data, a privacy risk of compendium exchange — [Search summary of FICAL / FAO](https://arxiv.org/html/2610.01195); [FICAL PDF](https://arxiv.org/pdf/2412.08054)
- Memory poisoning can spread across agents when memory is shared across principals — [arXiv 2604.16548](https://arxiv.org/html/2604.16548v1)
- "From Prompt Injections to Protocol Exploits" (arXiv 2506.23260) [PP] notes that MCP, A2A, and FL widen the attack surface, including leaks of private data across agents — [arXiv HTML](https://arxiv.org/html/2506.23260v1)
- **MemPrivacy** (edge-cloud agent memory privacy), arXiv 2605.09530 [PP] — [arXiv PDF](https://arxiv.org/pdf/2605.09530)
- **FedMentor: Domain-Aware Differential Privacy for Heterogeneous Federated LLMs in Mental Health**, arXiv 2509.14275 [PP]. DP federated LLM fine-tuning, an adjacent technique for sensitive personal-assistant domains — [arXiv PDF](https://arxiv.org/pdf/2509.14275)
- **FedMARL-LTI: Federated MARL with LLM-Compatible Threat Intelligence for Cooperative Cyber Defense**, *Applied Sciences* (MDPI) 2026 [PR, journal] — [DOI](https://doi.org/10.3390/app16168278)
- Privacy-preserving MAS safety guards via federated graph learning (see Section 2) — [arXiv 2609.02967](https://arxiv.org/pdf/2609.02967)
- AAAI'26 hosted relevant workshops, **TrustAgent** and **PerFM** (Personalized Foundation Models), where FedAgent won awards [WS] — [GitHub FedAgent](https://github.com/sunblaze-ucb/FedAgent)

### Inferences
- The main open security problems for federated agents are: (1) poisoning of **LLM aggregators** in artifact-space FL (a malicious skill edit or compendium entry absorbed by the merge agent); (2) Byzantine robustness of federated RL with heterogeneous rewards; (3) membership/attribute inference from text artifacts, where DP-on-text is immature; and (4) prompt injection carried through shared memory or skills.
- Personalization in agent FL is moving from personalized weights to personalized artifact sets (FederatedSkill), which may suit on-device agents with frozen base models.

### Gaps
- **No search coverage** for on-device/mobile agent FL (e.g., personal assistants on phones), because the web-search budget was exhausted. This needs a follow-up search: queries like "on-device LLM agent federated", "mobile GUI agent federated learning", "FedMobileAgent".
- No source found on empirical poisoning or backdoor attacks specifically against FedAgent/Fed-SE/FederatedSkill.

## 5. Protocols (MCP, A2A) in federated/decentralized settings; benchmarks; open problems

### Takeaway
No paper found directly combines **FL training** with MCP or A2A. The protocols appear instead as (a) transport for federated data fusion (MCP in federated digital health), (b) proposals for federated A2A orchestration with decentralized identity (ACP), and (c) a stated privacy gap, since MCP and A2A assume full trust. Benchmarks are emerging: FedAgentGym, AgentFL-Bench, FedAgentBench, and τ-bench/StableToolBench used for federated tool routing.

### Cited Findings
**Protocols**
- **Beyond Context Sharing: A Unified Agent Communication Protocol (ACP) for Secure, Federated, and Autonomous Agent-to-Agent (A2A) Orchestration**, arXiv 2602.15055 [PP]. It proposes a federated orchestration model with decentralized identity verification and names the lack of such a model as the main gap — [arXiv](https://arxiv.org/abs/2602.15055v1)
- **Secure Multi-Modal Data Fusion in Federated Digital Health Systems via MCP**, arXiv 2510.01780 [PP]. Claims beyond the title not verified — [arXiv PDF](https://arxiv.org/pdf/2510.01780)
- **SplitAgent** argues that MCP and A2A assume full trust and data sharing — [arXiv 2603.08221](https://arxiv.org/html/2603.08221)
- **Privacy in Action** (arXiv 2509.17488) [PP] builds a live privacy benchmark running MCP alone and MCP+A2A, and claims its privacy checker is protocol-agnostic — [arXiv](https://arxiv.org/html/2509.17488)
- **The Trust Fabric: Decentralized Interoperability and Economic Coordination for the Agentic Web**, arXiv 2507.07901 [PP] — [arXiv PDF](https://arxiv.org/pdf/2507.07901)
- **From Glue-Code to Protocols: A Critical Analysis of A2A and MCP Integration**, arXiv 2505.03864 [PP] — [arXiv PDF](https://arxiv.org/pdf/2505.03864)
- Typed Federated Artifacts is framed explicitly for the "Agentic Web", with schema-typed, validated exchange objects. This is conceptually a protocol-layer proposal for federated agent knowledge — [arXiv 2609.06815](https://arxiv.org/abs/2609.06815)

**Benchmarks (summary)**
- FedAgentGym: WebShop + ALFWorld, 4 agent types, 3 decentralized settings, 3 heterogeneity types — [ICLR 2026](https://iclr.cc/virtual/2026/10019292)
- AgentFL-Bench: 16 FL system-design tasks, 5 areas — [Helmsman](https://mlanthology.org/iclr/2026/li2026iclr-helmsman/)
- FedAgentBench: 201 medical datasets, 6 modalities, 40 FL algorithms, 24 LLMs evaluated — [arXiv 2509.23803](https://arxiv.org/pdf/2509.23803v1)
- Federated tool routing evaluated on τ-bench retail, StableToolBench, and a math proxy — [arXiv 2609.06815](https://arxiv.org/abs/2609.06815)
- FederatedSkill: 20 agent task families — [arXiv 2606.03143](https://arxiv.org/abs/2606.03143)

**Open problems (as stated by sources)**
- Validating FAO's framework and resolving its privacy and evaluation challenges remain open — [FAO](https://arxiv.org/abs/2610.01195)
- No formal privacy mechanism in text/rule-exchange agent FL — [FAO HTML](https://arxiv.org/html/2610.01195v1)
- No benchmark for private-vs-shared memory classification or for memory integrity under concurrent updates — [ResearchGate](https://www.researchgate.net/publication/415461413_Memory_as_Distributed_State_in_Multi-Agent_LLM_Systems)
- LLM orchestrators fail on interdependent, implicit-goal FL tasks — [FedAgentBench](https://arxiv.org/pdf/2509.23803v1); they misjudge client utility and have token-cost scaling problems — [arXiv 2604.04895](https://arxiv.org/html/2604.04895v1)
- Per-field privacy for typed artifacts may be specified rather than measured (the v1 wording and the abstract wording differ) — [arXiv HTML v1](https://arxiv.org/html/2609.06815v1)

### Inferences
- A natural research gap is **FL over protocol-native artifacts**: federating MCP tool descriptions or A2A agent cards and skills with DP or secure aggregation. Typed Artifacts and FederatedSkill are the closest precursors.
- Benchmark fragmentation is high. No common benchmark yet spans weight-space and artifact-space agent FL under one privacy, communication, and utility metric. FAO's three-objective framing could become the basis for one.

### Gaps
- I found no peer-reviewed work that empirically runs FL or federated agent learning over MCP or A2A.
- I could not verify whether the A2A/MCP specifications themselves (Google/Linux Foundation, Anthropic) have federation or privacy extensions in 2026; the search budget was exhausted.
