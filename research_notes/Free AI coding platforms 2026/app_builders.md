# Free Tiers of AI App-Builder / "Vibe Coding" Platforms (as of 2026-10-30)

**How this was researched:** Notes compiled 2026-10-30. Direct fetching of official pricing pages (replit.com, bolt.new, lovable.dev, v0.app) was **blocked by this session's network policy**, so the figures below come from web-search summaries. Most of the sources are third-party pricing guides dated June–October 2026. Official vendor sources reached through search were: Base44 Credits page (cited by search), Rork docs (docs.rork.com), Anything/Create docs (create.xyz/docs), Firebase blog and docs, and Google AI docs. **Check every number against the vendor's pricing page before publishing.** Pricing in this category changes every few months.

## Q1: What exactly is free, with what limits, as of 2026?

### Takeaway
Almost every builder now runs a credit system with a **small daily cap and a monthly ceiling**. The common pattern is about 5 actions/day, with monthly ceilings of roughly 25–35 credits. Bolt (tokens) and v0 ($5 credit) are the exceptions. Free tiers are for trying the tool or prototyping. Hosting on them is either branded, time-limited (Replit: 30 days), or missing (Rork, Tempo). Cheapest paid plans cluster at **$20–$25/month**.

### Cited Findings

#### Replit (Starter, free)
- Starter is free and includes **daily Agent credits** that reset each day, up to a monthly cap. Credits cannot be banked. Snapshot of sources dated 2026: [Softr](https://www.softr.io/blog/replit-pricing), [Lilach Bullock](https://www.lilachbullock.com/is-replit-free/), [lowcode.agency (Sept 2026)](https://www.lowcode.agency/blog/replit-pricing-explained)
- **One published app**, which goes offline after **30 days** and carries a "Made with Replit" badge. The newest pages (around Oct 2026) still list this limit — [lowcode.agency](https://www.lowcode.agency/blog/replit-pricing-explained); [agentdeals.dev Replit tracker](https://agentdeals.dev/vendor/replit)
- Agent on free is reportedly limited to **"Lite build"** (quick, targeted edits). Full build, Plan Mode, Connectors and extra published apps need Core. Sources conflict on the exact wording — [lowcode.agency](https://www.lowcode.agency/blog/replit-pricing-explained); [Superblocks](https://www.superblocks.com/blog/replit-pricing)
- Models: Replit routes models through modes. "Free Mode" (intelligent routing, no credits for everyday tasks) applies to **Core and Pro, not Starter**, per one review. Power Mode uses cost-optimized models and Max Mode the most capable ones — [Totalum review 2026](https://www.totalum.app/blog/replit-review-2026). Another source says Free Mode is usable on Starter within its daily limits. **Conflicting.**
- One source claims 1,200 dev minutes/month on free. **Unconfirmed, single source** — via [nocode.mba](https://www.nocode.mba/articles/replit-pricing)
- Cheapest paid plan: **Core at $20/mo on annual billing, ~$25/mo monthly**. One source says $18/mo annual. Pro is $100/mo (one source says $95 annual) for up to 15 builders — [Softr](https://www.softr.io/blog/replit-pricing); [PE Collective](https://pecollective.com/tools/replit-pricing/)
- Signup: free account, no card reported. Not verified on the official page.

#### Bolt.new (StackBlitz)
- Free: **300K tokens/day, 1M tokens/month**, $0, no credit card — [Activepieces](https://www.activepieces.com/blog/boltnew-pricing-2026-plans-tokens-and-costs-explained); [rationalgo](https://rationalgo.ai/resources/app-builder/pricing-guides/bolt-new-free-plan-token-limits); [Softr](https://www.softr.io/blog/bolt-ai-pricing)
- Daily tokens don't roll over, and unused monthly tokens expire. Free users **cannot buy token reloads**. Hosted sites carry **Bolt branding**. Bolt's help center says the reset is daily but gives no time or time zone — [rationalgo](https://rationalgo.ai/resources/app-builder/pricing-guides/bolt-new-free-plan-token-limits); [DigiJaws](https://digijaws.com/reviews/bolt-new-pricing/)
- 10MB file upload limit on free, per one comparison — via [dupple](https://dupple.com/learn/best-free-vibe-coding-tools)
- Models: **primarily Anthropic Claude**. Model selection is sometimes available, and heavier models burn more tokens. Secondary source — [toolhalla](https://toolhalla.ai/blog/bolt-new-vs-lovable-vs-replit-vs-v0-2026)
- Cheapest paid plan: **Pro $25/mo ($18/mo annual)**, no daily cap, starting at 10M tokens/mo. Teams is $30/member/mo — [axonbuild](https://axonbuild.com/blog/bolt-new-pricing/); [curatahub](https://curatahub.com/pricing/bolt)

#### Lovable
- Free: **5 build credits/day, capped at about 30/month**. Credits reset at midnight UTC and do not roll over — [eesel](https://www.eesel.ai/blog/lovable-pricing); [genvibe](https://genvibe.pro/blog/lovable-pricing)
- Free also gets monthly grants of **20 Cloud credits, 4 AI credits, 1 connector credit** (Lovable Cloud backend/hosting and in-app AI) — [promoclock](https://promoclock.co/en/tools/lovable/); [axonbuild](https://axonbuild.com/blog/lovable-free-tier-limits/)
- The 30/month cap may apply only "in some regions" — [creditforstartups](https://creditforstartups.com/pricing/lovable-pricing)
- Publishing goes to a **lovable.app subdomain**. Custom domains, code editing/download, removing the "Edit with Lovable" badge, rollover and top-ups all need a paid plan — [Softr](https://www.softr.io/blog/lovable-pricing); [layer3labs](https://www.layer3labs.io/guides/lovable-pricing)
- **Private vs public: sources CONFLICT.** Some say Free includes private projects and unlimited collaborators ([rapidevelopers](https://www.rapidevelopers.com/lovable-issues/is-lovable-free)). Others say Free projects are public by default and remixable, with private projects requiring Pro ([uxmagic](https://uxmagic.ai/blog/lovable-pricing); [shipper.now](https://shipper.now/lovable-pricing/)). Lovable has changed this before, so check lovable.dev/pricing.
- A limited-time free daily chat allowance was listed with an **end date of 2026-10-31**. It may be expiring right now — [promoclock](https://promoclock.co/en/tools/lovable/)
- Models: reportedly moved in 2025 from a GPT/Claude hybrid to almost exclusively Claude (Sonnet 4 at the time). The current model may differ. Secondary, non-English source — [tech-insider.org](https://tech-insider.org/sv/?p=662)
- Cheapest paid plan: **Pro $25/mo for 100 credits ($21/mo annual)**. Business is $50/mo — [we.inc](https://we.inc/pricing-guides/lovable-pricing)

#### v0 (Vercel)
- Free: **$5 of included credits/month, max 7 messages/day**. Credits don't roll over. Free users can't buy extra credits or use the API. One directory lists this as "Verified Aug 2026" — [uibakery](https://uibakery.io/blog/vercel-v0-pricing-explained-what-you-get-and-how-it-compares); [freetier.co](https://freetier.co/directory/products/v0-by-vercel); [nocode.mba](https://www.nocode.mba/articles/v0-pricing)
- Deploys go to Vercel, whose Hobby tier is free. Free-tier deploy specifics are not detailed in the sources found. **Gap.**
- Models: Vercel's own **v0 models (Mini/Pro/Max)** — via [techsy.io (Dutch)](https://techsy.io/nl/blog/lovable-vs-bolt-vs-v0-vergelijking)
- Cheapest paid plan: **sources conflict**. Some list a $20/mo tier, others say the cheapest paid tier is a "Plus" plan at $30/user/mo. Team is $30/user and Business $100/user — [costbench](https://costbench.com/software/ai-coding-assistants/v0-vercel/); [shipper.now](https://shipper.now/v0-pricing/)

#### Firebase Studio (Google) — closed to new users
- **Sign-ups closed 2026-06-22. Shutdown on 2027-03-22.** Google's docs say "We're sunsetting Firebase Studio on March 22, 2027" and recommend migrating to **Google AI Studio or Google Antigravity**. Core Firebase products (Firestore, Auth, App Hosting) are unaffected — [convika](https://convika.com/compare/firebase-studio-free-shutdown/); [nocode.mba](https://www.nocode.mba/articles/firebase-studio-pricing-2026)
- Historical free tier: 3 free workspaces, 10 for Google Developer Program members, 30 for Premium. Deploying via App Hosting required the Blaze (pay-as-you-go) plan with billing linked — [Firebase blog, Apr 2025](https://firebase.blog/posts/2025/04/introducing-firebase-studio/)
- **Successor: Google AI Studio "Build" mode** (ai.studio/build). It is free to start with no credit card, uses Gemini models, and deploys to Cloud Run — [VentureBeat](https://venturebeat.com/ai/googles-new-vibe-coding-ai-studio-experience-lets-anyone-build-deploy-apps); [Google AI docs](https://ai.google.dev/gemini-api/docs/aistudio-build-mode?hl=en). One guide claims the first two apps deploy to Google Cloud at no cost. **Single source** — [buildfastwithai](https://www.buildfastwithai.com/blogs/google-ai-studio-vibe-coding-guide)

#### Base44 (owned by Wix)
- Free: **25 message credits/month, max 5/day**, per Base44's own Credits page as quoted in search results — [Softr](https://www.softr.io/blog/base44-pricing); [rationalgo](https://rationalgo.ai/resources/app-builder/pricing-guides/is-base44-free-message-limits)
- Integration credits: most sources say **100/month**, one says 500. Unused credits expire each cycle. The free plan doesn't expire — [jetadmin](https://www.jetadmin.io/blog/base44-pricing-2026-guide-to-plans-credits-and-real-total-cost/); [fuzen](https://www.fuzen.io/posts/base44-review-2026-pricing-is-it-legit-and-is-it-free); [axonbuild](https://axonbuild.com/blog/base44-pricing/)
- Cheapest paid plan: Starter, which adds 100 message credits/month. The price was not captured in these results; it was historically about $20/mo. **Verify.**

#### Emergent (emergent.sh)
- Free: **10 credits/month, no credit card**. That was up from 5/month in a July 2025 help article. One review instead claims 5 monthly plus 10 daily. **Conflicting** — [justinmckelvey](https://justinmckelvey.com/blog/is-emergent-free); [usagepricing](https://www.usagepricing.com/blueprint/emergent); [sonary](https://sonary.com/b/emergent-sh/emergent-sh+ai-tools/)
- Deployment reportedly costs **50 credits/month per deployment**, which in practice puts hosting out of reach on free. Single source — [HostAdvice](https://hostadvice.com/hosting-company/emergent-reviews/pricing/)
- Cheapest paid plan: **Standard $20/mo ($17 annual) for 100 credits**. Pro is $200/mo for 750 credits — [eesel](https://www.eesel.ai/blog/emergent-ai-pricing)

#### Anything (formerly Create.xyz)
- Official docs: free plan has **3k credits plus daily message limits**. A message costs about 100–500 credits depending on model and task, so roughly 6–30 messages — [create.xyz docs](https://www.create.xyz/docs/account/credits)
- Third-party guides instead say **5,000 credits**, public projects only, 1GB storage. **Conflicts with the docs.** Starter is $19/mo (20k credits, private projects) and Pro $49/mo (custom domains) — [vp0.com](https://vp0.com/blogs/create-xyz-pricing-plans-2026/)

#### Rork (AI mobile apps, React Native/Expo)
- Free: **up to 35 design credits/month, max 5/day**. Only **5/month outside "major markets"** — [Rork docs](https://docs.rork.com/introduction/subscriptions)
- **The free plan is design-only.** Building a working app, testing it and publishing need **Rork Pro ($20/mo)** or Max (from $200/mo). Bonus credits come from connecting RevenueCat (up to 1,000) or Discord activity — [Rork docs](https://docs.rork.com/introduction/subscriptions)

#### Tempo Labs
- Free: reportedly **30 credits/month (about 5/day), no card**. Pro is $30/mo for 150 credits. Third-party sources only, and one site claims there is no free plan — [nocode.mba](https://www.nocode.mba/articles/tempo-pricing); [vp0.com](https://vp0.com/blogs/tempo-pricing-plans-2026/); contradicted by [aicoolies](https://aicoolies.com/pricing/tempo-labs)

#### Same.new
- **No 2026 pricing information found.** Searches returned nothing relevant.

### Inferences
- Measured in real build actions per month, the most generous free tiers appear to be **Bolt (1M tokens, ~300K/day)** and **Google AI Studio Build** (free, Gemini-powered, Cloud Run deploy). The credit-capped builders (Lovable, Base44, Rork, Tempo: about 5/day, ~25–35/month) are trial-sized.
- Hosting on free tiers is either branded (Bolt, Lovable, Replit), time-limited (Replit: 30 days), or not available/practical (Rork free is design-only; Emergent deploys cost 50 credits). None are fit for durable production hosting on free.

### Gaps
- Official pricing pages for Replit, Bolt, Lovable and v0 could not be fetched (network policy). All figures are second-hand.
- Exact models served on free tiers in Oct 2026 (e.g., which Claude/GPT/Gemini version) were not confirmed for any platform.
- Same.new: no data. v0 free deploy limits and the cheapest paid price are unresolved. Base44 Starter price was not captured.

## Q2: What changed recently (2025–2026)?

### Takeaway
The big shifts were: (1) Firebase Studio is being shut down, with sign-ups closed June 2026; (2) most builders moved to daily-capped credit grants and unified credit balances that also pay for hosting/AI; (3) Replit restructured its plans (Pro replaced Teams, Feb 2026) and limits free Agent use to lighter builds.

### Cited Findings
- **Firebase Studio:** sign-ups closed 2026-06-22, sunset on 2027-03-22, users told to migrate to AI Studio/Antigravity — [convika](https://convika.com/compare/firebase-studio-free-shutdown/)
- **Google AI Studio:** a I/O 2026 write-up claims native Kotlin vibe coding, one-click Cloud Run deploy, Firebase integration and Antigravity export were added. Third-party source — [converge.ai](https://enter.converge.ai/page/en-US/blog/google-ai-studio-2026)
- **Replit:** the Pro plan ($100/mo) replaced Teams on **2026-02-20**. Effort-based Agent credits are now split into Lite/Full builds and Free/Power/Max modes. Agent 4 was teased in April 2026 as "10x faster than Agent 3" with no release date given — [lowcode.agency](https://www.lowcode.agency/blog/replit-pricing-explained); [espressio](https://espressio.ai/blog/replit-guide-2026/)
- **Lovable:** the free plan moved to daily Build grants plus monthly Cloud/AI grants. Since **Aug 2026** a unified credit balance also pays for hosting and in-app AI. One source claims that from **2026-09-09** Lovable may train on Free and Pro workspace content unless you opt out. **Single source, unverified** — [axonbuild](https://axonbuild.com/blog/lovable-free-tier-limits/); [Softr](https://www.softr.io/blog/lovable-pricing)
- **Lovable:** a limited-time free daily chat allowance is listed as ending **2026-10-31** — [promoclock](https://promoclock.co/en/tools/lovable/)
- **Emergent:** free went from 5 to 10 credits/month between July 2025 and 2026 — [usagepricing](https://www.usagepricing.com/blueprint/emergent)
- **Rork:** introduced region-dependent free allowances (35 vs 5 credits) and made the free tier design-only — [Rork docs](https://docs.rork.com/introduction/subscriptions)

### Inferences
- Free tiers have mostly gotten **tighter** in 2025–2026: daily caps, design-only tiers, and region-based limits. Google is the main exception, consolidating its free offering into AI Studio.

### Gaps
- No official changelog entries were fetched, so dates of Bolt/v0 changes in 2026 are unknown.

## Q3: Which is most useful for free users, and for what kind of project?

### Takeaway
For a free user: **Bolt.new** gives the most generation volume for full-stack web prototypes. **Google AI Studio Build** is the best fully free Gemini-based option with deploy. **v0** suits React/Next.js UI work. **Lovable** produces polished small web apps but only ~5 prompts/day. **Replit** is the best for real code plus a short-lived live demo. **Rork** is the mobile option, but its free tier only covers design.

### Cited Findings
- Bolt free: 300K tokens/day and 1M/month, no card — [Activepieces](https://www.activepieces.com/blog/boltnew-pricing-2026-plans-tokens-and-costs-explained)
- AI Studio Build: free to start, no credit card, deploys to Cloud Run — [VentureBeat](https://venturebeat.com/ai/googles-new-vibe-coding-ai-studio-experience-lets-anyone-build-deploy-apps); [Google AI docs](https://ai.google.dev/gemini-api/docs/aistudio-build-mode?hl=en)
- v0: $5/month and 7 messages/day, own UI-focused models — [freetier.co](https://freetier.co/directory/products/v0-by-vercel)
- Lovable: 5 credits/day, lovable.app hosting with badge — [eesel](https://www.eesel.ai/blog/lovable-pricing); [layer3labs](https://www.layer3labs.io/guides/lovable-pricing)
- Replit: Lite-build Agent, one published app for 30 days — [lowcode.agency](https://www.lowcode.agency/blog/replit-pricing-explained)
- Rork free is design-only, and building and publishing a mobile app needs $20/mo Pro — [Rork docs](https://docs.rork.com/introduction/subscriptions)
- Comparison overviews ranking free vibe-coding tools in 2026 — [dupple](https://dupple.com/learn/best-free-vibe-coding-tools); [jetadmin best free builders (Sept 2026)](https://www.jetadmin.io/blog/best-free-ai-app-builders/)

### Inferences
- A practical free-user strategy is to combine tools: prototype the UI in v0 or Lovable, iterate heavily in Bolt or AI Studio, then export to GitHub and host on a free static/serverless host. This relies on export being available. Lovable reportedly restricts code editing/download on free, so check that first.
- For anything that has to stay live past a demo, every free tier is inadequate. Expect about $20–25/mo.

### Gaps
- No independent benchmark compares output quality per free credit. The rankings above are inferred from quota size and stated scope.
