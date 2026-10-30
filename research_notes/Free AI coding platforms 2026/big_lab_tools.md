# Free tiers of AI coding tools from major AI labs and big tech (as of 2026-10-30)

> Method note: research was done 2026-10-30. Direct page fetches to chatgpt.com, geminicli.com, docs.github.com, kiro.dev and jetbrains.com were blocked by the session's network allowlist, so for those vendors claims rely on search-engine snippets of the official pages plus secondary coverage. Only claude.com/pricing was fetched directly. Several numbers below come from third-party aggregators and are marked as such; verify against the vendor pages before publishing.

## What exactly is free, with what limits, as of late 2026?

### Takeaway
In late 2026 the free tiers have narrowed and are now mostly metered by credits. Claude Code still is not included on the Claude Free plan. OpenAI includes Codex on ChatGPT Free/Go but publishes no caps. Google retired the free Gemini CLI on 2026-06-18 and moved free users to Antigravity CLI. GitHub Copilot Free still has 2,000 completions/month plus a small AI-credit allowance. Kiro Free gives 50 credits/month, and JetBrains AI Free gives unlimited local completion plus only 3 cloud credits per 30 days.

### Cited Findings

**Anthropic: Claude Code / Claude.ai Free**
- The Claude Free plan costs $0 and includes limited usage that resets every 5 hours and every week, chat on web/desktop/mobile, web search, file creation, code execution, memory, app connections, Artifacts, and Claude Design/Slides/Docs. Fetched 2026-10-30 — [claude.com/pricing](https://claude.com/pricing)
- Claude Code is marked unavailable on Free in the pricing page's feature comparison. Free models are Sonnet and Haiku, and Opus is not included — [claude.com/pricing](https://claude.com/pricing)
- Cheapest paid upgrade: Pro at $20/month billed monthly, or $17/month billed annually ($200/year). Claude Code is included with Pro — [claude.com/pricing](https://claude.com/pricing)
- There is no individual student discount on the pricing page. An institution-level Education plan offers "student and faculty access" — [claude.com/pricing](https://claude.com/pricing)
- Most 2026 third-party guides agree Claude Code requires Pro, Max, a Team premium seat, or API pay-as-you-go. A few aggregators claim a "forever free" Claude Code tier with about 20 interactions a day. Those claims contradict the official page and should be disregarded — [eesel.ai](https://www.eesel.ai/blog/claude-code-pricing); contradicted by [costbench.com](https://costbench.com/software/ai-computer-use/claude-code/free-plan/), [aiproductivity.ai](https://aiproductivity.ai/blog/claude-code-pricing/)
- Free users can still do coding work in Claude.ai chat, with code execution and Artifacts, within the 5-hour and weekly limits — [claude.com/pricing](https://claude.com/pricing)

**OpenAI: Codex / ChatGPT Free**
- OpenAI's Codex pricing page (search snippet) says Codex is included in the Free and Go plans. Local messages and cloud tasks share the plan's usage allowance, and weekly limits may also apply — [chatgpt.com/codex/pricing](https://chatgpt.com/codex/pricing/); [developers.openai.com/codex/pricing](https://developers.openai.com/codex/pricing)
- OpenAI's help-center snippet contradicts itself: it says Codex is included across Free and Go and also says it is "not included with Free or Go". The live page needs checking — [help.openai.com](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan)
- Third-party guides disagree on which surfaces Free/Go users get:
  - One says Free and Go ($8/month) get a small coding model in the ChatGPT desktop app only, with Codex CLI, IDE, web and iOS starting at Plus — [eesel.ai](https://www.eesel.ai/blog/openai-codex-free-access-explained)
  - Another says any ChatGPT plan, including Free, can use Codex CLI by signing in with ChatGPT — [inventivehq.com](https://inventivehq.com/blog/codex-cli-pricing-explained)
  - Unresolved.
- OpenAI does not publish Free/Go caps. A community forum post notes Go limits are undocumented. Users report quotas resetting monthly instead of weekly, and one Free user hit the limit after about 30 minutes — [community.openai.com](https://community.openai.com/t/is-a-codex-free-vs-plus-limits-comparison-possible/1382408); [community.openai.com](https://community.openai.com/t/usage-quotas-changed-to-monthly/1382696)
- Free/Go users cannot buy extra Codex credits and are prompted to upgrade to Plus. Remaining limits can be checked with `/status` in the CLI — [community.openai.com (via search)](https://community.openai.com/t/is-a-codex-free-vs-plus-limits-comparison-possible/1382408)
- OpenAI runs a "Codex for Students" program page. Its terms were not retrieved — [developers.openai.com/community/students](https://developers.openai.com/community/students)
- Paid ladder: Go about $8/month, Plus $20/month (the usual baseline for regular Codex use), Pro tiers $100 and $200. These prices come from secondary sources — [seawork.ai](https://seawork.ai/en/blogs/codex-pricing/); [eesel.ai](https://www.eesel.ai/blog/openai-codex-free-access-explained)

**Google: Gemini CLI, Antigravity CLI, Code Assist, Jules, AI Studio, Firebase Studio**
- **Gemini CLI.** Google's GitHub discussion says Gemini CLI stopped serving requests for free, Google AI Pro and Ultra users on 2026-06-18, and those tiers moved to Antigravity CLI. Enterprise Gemini Code Assist Standard/Enterprise licenses keep access — [GitHub discussion #27274 (google-gemini/gemini-cli)](https://github.com/google-gemini/gemini-cli/discussions/27274)
- **Gemini CLI after the cutoff.** Antigravity CLI was announced at Google I/O on 2026-05-19, giving about a 30-day migration window. Gemini CLI reportedly still works with paid Gemini API keys after 2026-06-18 — [inventivehq.com](https://inventivehq.com/blog/gemini-cli-deprecated-antigravity-cli-migration); [digitalapplied.com](https://www.digitalapplied.com/blog/gemini-cli-to-antigravity-cli-migration-june-18-2026-guide)
- **Gemini CLI historical quotas (pre-retirement).** The geminicli.com quota page, seen only as a search snippet, listed:
  - 1,000 requests/day with a free Google-account login
  - 250 requests/day with a free Gemini API key, Flash models only
  - 1,500/day on AI Pro and 2,000/day on AI Ultra

  Some guides also cited 60 requests/min and a 1M-token context window. These figures are probably outdated for consumer users after 2026-06-18 — [geminicli.com quota page](https://geminicli.com/docs/resources/quota-and-pricing/); [inventivehq.com](https://inventivehq.com/blog/gemini-cli-free-tier-guide)
- **Gemini Code Assist for individuals.** It was reported as free with 180,000 code completions/month, 240 chat requests/day, and AI code reviews in GitHub — [dev.to](https://dev.to/alanwest/google-made-gemini-code-assist-free-whats-the-catch-4cae). One aggregator dates this to March 2026, but my training data has it launching in February 2025, so the date is unverified. Free Code Assist CLI/agent usage was folded into the 2026-06-18 cutoff above — [inventivehq.com](https://inventivehq.com/blog/gemini-cli-deprecated-antigravity-cli-migration)
- **Jules.**
  - Older listing: 15 tasks/day and 3 concurrent tasks on the free tier, on Gemini Flash — [GitHub list of free Google AI tools](https://github.com/Moh4696/list-of-free-google-ai-tools)
  - One 2026 review says Google ended free, Pro and Ultra access to Jules on 2026-06-18 and redirected users to Antigravity CLI — [codegen.com](https://codegen.com/ai-tools/gemini-cli/)
  - Only one source makes the shutdown claim, so it is unverified.
- **Firebase Studio.** An official Firebase docs page says new workspace creation and sign-ups were disabled from 2026-06-22. Existing workspaces can migrate to Google AI Studio or Google Antigravity, and full retirement is scheduled for 2027-03-22 — [firebase.google.com/docs/studio/ai-assistance](https://firebase.google.com/docs/studio/ai-assistance?authuser=0000&hl=it)
- **Google AI Studio.**
  - It still has a free tier that needs no credit card. Free access is rate-limited to the Gemini Flash/Flash-Lite lineup, Gemini 2.5 Pro/Flash and Gemma 4. The current 3.x Pro models are reported to be paid-only — [pricepertoken.com](https://pricepertoken.com/endpoints/google-ai-studio/free)
  - Per-model limits are per project and shown in the AI Studio console. They were cut several times in 2025 and early 2026 — [pricepertoken.com](https://pricepertoken.com/endpoints/google-ai-studio/free); [tinkerllm.com](https://tinkerllm.com/blog/gemini-api-free-tier-limits-rate-quotas/)

**GitHub Copilot**
- GitHub docs (snippet): Copilot Free includes 2,000 code completions/month plus an allowance of AI credits, with model access through auto model selection only. Copilot Student gets unlimited completions and auto model selection only — [docs.github.com: individual billing](https://docs.github.com/en/copilot/concepts/billing-and-usage/individuals/billing)
- On 2026-06-01 GitHub replaced premium request units with token-metered AI Credits, at 1 credit = $0.01 — [docs.github.com](https://docs.github.com/en/copilot/concepts/billing-and-usage/individuals/billing); [findskill.ai](https://findskill.ai/blog/github-copilot-ai-credits-billing-june-2026/)
- The exact number of Free AI credits was not found. Older and secondary sources still cite 50 chat/agent requests per month. One says Free has a hard 50-message cap rather than a credit pool — [costbench.com](https://costbench.com/software/ai-coding-assistants/github-copilot/free-plan/); [daily.dev](https://daily.dev/posts/github-copilot-billing-change-ai-credits-agent-mode-costs-and-how-to-set-a-spending-cap-hqf886txl)
- Students: the Copilot Student plan gets 200 AI credits/month from 2026-06-01 plus unlimited completions. Students report using up all 200 credits within days — [GitHub community discussion #189268](https://github.com/orgs/community/discussions/189268); [discussion #197557](https://github.com/orgs/community/discussions/197557)
- Copilot Pro is free for verified teachers and maintainers of popular open-source projects — [docs.github.com plans](https://docs.github.com/en/copilot/get-started/plans)
- Pro costs $10/month for 1,500 AI credits (1,000 base plus 500 flex), per GitHub docs (snippet). One secondary source says only 1,000 — [docs.github.com plans](https://docs.github.com/en/copilot/get-started/plans); [tech-insider.org](https://tech-insider.org/ie/github-copilot-ai-credits-pricing-2026/)
- Copilot coding agent: whether it is on Free was not confirmed. Agent runs consume AI credits, while standard completions do not — [daily.dev](https://daily.dev/posts/github-copilot-billing-change-ai-credits-agent-mode-costs-and-how-to-set-a-spending-cap-hqf886txl)
- Signup: a GitHub account is required. Students need GitHub Education verification — [github.com/education/students](https://github.com/education/students)

**Amazon: Kiro / Amazon Q Developer**
- New Kiro users start on a perpetual Kiro Free tier. The usage cap renews each billing cycle — [kiro.dev/docs/billing](https://kiro.dev/docs/billing/)
- Kiro Free gives 50 credits/month. Paid plans are Pro $20 (1,000 credits), Pro+ $40 (2,000) and Power $200 (10,000), with overage at $0.04/credit. These figures are from aggregators — [stackpick.net](https://www.stackpick.net/pricing/kiro/); [morphllm.com](https://www.morphllm.com/kiro-pricing)
- One aggregator reports a one-time 500 bonus credits for new users, valid 30 days. Unconfirmed — [usagebar.com](https://usagebar.com/blog/kiro-pricing-and-rate-limits)
- Amazon Q Developer: new Free Tier accounts and new Pro subscriptions reportedly closed in May 2026. Existing users are supported until end of support on 2027-04-30, and AWS is steering users to Kiro. The old Q free tier offered 50 agentic requests/month — [usagepricing.com](https://www.usagepricing.com/blueprint/activity/amazon-q-developer-2026-04-30-deprecation); [costbench.com](https://costbench.com/software/developer-copilots-enterprise/amazon-q-developer/)

**JetBrains: AI Assistant / Junie**
- AI Free includes unlimited code completion and local-model use, plus 3 AI Credits per 30 days for cloud features including Junie. No top-up is allowed on AI Free. It requires IDE 2025.1 or later and is not available in Android Studio or the PyCharm/IntelliJ IDEA Community editions — [JetBrains Junie Help: licensing](https://www.jetbrains.com.cn/help/junie/licensing-and-subscriptions.html)
- The AI Free tier launched with the 2025.1 IDEs in April 2025 — [JetBrains blog 2025-04-16](https://blog.jetbrains.com/blog/2025/04/16/jetbrains-ides-go-ai/); [devclass.com](https://devclass.com/2025/04/16/jetbrains-goes-live-with-junie-ai-agent-updates-ai-assistant-adds-free-tier/)
- From 2026-09-01, organizations on JetBrains Central share a credit pool, with each AI Free license contributing 3 credits — [usagepricing.com](https://www.usagepricing.com/blueprint/activity/jetbrains-ai-2026-09-01-packaging)
- One aggregator lists 35 credits/month for AI Free. This contradicts JetBrains' own help page — [airframe.ai](https://www.airframe.ai/product/jetbrains-com-ai/compare)

### Inferences
- Claude Code is the only flagship lab coding agent with no free tier at all. The cheapest route is Pro at $17–20/month, or API pay-as-you-go.
- "Free" in 2026 increasingly means a small credit or token allowance (GitHub AI credits, Kiro credits, JetBrains credits) rather than counted requests. That makes free usage harder to compare and easier to exhaust with agentic workloads.

### Gaps
- The exact free-tier limits of Google Antigravity CLI and the Antigravity IDE were not found.
- The exact AI-credit allowance for Copilot Free after 2026-06-01, and whether the Copilot coding agent is available on Free, were not confirmed.
- Exact Codex Free/Go quotas are unpublished. Which surfaces (CLI, IDE, cloud) Free users can use remains contradictory.
- Jules' current free status (15 tasks/day vs shut down 2026-06-18) is unresolved.
- Kiro's free-tier models and its new-user bonus are unconfirmed.
- No research was done on xAI (Grok Code), Meta, or Microsoft-specific offerings beyond GitHub Copilot, because of the tool-call budget.

## What changed recently (2025–2026)?

### Takeaway
2026 brought large structural changes:
- Google retired free Gemini CLI on 2026-06-18 in favour of Antigravity CLI, and closed Firebase Studio signups on 2026-06-22.
- GitHub switched Copilot to token-metered AI Credits on 2026-06-01.
- AWS closed new Amazon Q Developer signups in May 2026 in favour of Kiro.
- JetBrains moved to pooled credits on 2026-09-01.

### Cited Findings
- Google I/O (2026-05-19) announced Antigravity CLI. Gemini CLI stopped serving free, AI Pro and Ultra users on 2026-06-18 — [GitHub discussion #27274](https://github.com/google-gemini/gemini-cli/discussions/27274); [aibuilderclub.com](https://www.aibuilderclub.com/blog/google-kills-gemini-cli-june-18-2026)
- Firebase Studio sign-ups and new workspaces were disabled 2026-06-22, with retirement on 2027-03-22 — [firebase.google.com](https://firebase.google.com/docs/studio/ai-assistance?authuser=0000&hl=it)
- GitHub Copilot moved from PRUs to AI Credits on 2026-06-01. The Student plan got 200 credits/month. One source says a spending-cap control was added 2026-07-02 — [GitHub discussion #189268](https://github.com/orgs/community/discussions/189268); [daily.dev](https://daily.dev/posts/github-copilot-billing-change-ai-credits-agent-mode-costs-and-how-to-set-a-spending-cap-hqf886txl)
- Amazon Q Developer closed new Free and Pro signups in May 2026, with end of support on 2027-04-30 — [usagepricing.com](https://www.usagepricing.com/blueprint/activity/amazon-q-developer-2026-04-30-deprecation)
- The JetBrains AI Free tier was introduced in April 2025. Pooled organization credits started 2026-09-01. An unconfirmed October 2026 change reportedly metered all tiers in $1 AI Credits — [JetBrains blog](https://blog.jetbrains.com/blog/2025/04/16/jetbrains-ides-go-ai/); [usagepricing.com](https://www.usagepricing.com/blueprint/activity/jetbrains-ai-2026-09-01-packaging)
- OpenAI extended Codex to Free/Go plans, date not pinned down. Users report quota resets moving from weekly to monthly — [chatgpt.com/codex/pricing](https://chatgpt.com/codex/pricing/); [community.openai.com](https://community.openai.com/t/usage-quotas-changed-to-monthly/1382696)

### Inferences
- The trend is consolidation: Google moved to Antigravity and AWS to Kiro. Billing shifted from request counts to token or credit metering, which in practice tightens free agentic usage.

### Gaps
- The exact date OpenAI opened Codex to Free/Go was not found.
- Whether Gemini Code Assist's IDE extension, as opposed to its CLI/agent mode, remains free for individuals after June 2026 is unconfirmed.

## Which give the most generous free usage for real coding work?

### Takeaway
For autocomplete, the most generous verified options are:
- JetBrains AI Free (unlimited local completion)
- Gemini Code Assist for individuals (180k completions/month, if still active)
- GitHub Copilot Free (2,000/month)

For agentic work, every free tier is small: 50 Kiro credits, 3 JetBrains credits, a small GitHub credit pool, and unpublished Codex caps. Students and OSS maintainers get much more through free Copilot Pro or Student.

### Cited Findings
- Gemini Code Assist for individuals: 180,000 completions/month and 240 chats/day — [dev.to](https://dev.to/alanwest/google-made-gemini-code-assist-free-whats-the-catch-4cae)
- Copilot Free: 2,000 completions/month. Copilot Pro is free for verified teachers and OSS maintainers, with 1,500 AI credits — [docs.github.com](https://docs.github.com/en/copilot/get-started/plans)
- Before its retirement, Gemini CLI's 1,000 free requests/day was the most generous free agentic CLI quota. It is no longer available to free users — [geminicli.com](https://geminicli.com/docs/resources/quota-and-pricing/); [GitHub discussion #27274](https://github.com/google-gemini/gemini-cli/discussions/27274)
- Google AI Studio still offers a no-card free API tier on Flash-class models. It can drive third-party coding agents, within per-project rate limits — [pricepertoken.com](https://pricepertoken.com/endpoints/google-ai-studio/free)
- Codex on Free: one user ran out after about 30 minutes of work — [community.openai.com](https://community.openai.com/t/is-a-codex-free-vs-plus-limits-comparison-possible/1382408)

### Inferences
- Without verified Antigravity CLI quotas, no big-lab free agent can be confirmed as suitable for sustained daily coding in late 2026. The best free "agentic" value is likely Antigravity (unverified), the AI Studio API key paired with an open-source agent, or the education/OSS programs (free Copilot Pro).

### Gaps
- No head-to-head measurement of free-tier throughput was found.
- Antigravity CLI free quotas are the most important missing data point.
