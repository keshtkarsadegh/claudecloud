# Free Tiers of AI-Native Code Editors / IDEs (as of 2026-10-30)

> Method note: Research done 2026-10-30. Direct page fetches of cursor.com, windsurf.com, zed.dev, kiro.dev and trae.ai were BLOCKED by this session's network allowlist, so official pages could not be read in full. Findings come from web-search result snippets. Official-domain URLs below (cursor.com/help, zed.dev/docs, kiro.dev/faq, kiro.dev/docs/billing, help.aliyun.com) were cited from search snippets, not full-page reads. Most other figures come from third-party pricing aggregators, which often disagree and go stale. Every claim should be re-checked against the vendor page before publication. Big-lab IDEs (Google Antigravity) appear only briefly in case another researcher covers them.

## What exactly is free, with what limits, as of 2026? Is it a permanent free tier or a trial?

### Takeaway
Every major AI IDE in this scope still has a permanent $0 tier, but they are all metered, and agent use runs out fast. Unlimited autocomplete is the most common generous perk (Windsurf/Devin Desktop and Antigravity). Zed (2,000 accepted predictions plus unlimited bring-your-own-key) and Trae (5,000 completions a month) are next. Agent and chat usage is tightly capped everywhere. Cursor publishes no exact Hobby caps.

### Cited Findings

**Cursor: Hobby (free), Pro trial, students**
- Hobby is a permanent free tier at $0 with no credit card. The pricing page describes it only as "limited Agent requests" and "limited Tab completions", with no published numbers — [eastondev guide, 2026-06-04](https://eastondev.com/blog/en/posts/ai/20260604-cursor-free-tier-guide/); [JetAdmin, 2026-07-27](https://www.jetadmin.io/blog/cursor-pricing-explained-plans-credit-system-and-real-costs-in-2026/)
- Hobby has no overage path. Usage pauses at the cap until the cap resets — [JetAdmin, 2026-07](https://www.jetadmin.io/blog/cursor-pricing-explained-plans-credit-system-and-real-costs-in-2026/)
- Community estimates (unofficial) put Hobby at about 2,000 Tab completions a month and about 50 slow premium/agent requests a month. These figures may come from the 2024–25 Hobby definition and could be outdated — [Verdent guide (vendor-authored, competitor)](https://www.verdent.ai/guides/cursor-free-trial-free-plan); [vp0.com](https://vp0.com/blogs/is-cursor-ai-free)
- Pro trial: sources conflict. One says it lasts 7 days and another says about 2 weeks. Accounts revert to Hobby automatically after the trial — [Verdent](https://www.verdent.ai/guides/cursor-free-trial-free-plan); [vp0.com](https://vp0.com/blogs/is-cursor-ai-free)
- Paid plans: Pro $20/mo (a $20 monthly usage/credit pool), Pro+ $60/mo, Ultra $200/mo, and Teams (sometimes called "Business") $40/user/mo — [aicodereview.cc](https://aicodereview.cc/blog/cursor-pricing); [JetAdmin](https://www.jetadmin.io/blog/cursor-pricing-explained-plans-credit-system-and-real-costs-in-2026/)
- Student offer: Cursor's help page says it **discontinued new sign-ups for the legacy student discount (free year of Pro) on June 25, 2026**. Existing redemptions keep their rate until expiry and then roll to $20/mo unless cancelled — [Cursor help: Student discount](https://cursor.com/help/account-and-billing/student-discount.md)
- A cached cursor.com/students page still advertises "one free year of Cursor Pro", which looks stale — [cursor.com/en-US/students](https://cursor.com/en-US/students)
- Replacement for students (lower confidence): event-based credits for undergraduates from fall 2026, and credits on request for graduate students and researchers — [dupple.com](https://dupple.com/learn/how-to-get-cursor-pro-for-free); [aifreeapi guide](https://www.aifreeapi.com/en/posts/cursor-education-complete-guide.md)

**Windsurf (formerly Codeium), now "Devin Desktop" (Cognition)**
- On **March 19, 2026**, Windsurf replaced monthly credits with **daily and weekly usage quotas** that refresh automatically. The quotas are token-measured, so frontier models drain them faster — [provenbrief](https://provenbrief.com/story/windsurf-pricing-plans-quotas-and-what-changed); [usepoly](https://usepoly.co/windsurf-pricing); [Devin blog: new Windsurf pricing plans](https://devin.ai/blog/windsurf-pricing-plans)
- Free plan (permanent): a light agent (Cascade) quota that resets over time, limited models, and **unlimited Tab completions and inline edits**. It has no premium models, Devin Cloud, Devin API, Ask Devin or DeepWiki — [continuumcode](https://continuumcode.ai/guides/windsurf-pricing/); [checkthat.ai](https://checkthat.ai/brands/windsurf/pricing)
- Sources conflict on premium models for free users. One says the free plan excludes them entirely and another says all tiers include them — [Verdent](https://www.verdent.ai/guides/windsurf-pricing-2026); [costbench](https://costbench.com/software/ai-coding-assistants/windsurf/free-plan)
- The older "25 credits/month" free plan figure is outdated (pre-March 2026) — [lowcode.agency (still lists credits)](https://www.lowcode.agency/blog/windsurf-pricing)
- Paid plans: Pro $20/mo (up from $15; pre-switch subscribers grandfathered at $15), Max $200/mo, Teams $40/seat with an $80/mo minimum, Enterprise custom — [provenbrief](https://provenbrief.com/story/windsurf-pricing-plans-quotas-and-what-changed); [eesel.ai](https://www.eesel.ai/blog/windsurf-pricing)
- **June 2026: Cognition rebranded Windsurf to "Devin Desktop"**, and the windsurf.com pricing URL redirects to devin.ai — [promoclock](https://promoclock.co/en/tools/windsurf/); [Wikipedia: Cognition AI](https://en.wikipedia.org/wiki/Cognition_AI)
- A promotion offered free use of SWE-2 (Cognition's in-house model) in Devin Desktop and the CLI until about October 10 or 16, 2026; sources conflict on the date. It has **likely expired as of 2026-10-30** — [usepoly](https://usepoly.co/windsurf-pricing); [checkthat.ai](https://checkthat.ai/brands/windsurf/pricing)

**Zed**
- Personal (free): **$0 forever**, with **2,000 accepted edit predictions** (per month) and **unlimited use with your own API keys (BYOK) or external agents** (e.g., ACP agents such as Claude Code or Gemini CLI). Edit predictions from other providers (GitHub Copilot, local models) also work — [zed.dev/pricing](https://zed.dev/pricing); [Zed docs: Plans & Pricing](https://zed.dev/docs/account/plans-and-pricing)
- Zed is open source, so the editor itself is free regardless of plan — [Zed docs](https://zed.dev/docs/ai/plans-and-usage.html)
- Pro: **$10/mo**, with unlimited edit predictions and $5/mo of token credit. Usage above that is billed at Zed-hosted model rates. One review says the Pro trial includes $20 of token credits — [Zed docs](https://zed.dev/docs/account/plans-and-pricing); [techpresso review](https://academy.techpresso.co/reviews/is-zed-worth-it)
- Student plan (launched **March 2026**): verified university students get **all Pro features free for 1 year**, with $10/mo in AI token credits and unlimited edit predictions — [AlternativeTo news, 2026-03](https://alternativeto.net/news/2026/3/zed-introduces-free-student-plan-with-pro-features-for-university-students)
- Business plan: $30/seat/mo — [costbench](https://costbench.com/software/ai-coding-assistants/zed-ai/)
- Some aggregators (CostBench) still list Pro at $20 and "no AI on free"; both claims are outdated — [costbench](https://costbench.com/software/ai-coding-assistants/zed-ai/free-plan/)

**Trae (ByteDance)**
- Trae launched as a fully free IDE (early 2025). It now has a permanent free tier with **5,000 autocompletions a month**, limited usage on a standard (slow) queue, **limited SOLO (agent) mode**, and limited early access to new models — [costbench](https://costbench.com/software/ai-coding-assistants/trae/free-plan/); [aiidelist, 2026](https://aiidelist.com/blog/trae-ai-ide-pricing-2026)
- **Feb 24, 2026**: Trae moved from request-based to **token-based billing**, with Free, Lite (~$3/mo), Pro (~$10/mo), Pro+ (~$30), and Ultra (~$100) tiers — [IT之家 (ithome)](https://www.ithome.com/0/923/234.htm); [costbench](https://costbench.com/software/ai-coding-assistants/trae/)
- Older guides citing "600 fast requests" on Pro are outdated — [aiidelist](https://aiidelist.com/blog/trae-ai-ide-pricing-2026)
- The free plan's usage credit is unconfirmed: one source says a $3 basic usage credit and another implies $5. Accounts of which models free users get also conflict: one says cheaper models only (frontier models such as GPT-5.4 need payment), while another says Claude/GPT-class/DeepSeek models within quotas — [a2a-mcp.org](https://a2a-mcp.org/blog/is-trae-ide-gpt-5.4-free); [stackpick](https://stackpick.net/tools/trae/)
- SOLO shipped as a standalone app on March 31, 2026. It has limited access on the free tier — [aiidelist](https://aiidelist.com/blog/trae-ai-ide-pricing-2026)

**Kiro (AWS)**
- New users start on the **perpetual Kiro Free tier with 50 credits**, which reset monthly. No overage is available on Free — [Kiro FAQ](https://kiro.dev/faq); [Kiro docs: Billing](https://kiro.dev/docs/billing/)
- Free tier models: "open weight models and Claude Sonnet 4.6, with limits", and "only some models are available in free tier". Third-party sites saying Sonnet 4.5 are older — [Kiro FAQ](https://kiro.dev/faq); [Kiro CLI billing docs](https://kiro.dev/docs/cli/billing/)
- Paid plans: Pro $20/mo (1,000 credits), Pro+ $40 (2,000), Pro Max $100 (5,000), Power $200 (10,000). Overage costs $0.04/credit, opt-in, and unused credits do not roll over — [Kiro docs: Billing](https://kiro.dev/docs/billing/); [morphllm](https://www.morphllm.com/kiro-pricing)
- Signup bonus: one source reports 500 bonus credits for 30 days for new users. This is unverified on the official page — [usagebar](https://usagebar.com/blog/kiro-pricing-and-free-tier)
- Students: one source reports 1 year of Pro free (1,000 credits/mo) for eligible university students. This is unverified on the official page — [usagebar](https://usagebar.com/blog/kiro-pricing-and-rate-limits)
- Pricing announcements on the official blog — [Kiro: pricing plans are live](https://kiro.dev/blog/pricing-plans-are-live/); [Kiro: new pricing plans and Auto](https://kiro.dev/blog/new-pricing-plans-and-auto/)

**Void, PearAI, Melty**
- **Void** (open-source VS Code fork, BYOK/local models): the team **paused development**. The README says work stopped "to explore a few novel coding ideas", features "might stop working over time", and Issues/PRs are not reviewed. The last release still works and costs nothing, since you bring your own keys — [codersera guide 2026](https://codersera.com/blog/void-ide-complete-guide-2026/amp/); [mirrored README](https://github.com/feixuwu/void)
- One directory says the Void repo is archived and deprecated; this is unconfirmed — [aiidelist, 2026-10](https://aiidelist.com/ide/void.md)
- **PearAI**: one source says it has not been updated in about 5 months and may be unmaintained. I found no formal shutdown notice — [openalternative](https://openalternative.co/compare/pearai/vs/void)
- **Melty** (YC S24 chat-first editor): I found no evidence of 2026 activity — [extruct](https://www.extruct.ai/hub/melty-sh-funding)

**Other notable AI IDEs launched 2025–2026**
- **Google Antigravity** (big lab, launched Nov 2025): free Individual tier with unlimited Tab and Command requests, plus a weekly-refreshed agent quota that Google has cut repeatedly since launch. The free tier reportedly includes Claude Opus 4.6 only until **Nov 2, 2026**, then Gemini models only. Pro costs about $20/mo — [preuve.ai review](https://preuve.ai/blog/antigravity-review); [cloudzero](https://www.cloudzero.com/blog/google-antigravity-pricing/)
- **Qoder** (Alibaba, launched Aug 2025): sources conflict on whether it has a permanent free tier. One reports a free tier with limited credits, a 2-week Pro trial and unlimited basic completions, while another says there is no permanent free tier. Qoder CN switched to a Credits quota system on **May 20, 2026**. Pro is listed at $30/mo (2,000 credits) by one source and at $10 promotional by another — [makerstack](https://makerstack.co/reviews/qoder-review/); [stackpick](https://www.stackpick.net/pricing/qoder/); [Alibaba Cloud help: billing](https://help.aliyun.com/en/lingma/billing-description)

### Inferences
- **Most generous for free users (my ranking, depends on use case):**
  1. **Zed**: unlimited, permanent AI via BYOK, external agents or local models, plus 2,000 hosted predictions. It is the best free option if you have your own API key or a free API source (e.g., Gemini free tier, local Ollama).
  2. **Windsurf/Devin Desktop**: unlimited Tab and inline edits, plus a small, auto-refreshing agent quota with no card required. It is the best "zero-setup" autocomplete.
  3. **Trae**: 5,000 completions a month plus a token credit and limited SOLO agent. It is historically the most generous on hosted models, but has tightened since Feb 2026. Some users have privacy and telemetry concerns because ByteDance owns it.
  4. **Kiro**: 50 credits a month is small, but it includes Claude Sonnet 4.6.
  5. **Cursor Hobby**: undisclosed and small caps with no overage. It is mainly a taste of the product.
- Every free tier is permanent rather than a time-limited trial. Trials (Cursor Pro, Qoder Pro, Zed Pro credits) sit on top of the free tier.
- No credit card is required for any free tier, according to the sources that mention it (Cursor, Windsurf). I found no card requirement reported for the others.
- The cheapest paid entry points are Trae Lite at about $3, Zed Pro at $10 and Trae Pro at about $10. Cursor, Windsurf, Kiro and Antigravity all sit at about $20.

## What changed recently (2025–2026) in their pricing/free offers?

### Takeaway
2025–26 saw a broad shift from request counts to token, credit or quota metering, which cuts free and cheap usage of frontier models. Cursor ended its free student year (June 2026), Windsurf moved to quotas and was rebranded Devin Desktop, and Trae went token-based. Zed went the other way by adding a free student plan, and Antigravity's free tier keeps shrinking.

### Cited Findings
- Cursor: Pro became a $20 usage/credit pool (2025). Pro+ ($60) and Ultra ($200) were added. The student free-year program closed to new sign-ups on 2026-06-25 — [JetAdmin](https://www.jetadmin.io/blog/cursor-pricing-explained-plans-credit-system-and-real-costs-in-2026/); [Cursor help](https://cursor.com/help/account-and-billing/student-discount.md)
- Windsurf: credits became daily and weekly quotas on 2026-03-19. Pro went from $15 to $20, Teams from $30 to $40, and Max ($200) was added. The product was renamed Devin Desktop in June 2026 — [provenbrief](https://provenbrief.com/story/windsurf-pricing-plans-quotas-and-what-changed); [promoclock](https://promoclock.co/en/tools/windsurf/)
- Zed: Pro is now $10/mo, the free tier offers 2,000 predictions plus BYOK, and a Student plan arrived in March 2026 — [zed.dev/pricing](https://zed.dev/pricing); [AlternativeTo](https://alternativeto.net/news/2026/3/zed-introduces-free-student-plan-with-pro-features-for-university-students)
- Trae: the fully free launch (2025) gave way to paid tiers. Billing became token-based on 2026-02-24, and SOLO became a standalone app on 2026-03-31 — [ithome](https://www.ithome.com/0/923/234.htm); [aiidelist](https://aiidelist.com/blog/trae-ai-ide-pricing-2026)
- Kiro: it left its free preview and waitlist for paid plans in 2025. Credit tiers now run up to Power ($200). The free model is now Sonnet 4.6 — [Kiro blog](https://kiro.dev/blog/pricing-plans-are-live/); [Kiro FAQ](https://kiro.dev/faq)
- Antigravity: free quotas have been cut repeatedly since Nov 2025, and Claude models leave the free tier on 2026-11-02 — [preuve.ai](https://preuve.ai/blog/antigravity-review)
- Void: development paused (2025–26) — [codersera](https://codersera.com/blog/void-ide-complete-guide-2026/amp/)

### Inferences
- The free tiers are converging on "unlimited autocomplete plus a small agent quota". The free usage that remains for agentic work comes from BYOK (Zed, Void) or from big-lab subsidies (Antigravity), which tend to shrink over time.

## Gaps
- I could not read the official pricing pages directly (network allowlist block). Exact Cursor Hobby caps are unpublished by Cursor. Exact Windsurf free daily and weekly quota sizes were not found.
- The Cursor Pro trial length (7 vs ~14 days) is unresolved.
- Trae's free-plan token credit ($3 vs $5) and free model list are unresolved. No official trae.ai source was retrieved.
- The Kiro 500-credit signup bonus and student program come from a third party and are unverified.
- Qoder's free-tier existence and prices conflict between sources.
- Whether the Void repo is "paused" or "archived" is unconfirmed. Melty's 2026 status is unknown, and PearAI's status is unclear.
- Newer entrants (e.g., Cline/Roo-based standalone IDEs, Firebase Studio, others) were not researched because of the tool-call budget.
