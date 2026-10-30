# Open-Source AI Coding Agents and Free Model Access (as of 2026-10-30)

Research method note: Research done on 2026-10-30. Direct fetches of openrouter.ai and ai.google.dev failed because the research sandbox could not reach them, so OpenRouter and Gemini figures come from secondary sources. github.com pages were fetched directly. Many sources are vendor-written or aggregator blogs, and they often contradict each other. The notes flag these cases. **Free tiers changed a lot in 2026, mostly getting smaller. Check every number against the provider's own page before publishing.**

## Q1: Open-source agents and extensions: license, how they work, status

### Takeaway
The open-source agent layer is still healthy and free. The main options are Cline, Kilo Code, OpenCode, Continue, Aider, Goose, OpenHands and Qwen Code. The big 2026 change is that **Roo Code was shut down on May 15, 2026**, and Kilo Code was rebuilt on OpenCode. All of these are BYOK (bring your own key) shells, so what you pay depends on the model you plug in.

### Cited Findings
- **Roo Code: shut down.** The GitHub repo says "This repository was archived by the owner on May 15, 2026. It is now read-only" and "The Roo Code Extension was shut down on May 15th." It points users to ZooCode (a community fork) and Cline. License: Apache 2.0. (fetched 2026-10-30) — [GitHub RooCodeInc/Roo-Code](https://github.com/RooCodeInc/Roo-Code)
- A third-party review says existing Roo installs still run but get no security patches. Kilo's site says the sunset was announced in April 2026. — [morphllm Kilo vs Cline](https://www.morphllm.com/comparisons/kilo-code-vs-cline); [kilo.ai Roo vs Cline vs Kilo](https://kilo.ai/compare/roo-vs-cline-vs-kilo) (vendor source)
- **Kilo Code** is positioned as Roo's successor and reads `.roomodes` and Roo rules. It was rebuilt in April 2026 on the OpenCode server and runs in VS Code, JetBrains and a CLI. License: Apache-2.0 for the extension, MIT for the CLI core. — [morphllm Kilo vs OpenCode](https://www.morphllm.com/comparisons/kilo-code-vs-opencode); [kilo.ai/landing/roo-code](https://kilo.ai/landing/roo-code) (vendor)
- Kilo's free access goes through "Kilo Gateway", which offers some free models. A March 2026 comparison says new users get $20 in credits (not verified as current). — [kilo.ai OpenCode Zen vs Kilo Gateway](https://kilo.ai/compare/opencode-zen-vs-kilo-gateway); [stackpick Kilo pricing](https://stackpick.net/pricing/kilo-code/)
- **Cline** is an Apache-2.0 VS Code-first agent with step-by-step approval and the largest community of the group (58K+ stars per one comparison). Its "Cline Provider" lists models marked FREE after sign-in. — [fast.io Kilo vs Cline](https://fast.io/resources/kilo-code-vs-cline/); [kdnuggets 5 free agents 2026](https://kdnuggets.com/5-free-ai-coding-agents-you-should-try-in-2026)
- **OpenCode** is MIT-licensed and terminal-native, and also ships as a desktop app and IDE extension. Reported star counts vary between about 147K and 213K, so treat them as unreliable. "OpenCode Zen" offers some free models that rotate (examples cited: "Big Pickle", Grok Code Fast, GLM 4.7). — [wetheflywheel open-source agents 2026](https://wetheflywheel.com/en/guides/open-source-ai-coding-agents-2026/); [OpenCode School models lesson](https://opencode.school/lessons/models/)
- **Qwen Code CLI** is Apache-2.0. It "Supports OpenAI, Anthropic, Gemini, and Qwen APIs" plus "Any third-party provider or local model (Ollama / vLLM)", and you configure it with `/auth`. (fetched 2026-10-30) — [GitHub QwenLM/qwen-code](https://github.com/QwenLM/qwen-code)
- **Goose** (originally from Block) was donated to the Linux Foundation's Agentic AI Foundation (AAIF), which formed in December 2025 alongside MCP and AGENTS.md. The repo moved to github.com/aaif-goose/goose on April 7, 2026. License: Apache 2.0. It ships as a desktop app, CLI and API. — [DevOps Digest AAIF announcement](https://devopsdigest.com/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation-aaif); [rywalker Goose](https://rywalker.com/research/goose); [knightli goose 2026-05](https://knightli.com/2026/05/08/goose-open-source-ai-agent-desktop-cli-api/)
- **Crush** (Charm) is licensed FSL-1.1-MIT. That makes it source-available, not OSI open source: competing commercial use is restricted, and each release converts to MIT after two years. The latest version was v0.85.0 as of a July 2026 review, which may be stale. — [vibecodinghub Crush review](https://vibecodinghub.org/blog/crush-coding-agent-review); [rywalker Crush](https://rywalker.com/research/crush)
- **Continue** is listed as Apache-2.0 (page last updated June 16, 2026). — [terminaltrove Crush vs Continue](https://terminaltrove.com/compare/ai-coding-agents/charm-cli-vs-continue/)
- **Mistral Vibe CLI** is open source (Apache 2.0), launched December 2025 with Devstral 2. — [introl Vibe CLI](https://introl.com/blog/mistral-vibe-cli-devstral-2-claude-code-competitor-2025); [hackernoon](https://sia.hackernoon.com/mistral-bets-on-enterprise-vibe-coding-with-devstral-2-and-an-open-source-cli-agent)
- **Aider** is described as "the most mature git-native workflow option". — [kdnuggets](https://kdnuggets.com/5-free-ai-coding-agents-you-should-try-in-2026)
- An August 2026 source says Anthropic stopped letting Claude subscriptions cover third-party tools such as OpenCode and Cline. — [agent37 Claude Code free alternatives](https://www.agent37.com/blog/claude-code-free-alternative) (single source, verify)

### Inferences
- In 2026 the IDE options are Cline (most conservative and mature) and Kilo Code (most features, Roo's successor). In the terminal, the options are OpenCode, Aider, Qwen Code, Goose and Crush. Any Roo Code guide written before May 2026 is outdated.
- Because every tool is BYOK, "free" depends on the model source (Q2). The free credits bundled with OpenCode Zen, Kilo Gateway and Cline Provider are promotional and change often.

### Gaps
- I could not verify from primary sources in this session: Aider's license (Apache-2.0 per prior knowledge) and latest 2026 version; OpenHands' license (MIT core per prior knowledge) and 2026 status; Continue's 2026 product changes; current GitHub star counts. Check these at github.com/Aider-AI/aider, github.com/OpenHands/OpenHands and github.com/continuedev/continue.
- Not researched in depth: other notable 2025–2026 agents such as ZooCode (Roo fork), Antigravity CLI and Kimi Code CLI.

## Q2: Free model sources: exact limits and catches

### Takeaway
Free model access shrank sharply in 2026. **The Qwen OAuth free tier ended on April 15, 2026.** **Gemini CLI's free personal access reportedly ended on June 18, 2026.** **Cerebras' no-card free tier reportedly ended in July 2026.** **GitHub Models may have been retired on July 30, 2026** (unconfirmed). What remains: the OpenRouter `:free` models (20 RPM; 50 requests/day, or 1,000/day after a one-time $10 credit purchase), the Gemini API free tier (Flash-class models, low RPD, data used to improve Google products), Groq's free tier, rotating free models in OpenCode Zen, Kilo and Cline, and cheap paid options (DeepSeek V4 API, GLM Coding Plan).

### Cited Findings
**Qwen Code / Qwen OAuth**
- The daily free quota was cut from 1,000 to 100 requests (dated around April 13, 2026), and the free OAuth tier then ended on April 15, 2026, per the Qwen Code README and docs as reported. The CLI itself remains free. Users must switch to a paid Alibaba Cloud Coding Plan, OpenRouter, Fireworks or their own API key. — [inventivehq: Qwen Code still free 2026 shutdown](https://inventivehq.com/blog/qwen-code-still-free-2026-shutdown); [GitHub issue #3203](https://github.com/QwenLM/qwen-code/issues/3203); [sessionwatcher](https://sessionwatcher.com/news/qwen-code-oauth-free-quota-1000-requests-per-day)
- Conflict: SessionWatcher reports a 2,000 to 1,000 cut at an unclear date. The current README I fetched no longer mentions any OAuth free tier, which is consistent with its removal. — [GitHub QwenLM/qwen-code](https://github.com/QwenLM/qwen-code)

**OpenRouter free models (`:free` IDs)**
- 20 requests/minute for all accounts. 50 requests/day if you have bought less than $10 of credits in total, and 1,000 requests/day once you have bought at least $10 (a permanent upgrade even if the balance later reaches zero). The window resets daily at UTC, and going over returns HTTP 429. Usage can be checked via `GET /api/v1/key` (`free_model_daily_requests`). One source says it read OpenRouter's docs on 2026-10-17. — [developersdigest OpenRouter rate limits](https://www.developersdigest.tech/rate-limits/openrouter); [TrueFoundry](https://www.truefoundry.com/es/blog/openrouter-rate-limits-your-free-tier-depends-on-what-youve-already-paid)
- Unconfirmed: failed requests may count toward the daily cap. — [TrueFoundry](https://www.truefoundry.com/es/blog/openrouter-rate-limits-your-free-tier-depends-on-what-youve-already-paid)
- OpenRouter's own 2026 comparison of free LLM APIs exists but was not fetched. — [openrouter.ai blog](https://openrouter.ai/blog/tutorials/free-llm-apis-compared/)
- Catch (general knowledge, unverified this session): some free providers on OpenRouter log or train on prompts. Check each model's privacy setting.

**Google Gemini API (AI Studio) and Gemini CLI**
- Free-tier limits were cut 50–80% around December 6–7, 2025. Early-2026 reports range from 5–15 RPM and 100–1,000 RPD depending on model. — [agentdeals Gemini pricing changes](https://agentdeals.dev/gemini-api-pricing-changes); [tinkerllm](https://tinkerllm.com/blog/gemini-api-free-tier-limits-rate-quotas/)
- Conflict on which models are free. One source (April 2026) says the free tier is Flash/Flash-Lite only and 3.1 Pro is paid-only from April 1, 2026. A September 2026 source says Flash, Flash-Lite and 2.5 Pro remain free. — [agentdeals Gemini 2026](https://agentdeals.dev/gemini-api-pricing-2026); [costbench Gemini free plan](https://costbench.com/software/llm-api-providers/google-gemini-api/free-plan/)
- Gemini CLI: reportedly on **June 18, 2026** Google stopped serving Gemini CLI requests for free, Google AI Pro/Ultra and individual Code Assist accounts, and pointed individuals to the "Antigravity CLI". The free quota there is unpublished, with community reports of "low tens of requests per day". A free API key in Gemini CLI is reported at 250 requests/day on Flash only. Code Assist Standard gets 1,500 per user per day and Enterprise 2,000. The old figure of 1,000 RPD / 60 RPM from June 2025 is **outdated**. — [tembo Gemini CLI pricing](https://www.tembo.io/blog/gemini-cli-pricing); [amplifying.ai Gemini CLI / Antigravity](https://amplifying.ai/coding-agents/gemini-cli); [fast.io Code Assist limits](https://fast.io/resources/gemini-code-assist-limits/)
- Catch (prior knowledge, verify on ai.google.dev/gemini-api/terms): content sent on the unpaid tier may be used to improve Google products and reviewed by humans. The free tier is also unavailable in some regions (EU/UK rules have differed over time).

**Groq / Cerebras**
- Groq: older guides give about 30 RPM and 14,400 RPD on Llama 3.3 70B with no card. Another gives about 30 RPM and 6,000 TPM. A German roundup says Groq moved to per-model daily limits in 2026. — [freeapihub Cerebras vs Groq](https://freeapihub.com/blog/cerebras-vs-groq-fast-inference-api); [mydealz roundup](https://www.mydealz.de/deals/sammeldeal-kostenlose-ki-api-kapazitaten-fur-entwickler-gemini-groq-openrouter-cloudflare-workers-ai-cerebras-hugging-face-2783697)
- Cerebras: the open free tier (about 1M tokens/day, no card) reportedly ended for new accounts on July 21, 2026. On October 5, 2026 the tiers were reportedly merged into pay-as-you-go with a $5 starter credit. — [claudelog Is Cerebras free?](https://claudelog.com/faqs/is-cerebras-free/). Conflict: benchlm (citing Cerebras' page as of 2026-09-14) lists a "Free Trial" tier at 5 RPM, 30K uncached TPM and 1M tokens/day, with a card required. — [benchlm Cerebras](https://benchlm.ai/md/free-tier/cerebras.md). Many guides that still say "1M tokens/day, no card" are probably outdated.

**GitHub Models**
- One page (reviewed 2026-10-24) says GitHub Models was **fully retired on July 30, 2026**. — [fast.io GitHub Models rate limit](https://fast.io/resources/github-models-rate-limit/). I could not confirm this independently. Earlier figures: Copilot Free about 10 RPM, 8K input / 4K output tokens per request; DeepSeek-R1 1 RPM / 8 RPD. — [freellm GitHub Models](https://freellm.net/providers/github-models); [nitpik wiki](https://github.com/nsrosenqvist/nitpik/wiki/04-GitHub-Models)

**Mistral (Codestral / Devstral / Vibe)**
- Devstral 2 was free on Mistral's API at its December 2025 launch, with later pricing announced at $0.40/$2.00 per M tokens (Devstral 2) and $0.10/$0.30 (Devstral Small). I could not confirm whether the free window has ended. — [introl](https://introl.com/blog/mistral-vibe-cli-devstral-2-claude-code-competitor-2025); [TechCrunch via scour](https://scour.ing/@matmat/p/https://techcrunch.com/2025/12/09/mistral-ai-surfs-vibe-coding-tailwinds-with-new-coding-models)
- Mistral's pricing page says its free plan gives "limited access to Vibe … for agentic coding in your terminal". A Le Chat Pro tier is reported at about $14.99/month, and a student plan at €5.99/month. — [mistral.ai/pricing](https://mistral.ai/en/pricing); [pasqualepillitteri](https://pasqualepillitteri.it/en/news/3854/mistral-vibe-student-discount-coding-ai-agent)
- Conflict on Codestral: the pricing page reportedly lists a free Codestral endpoint (text unclear), while a 2026 blog says $0.30/$0.90. — [mistral.ai/pricing](https://mistral.ai/en/pricing)

**DeepSeek / Zhipu GLM / Kimi (Moonshot)**
- DeepSeek has no subscription, only pay-as-you-go API pricing. V4 Pro is reported at about $0.435 input / $0.87 output per M tokens, possibly an off-peak rate (one source says 2× at peak). A cheaper V4 Flash tier also exists. The chat web app has historically been free (not re-verified this session). — [devtk GLM-5.3 vs DeepSeek V4 vs Kimi K3](https://devtk.ai/en/blog/glm-5-3-vs-deepseek-v4-kimi-k3-2026/); [andrew.ooo](https://andrew.ooo/answers/deepseek-v4-pro-vs-glm-5-3-vs-kimi-k3-cheap-frontier-august-2026/)
- Z.ai GLM Coding Plan is reported at about $18/month Lite, $80 Pro and $168 Max. GLM-5.3 is said to be available in the Coding Plan before the standard API. — [mindstudio open-model coding plans](https://www.mindstudio.ai/blog/open-model-coding-plans-glm-kimi-deepseek); [devtk](https://devtk.ai/en/blog/glm-5-3-vs-deepseek-v4-kimi-k3-2026/)
- Kimi: the Kimi K3 API is reported at $3/$15 per M tokens. Kimi Code plans are reported at about $19 (Moderato) and $49 (Allegretto) as of April 2026, but another source says the plan was not purchasable at the time it was written. — [devtk](https://devtk.ai/en/blog/glm-5-3-vs-deepseek-v4-kimi-k3-2026/); [mindstudio](https://www.mindstudio.ai/blog/open-model-coding-plans-glm-kimi-deepseek)

### Inferences
- "Free" in late 2026 mostly means **rate-limited trial access**. None of the remaining free API tiers supports a full day of agentic coding: one agent task can use 20–100 requests, so 50 OpenRouter requests a day runs out fast. The sustainable "nearly free" options are a one-time $10 OpenRouter top-up (unlocks 1,000 free requests/day), DeepSeek V4 pay-as-you-go (cents per task), or the roughly $18/month GLM Coding Plan.
- Data and privacy catches matter most for free tiers. Gemini's unpaid tier and many OpenRouter free endpoints may log or train on prompts. Chinese providers (DeepSeek, Zhipu, Moonshot, Alibaba) process data in or under PRC jurisdiction, which some employers forbid.

### Gaps
- No primary-source confirmation (sites blocked or not fetched): exact current Gemini free RPM/RPD per model; Groq's current per-model limits; Cerebras' status; whether GitHub Models was retired; Mistral's current free API tier (the "Experiment" plan); DeepSeek's official price page; official Z.ai and Moonshot plan pages.
- Region restrictions per provider were not researched in detail.

## Q3: Running locally (Ollama / LM Studio) and hardware needs

### Takeaway
Local inference is the only option that is truly unlimited and private. Rough guide: about 16 GB VRAM runs gpt-oss-20b, and about 24 GB runs Qwen3-Coder-30B-A3B or Devstral Small 2 at Q4. Frontier open models (Qwen3-Coder-480B, DeepSeek V4, GLM-5.x, Kimi) need around 150 GB or more of memory, which is out of reach for consumer hardware.

### Cited Findings
- gpt-oss-20b: runs in 16 GB (MXFP4 build about 14 GB). A 128K context fits in 16 GB only at modest context lengths. gpt-oss-120b needs one 80 GB GPU. — [morphllm best Ollama models (Aug 2026)](https://www.morphllm.com/best-ollama-models); [computingforgeeks Ollama cheat sheet](https://computingforgeeks.com/ollama-models-cheat-sheet/)
- Qwen3-Coder 30B-A3B (MoE): the default Ollama tag is about 19 GB and fits a 24 GB card at Q4. Qwen3-Coder-480B needs about 150 GB for 6+ tokens/s even at Unsloth's UD-Q2_K_XL quantization. — [inventivehq run Qwen3-Coder locally](https://inventivehq.com/blog/run-qwen3-coder-locally-ollama); [morphllm](https://www.morphllm.com/best-ollama-models)
- Devstral Small 2 (24B): one source says a 24 GB card runs it comfortably at 32K context. Another lists 16 GB VRAM and 256K context, which conflicts. SWE-bench Verified is reported at 68% in one source and 72.2% in another. The older devstral:24b scored 46.8%. — [Kilo blog best local coding models](https://blog.kilo.ai/p/the-best-local-coding-models-for); [morphllm](https://www.morphllm.com/best-ollama-models)
- All the agents above (Cline, Kilo, OpenCode, Aider, Qwen Code, Goose, Continue) accept OpenAI-compatible local endpoints. For example, Qwen Code explicitly supports "local model (Ollama / vLLM)". — [GitHub QwenLM/qwen-code](https://github.com/QwenLM/qwen-code)

### Inferences
- Local 20–30B models handle autocomplete, small edits and simple agent loops, but clearly lag hosted frontier models on long multi-file agentic tasks. Agent tools also need a large context (32K or more), which pushes up VRAM and unified-memory needs. Macs with 32–64 GB of unified memory are a common sweet spot (inference, not verified this session).

### Gaps
- LM Studio specifics, Apple Silicon throughput figures, and DeepSeek or GLM distilled local variants were not verified this session.

## Synthesis: best free combinations (late October 2026), for the report writer
- **Zero cost, no card:** Cline or Kilo Code (IDE), or OpenCode (terminal), using their built-in rotating free models (Cline Provider FREE models, Kilo Gateway, OpenCode Zen). Add OpenRouter `:free` models (50 requests/day) and the Gemini API free key (Flash) as fallbacks. Sources: see Q1 and Q2.
- **Best value for about $10 once:** buy $10 of OpenRouter credit to unlock 1,000 free-model requests/day, and use it with Cline, Kilo, OpenCode or Aider.
- **Cheap paid backbone:** DeepSeek V4 API (well under $1 per M tokens) or the GLM Coding Plan (about $18/month), with any BYOK agent.
- **Private and unlimited:** Ollama with Qwen3-Coder-30B or gpt-oss-20b, used through Continue, Cline, Aider or Qwen Code, on a 16–24 GB GPU.
- **Avoid or outdated:** Roo Code (shut down May 15, 2026), the Qwen OAuth free tier (ended April 15, 2026), the Gemini CLI personal free tier (reportedly ended June 18, 2026), Cerebras' no-card 1M tokens/day (reportedly ended July 2026), and GitHub Models (reportedly retired July 30, 2026, unconfirmed).
