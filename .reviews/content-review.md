# Content Expert Review: RAXE CE Documentation

**Reviewer:** Content Expert (Writing Quality, Clarity, Consistency)
**Date:** 2026-02-06
**Scope:** All 47 MDX documentation pages + mint.json
**Repository:** raxe-ce-docs (main branch, commit 1ef56a8)

---

## Overall Grade: B

---

## Executive Summary

The RAXE CE documentation is an ambitious, 47-page corpus that covers a complex product with genuine depth. The writing is overwhelmingly clear, the code examples are abundant and well-formatted, and the use of Mintlify components (Accordions, Tabs, Steps, Cards) is generally skillful. For a security product at this stage, the breadth of coverage -- from a 60-second quickstart to enterprise MSSP deployment -- is commendable.

However, the documentation suffers from a **pervasive content consistency problem** that drags down the overall quality. The same metrics, class names, and API surfaces are described differently across pages, creating an experience where any two pages might subtly contradict each other. This is the kind of problem that builds distrust incrementally: a developer who notices "514 rules" on one page and "515+ rules" on another begins to doubt everything. When that same developer finds that `RaxeBlockedError` is the exception class on one page but `SecurityException` on another, doubt becomes frustration.

The writing quality itself is strong. Sentences are concise, the tone is developer-friendly without being flippant, and technical jargon is mostly well-calibrated for the audience. The documentation's biggest content wins are the quickstart page (genuinely excellent progressive disclosure), the integrations index decision guide, the agentic scanning page (a differentiating technical showcase), and the production checklist. Its biggest content weaknesses are the overloaded why-raxe.mdx page, the substantial redundancy between pages, and the inconsistent terminology for core concepts.

This review focuses specifically on **writing quality, clarity, terminology consistency, readability, heading structure, content gaps, and redundancy** -- complementing the technical accuracy, UX, flow, language, and marketing reviews produced by other reviewers.

---

## Top 5 Critical Findings

### 1. Terminology for Identical Concepts Varies Across Pages

**Severity:** Critical
**Pages affected:** Nearly every page that discusses detection results or error handling

The documentation uses multiple terms for the same concepts without establishing that they are synonyms or explaining the differences. This is the single largest content quality issue.

**Detection result assessment:**
- `result.has_threats` (sdk/python.mdx, scan-results.mdx) -- the canonical property
- `result.is_safe` (api-reference/overview.mdx, exceptions.mdx, faq.mdx) -- never defined in the canonical SDK reference
- These are logically inverses, but a reader encountering `is_safe` on one page and `has_threats` on another has no way to know if they are the same object or different APIs

**Severity access on results:**
- `result.severity` (sdk/python.mdx, scan-results.mdx)
- `result.highest_severity` (api-reference/overview.mdx, exceptions.mdx)
- Are these the same property? Different properties? The reader cannot tell.

**Exception accessing the scan result:**
- `e.result` (sdk/python.mdx:181)
- `e.scan_result` (api-reference/exceptions.mdx:65)
- One of these will cause an `AttributeError` at runtime.

**Detection count:**
- `result.total_detections` (sdk/python.mdx, scan-results.mdx)
- `result.detection_count` (api-reference/exceptions.mdx:82)

**Recommendation:** Establish a terminology glossary as an authoritative reference. Every page must use the same property names, class names, and method signatures. If aliases exist in the actual API, document them explicitly in one place and pick one canonical name to use throughout the prose.

---

### 2. Three Different Exception Class Names Used Without Explanation

**Severity:** Critical
**Pages affected:** 12+ integration and reference pages

This is a subset of Finding #1 but deserves separate attention because it directly causes runtime errors for developers who copy examples from multiple pages.

| Exception Name | Pages Using It |
|---|---|
| `RaxeBlockedError` | sdk/python.mdx, sdk/overview.mdx, openai-wrapper.mdx, crewai.mdx, langchain.mdx, api-reference/exceptions.mdx |
| `SecurityException` | autogen.mdx, llamaindex.mdx, portkey.mdx, common-patterns.mdx |
| `ThreatDetectedError` | dspy.mdx, litellm.mdx |

The canonical exception hierarchy (from api-reference/exceptions.mdx) lists `RaxeBlockedError` as the blocking exception. Neither `SecurityException` nor `ThreatDetectedError` appear in this hierarchy. The documentation never explains whether these are:
- Framework-specific wrappers around `RaxeBlockedError`
- Aliases
- Errors in the documentation
- Different classes entirely

**Recommendation:** Either (a) use `RaxeBlockedError` consistently across all pages, with a note in each integration that this is the class to catch, or (b) if framework-specific wrappers genuinely exist, add a mapping table to the exceptions page and note the wrapper name on first use in each integration page.

---

### 3. Redundant Content Creates Maintenance Burden and Drift

**Severity:** High
**Pages affected:** why-raxe.mdx, introduction.mdx, detection-engine.mdx, threat-families.mdx, faq.mdx, quickstart.mdx

Several pages duplicate the same conceptual content, and the duplicated versions have already drifted out of sync. This is a classic content management anti-pattern: every duplication is a future inconsistency.

**Examples of redundant content:**

| Content | Appears In |
|---|---|
| L1/L2 detection overview | why-raxe.mdx, introduction.mdx, detection-engine.mdx, faq.mdx |
| Threat family list/table | why-raxe.mdx, threat-families.mdx, mcp.mdx (different subset), detection-engine.mdx |
| OWASP alignment table | why-raxe.mdx, langchain.mdx, agentic-scanning.mdx, mcp.mdx |
| Performance numbers | introduction.mdx, why-raxe.mdx, performance.mdx, detection-engine.mdx, configuration.mdx, faq.mdx, mcp.mdx |
| FAQ-style Q&A | why-raxe.mdx (bottom accordion), faq.mdx |
| Web framework patterns | common-patterns.mdx, migration.mdx |

The why-raxe.mdx page is the worst offender: at ~346 lines, it contains its own threat detection overview, its own OWASP table, its own L1/L2 explanation, its own FAQ section, and its own comparison table -- all of which have dedicated pages elsewhere. The duplicated content has already drifted: why-raxe says "515+ rules" while introduction says "514+", the FAQ says L2 takes "~50ms" while detection-engine says "~3ms", and the threat family list on the MCP page uses completely different family codes than the canonical list.

**Recommendation:** Apply the DRY principle to documentation content. Each fact should have one authoritative source:
- **Rule count:** defined in one place, referenced everywhere
- **Latency numbers:** authoritative on performance.mdx, summarized (not duplicated) elsewhere
- **Threat families:** authoritative on threat-families.mdx, summarized elsewhere
- **why-raxe.mdx:** trim to the business case (privacy, latency, cost, comparison) and link to technical pages for depth. Remove the redundant FAQ accordion (or move unique questions to the main FAQ).

---

### 4. Inconsistent Numeric Claims Across Pages

**Severity:** High
**Pages affected:** 7+ pages

The documentation cites different numbers for the same metrics on different pages. While some variation is expected between "marketing" and "technical" pages, the range of discrepancy is too wide to be intentional rounding.

**Rule count:**
- "514+" (introduction.mdx)
- "514 rules" (cli/commands.mdx doctor output)
- "514 curated regex patterns" (detection-engine.mdx)
- "515+" (why-raxe.mdx, integrations/index.mdx)
- The threat-families.mdx page provides itemised counts that sum to 514.

**L1 latency:**
- "~0.3ms" (configuration.mdx fast mode)
- "P50=0.37ms, P95=0.49ms" (performance.mdx)
- "~0.4ms" (detection-engine.mdx)
- "~1ms" (introduction.mdx)
- "~3ms" (why-raxe.mdx)
- "<5ms" (mcp.mdx)

**L2 latency:**
- "~3ms" (detection-engine.mdx)
- "~7ms" (why-raxe.mdx)
- "~50ms" (faq.mdx)

A tenfold discrepancy (3ms vs 50ms) for L2 latency is not a rounding difference; it is a factual contradiction.

**Recommendation:** Designate performance.mdx as the authoritative source for benchmarks. All other pages should use consistent approximate figures that do not contradict the benchmarks. Consider using ranges ("sub-millisecond" for L1, "under 5ms" for L2) on non-technical pages to avoid precise-but-wrong numbers.

---

### 5. Content Gaps in Explanatory Bridging Between Sections

**Severity:** High
**Pages affected:** All integration pages, several concept pages

Integration pages assume knowledge of concepts that are not yet introduced in the reading order. Terms like "L1 rules", "L2 ML classifier", "severity levels", "threat families", and "scan pipeline" appear on integration pages without definition or link. A developer arriving at the LangChain page from the quickstart encounters these terms cold.

This is compounded by the navigation order: "Protect Your AI" (integrations) appears before "How It Works" (concepts) in the sidebar. A developer following the natural top-to-bottom reading order encounters the applications before the foundations.

No integration page includes a "Prerequisites" or "Before You Start" section linking to the conceptual foundation. The gap between "I installed it" and "I understand what it is scanning for" is left unbridged.

**Recommendation:** Add a brief "Prerequisites" info box to each integration page linking to at minimum the detection engine and threat families pages. Alternatively, rearrange the sidebar to place "How It Works" before "Protect Your AI" so the concepts are introduced first.

---

## Top 5 Quick Wins

### 1. Standardise the Rule Count to a Single Number
Pick "514" or "515+" (not both) and use it on every page. If rules are added between releases, use "500+" as an evergreen approximation. A global search-and-replace for rule count references would take under 30 minutes and eliminate a recurring trust-eroding inconsistency.

**Files to update:** introduction.mdx, why-raxe.mdx, integrations/index.mdx, detection-engine.mdx, cli/commands.mdx, configuration.mdx

### 2. Add a One-Line "Prerequisites" Note to Each Integration Page
Add a consistent info callout at the top of every integration page:

```
<Info>New to RAXE? Complete the [Quick Start](/quickstart) first, and see [How Detection Works](/concepts/detection-engine) to understand L1/L2 scanning.</Info>
```

This takes 2 minutes per page and eliminates the "undefined terminology" problem for new users.

### 3. Remove the Redundant FAQ Section from why-raxe.mdx
The bottom of why-raxe.mdx has an accordion FAQ that overlaps significantly with resources/faq.mdx. Remove it and add a link: "Have questions? See the [FAQ](/resources/faq)." This removes ~50 lines of potentially-drifting duplicate content.

### 4. Update Stale Dates and Versions
Three quick fixes:
- `cli/overview.mdx:129`: REPL version shows `v0.0.1` -- update to current version
- `cli/commands.mdx:494`: Suppression example uses `--expires 2025-06-01` -- update to a future date
- `resources/changelog.mdx`: Roadmap section references v0.3 as "Coming Soon" when current version is v0.9.1 -- update or remove

### 5. Standardise the Opening Pattern for Integration Pages
Most integration pages open with "## Overview" followed by a description. Some open with "## Installation" directly. A consistent opening pattern (brief overview sentence, then installation, then quick start) would make the integration pages feel like a cohesive set rather than independent documents. Currently, the variation in structure between integration pages forces readers to reorient themselves on each page.

---

## Writing Quality Assessment

### Strengths

**Concise, direct prose.** The documentation avoids the bloated passive-voice style common in enterprise technical writing. Sentences are short, paragraphs are brief, and instructions are imperative ("Add to your config", "Run this command", "Scan a prompt"). This is exactly right for developer documentation.

**Consistent technical register.** The writing consistently pitches at a mid-senior developer level. It does not over-explain basic concepts (what pip is, what JSON is) or under-explain RAXE-specific concepts (threat families, scan pipeline). The calibration is appropriate.

**Effective use of code examples.** Nearly every concept is followed immediately by a working code example. The code examples use realistic filenames (`basic.py`, `config.py`, `error_handling.py`), include comments where non-obvious, and are syntactically correct. The `title` attribute on code blocks aids comprehension.

**Good progressive disclosure.** Within individual pages, information is layered from simple to complex. The quickstart starts with a CLI command, then shows the SDK, then shows framework integration. The production checklist starts with shadow mode, then A/B testing, then full blocking. This is well-designed.

### Weaknesses

**Heading hierarchy is inconsistent across pages.** Some pages use H2 as the top-level heading (most pages), while the content within sections jumps between H3 and H4 inconsistently. For example:
- `cli/commands.mdx` uses H2 for each command, H3 for "Options", H4 for sub-command options -- this is correct
- `integrations/mcp.mdx` uses H2 for major sections, H3 for subsections, but some subsections that should be H4 are H3
- Several concept pages use H3 for what should be H2-level content

**Paragraph length varies wildly.** Most pages maintain a good paragraph length (2-4 sentences), but some pages have single-sentence paragraphs that fragment the reading experience, while others have dense 6-8 sentence paragraphs that should be broken up. The MSSP integration page and the detection engine page tend toward dense paragraphs; the wrapper pages tend toward one-liner paragraphs.

**Inconsistent use of "you" vs impersonal voice.** Most pages address the developer directly ("You can configure...", "Add to your config...") which is good. But some reference sections shift to impersonal voice ("The scan method returns...", "Threats are detected by...") without a clear pattern for when each voice is appropriate.

---

## Terminology Consistency Audit

### Core Terms Used Inconsistently

| Concept | Variant 1 | Variant 2 | Variant 3 | Recommendation |
|---|---|---|---|---|
| Detection count | `total_detections` | `detection_count` | -- | Use `total_detections` (canonical) |
| Safety check | `has_threats` | `is_safe` | -- | Use `has_threats` (canonical) |
| Severity access | `severity` | `highest_severity` | -- | Use `severity` (canonical) |
| Blocked exception | `RaxeBlockedError` | `SecurityException` | `ThreatDetectedError` | Use `RaxeBlockedError` (canonical) |
| Base exception | `RaxeError` | `RaxeException` | -- | Use `RaxeException` (per exceptions.mdx) |
| Result access on error | `e.result` | `e.scan_result` | -- | Pick one, document authoritatively |
| OpenAI wrapper import | `from raxe import RaxeOpenAI` | `from raxe.wrappers import` | `from raxe.sdk.wrappers import` | Use `from raxe import RaxeOpenAI` |
| Category code format | `"PI"` (short) | `"prompt_injection"` (long) | -- | Use `"PI"` (matches rule IDs) |
| Detection layer | "L1 rules" / "L2 ML" | "rule-based" / "neural" | "pattern" / "classifier" | Pick one pair consistently |
| Performance mode name | `"thorough"` | `"accurate"` | -- | Pick one (used in config vs API) |
| Rule count | "514" | "514+" | "515+" | Pick one |

### Terms That Should Be Defined on First Use

The following terms appear without definition or link on their first occurrence in integration pages:

- **L1 / L2**: Used on every integration page but only defined on detection-engine.mdx
- **Threat family**: Used in every scan result example but only defined on threat-families.mdx
- **Severity levels**: Referenced in every blocking configuration but the scale (LOW/MEDIUM/HIGH/CRITICAL) is only documented on scan-results.mdx and detection-engine.mdx
- **Scan pipeline**: Used on architecture.mdx and several integration pages but never formally defined as a term
- **Think-time security**: Used as a tagline on langchain.mdx and crewai.mdx but never defined -- is this RAXE's term for inference-time scanning? If so, it should be defined once and linked.

---

## Readability Assessment

### Scanning Patterns

The documentation generally supports F-pattern scanning (the way developers read docs: headings first, then code blocks, then prose). Code blocks are visually prominent, headings are descriptive, and tables provide scannable data.

**Good scanning patterns:**
- The quickstart page's numbered steps with CodeGroups
- The integration index's comparison table
- The troubleshooting page's "Quick Diagnosis" table
- The performance page's stat cards at the top

**Poor scanning patterns:**
- why-raxe.mdx: Too many accordion sections that hide important content behind clicks
- cli/commands.mdx (764 lines): Very long with no internal navigation aid or table of contents
- concepts/mssp-integration.mdx: Dense prose sections that should be broken into subsections with headings
- concepts/threat-families.mdx: Large tables with small text that require horizontal scrolling

### Jargon Calibration

The documentation generally assumes the right level of knowledge. Terms like "callback handler", "middleware", "decorator pattern", "async/await", "ReAct agent", and "RAG pipeline" are used without explanation, which is correct for the target audience of Python developers working with LLM frameworks.

However, RAXE-specific jargon is sometimes used without introduction:
- "L1" and "L2" appear dozens of times before being defined
- "Voting engine" appears on the detection-engine page without first explaining why voting is needed
- "BinaryFirstEngine" is used as a class name before explaining what binary-first means in context
- "Think-time security" is a coined phrase that should be explicitly defined on first use

---

## Content Gap Analysis

### Missing Content

1. **No glossary or terminology page.** Given the number of RAXE-specific terms (L1, L2, threat family, scan pipeline, voting engine, suppression, policy, etc.), a glossary page would significantly aid comprehension and serve as a canonical reference for consistent terminology.

2. **No "How to Read This Documentation" guide.** The docs cover 9 navigation sections with different purposes (learning, integrating, understanding, operating, referencing). A brief "How to use these docs" section on the introduction page would help readers self-navigate.

3. **No content on what happens when L2 fails but L1 succeeds.** The dual-layer architecture implies partial failure modes, but no page addresses this. Does the result include partial data? Does it fall back to L1-only? Does it error?

4. **No content on rule versioning and updates.** Rules are central to RAXE but there is no documentation on how rules are updated, whether users can update rules independently, or what the rule update lifecycle looks like.

5. **No content on the L2 model's token handling.** The 512-token limit is mentioned on detection-engine.mdx but what happens with longer inputs? Truncation? Sliding window? Chunking? This is critical for developers sending long prompts.

6. **Anthropic wrapper page is thinner than OpenAI wrapper.** The OpenAI wrapper page has benefits cards, a mermaid diagram, streaming examples, and a migration guide. The Anthropic wrapper page has a subset of these. Given Anthropic's growing market share, this asymmetry should be addressed.

### Content That Could Be Removed

1. **why-raxe.mdx FAQ section** -- duplicates resources/faq.mdx
2. **why-raxe.mdx L1/L2 technical detail** -- duplicates detection-engine.mdx
3. **why-raxe.mdx threat family list** -- duplicates threat-families.mdx
4. **migration.mdx web framework patterns** -- duplicates common-patterns.mdx
5. **Multiple OWASP alignment tables** -- could be consolidated to one page and linked

---

## Page-by-Page Notes

### Getting Started

**introduction.mdx** -- Grade: A-. Strong opening, good code example, effective use of tabs for framework examples. Minor issues: rule count inconsistency ("514+"), performance numbers don't match performance.mdx benchmarks. The page sets expectations well but could benefit from a brief "How to use these docs" orientation.

**why-raxe.mdx** -- Grade: C+. Tries to do too much. Contains 6+ accordion sections, a comparison table, threat detection overview, FAQ, persona cards, and getting-started steps. The business case content (privacy, latency, cost comparison) is excellent, but it is diluted by technical content that belongs on dedicated pages. At ~346 lines, this is the longest page and should be split or trimmed significantly.

**quickstart.mdx** -- Grade: A. The best page in the documentation. The "60-second" promise is honest, the progression from CLI to SDK to framework is well-structured, and the "What Just Happened" section is superb for building understanding. The production readiness steps at the bottom provide a clear forward path. No significant content issues.

**installation.mdx** -- Grade: A-. Clean, focused, and well-organized. The optional extras table is helpful. The troubleshooting accordions are well-targeted. Minor: the "Next Steps" section only points to Configuration -- should also point to the integrations index and quickstart.

**configuration.mdx** -- Grade: B+. Good coverage of YAML config, environment variables, and programmatic config. The performance modes table is useful. Minor: latency numbers for modes don't align with performance.mdx.

### Integrations

**integrations/index.mdx** -- Grade: A. Excellent wayfinding page. The accordion-based "Quick Decision Guide" is a brilliant UX pattern. The comparison table communicates complexity, protection level, and best-use at a glance. The card groups are well-organized by category.

**integrations/mcp.mdx** -- Grade: A-. Comprehensive coverage of both Gateway and Server modes. The ASCII architecture diagram is clear. Claude Desktop and Cursor setup configs are copy-paste ready. Minor: the threat families listed in the MCP Server tools section (PI, JB, DE, RP, CS, SE, TA, CI) use different family codes than the canonical L1 list (PI, JB, PII, CMD, ENC, HC, RAG). This is confusing -- are these L2 families? MCP-specific families? The page does not explain.

**integrations/langchain.mdx** -- Grade: A-. Strong coverage of callback handler, agentic methods, chain/agent/RAG integration. The goal hijack detection example is compelling. The OWASP alignment table adds credibility. Weakness: no next steps section (dead end), no prerequisites linking to concepts.

**integrations/crewai.mdx** -- Grade: B+. Good structure. The ScanMode enum table is clear. Weakness: thinner than LangChain page, no OWASP table, dead end.

**integrations/autogen.mdx** -- Grade: B+. Good dual coverage of v0.2 hooks and v0.4 wrapper. Weakness: uses `SecurityException` instead of `RaxeBlockedError`, dead end.

**integrations/llamaindex.mdx** -- Grade: B+. Three callback types well-differentiated. Instrumentation API is forward-looking. Weakness: uses `SecurityException`, dead end.

**integrations/dspy.mdx** -- Grade: B. Adequate but thinner than other pages. Uses `ThreatDetectedError`, no OWASP table, dead end.

**integrations/portkey.mdx** -- Grade: B. Two integration patterns clearly differentiated. Weakness: assumes familiarity with Portkey's architecture, uses `SecurityException`, dead end.

**integrations/openclaw.mdx** -- Grade: C. The prominent WARNING about unimplemented features is a credibility issue. The page documents an API that doesn't work yet, then offers a workaround below. The recommended approach (MCPorter) should lead, with the unimplemented API moved to a "Planned" section. Broken link to `/rules/custom` (should be `/rules/custom-rules`).

**integrations/litellm.mdx** -- Grade: B+. Clean, focused. Uses `ThreatDetectedError`, dead end.

**sdk/openai-wrapper.mdx** -- Grade: A-. The drop-in replacement concept is brilliantly clear. The mermaid diagram showing scan-before-call flow is excellent. The migration guide (one import change) is compelling. Dead end.

**sdk/anthropic-wrapper.mdx** -- Grade: B. Mirrors OpenAI wrapper but notably thinner. Missing: benefits cards, mermaid diagram, detailed streaming section. Should be brought to parity with the OpenAI page. Dead end.

**integrations/huggingface.mdx** -- Grade: B+. RaxePipeline wrapper is straightforward. Broken link to `/wrappers/openai` (should be `/sdk/openai-wrapper`). Dead end (though has a "Related" card group with the broken link).

**integrations/common-patterns.mdx** -- Grade: A-. Practical FastAPI, Flask, and Django patterns. Async queue and batch processing examples are production-ready. Dead end.

**integrations/ci-cd.mdx** -- Grade: B+. GitHub Actions and GitLab CI examples are copy-paste ready. Pre-commit hook is practical. Dead end.

### How It Works / Concepts

**concepts/detection-engine.mdx** -- Grade: A-. Core conceptual page. L1/L2 architecture well-explained. Voting engine and classification heads are detailed. Weakness: could be encountered earlier in the reading journey; some dense paragraphs should be broken up.

**concepts/threat-families.mdx** -- Grade: B+. Comprehensive listing of all families. L1/L2 distinction is clear. Minor grammar issue: "personal identifiable information" should be "personally identifiable information". Dense tables may overwhelm new users.

**rules/overview.mdx** -- Grade: B+. Good entry point. Rule YAML structure is clear. Has next steps (good). Weakness: opens weakly with "What Are Rules?" -- could be more engaging.

**concepts/architecture.mdx** -- Grade: B+. Clean architecture explanation. Privacy design is well-documented. Weakness: dead end, should link to detection-engine and policies.

**concepts/policies.mdx** -- Grade: B+. ALLOW/FLAG/BLOCK/LOG well-explained. YAML examples are practical. Weakness: dead end, should link to suppressions (the natural companion).

**concepts/suppressions.mdx** -- Grade: B+. Comprehensive. SDK inline and context manager patterns are practical. Weakness: example expiry date "2025-06-01" is in the past, dead end.

**concepts/mssp-integration.mdx** -- Grade: B. Thorough enterprise content. "Who Is This For?" segmentation at the top is good. Weakness: very long, dense prose sections, should cross-link to multi-tenant.

**concepts/multi-tenant.mdx** -- Grade: B+. Hierarchy well-explained. Policy modes and resolution chain are clear. Weakness: dead end, should link to mssp-integration and policies.

### Guides

**guides/migration.mdx** -- Grade: A-. The three-phase approach (Shadow, Wrapper, Blocking) is well-structured. Rollback patterns build confidence. Some overlap with common-patterns.mdx (web framework examples duplicated).

**guides/production-checklist.mdx** -- Grade: A-. Thorough, practical. The week-by-week rollout plan is realistic. False positive handling and fail-open/fail-closed patterns are essential. Good next steps.

### SDK / Advanced

**sdk/overview.mdx** -- Grade: B+. Good summary and pattern comparison. Links to sub-pages well. Minor: some import paths differ from individual pages.

**sdk/python.mdx** -- Grade: A-. Comprehensive ScanPipelineResult reference. Should be THE authoritative source. Multi-tenant and thread safety sections are well-placed. Dead end.

**sdk/agentic-scanning.mdx** -- Grade: A. One of the strongest pages. The 12 scan types, 4 agentic rule families, and OWASP alignment are genuinely differentiating content. Dead end -- should be linked more prominently from integration pages and the introduction.

**sdk/async.mdx** -- Grade: B+. AsyncRaxe and batch scanning well-documented. FastAPI integration example is practical. Dead end.

**rules/custom-rules.mdx** -- Grade: B+. Rule format and validation are clear. CE limit of 50 rules noted. Weakness: no complete end-to-end custom rule example for a common use case.

### Enterprise

**integrations/siem.mdx** -- Grade: B+. Comprehensive SIEM coverage. CEF field mapping is thorough. Weakness: jumps into CLI commands without first explaining what data RAXE sends to SIEMs. No clear tier-requirement notice.

### Reference

**resources/troubleshooting.mdx** -- Grade: A-. The "Quick Diagnosis" table at the top is excellent. Comprehensive coverage. Weakness: false positives section does not link to suppressions page.

**resources/faq.mdx** -- Grade: B-. Covers common questions. Critical issues: reveals 1,000 scans/day limit (not mentioned anywhere else in the funnel), claims L2 takes "~50ms" (contradicts other pages), states RAXE is "not open source" (contradicts why-raxe.mdx implications). Answers rarely link to detailed docs.

**resources/error-codes.mdx** -- Grade: B+. Well-structured error code reference. Links to troubleshooting.

**resources/performance.mdx** -- Grade: A-. Most credible performance numbers. Pipeline diagram is helpful. Should be the authoritative source referenced by all other pages. Dead end.

**resources/changelog.mdx** -- Grade: C+. Version history is adequate. Critical: roadmap section references v0.3 as "Coming Soon" when current version is v0.9.1.

### API Reference

**api-reference/overview.mdx** -- Grade: B+. Clean index page. Weakness: uses `result.is_safe` and `result.highest_severity` which are not in the canonical SDK reference.

**api-reference/raxe-client.mdx** -- Grade: A-. Comprehensive constructor and method docs. Minor: `scan_fast()` latency claim ("<3ms") seems high for L1-only; mode naming ("accurate" vs "thorough") inconsistency.

**api-reference/scan-results.mdx** -- Grade: A-. Most complete result reference. Weakness: detection category shown as `"prompt_injection"` while other pages use `"PI"`.

**api-reference/exceptions.mdx** -- Grade: B+. Good hierarchy documentation. Weakness: uses non-canonical property names (`is_safe`, `highest_severity`, `detection_count`, `scan_result`).

### CLI Reference

**cli/overview.mdx** -- Grade: B+. Effective quick reference. REPL version shows v0.0.1 (stale).

**cli/commands.mdx** -- Grade: B+. Comprehensive. Very long (764 lines) -- could benefit from splitting. Stale suppression expiry date. The "raxe doctor" output shows "514 rules" which matches some pages but not others.

---

## Heading Hierarchy Assessment

### Common Patterns (Good)
Most pages follow: H2 for major sections, H3 for subsections, H4 for sub-subsections. Code blocks appear under H3 or H4 headings. This is correct and consistent.

### Issues
- **integrations/mcp.mdx**: Some H3 headings ("Claude Desktop Setup", "Cursor Setup", "What Gets Scanned") are logically H4 under the H2 "MCP Security Gateway" section
- **why-raxe.mdx**: The page has too many H2 sections (8+), making the page feel flat rather than hierarchical
- **cli/commands.mdx**: Correct hierarchy but the sheer number of H2 sections (15+ commands) makes the sidebar TOC very long
- **Several pages**: Use H2 "## Overview" as the first heading after frontmatter -- this is redundant when the frontmatter title already conveys the topic

### Recommendation
Drop the generic "## Overview" headings. The page title (from frontmatter) is the H1. The first H2 should be the first substantive section, not a repetition of what the page is about.

---

## Recommendations Summary

### Priority 1: Must-Fix (This Sprint)
1. **Standardise terminology** across all 47 pages: property names, exception classes, import paths, category codes
2. **Resolve numeric contradictions**: rule count, latency figures, L2 latency (3ms vs 50ms), threat family counts
3. **Add prerequisites/concept links** to all integration pages
4. **Fix two broken internal links**: openclaw.mdx (`/rules/custom`), huggingface.mdx (`/wrappers/openai`)
5. **Fix stale content**: REPL version, suppression expiry date, changelog roadmap

### Priority 2: Should-Fix (This Month)
6. **Trim why-raxe.mdx** -- remove redundant technical content, keep business case, link to authoritative pages
7. **Bring Anthropic wrapper** to parity with OpenAI wrapper page
8. **Restructure openclaw.mdx** -- lead with MCPorter (the working approach), move unimplemented API to bottom
9. **Add "What's Next" sections** to all dead-end pages (approximately 20 pages)
10. **Cross-link policies and suppressions** pages to each other
11. **Cross-link FAQ and troubleshooting** false positive sections to suppressions page

### Priority 3: Nice-to-Have (Backlog)
12. **Create a terminology glossary** page as canonical reference for RAXE-specific terms
13. **Document partial failure behaviour** (what happens when L2 fails but L1 succeeds)
14. **Document rule update mechanism** and versioning
15. **Document L2 token handling** for inputs exceeding 512 tokens
16. **Consolidate OWASP alignment tables** to one authoritative page and link from integrations
17. **Split cli/commands.mdx** into per-category sub-pages for improved navigability
18. **Add "Most Common Threats" summary** to the top of threat-families.mdx for new users

---

*Review completed: 2026-02-06*
*Reviewer: Content Expert (Writing Quality, Clarity, Consistency)*
*Pages reviewed: 47/47*
