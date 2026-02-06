# RAXE CE Documentation - UX & Developer Experience Review

**Reviewer:** UX/Developer Experience Specialist
**Date:** 2026-02-06
**Scope:** All 47 MDX pages + mint.json configuration
**Documentation Platform:** Mintlify

---

## Overall Grade: B+

The RAXE CE documentation is a **strong, comprehensive body of work** that covers an ambitious breadth of integrations, concepts, and reference material. The first-visit experience is well-crafted, the quickstart genuinely achieves its "60-second" promise, and the use of Mintlify components is generally effective. However, several inconsistencies in numbers and claims across pages, an outdated changelog roadmap, missing pricing transparency, and a few navigation dead-ends prevent it from reaching an A-tier developer experience.

---

## Executive Summary

**Strengths:**
- Excellent quickstart flow -- 3 commands from zero to first scan
- Strong integration decision guide (`integrations/index.mdx`) that routes developers to the right page
- Good use of progressive disclosure (Accordions, Tabs, Steps) to manage information density
- Privacy-first messaging is consistent and well-reinforced across all pages
- API reference section is thorough with real type annotations and return value documentation

**Weaknesses:**
- Statistics and numbers are inconsistent across pages (rule counts, latency figures, threat family counts)
- The free-tier 1,000 scans/day limit is buried in the FAQ and absent from marketing pages
- The changelog roadmap section is outdated (references v0.3 as "Coming Soon" when current version is v0.9.1)
- Some pages are excessively long without internal navigation aids (why-raxe.mdx at ~346 lines)
- The OpenClaw integration page ships with a prominent "NOT YET IMPLEMENTED" warning, damaging credibility

---

## Top 5 Critical Findings (Must-Fix)

### 1. Inconsistent Numbers Across Pages
**Severity:** High
**Impact:** Erodes developer trust; makes documentation feel unreliable

The documentation cites different numbers for the same metrics across pages:

| Metric | Page A | Page B | Page C |
|--------|--------|--------|--------|
| Rule count | "514 rules" (introduction) | "514+ patterns" (config) | "515+" (why-raxe) |
| Threat families | "7 threat families" (intro) | "7+4 L2 families" (detection-engine) | "14 threat families plus benign" (detection-engine L2 section) |
| L1 scan latency | "~1ms" (introduction) | "<5ms" (performance) | "<5ms" (MCP) |
| Full scan latency | "~10ms" (introduction) | "<15ms" (MCP) | "~12ms" (performance) |

**Recommendation:** Establish a single source of truth. Use Mintlify snippets or variables if available to define these numbers once and reference them everywhere. Alternatively, use approximate ranges consistently ("500+ rules", "sub-5ms L1 scanning").

### 2. Free-Tier Limit Hidden from Marketing Pages
**Severity:** High
**Impact:** Developer frustration when they hit limits; feels like a bait-and-switch

The FAQ page (`resources/faq.mdx`) reveals a 1,000 scans/day limit for the free Community Edition. This limit is **not mentioned** on:
- `introduction.mdx` (the landing page)
- `why-raxe.mdx` (the persuasion page)
- `quickstart.mdx` (where developers commit to the tool)
- `installation.mdx` (where they install it)

A developer could go through the entire onboarding flow, integrate RAXE into their application, and only discover the limit when they exceed it in production.

**Recommendation:** Add a brief, non-alarming mention of the free tier limit on the introduction page and quickstart. Something like: "Free tier includes 1,000 scans/day -- more than enough for development and small deployments. Need more? See pricing." This is honest and actually builds trust.

### 3. Outdated Changelog Roadmap
**Severity:** High
**Impact:** Makes the project appear abandoned or poorly maintained

The `resources/changelog.mdx` page has a "Roadmap" section that lists "v0.3 - Coming Soon" with features. The current version is v0.9.1. This is a significant gap that suggests the roadmap has not been updated since early development.

**Recommendation:** Either update the roadmap to reflect v1.0 goals and current priorities, or remove the roadmap section entirely and link to a GitHub project board or discussions page for future plans.

### 4. OpenClaw Integration Page Ships Unimplemented Feature
**Severity:** High
**Impact:** Damages credibility; confuses developers who try to use it

The `integrations/openclaw.mdx` page has a large `<Warning>` block at the top stating that message hooks are not yet implemented. The page then proceeds to document the unimplemented API anyway, followed by a workaround (MCPorter approach). The page also includes an extensive "Test Environment Setup" section.

**Recommendation:** Either (a) remove the page entirely until the feature is implemented, replacing it with a single card on the integrations index that says "Coming Soon," or (b) restructure the page to lead with the MCPorter workaround as the primary approach and move the unimplemented API to a collapsed "Future API (Planned)" section at the bottom.

### 5. No Clear Pricing/Upgrade Path
**Severity:** Medium-High
**Impact:** Developers who hit limits or need enterprise features have no clear path forward

Multiple pages reference "Pro" or "Enterprise" tiers (custom rules limit of 50 in CE, MSSP features, multi-tenant policies) but there is no pricing page, no link to a pricing page, and no clear CTA for upgrading. The topbar has links to "Community" and "GitHub" but no "Pricing" or "Get Started" link.

**Recommendation:** Add a "Pricing" link to the topbar navigation in mint.json, or at minimum add a `/pricing` or `/enterprise` page that explains the tiers and includes a contact form or link.

---

## Top 5 Quick Wins (Easy Improvements)

### 1. Standardize the "Next Steps" Cards Across All Pages
**Effort:** Low (1-2 hours)
**Impact:** Better navigation flow, reduced bounce rate

Many pages end with `<CardGroup>` next-step cards, but the selection is inconsistent. Some pages link to conceptual docs, others to integrations, and some have no next steps at all. Create a consistent pattern:
- Getting Started pages -> link to the next sequential page + relevant integration
- Integration pages -> link to production checklist + common patterns
- Concept pages -> link to related integration + API reference
- Reference pages -> link back to the relevant concept + quickstart

### 2. Add Anchor Links to Long Pages
**Effort:** Low (30 minutes)
**Impact:** Improved navigation for why-raxe.mdx, production-checklist.mdx, migration.mdx

Pages like `why-raxe.mdx` (~346 lines), `guides/production-checklist.mdx`, and `guides/migration.mdx` are long enough that users lose context. Adding a brief table-of-contents or ensuring Mintlify's auto-generated sidebar TOC works well for these pages would help. Consider also splitting `why-raxe.mdx` into separate concerns: a concise comparison page and a deeper "How It Works" page.

### 3. Add Copy Buttons to All JSON Config Blocks
**Effort:** Low (already supported by Mintlify)
**Impact:** Reduces friction for Claude Desktop and Cursor configuration

The MCP, Claude Desktop, and Cursor configuration JSON blocks are prime copy-paste targets. Ensure all code blocks have Mintlify's copy button enabled (they should by default, but verify the `title` attribute is set for clarity, e.g., `claude_desktop_config.json`).

### 4. Fix the FAQ to Link Back to Relevant Docs
**Effort:** Low (1 hour)
**Impact:** Reduces dead-end experience in FAQ

The FAQ answers are self-contained but rarely link back to the detailed documentation pages. For example, "How fast is scanning?" should link to `/resources/performance`, and "Does RAXE work offline?" should link to `/concepts/architecture`. This turns the FAQ from a dead-end into a navigation hub.

### 5. Add a "Supported Versions" Summary Table to Integrations Index
**Effort:** Low (30 minutes)
**Impact:** Saves developers from clicking into each integration page to check compatibility

The integrations index page (`integrations/index.mdx`) has a great decision guide but doesn't show version compatibility. Add a summary table:

| Framework | Minimum Version | Status |
|-----------|----------------|--------|
| LangChain | 0.1.x+ | Stable |
| CrewAI | 0.28.0+ | Stable |
| AutoGen | 0.4+ | Stable |
| etc. | | |

---

## Detailed Page-by-Page Notes

### Getting Started

#### introduction.mdx
- **Grade: A-**
- Strong opening with clear value proposition and the 3-line code example
- The threat families table is effective at communicating breadth
- The tabbed integration examples are a good use of progressive disclosure
- Performance cards (sub-millisecond, 500+ rules, zero network) are compelling
- **Issue:** "514 rules" here vs "515+" elsewhere
- **Issue:** "~1ms L1, ~10ms full scan" conflicts with performance page numbers
- **Suggestion:** The "What's Next" cards at the bottom could benefit from a "Why RAXE?" card for evaluators who haven't committed yet

#### why-raxe.mdx
- **Grade: B**
- Very thorough but excessively long (~346 lines)
- The comparison table (RAXE vs Cloud APIs vs Regex vs WAFs) is excellent
- The persona-based cards ("I'm a startup CTO", "I'm a security engineer") are a smart touch
- The accordion FAQ section at the bottom is well-done
- **Issue:** The page tries to do too much -- it's simultaneously a comparison page, a features page, an objection-handler, and an architecture overview
- **Issue:** Statistics quoted here ("515+ detection patterns") don't match introduction page ("514 rules")
- **Suggestion:** Consider splitting into 2 pages: a concise "Why RAXE" comparison and a "How RAXE Works" deep-dive

#### quickstart.mdx
- **Grade: A**
- This is the best page in the documentation. The "60-second" promise is honest
- Clear numbered steps with CodeGroup for pip/pipx choices
- The CLI verification step (`raxe --version`) builds confidence
- Framework-specific quick examples in tabs are perfect for the "show me my framework" developer
- The "Scan Points" table efficiently communicates what gets scanned
- Production readiness steps at the bottom provide a clear path forward
- **No significant issues**

#### installation.mdx
- **Grade: A-**
- Clean, focused, and well-organized
- The requirements section is clear (Python 3.9+, 50MB disk, no internet required)
- Optional extras table (mcp, langchain, crewai, wrappers) is helpful
- Troubleshooting accordions are well-targeted
- **Minor issue:** Could mention the 1,000 scans/day CE limit here

#### configuration.mdx
- **Grade: B+**
- Good coverage of YAML config, env vars, and programmatic config
- The "Performance Modes" table (balanced/low-latency/thorough) is very useful
- Telemetry explanation is honest and trust-building
- **Issue:** References "514+ patterns" (different from intro page "514 rules")
- **Suggestion:** The precedence order (CLI > env vars > config file > defaults) should be more prominently displayed, possibly as a callout box

### Integrations

#### integrations/index.mdx
- **Grade: A**
- Excellent decision guide with "Which integration should I use?" accordions
- The comparison table (Integration, Best For, Complexity, Protection Level) is the MVP of this page
- Card groups are well-organized by category (Frameworks, Providers, SIEM, Web)
- **Suggestion:** Add version compatibility info (as noted in Quick Wins)

#### integrations/mcp.mdx
- **Grade: A-**
- Comprehensive coverage of both gateway and server modes
- The ASCII architecture diagram is clear and effective
- Claude Desktop and Cursor setup instructions are copy-paste ready
- The YAML config example for multi-server setups is practical
- Policy options table is concise
- **Issue:** Performance numbers here ("<5ms L1", "<15ms L1+L2") differ from intro page
- **Suggestion:** The privacy section could link to the architecture page for deeper explanation

#### integrations/langchain.mdx
- **Grade: A-**
- Strong coverage of callback handler, agentic scanning, and RAG protection
- Goal hijack detection example is compelling and well-explained
- Tool chain validation example clearly shows the data-exfiltration pattern detection
- OWASP alignment table adds credibility
- **Suggestion:** The "Agentic Security Scanning" section is substantial enough to warrant its own page or at least a more prominent heading

#### integrations/crewai.mdx
- **Grade: B+**
- Good structure with quick start, callbacks, configuration, and tool scanning
- The ScanMode enum table is clear
- **Issue:** The page is slightly shorter than LangChain, which may leave CrewAI users feeling like second-class citizens
- **Suggestion:** Add an example of a complete multi-agent crew with protection (end-to-end example)

#### integrations/autogen.mdx
- **Grade: B+**
- Good coverage of both hook-based and wrapper-based approaches
- GroupChat scanning example is practical
- v0.4+ requirement is clearly stated
- **Issue:** The "Test Your Integration" section at the bottom feels tacked on
- **Suggestion:** Move testing guidance into the examples themselves

#### integrations/llamaindex.mdx
- **Grade: B+**
- Good dual approach (callback + instrumentation)
- Specialized callbacks for QueryEngine and Agent are thoughtful
- **Suggestion:** Add a RAG-specific example since LlamaIndex is heavily used for RAG

#### integrations/dspy.mdx
- **Grade: B**
- Adequate coverage but feels thinner than other integration pages
- The module guard wrapper pattern is well-explained
- **Issue:** No OWASP alignment section (present on LangChain, MCP pages)
- **Suggestion:** Add a RAG pipeline example to match the DSPy use case

#### integrations/portkey.mdx
- **Grade: B**
- Two integration patterns (webhook + client wrapper) are clearly differentiated
- Flask webhook example is practical
- **Issue:** The page assumes familiarity with Portkey's guardrail architecture
- **Suggestion:** Add a brief "What is Portkey?" intro for developers encountering it here first

#### integrations/openclaw.mdx
- **Grade: D**
- The prominent WARNING about unimplemented features is a critical credibility issue
- The page documents an API that doesn't exist yet
- The MCPorter workaround is buried below the non-functional primary approach
- The extensive "Test Environment Setup" section adds confusion
- **Recommendation:** Restructure or remove (see Critical Finding #4)

#### integrations/litellm.mdx
- **Grade: B+**
- Clean, focused page for the LiteLLM callback handler
- Factory function pattern is convenient
- Multi-provider scanning example is practical
- **Suggestion:** Add a table of tested LiteLLM providers

#### integrations/huggingface.mdx
- **Grade: B+**
- RaxePipeline wrapper is straightforward
- Supported pipeline tasks table is helpful
- GPU acceleration mention is relevant
- **Suggestion:** Add a note about model size considerations with L2 scanning

#### integrations/common-patterns.mdx
- **Grade: A-**
- Excellent coverage of FastAPI, Flask, and Django patterns
- Middleware, decorator, and dependency injection patterns cover all common approaches
- Async queue and batch processing examples are production-ready
- **Suggestion:** Add a "Choosing Your Pattern" decision guide at the top

#### integrations/ci-cd.mdx
- **Grade: A-**
- GitHub Actions and GitLab CI examples are copy-paste ready
- CI mode flags and exit codes table is essential for automation
- Pre-commit hook example is practical
- PR comment example is a nice touch
- **Suggestion:** Add a Bitbucket Pipelines example

#### integrations/siem.mdx
- **Grade: B+**
- Good coverage of major SIEM platforms (Splunk, CrowdStrike, Sentinel, ArcSight)
- Both CLI and Python config patterns provided
- CEF message format documentation is thorough
- **Issue:** The page is enterprise-focused but doesn't clearly state this is a Pro/Enterprise feature
- **Suggestion:** Add a note about tier requirements at the top

### Concepts

#### concepts/detection-engine.mdx
- **Grade: A-**
- Excellent deep-dive into L1 and L2 detection mechanics
- Token limit (512) and classification heads (5) details are valuable for power users
- Voting engine explanation with decision zones is well-done
- Preset comparison table (balanced/high_security/low_fp) is practical
- **Issue:** "14 threat families plus benign" for L2 vs "7 threat families" on intro page -- the distinction between L1 and L2 family counts needs to be clearer on introductory pages

#### concepts/threat-families.mdx
- **Grade: B+**
- Comprehensive listing of L1 (7+4) and L2 (14) families
- Attack techniques (35) and harm types (10) tables are thorough
- **Issue:** The sheer density of tables may overwhelm new users
- **Suggestion:** Add a "Most Common Threats" section at the top with the top 3-5 families that developers will encounter most frequently

#### concepts/architecture.mdx
- **Grade: B+**
- Clean/hexagonal architecture explanation builds confidence
- Privacy architecture section reinforces the zero-network-required claim
- Scan pipeline pseudocode is a nice touch
- **Suggestion:** A visual architecture diagram (mermaid or image) would significantly improve comprehension

#### concepts/policies.mdx
- **Grade: B+**
- Policy actions (ALLOW/FLAG/BLOCK/LOG) are clearly defined
- YAML config examples are practical
- Targeting by severity/family/rule/confidence is well-explained
- **Issue:** CE limit of policies is not mentioned prominently
- **Suggestion:** Add a "Policy Examples for Common Scenarios" section

#### concepts/suppressions.mdx
- **Grade: B+**
- YAML config with glob-pattern support is well-documented
- SDK inline and context manager patterns are practical
- CLI commands for managing suppressions are complete
- **Suggestion:** Add examples of common false-positive suppressions

#### concepts/mssp-integration.mdx
- **Grade: B**
- Comprehensive MSSP documentation with hierarchy explanation
- Setup journey steps are clear
- Data privacy modes table is important
- **Issue:** This is clearly an enterprise feature but the page doesn't gate or indicate this clearly
- **Issue:** Very long page that could benefit from being split into overview + setup guide

#### concepts/multi-tenant.mdx
- **Grade: B+**
- Tenant/App/Request hierarchy is well-explained
- Policy modes and resolution chain are clear
- Gateway pattern for multi-tenant is practical
- **Issue:** CE limits on tenants/apps should be more prominent

### Guides

#### guides/migration.mdx
- **Grade: A-**
- Shadow mode approach is the right recommendation
- Framework-specific migration steps (LangChain, LiteLLM, CrewAI) are practical
- FastAPI/Flask/Django patterns mirror the common-patterns page (some duplication)
- Rollback plan section builds confidence
- "Measuring Success" metrics are realistic
- **Issue:** Some overlap with common-patterns.mdx and production-checklist.mdx
- **Suggestion:** Cross-link more aggressively to avoid duplication

#### guides/production-checklist.mdx
- **Grade: A-**
- Thorough checklist covering shadow mode, A/B testing, monitoring, rollout
- 4-week gradual rollout plan is realistic and builds confidence
- False positive handling section is practical
- Fail-open vs fail-closed comparison is essential
- Circuit breaker pattern is a professional touch
- **Issue:** Very long page; could benefit from a progress tracker or checklist format
- **Suggestion:** Consider using Mintlify's Steps component for the rollout timeline

### SDK

#### sdk/overview.mdx
- **Grade: B+**
- Good import reference and pattern comparison
- Quick examples of each pattern (direct, decorator, wrapper, async, context manager)
- **Issue:** Some import paths shown here differ slightly from those in individual integration pages
- **Suggestion:** Add a "When to Use Each Pattern" decision matrix

#### sdk/python.mdx
- **Grade: A-**
- Comprehensive ScanPipelineResult documentation with all properties
- Detection and Match object docs are thorough
- Multi-tenant scanning example is well-placed
- Thread safety section is important and well-documented
- **Minor issue:** The filtering examples could be more practical (filter for specific use cases)

#### sdk/agentic-scanning.mdx
- **Grade: A**
- Excellent coverage of all 12 agentic scan types
- Goal hijack, memory poisoning, tool chain, handoff examples are compelling
- Privilege escalation and plan scanning are unique differentiators
- OWASP alignment table adds credibility
- 4 agentic rule families (AGENT, TOOL, MEM, MULTI) are well-explained
- **This is one of the strongest pages in the docs**

#### sdk/async.mdx
- **Grade: B+**
- AsyncRaxe with batch scanning is well-documented
- FastAPI and aiohttp integration examples are practical
- Performance tuning tips are relevant
- Sync vs async comparison table is helpful
- **Suggestion:** Add a streaming scan example for real-time applications

#### sdk/openai-wrapper.mdx
- **Grade: A-**
- Drop-in replacement approach is brilliantly simple
- Mermaid flow diagram clearly shows the scan-before-call pattern
- Streaming support documentation is essential
- Migration guide (one-line change) is compelling
- Async support is included
- **Suggestion:** Add a note about what happens with function calling / tool use

#### sdk/anthropic-wrapper.mdx
- **Grade: B+**
- Mirrors OpenAI wrapper structure (good consistency)
- System prompt scanning is a nice differentiator
- **Issue:** Slightly less detailed than the OpenAI wrapper page
- **Suggestion:** Match the OpenAI page's depth (add streaming details, multi-turn example)

### Rules

#### rules/overview.mdx
- **Grade: B+**
- Rule structure YAML example is clear
- Family table and CLI browsing commands are practical
- Severity levels and confidence scores are well-defined
- **Suggestion:** Add example output from `raxe rules list` for each family

#### rules/custom-rules.mdx
- **Grade: B+**
- Rule format with required fields is clear
- Pattern syntax documentation is adequate
- Validation and testing commands are practical
- CE limit of 50 custom rules is clearly stated
- **Issue:** The "Contributing Rules" section could link to a GitHub CONTRIBUTING.md
- **Suggestion:** Add 2-3 complete custom rule examples for common use cases

### Resources

#### resources/troubleshooting.mdx
- **Grade: A-**
- Quick diagnosis table at the top is excellent for fast triage
- `raxe doctor` command is a great self-service tool
- Comprehensive sections covering all major issue categories
- **Suggestion:** Add a "Still stuck?" section at the bottom with links to GitHub issues and community

#### resources/faq.mdx
- **Grade: B**
- Covers common questions adequately
- **Critical Issue:** This is where the 1,000 scans/day limit is revealed -- it should be mentioned earlier (see Critical Finding #2)
- **Issue:** Answers are self-contained but rarely link to detailed docs
- **Suggestion:** Add links from each answer to the relevant documentation page

#### resources/error-codes.mdx
- **Grade: B+**
- Error code format explanation is clear
- Categorized tables (AUTH, SCAN, RULE, CONFIG, DB, NET, ML) are well-organized
- Resolution steps are actionable
- **Suggestion:** Add a "Common Errors" section at the top with the 3-5 most frequent errors

#### resources/performance.mdx
- **Grade: A-**
- Benchmark cards are compelling and well-presented
- Latency breakdown by configuration is detailed
- Hardware recommendations are practical
- Monitoring and profiling sections are production-ready
- Performance guarantees section builds confidence
- **Issue:** Some latency numbers here conflict with other pages

#### resources/changelog.mdx
- **Grade: C+**
- Version history is well-structured with clear release notes
- **Critical Issue:** Roadmap section is severely outdated (v0.3 "Coming Soon" when current is v0.9.1)
- **Issue:** No release dates on versions
- **Suggestion:** Add dates, fix the roadmap, and consider linking to GitHub releases

### API Reference

#### api-reference/overview.mdx
- **Grade: B+**
- SDK components cards provide a good overview
- Import reference is handy
- API stability table (Stable/Beta/Internal) is important
- **Suggestion:** Add a "Getting Started with the API" quick example

#### api-reference/raxe-client.mdx
- **Grade: A-**
- Comprehensive constructor documentation
- All scan method variants (scan, scan_fast, scan_thorough) documented
- Protect decorator and suppressed context manager are included
- AsyncRaxe with cache management is thorough
- Thread safety documentation is important
- **Minor issue:** Could benefit from more inline examples showing return values

#### api-reference/scan-results.mdx
- **Grade: A-**
- ScanPipelineResult properties are thorough
- Boolean evaluation explanation is important (True when safe)
- Detection properties, Severity enum, and filtering patterns are well-documented
- Serialization example is practical for logging/SIEM
- **Suggestion:** Add a visual diagram of the result object hierarchy

#### api-reference/exceptions.mdx
- **Grade: A-**
- Exception hierarchy is clearly documented
- All exception types with properties are listed
- Error handling patterns (comprehensive, web app, async) are practical
- **Suggestion:** Add a decision tree for "which exception should I catch?"

### CLI

#### cli/overview.mdx
- **Grade: B+**
- Quick reference table is effective
- Global options and env vars documented
- Exit codes are essential for CI/CD
- Common workflows section is practical
- **Suggestion:** Add example output for the most common commands

#### cli/commands.mdx
- **Grade: B+**
- Comprehensive reference for all commands
- Sub-commands for mcp, tenant, app, policy, suppress are documented
- **Issue:** Very long page -- consider splitting into categories or using deeper accordion nesting
- **Suggestion:** Add a command search/filter mechanism or alphabetical index at the top

---

## Recommendations Summary

### Priority 1: Trust & Consistency (This Week)
1. **Audit and unify all statistics** across all 47 pages -- rule counts, latency figures, threat family counts, detection counts. Use a single canonical number or consistent ranges.
2. **Add free-tier limit disclosure** to introduction.mdx and quickstart.mdx. Be upfront; it builds trust.
3. **Fix or remove the changelog roadmap**. An outdated roadmap is worse than no roadmap.
4. **Restructure or remove the OpenClaw page**. Do not ship documentation for unimplemented features.

### Priority 2: Navigation & Discoverability (This Sprint)
5. **Add pricing/upgrade path** -- at minimum a topbar link and a simple page explaining CE vs Pro vs Enterprise.
6. **Standardize "Next Steps" cards** across all pages for consistent navigation flow.
7. **Add cross-links from FAQ answers** to detailed documentation pages.
8. **Add a "Supported Versions" table** to the integrations index page.

### Priority 3: Content Quality (Next Sprint)
9. **Split why-raxe.mdx** into a concise comparison page and a deeper technical page.
10. **Ensure Anthropic wrapper page** matches OpenAI wrapper page in depth and examples.
11. **Add practical custom rule examples** to rules/custom-rules.mdx.
12. **Reduce duplication** between migration.mdx, common-patterns.mdx, and integration pages by cross-linking.

### Priority 4: Polish & Enhancement (Backlog)
13. **Add a visual architecture diagram** (mermaid) to concepts/architecture.mdx.
14. **Add release dates** to changelog entries.
15. **Add a "Most Common Threats" section** to threat-families.mdx for new users.
16. **Consider splitting cli/commands.mdx** into per-category sub-pages.
17. **Add Bitbucket Pipelines** example to ci-cd.mdx.
18. **Add a "Still stuck?" footer** to troubleshooting.mdx with community/GitHub links.

---

## Information Architecture Assessment

### Navigation Structure (mint.json)
The navigation is organized into 9 groups across 2 tabs:
- **Main tab:** Getting Started, Integrations (with nested sub-groups), Concepts, Guides, Advanced, Enterprise, Reference
- **API Reference tab:** Overview, Client, Results, Exceptions
- **CLI tab:** Overview, Commands

**Assessment:** The structure is logical and follows the "learning path" pattern (Getting Started -> Integration -> Concepts -> Reference). The two-tab separation of API and CLI reference is a good choice. However:
- The "Advanced" group (Rules Overview, Custom Rules) feels misplaced -- these could live under "Concepts" or "Reference"
- The "Enterprise" group (SIEM, MSSP, Multi-Tenant) is correct but should more clearly indicate tier requirements

### Search Discoverability
- Mintlify provides built-in search, which should index all page content
- Page titles and descriptions in frontmatter are generally well-crafted for search
- **Gap:** Some pages lack descriptive `sidebarTitle` overrides, leading to long sidebar entries

### Mobile Experience
- Mintlify's responsive layout handles mobile well by default
- ASCII art diagrams (MCP architecture) may not render well on narrow screens
- Long tables (SIEM field mapping, CLI commands) may require horizontal scrolling
- **Suggestion:** Test the 5 most critical pages (intro, quickstart, top integration, performance, troubleshooting) on mobile viewports

### First-Visit Experience
The path from landing to first value is well-designed:
1. **Introduction** (value prop + quick code example) -> 2. **Quickstart** (60-second setup) -> 3. **Integration page** (framework-specific) -> 4. **Production Checklist** (go-live)

This is a strong funnel. The main risk is developers bouncing from the introduction page if they don't see their framework immediately -- the tabbed integration examples mitigate this well.

### Time-to-First-Value
Estimated at **2-5 minutes** for a developer who follows the quickstart:
- `pip install raxe` (30s)
- `raxe init` (10s)
- `raxe scan "test"` (10s)
- Browse first integration example (1-3 min)

This is excellent and competitive with similar developer tools.

---

*Review completed: 2026-02-06*
*Pages reviewed: 47 MDX files + mint.json*
