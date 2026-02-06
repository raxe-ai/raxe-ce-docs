# RAXE CE Documentation Review
## Comprehensive 7-Angle Analysis

**Date:** 2026-02-06
**Scope:** All 47 MDX pages + mint.json configuration
**Branch:** main (commit 1ef56a8)
**Method:** 7 specialist reviewers analysed every page in parallel

---

## Executive Summary

The RAXE CE documentation is **technically impressive and remarkably comprehensive** for a product at this stage. The 47-page corpus covers everything from a 60-second quickstart to enterprise MSSP deployment, with clean code examples, good use of Mintlify components, and a solid information architecture. The quickstart is genuinely excellent, the integration breadth (10+ frameworks) is competitive, and the agentic scanning capabilities are a genuine differentiator.

However, the documentation has a **systemic trust problem** that threatens to undermine all of this good work. The same metrics, class names, API surfaces, and licensing terms are described differently across pages, creating an experience where any two pages might subtly contradict each other. A skeptical evaluator will find these contradictions within 30 minutes of reading and walk away.

**The three most urgent issues are:**

1. **"Free forever, no usage limits"** on the marketing page directly contradicts the FAQ's **1,000 scans/day limit** and mandatory telemetry -- this is the single most trust-damaging issue
2. **Three different exception class names** (`RaxeBlockedError`, `SecurityException`, `ThreatDetectedError`) across integration pages will cause runtime errors for developers
3. **Performance claims range from 0.37ms to 50ms** for the same metric depending on which page you read

### Grades by Dimension

| Reviewer | Grade | One-Line Summary |
|----------|-------|-----------------|
| UX & Developer Experience | **B+** | Strong quickstart and navigation; inconsistent numbers and hidden limits hurt trust |
| Technical Architecture | **C+** | Impressive breadth but pervasive API inconsistencies will cause runtime errors |
| Content Quality | **B** | Clean writing, good progressive disclosure; terminology chaos across pages |
| British English | **B+** | Consistently American English (not British); ~15 instances need conversion if BrE intended |
| Documentation Flow | **B+** | Good structure but concepts explained after they're used; 20 dead-end pages |
| "Make It Sexy" | **B** | Comprehensive but reads like reference docs, not irresistible marketing |
| Devil's Advocate | **B-** | Strong technical foundation but zero social proof, unclear licensing, trust gaps |

---

## Priority-Ranked Action Items

### P0: Fix This Week (Trust-Breaking Issues)

These issues actively damage credibility and will cause developers to leave or hit runtime errors.

| # | Issue | Source | Effort | Impact |
|---|-------|--------|--------|--------|
| 1 | **Resolve "free forever, no limits" vs 1,000 scans/day contradiction** -- why-raxe.mdx claims no limits; FAQ reveals 1K/day cap. Add honest tier disclosure to intro and quickstart. | Devil's Advocate, UX, Tech, Content | Low | Critical |
| 2 | **Standardise exception class names** -- `RaxeBlockedError` (canonical) is used on 6 pages but `SecurityException` on 5 pages and `ThreatDetectedError` on 2 pages. Pick one, update all 47 pages. | Tech, Content, Flow | Medium | Critical |
| 3 | **Standardise result property names** -- `has_threats` vs `is_safe`, `severity` vs `highest_severity`, `total_detections` vs `detection_count`, `e.result` vs `e.scan_result`. These cause `AttributeError` at runtime. | Tech, Content | Medium | Critical |
| 4 | **Standardise import paths** -- `RaxeOpenAI` has 3 different import paths across pages. Pick `from raxe import RaxeOpenAI` and update everywhere. | Tech | Low | High |
| 5 | **Fix performance number contradictions** -- L1 ranges from 0.37ms to 5ms; L2 ranges from 3ms to 50ms across pages. Use performance.mdx as single source of truth. | Tech, UX, Devil's Advocate, Content, Sexy | Medium | High |
| 6 | **Clarify licensing** -- FAQ says "not open source" while everything else implies OSS. Add explicit license type to introduction or installation page. | Devil's Advocate | Low | High |
| 7 | **Fix or remove stale changelog roadmap** -- v0.3 "Coming Soon" when current is v0.9.1 makes project look abandoned. | UX, Tech, Content, Sexy, Devil's Advocate | Low | High |

### P1: Fix This Sprint (Trust-Eroding Issues)

These issues erode trust incrementally and create friction for new users.

| # | Issue | Source | Effort | Impact |
|---|-------|--------|--------|--------|
| 8 | **Standardise rule count** -- oscillates between 514, 514+, 515, 515+ across 6+ pages. Pick one number or use "500+". | All 7 reviewers | Low | Medium |
| 9 | **Fix 2 broken internal links** -- openclaw.mdx links to `/rules/custom` (should be `/rules/custom-rules`); huggingface.mdx links to `/wrappers/openai` (should be `/sdk/openai-wrapper`). | Tech, Flow | Low | Medium |
| 10 | **Fix stale dates/versions** -- REPL shows v0.0.1 (cli/overview.mdx:129), suppression expiry "2025-06-01" (cli/commands.mdx:494, suppressions.mdx:27), changelog roadmap. | Tech, Content, English | Low | Medium |
| 11 | **Standardise category codes** -- `"PI"` (short) vs `"prompt_injection"` (long) used interchangeably across pages. | Tech, Content | Low | Medium |
| 12 | **Fix grammar: "personal identifiable information"** should be "personally identifiable information" in threat-families.mdx:62. | English | Low | Low |
| 13 | **Remove/restructure OpenClaw page** -- prominent WARNING about unimplemented features damages credibility. Lead with MCPorter workaround or remove page. | UX, Tech, Content, Devil's Advocate | Low | Medium |
| 14 | **Fix MCP troubleshooting reference** to non-existent `@anthropic-ai/raxe-mcp` npm package in troubleshooting.mdx. | Tech | Low | Medium |
| 15 | **Add pricing/upgrade path** -- Pro/Enterprise mentioned on 10+ pages with zero pricing info. At minimum add a contact page. | UX, Devil's Advocate, Sexy | Medium | High |

### P2: Fix This Month (Developer Experience)

These improve navigation, learning flow, and content quality.

| # | Issue | Source | Effort | Impact |
|---|-------|--------|--------|--------|
| 16 | **Move "How It Works" above "Protect Your AI" in navigation** -- concepts (L1/L2, threat families) are used on integration pages before they're defined. | Flow, Content | Low | High |
| 17 | **Add "What's Next" cards to 20 dead-end pages** -- integration pages, concept pages, and reference pages end abruptly with no forward guidance. | Flow, UX, Content | Medium | High |
| 18 | **Add prerequisite/concept links to all integration pages** -- a one-line Info callout linking to detection-engine and quickstart. | Flow, Content | Low | High |
| 19 | **Cross-link policies <-> suppressions pages** to each other (natural companions). | Flow | Low | Medium |
| 20 | **Cross-link troubleshooting and FAQ false-positive sections** to suppressions page. | Flow, Content | Low | Medium |
| 21 | **Trim why-raxe.mdx** -- remove redundant L1/L2 overview, threat catalogue, and FAQ accordion that duplicate detection-engine, threat-families, and faq pages. Keep the business case. | UX, Content, Flow, Sexy | Medium | Medium |
| 22 | **Bring Anthropic wrapper to parity with OpenAI wrapper** -- missing benefits cards, mermaid diagram, streaming details. | UX, Content, Sexy | Medium | Medium |
| 23 | **Add social proof** -- GitHub stars badge, download counts, developer quotes, or at minimum a reproducible benchmark methodology for the 95%+ detection claim. | Sexy, Devil's Advocate | Medium | High |
| 24 | **Add enterprise overview landing page** -- enterprise evaluators have no entry point; they wade through developer quickstarts to find SIEM/MSSP content. | Flow, Devil's Advocate | Medium | Medium |
| 25 | **Convert ~15 American English spellings** to British English in prose text (behavior->behaviour, customize->customise, specialized->specialised, organization->organisation, etc.) if BrE is the target dialect. | English | Low | Low |

### P3: Backlog (Polish & Enhancement)

| # | Issue | Source | Effort | Impact |
|---|-------|--------|--------|--------|
| 26 | Add a terminology glossary page as canonical reference | Content | Medium | Medium |
| 27 | Add a hero section with bold headline + stat strip to introduction.mdx | Sexy | Medium | High |
| 28 | Create a named competitor comparison (Lakera, Rebuff, NeMo Guardrails, LLM Guard) | Sexy, Devil's Advocate | Medium | High |
| 29 | Promote agentic scanning to top-level navigation (currently buried under Advanced) | Sexy, Flow | Low | High |
| 30 | Add animated terminal demo showing threat detection in action | Sexy | Medium | High |
| 31 | Document L2 model details (size, token handling beyond 512, quantisation, hardware) | Tech, Devil's Advocate | Medium | Medium |
| 32 | Document partial failure behaviour (what happens when L2 fails but L1 succeeds) | Tech, Content | Medium | Medium |
| 33 | Document rule update mechanism and versioning | Tech, Content | Medium | Medium |
| 34 | Add visual architecture diagram (mermaid) to concepts/architecture.mdx | UX | Low | Medium |
| 35 | Create unified OWASP coverage page (currently scattered across 3+ pages) | Sexy, Content | Medium | Medium |
| 36 | Split cli/commands.mdx (764 lines) into per-category sub-pages | UX, Content | Medium | Low |
| 37 | Add "Most Common Threats" quick-reference to top of threat-families.mdx | UX | Low | Low |
| 38 | Add Bitbucket Pipelines and Jenkins CI/CD examples | UX, Tech | Low | Low |
| 39 | Add release dates to changelog entries | UX, Devil's Advocate | Low | Low |
| 40 | Create a written style guide for contributors (dialect, Oxford comma, heading case) | English | Medium | Low |
| 41 | Add "Protected by RAXE" badge for READMEs (viral distribution) | Sexy | Low | Medium |
| 42 | Explain API key purpose clearly (why needed if 100% local?) | Devil's Advocate | Low | Medium |
| 43 | Add compliance/security page (SOC 2 status, vulnerability disclosure, BAA) | Devil's Advocate | Medium | Medium |
| 44 | Add telemetry payload example for full transparency | Devil's Advocate | Low | Medium |

---

## Cross-Reviewer Consensus

Issues found independently by 3+ reviewers carry the highest confidence:

| Issue | Reviewers Who Found It | Count |
|-------|----------------------|-------|
| Inconsistent rule counts (514/515/515+) | All 7 reviewers | 7/7 |
| Contradictory performance numbers across pages | UX, Tech, Content, English, Sexy, Devil's Advocate | 6/7 |
| "Free forever" vs 1K/day limit contradiction | UX, Tech, Content, Sexy, Devil's Advocate | 5/7 |
| Stale changelog roadmap (v0.3 "Coming Soon") | UX, Tech, Content, Sexy, Devil's Advocate | 5/7 |
| Exception class name chaos (3 different names) | Tech, Content, Flow, UX, Sexy | 5/7 |
| why-raxe.mdx is overloaded and duplicates other pages | UX, Content, Flow, Sexy | 4/7 |
| OpenClaw page ships unimplemented feature | UX, Tech, Content, Devil's Advocate | 4/7 |
| "Not open source" buried/contradicted | English, Content, Sexy, Devil's Advocate | 4/7 |
| Missing "What's Next" on many pages (dead ends) | UX, Flow, Content | 3/7 |
| Zero social proof anywhere | Sexy, Devil's Advocate | 2/7 (but highest-impact) |
| Concepts explained after they're used in nav order | Flow, Content | 2/7 (but high friction) |

---

## Key Technical Inconsistencies (Quick Reference)

### Exception Class Names
| Class | Should Use | Currently Used On |
|-------|-----------|-------------------|
| `RaxeBlockedError` | Yes (canonical) | sdk/python, sdk/overview, openai-wrapper, crewai, langchain, api-ref/exceptions |
| `SecurityException` | No | autogen, llamaindex, portkey, common-patterns |
| `ThreatDetectedError` | No | dspy, litellm |

### Result Property Names
| Canonical (sdk/python.mdx) | Non-Canonical Variant | Used On |
|---|---|---|
| `result.has_threats` | `result.is_safe` | api-ref/overview, exceptions, faq |
| `result.severity` | `result.highest_severity` | api-ref/overview, exceptions |
| `result.total_detections` | `result.detection_count` | api-ref/exceptions |
| `e.result` | `e.scan_result` | api-ref/exceptions |

### Import Path Variants
| Class | Canonical | Variant 1 | Variant 2 |
|-------|-----------|-----------|-----------|
| `RaxeOpenAI` | `from raxe import RaxeOpenAI` | `from raxe.wrappers import` | `from raxe.sdk.wrappers import` |

### Performance Numbers
| Metric | Lowest Claim | Highest Claim | Benchmarked (performance.mdx) |
|--------|-------------|---------------|-------------------------------|
| L1 latency | ~0.3ms | ~5ms | P50=0.37ms, P95=0.49ms |
| L2 latency | ~3ms | ~50ms | Not benchmarked independently |
| L1+L2 combined | ~2ms | ~15ms | Not benchmarked independently |

---

## What RAXE Documentation Does Well

Despite the issues above, multiple reviewers praised these strengths:

1. **Quickstart page** (rated A by 4 reviewers) -- genuinely achieves the "60-second" promise with clear progressive disclosure
2. **Integrations index** decision guide -- brilliant accordion-based routing to the right integration page
3. **Agentic scanning** (sdk/agentic-scanning.mdx) -- 12 scan types, OWASP ASI alignment, genuinely differentiated capabilities
4. **Production checklist** -- week-by-week rollout plan with shadow mode, circuit breakers, and gradual enforcement
5. **SIEM integration** -- comprehensive multi-platform coverage (Splunk, CrowdStrike, Sentinel, ArcSight, CEF)
6. **Migration guide** -- zero-risk phased approach (shadow, wrapper, blocking) with rollback patterns
7. **Detection engine** explainer -- clear L1/L2 dual-layer architecture with voting engine details
8. **Code examples** throughout -- consistently copy-paste ready, syntactically correct, and production-oriented
9. **Privacy-first messaging** -- consistently reinforced "100% on-device" narrative across all pages
10. **OpenAI wrapper** -- drop-in replacement with one-line migration is the most frictionless integration

---

## Devil's Advocate: Unanswered Questions

The skeptic's review surfaced these unaddressed questions that real evaluators will ask:

1. **Who is actually using this in production?** Not a single company or use case is named.
2. **What is the false positive rate?** Detection rate is claimed but FP rate is never mentioned.
3. **How does this compare to Lakera Guard, Prompt Armor, Rebuff?** Only generic comparisons exist.
4. **What happens when I hit the 1K scans/day limit?** Fail open? Fail closed? Error?
5. **Why is telemetry mandatory for CE?** If local-first, why phone home?
6. **What is the actual license?** MIT? Apache? Proprietary? BSL? Not specified anywhere.
7. **Is there a company behind this?** No about page, no team page, no company info.
8. **What was the L2 model trained on?** "ONNX neural classifier" is vague.
9. **How do I verify RAXE works?** No test corpus, benchmark suite, or red-team report provided.
10. **What is the upgrade path from CE to Pro?** Config change or painful migration?

### Enterprise Buyer Objections Not Addressed

| Objection | Where It Should Be Answered |
|-----------|----------------------------|
| "What is your SOC 2 / ISO 27001 status?" | Security/compliance page (missing) |
| "Can we get an SLA?" | Pricing/enterprise page (missing) |
| "Who do we call at 3 AM?" | Support page (missing) |
| "What is your vulnerability disclosure process?" | Security page (missing) |
| "Can we run this without any network connectivity?" | Architecture page (partially) |
| "What happens if your company shuts down?" | FAQ (not addressed) |
| "Is there a BAA for healthcare?" | Compliance (missing) |
| "What is the total cost of ownership?" | Pricing page (missing) |

---

## Detailed Reviews

The 7 individual review files contain full page-by-page analysis with line numbers:

| File | Reviewer | Grade | Pages |
|------|----------|-------|-------|
| `.reviews/ux-review.md` | UX & Developer Experience | B+ | 600 lines |
| `.reviews/tech-review.md` | Technical Architecture | C+ | 602 lines |
| `.reviews/content-review.md` | Content Quality | B | 461 lines |
| `.reviews/english-review.md` | British English | B+ | 334 lines |
| `.reviews/flow-review.md` | Documentation Flow | B+ | 375 lines |
| `.reviews/sexy-review.md` | "Make It Sexy" | B | 436 lines |
| `.reviews/devils-advocate-review.md` | Devil's Advocate | B- | 453 lines |

---

*Generated by 7 specialist AI reviewers working in parallel.*
*Total pages reviewed: 47 MDX files + mint.json per reviewer = 329 page-reviews.*
*Review date: 2026-02-06*
