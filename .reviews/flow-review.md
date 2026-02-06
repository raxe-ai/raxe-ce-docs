# Documentation Flow Review

## Overall Grade: B+

## Executive Summary

The RAXE documentation is comprehensive and well-structured for a product of this complexity. The 47 pages cover everything from a 60-second quickstart to enterprise MSSP deployments, and the writing is generally clear and code-example-rich. The navigation grouping in mint.json is logical and the "Choose Your Integration" index page is an excellent wayfinding tool.

However, the documentation suffers from several flow-related issues that create friction for new users. The most significant is that conceptual foundations (detection engine, threat families, L1/L2 architecture) are placed *after* the integration guides in the sidebar, meaning users encounter unexplained terminology before they reach the pages that define it. Several integration pages are dead ends with no "What's Next" section, and cross-linking is inconsistent -- some pages link generously to related content while others exist in near-isolation. The progressive disclosure from simple to advanced is generally good within each section but breaks down at the navigation level where enterprise content (SIEM, MSSP) appears without clear prerequisite signposting.

The documentation excels at serving the "I want to protect my LangChain agent right now" user journey but is weaker for the "I want to understand what RAXE actually does before committing" journey. Enterprise evaluators will find the technical depth they need but may struggle to locate it without using search.

## Top 5 Critical Findings (Must-Fix)

### 1. Concepts Explained After They Are Used (Inverted Learning Order)
**Pages affected:** All integration pages reference L1/L2, threat families, and severity levels, but `concepts/detection-engine`, `concepts/threat-families`, and `rules/overview` appear in the "How It Works" section *below* "Protect Your AI" in the sidebar.

**User impact:** A developer reading the LangChain integration page encounters terms like "L1 rules", "L2 ML classifier", "severity HIGH", and "threat families" without prior explanation. They must either guess what these mean or hunt for the definitions. This creates cognitive friction at the exact moment the user should be building confidence.

**Recommendation:** Move "How It Works" above "Protect Your AI" in the navigation, or at minimum add a brief inline explanation and link on first use in each integration page.

### 2. Many Integration Pages Are Dead Ends
**Pages affected:** `integrations/langchain`, `integrations/autogen`, `integrations/llamaindex`, `integrations/dspy`, `integrations/litellm`, `integrations/huggingface`, `integrations/common-patterns`, `integrations/ci-cd`, `sdk/openai-wrapper`, `sdk/anthropic-wrapper`, `sdk/async`

**User impact:** After completing an integration setup, the user has no guidance on what to do next. There is no "What's Next" section, no link to the production checklist, no pointer to monitoring or tuning. The user's journey abruptly stops.

**Recommendation:** Add a "What's Next" CardGroup to every integration page pointing to at minimum: (a) the production checklist, (b) custom rules, and (c) the detection engine concepts page.

### 3. Inconsistent Exception/Error Class Names Across Pages
**Pages affected:** Different integration pages reference different exception classes for the same concept:
- LangChain uses `RaxeBlockedError` and `RaxeError`
- AutoGen uses `SecurityException`
- LlamaIndex uses `SecurityException`
- DSPy uses `ThreatDetectedError`
- Common Patterns uses `SecurityException` and `RaxeException`
- API Reference uses `RaxeBlockedError` and `RaxeException`

**User impact:** A developer using multiple integrations (common in production) will be confused about which exception to catch. This is either a documentation inconsistency or an actual API inconsistency that needs documenting.

**Recommendation:** Either standardise the exception names across the docs (if they are the same class with different aliases) or create a clear mapping table in the exceptions API reference page showing which integration uses which exception class and why.

### 4. No Clear "Start Here" Path for Enterprise Evaluators
**Pages affected:** Enterprise section (`integrations/siem`, `concepts/mssp-integration`, `concepts/multi-tenant`)

**User impact:** An enterprise security team evaluating RAXE has no dedicated entry point. They must wade through developer-focused quickstarts and integration guides to find enterprise-relevant content. The SIEM page jumps straight into CLI commands without first explaining what data RAXE can feed into a SIEM and why that matters.

**Recommendation:** Add an "Enterprise Overview" page at the top of the Enterprise section that maps the evaluation journey: (1) what RAXE detects, (2) how it fits into SOC workflows, (3) deployment architecture, (4) compliance/privacy story, (5) links to SIEM/MSSP/multi-tenant pages.

### 5. `why-raxe.mdx` Duplicates and Conflicts with Other Pages
**Pages affected:** `why-raxe.mdx` vs `introduction.mdx`, `concepts/detection-engine.mdx`, `concepts/threat-families.mdx`

**User impact:** The "Why RAXE?" page contains its own threat detection overview, its own OWASP list, its own L1/L2 explanation, and its own FAQ -- all of which overlap with dedicated pages elsewhere. The L2 model is described as having "14 threat families with real-world examples" and "35 attack techniques" in why-raxe.mdx but the detection-engine page says "14 families" for L2 and the threat-families page provides the authoritative list. The why-raxe page also mentions "515+ rules" while other pages say "514" or "514+". This creates confusion about which page is the source of truth.

**Recommendation:** Trim why-raxe.mdx to focus on the *business case* (privacy, latency, cost) and link out to the authoritative technical pages rather than duplicating their content. Ensure rule counts are consistent across all pages.

## Top 5 Quick Wins (Easy Improvements)

### 1. Add "Prerequisites" Note to Each Integration Page
Add a small note at the top of each integration page:
```
<Info>Before diving in, make sure you've completed the [Quick Start](/quickstart) and understand [how RAXE detection works](/concepts/detection-engine).</Info>
```
This takes 2 minutes per page and dramatically improves the learning path.

### 2. Add "What's Next" Cards to Dead-End Pages
12 pages currently lack next-step guidance. Adding a simple CardGroup with 2-3 links to each takes about 30 minutes total and eliminates all dead ends.

### 3. Fix the Broken Link in `integrations/huggingface.mdx`
Line 172: `href="/wrappers/openai"` should be `href="/sdk/openai-wrapper"`. This is a 404 for any user clicking through.

### 4. Fix the Broken Link in `integrations/openclaw.mdx`
Line 666: `href="/rules/custom"` should be `href="/rules/custom-rules"`. Another 404.

### 5. Add Integrations Index Link to Installation Page
The installation page's "Next Steps" only links to Configuration. Add a card linking to the integrations index page, since most users install RAXE specifically to integrate it with something.

## User Journey Maps

### Journey 1: New Developer (First Time)

**Goal:** "I heard RAXE can protect my AI app. Let me try it."

```
introduction.mdx --> quickstart.mdx --> [SUCCESS: first scan works]
                                    --> "Protect Your First Agent" section
                                    --> Clicks LangChain/OpenAI link
                                    --> integration page (e.g., langchain.mdx)
                                    --> [FRICTION: L1/L2/severity terms unexplained]
                                    --> [DEAD END: no next steps on langchain page]
                                    --> [LOST: where do I go to learn about tuning, production?]
```

**Friction points:**
1. The quickstart is excellent and gets the user to "wow" fast.
2. Integration pages assume prior knowledge of L1/L2 detection concepts.
3. After completing integration, no guidance toward production readiness.
4. The user may never discover the migration guide or production checklist unless they browse the sidebar.

**Recommendation:** After the integration page, the user should be directed to: production checklist --> custom rules --> troubleshooting.

### Journey 2: Enterprise Evaluator

**Goal:** "Can RAXE fit into our security stack? What does it detect and how do we monitor it?"

```
introduction.mdx --> [FRICTION: too developer-focused for a security evaluator]
                 --> why-raxe.mdx --> [GOOD: privacy, latency, cost comparison]
                 --> Looks for "Enterprise" in sidebar
                 --> integrations/siem.mdx --> [FRICTION: jumps into CLI commands]
                 --> concepts/mssp-integration.mdx --> [GOOD: clear architecture]
                 --> concepts/detection-engine.mdx --> [GOOD: technical depth]
                 --> concepts/threat-families.mdx --> [GOOD: OWASP mapping]
                 --> [FRICTION: no single page ties the enterprise story together]
```

**Friction points:**
1. No "Enterprise Overview" landing page.
2. SIEM page assumes you already know RAXE's data model.
3. Architecture page is buried under "How It Works" rather than being surfaced for evaluators.
4. Multi-tenant and MSSP pages don't cross-link to each other effectively.

### Journey 3: Existing User Looking for Specific Info

**Goal:** "I'm already using RAXE. How do I handle false positives?"

```
[Searches "false positives"] --> May land on:
  - resources/troubleshooting.mdx (has False Positives section) --> [GOOD]
  - resources/faq.mdx (has false positive Q&A) --> [OK but brief]
  - concepts/suppressions.mdx --> [GOOD: detailed suppression system]
  - guides/production-checklist.mdx --> [GOOD: handling FP section]
```

**Friction points:**
1. The journey actually works well via search.
2. But if browsing the sidebar, suppressions is buried under "Advanced" -- a user dealing with false positives may not think to look there.
3. The troubleshooting page's false positive section doesn't link to the suppressions page.
4. The FAQ's false positive answer doesn't link to the suppressions page either.

## Cross-Link Audit

| Source Page | Concept Mentioned | Target Page | Currently Linked? |
|---|---|---|---|
| `introduction.mdx` | "Why RAXE?" card | `/concepts/detection-engine` | Misleadingly labelled "Why RAXE?" but links to detection engine -- confusing |
| `installation.mdx` | "See Integrations" | `/integrations` | Links to `/integrations` which may 404 (should be `/integrations/index`) |
| `integrations/langchain.mdx` | L1/L2 detection, severity levels | `/concepts/detection-engine` | No |
| `integrations/langchain.mdx` | Threat families (PI, JB, etc.) | `/concepts/threat-families` | No |
| `integrations/langchain.mdx` | Agentic scanning methods | `/sdk/agentic-scanning` | No |
| `integrations/crewai.mdx` | L1/L2, severity | `/concepts/detection-engine` | No |
| `integrations/autogen.mdx` | SecurityException | `/api-reference/exceptions` | No |
| `integrations/llamaindex.mdx` | SecurityException | `/api-reference/exceptions` | No |
| `integrations/dspy.mdx` | ThreatDetectedError | `/api-reference/exceptions` | No |
| `integrations/litellm.mdx` | ThreatDetectedError | `/api-reference/exceptions` | No |
| `integrations/portkey.mdx` | SecurityException | `/api-reference/exceptions` | No |
| `integrations/common-patterns.mdx` | AsyncRaxe | `/sdk/async` | No |
| `integrations/ci-cd.mdx` | Batch scanning | `/cli/commands` | No |
| `sdk/openai-wrapper.mdx` | Async support | `/sdk/async` | No |
| `sdk/anthropic-wrapper.mdx` | Async support | `/sdk/async` | No |
| `sdk/agentic-scanning.mdx` | OWASP ASI references | External OWASP page | No |
| `concepts/detection-engine.mdx` | Custom rules | `/rules/custom-rules` | No |
| `concepts/detection-engine.mdx` | Policies | `/concepts/policies` | No |
| `concepts/threat-families.mdx` | Detection engine severity mapping | `/concepts/detection-engine` | Yes (good) |
| `rules/overview.mdx` | Agentic rule families | `/sdk/agentic-scanning` | No |
| `concepts/policies.mdx` | Suppressions | `/concepts/suppressions` | No |
| `concepts/suppressions.mdx` | Policies | `/concepts/policies` | No |
| `resources/troubleshooting.mdx` | False positives / suppressions | `/concepts/suppressions` | No |
| `resources/faq.mdx` | False positives | `/concepts/suppressions` | No |
| `resources/faq.mdx` | Detection engine details | `/concepts/detection-engine` | Yes |
| `integrations/siem.mdx` | MSSP integration | `/concepts/mssp-integration` | No |
| `concepts/mssp-integration.mdx` | Multi-tenant policies | `/concepts/multi-tenant` | No |
| `concepts/multi-tenant.mdx` | MSSP integration | `/concepts/mssp-integration` | No |
| `integrations/huggingface.mdx` | OpenAI wrapper | `/wrappers/openai` (BROKEN) | Broken link |
| `integrations/openclaw.mdx` | Custom rules | `/rules/custom` (BROKEN) | Broken link |
| `guides/migration.mdx` | Suppressions | `/concepts/suppressions` | No |
| `guides/production-checklist.mdx` | Suppressions | `/concepts/suppressions` | Yes (good) |

## Dead End Pages

The following pages have no "What's Next" or navigation guidance at the end:

1. **`integrations/langchain.mdx`** - Ends with "Supported LangChain Versions" table. No next steps.
2. **`integrations/autogen.mdx`** - Ends with "Supported Versions" table. No next steps.
3. **`integrations/llamaindex.mdx`** - Ends with "Supported Versions" table. No next steps.
4. **`integrations/dspy.mdx`** - Ends with "Supported DSPy Versions" table. No next steps.
5. **`integrations/litellm.mdx`** - Ends with "Supported LiteLLM Versions" table. No next steps.
6. **`integrations/portkey.mdx`** - Ends with "Supported Versions" table. No next steps.
7. **`integrations/huggingface.mdx`** - Has "Related" cards but one link is broken.
8. **`integrations/common-patterns.mdx`** - Ends with logging integration code. No next steps.
9. **`integrations/ci-cd.mdx`** - Ends with Docker example. No next steps.
10. **`sdk/openai-wrapper.mdx`** - Ends with async support code. No next steps.
11. **`sdk/anthropic-wrapper.mdx`** - Ends with async support code. No next steps.
12. **`sdk/async.mdx`** - Ends with comparison table. No next steps.
13. **`sdk/python.mdx`** - Ends with thread safety example. No next steps.
14. **`sdk/agentic-scanning.mdx`** - Ends with privacy section. No next steps.
15. **`concepts/architecture.mdx`** - Ends with offline mode. No next steps.
16. **`concepts/policies.mdx`** - Ends with limits table. No next steps.
17. **`concepts/suppressions.mdx`** - Ends with troubleshooting. No next steps.
18. **`concepts/multi-tenant.mdx`** - Ends with best practices. No next steps.
19. **`resources/performance.mdx`** - Ends with performance guarantees. No next steps.
20. **`resources/changelog.mdx`** - Ends with links (acceptable for a changelog).

## Detailed Page-by-Page Notes

### Getting Started Section

**introduction.mdx** - Strong opening page. The code example is immediately compelling. Minor issue: the "Why RAXE?" card links to `/concepts/detection-engine` which is confusing labelling. The Performance section says "11" threat families but detection-engine page describes 7 L1 families + 4 agentic + 14 L2 families. Inconsistent.

**why-raxe.mdx** - Excellent persuasion page but too long and duplicates content from detection-engine and threat-families pages. Should be trimmed to focus on business value and link to technical pages.

**quickstart.mdx** - One of the best pages in the entire docs. Clear, progressive, immediately actionable. The "What Just Happened" section is superb for building understanding. The "Going to Production" steps and the production checklist are great progressive disclosure.

**installation.mdx** - Solid but the "Next Steps" section only points to Configuration. Should also point to the integrations index and quickstart.

**configuration.mdx** - Good, concise. Next steps correctly point to rules and policies.

### Protect Your AI Section

**integrations/index.mdx** - Excellent wayfinding page. The "Quick Decision Guide" accordion is a brilliant UX pattern. The comparison table is very helpful.

**integrations/mcp.mdx** - Very thorough. Good next steps section. Could benefit from a link back to detection-engine to explain L1/L2.

**integrations/langchain.mdx** - Comprehensive but a dead end. Needs "What's Next" section and should link to agentic-scanning for the advanced methods it demonstrates.

**integrations/crewai.mdx** - Good coverage. Dead end.

**integrations/autogen.mdx** - Good. Dead end. Uses `SecurityException` instead of `RaxeBlockedError`.

**integrations/llamaindex.mdx** - Good. Dead end.

**integrations/dspy.mdx** - Good. Dead end. Uses `ThreatDetectedError`.

**integrations/portkey.mdx** - Very thorough two-pattern approach. Dead end.

**integrations/openclaw.mdx** - Unusually long and detailed for an integration with known limitations. The transparency about message hooks not being implemented is commendable. Has a broken link to `/rules/custom`.

**integrations/litellm.mdx** - Good. Dead end. Uses `ThreatDetectedError`.

**sdk/openai-wrapper.mdx** - Clean, focused. Dead end.

**sdk/anthropic-wrapper.mdx** - Clean, focused. Dead end.

**integrations/huggingface.mdx** - Good. Has broken link `/wrappers/openai`.

**integrations/common-patterns.mdx** - Very practical. Dead end.

**integrations/ci-cd.mdx** - Good but sparse compared to other integration pages. Dead end.

### How It Works Section

**concepts/detection-engine.mdx** - Core conceptual page. Well-written. Should be encountered earlier in the user journey. Lacks next steps.

**concepts/threat-families.mdx** - Comprehensive. Good L1/L2 distinction. Links back to detection-engine for severity mapping (good).

**rules/overview.mdx** - Good entry point to rules system. Has next steps linking to threat-families and custom-rules (good).

**concepts/architecture.mdx** - Good for technical evaluators. Dead end. Should link to detection-engine and policies.

### Guides Section

**guides/migration.mdx** - Excellent. One of the best pages. The phased approach (shadow, wrapper, blocking) is perfectly structured. Good next steps.

**guides/production-checklist.mdx** - Excellent. Thorough, practical. Good next steps linking to SIEM, policies, suppressions, performance.

### Advanced Section

**sdk/overview.mdx** - Good summary/index page. Links to sub-pages well.

**sdk/python.mdx** - Comprehensive API usage guide. Dead end. Should link to async, agentic-scanning.

**sdk/agentic-scanning.mdx** - Critical content for agent developers. Dead end. Should link to LangChain integration and the OWASP mapping.

**sdk/async.mdx** - Good. Dead end.

**rules/custom-rules.mdx** - Good. Ends with community contribution info. Should link to rules/overview and policies.

**concepts/policies.mdx** - Good. Dead end. Should link to suppressions (the natural companion concept).

**concepts/suppressions.mdx** - Good. Should link to policies and production-checklist.

### Enterprise Section

**integrations/siem.mdx** - Thorough technical reference. Jumps straight into CLI without context. Should have an intro explaining what RAXE sends to SIEMs and why.

**concepts/mssp-integration.mdx** - Very thorough. Good "Who Is This For?" segmentation at the top. Has next steps. Should link to multi-tenant.

**concepts/multi-tenant.mdx** - Good. Dead end. Should link to mssp-integration and policies.

### Reference Section

**resources/troubleshooting.mdx** - Excellent. The "Quick Diagnosis" table at the top is a great UX pattern. Should link to suppressions page from the false positives section.

**resources/faq.mdx** - Good. Covers common questions. Should link to suppressions from the false positive FAQ.

**resources/error-codes.mdx** - Comprehensive reference. Links to troubleshooting (good).

**resources/performance.mdx** - Thorough benchmarks. Dead end. Should link to async SDK and configuration.

**resources/changelog.mdx** - Good. Roadmap section at the bottom has outdated "v0.3 Coming Soon" items that appear to already be shipped.

### API Reference Section

**api-reference/overview.mdx** - Clean index page.

**api-reference/raxe-client.mdx** - Comprehensive. Good parameter tables.

**api-reference/scan-results.mdx** - Comprehensive.

**api-reference/exceptions.mdx** - Good. Should note the different exception names used by different integrations.

### CLI Reference Section

**cli/overview.mdx** - Good quick reference. Links to full commands page.

**cli/commands.mdx** - Comprehensive. Good reference material.

## Recommended Navigation Restructure

Current order:
```
1. Getting Started
2. Protect Your AI (integrations)
3. How It Works (concepts)
4. Guides
5. Advanced
6. Enterprise
7. Reference
8. API Reference
9. CLI Reference
```

Recommended order:
```
1. Getting Started (keep as-is)
2. How It Works (move UP -- users need concepts before integrations)
   - Detection Engine
   - Threat Families
   - Rules Overview
   - Architecture
3. Protect Your AI (integrations -- now users understand the terminology)
4. Guides (keep)
5. Advanced (keep)
6. Enterprise (keep)
7. Reference (keep)
8. API Reference (keep)
9. CLI Reference (keep)
```

**Rationale:** By moving "How It Works" before "Protect Your AI", users will understand L1/L2 detection, threat families, and severity levels *before* they encounter these concepts in integration guides. This eliminates the #1 critical finding.

## Recommendations Summary

### Must-Fix (Critical)
1. Move "How It Works" above "Protect Your AI" in navigation (or add inline concept explanations to integration pages)
2. Add "What's Next" sections to all 20 dead-end pages
3. Standardise exception class documentation across integration pages
4. Create an enterprise overview/landing page
5. Deduplicate why-raxe.mdx content that overlaps with concept pages

### Should-Fix (Important)
6. Fix broken link in huggingface.mdx (`/wrappers/openai` --> `/sdk/openai-wrapper`)
7. Fix broken link in openclaw.mdx (`/rules/custom` --> `/rules/custom-rules`)
8. Add prerequisite notes to integration pages
9. Cross-link troubleshooting and FAQ false positive sections to suppressions page
10. Cross-link policies and suppressions pages to each other
11. Standardise rule counts across all pages (514 vs 515 vs 514+ vs 515+)
12. Update changelog roadmap (v0.3 items appear shipped)

### Nice-to-Have (Polish)
13. Add a "Learning Path" visual to the introduction page showing the recommended reading order
14. Add breadcrumb-style "You are here" context to each section
15. Cross-link MSSP and multi-tenant pages to each other
16. Add SIEM page intro explaining what data flows to the SIEM before diving into configuration
17. Link agentic-scanning page to OWASP references (currently just mentions ASI codes without links)
18. Add installation page link to integrations index in "Next Steps"
