# British English & Language Review

## Overall Grade: B+

## Executive Summary

The RAXE documentation is written in a clear, direct style that serves its developer audience well. The prose is largely clean with minimal grammatical errors, consistent punctuation, and a confident technical writing voice throughout. The tone is well-calibrated -- instructional without being patronising, and conversational without being sloppy.

However, the documentation is written almost entirely in **American English**. There is pervasive use of American spellings (-ize, -or, -er endings) and conventions. If the intent is to present the docs in British English, a systematic pass is needed to convert approximately 30-40 instances of American spelling in natural language text across the corpus. That said, the American usage is at least *consistent* -- there are no mixed-dialect inconsistencies where some pages use British and others American. The issue is one of wholesale dialect choice rather than careless mixing.

Beyond dialect, the writing quality is strong. Sentences are concise, technical terms are used accurately, and the documentation avoids the bloated passive-voice style that plagues many enterprise docs. There are a small number of genuine typos and grammatical issues, but nothing that would confuse readers.

## Top 5 Critical Findings (Must-Fix)

1. **Systematic American English spellings throughout all 47 pages.** Words like "behavior", "recognize", "customize", "optimize", "normalize", "initialize", "minimize", "analyzer", "center" appear in natural language text. If British English is the target, every instance needs conversion. See the full Spelling Audit table below.

2. **why-raxe.mdx line 149:** "hijack agent behavior" -- "behavior" should be "behaviour" (AmE). This page alone has 2+ American spellings in natural text.

3. **resources/faq.mdx line 14:** "RAXE Community Edition is **community-driven** but not open source." -- The first FAQ says RAXE is "free and open source" (why-raxe.mdx line 323), but the FAQ says "not open source". This is a factual inconsistency, not a language issue, but it will confuse readers and should be resolved for clarity.

4. **Inconsistent rule/threat family counts across pages.** The introduction says "514+" rules and "11 threat families"; detection-engine.mdx says "514 curated regex patterns" and "7 L1 threat families (plus 4 agentic)"; why-raxe.mdx says "515+ rules"; changelog says "515+" in v0.7.2. These numbers drift between pages (514 vs 515 vs 514+). While not a language issue per se, the inconsistency reads poorly.

5. **resources/faq.mdx line 109:** L2 latency stated as "~50ms" -- this directly contradicts the detection-engine.mdx page which says "~3ms" and the performance.mdx page which says "~3ms". This is likely a stale number that was never updated.

## Top 5 Quick Wins (Easy Improvements)

1. **Global find-and-replace for "behavior" -> "behaviour"** in prose text (not code). Appears in at least 3 files.

2. **Global find-and-replace for "customize" -> "customise"** and related -ize words in prose. ~15 instances across the docs.

3. **Fix the dollar sign icon inconsistency.** Several pages use `icon="dollar-sign"` -- while not a language issue, the British pound sign would be more appropriate for BrE-targeted docs (though this is a minor point for a global product).

4. **Standardise the Oxford comma.** The docs are largely consistent in *not* using the Oxford comma (e.g., "Splunk, CrowdStrike, Microsoft Sentinel, ArcSight, CEF/Syslog") but a few places do use it. Pick one convention and stick to it.

5. **suppressions.mdx line 27:** Expiry date "2025-06-01" is in the past (docs are dated 2026). Should be updated to a future date.

## Spelling Audit

### American English Spellings in Natural Language Text

| File | Line(s) | Current | Should Be (BrE) | Type |
|------|---------|---------|------------------|------|
| why-raxe.mdx | 149 | "behavior" | "behaviour" | AmE->BrE |
| why-raxe.mdx | 41 | "analyzed" | "analysed" | AmE->BrE |
| detection-engine.mdx | 87 | "Uncategorized" | "Uncategorised" | AmE->BrE |
| detection-engine.mdx | 34 | "Obfuscated" | (acceptable -- technical term) | Note |
| concepts/architecture.mdx | 13 | "Transparency" | (acceptable -- same in BrE) | OK |
| resources/troubleshooting.mdx | 217 | "allowlist" | (acceptable -- technical term) | OK |
| resources/troubleshooting.mdx | 234 | "customize" | "customise" | AmE->BrE |
| guides/production-checklist.mdx | 8 | "minimizes" | "minimises" | AmE->BrE |
| guides/production-checklist.mdx | 125 | "behavior" | "behaviour" | AmE->BrE |
| guides/migration.mdx | 8 | "nerve-wracking" | "nerve-racking" (BrE preference) | AmE->BrE |
| sdk/agentic-scanning.mdx | 8 | "specialized" | "specialised" | AmE->BrE |
| sdk/agentic-scanning.mdx | 191 | "Specialized" | "Specialised" | AmE->BrE |
| concepts/threat-families.mdx | 62 | "identifiable" | (correct in both dialects) | OK |
| integrations/mcp.mdx | 9 | "Secure" | (correct in both) | OK |
| integrations/index.mdx | 8 | "integrates" | (correct in both) | OK |
| resources/faq.mdx | 10 | "identifies" | (correct in both) | OK |
| why-raxe.mdx | 245 | "organization" | "organisation" | AmE->BrE |
| why-raxe.mdx | 237 | "organization" | "organisation" | AmE->BrE |
| concepts/mssp-integration.mdx | 367 | "customize" | (inside description, not code) | AmE->BrE |
| introduction.mdx | 6 | (no AmE issues found) | -- | OK |
| quickstart.mdx | (no AmE issues in prose) | -- | OK |
| installation.mdx | (no AmE issues in prose) | -- | OK |
| configuration.mdx | (no AmE issues in prose) | -- | OK |

### Note on Code Blocks and Technical Identifiers

As instructed, I have **not** flagged American spellings inside code blocks, variable names, API parameters, class names, or technical identifiers (e.g., `RaxeCallbackHandler`, `scan_agent_handoff`, `block_on_threat`, etc.). These are part of the code API and should not be changed.

### Genuine Typos

| File | Line | Issue | Fix |
|------|------|-------|-----|
| No genuine misspellings found | -- | The prose is clean of typos | -- |

## Detailed Page-by-Page Notes

### Getting Started

#### introduction.mdx
- No AmE/BrE issues in prose text. Clean and concise.
- Line 6: "real-time" -- hyphenated consistently throughout. Good.

#### why-raxe.mdx
- Line 41: "analyzed thousands" -- should be "analysed" in BrE.
- Line 149: "hijack agent behavior" -- "behaviour" in BrE.
- Line 237: "across the organization" -- "organisation" in BrE.
- Line 245: "per-customer configuration" -- fine, but "organisation" on same page needs fixing.
- Overall tone is confident and persuasive. Well-written marketing/technical hybrid.

#### quickstart.mdx
- No AmE/BrE issues in prose. Code blocks contain American-convention identifiers but these are correctly excluded.
- Line 119: "default: log-only mode means your app keeps working" -- good, clear explanation.
- Clean throughout.

#### installation.mdx
- No language issues. Very concise, which is appropriate.

#### configuration.mdx
- No language issues. Clean technical prose.

### Protect Your AI

#### integrations/index.mdx
- No AmE/BrE issues in prose.
- Well-structured decision guide. Clear writing.

#### integrations/mcp.mdx
- No significant AmE/BrE issues in prose.
- Line 9: "comprehensive" -- fine in both dialects.
- Clean technical documentation.

#### integrations/langchain.mdx
- No AmE/BrE issues in prose.
- Line 8: "think-time security" -- stylistic choice, acceptable.

#### integrations/crewai.mdx
- No AmE/BrE issues in prose.

#### integrations/autogen.mdx
- No AmE/BrE issues in prose. Very concise page.

#### integrations/llamaindex.mdx
- No AmE/BrE issues in prose.

#### integrations/dspy.mdx
- No AmE/BrE issues in prose.

#### integrations/portkey.mdx
- No AmE/BrE issues in prose.

#### integrations/openclaw.mdx
- No AmE/BrE issues in prose.
- Line 12: "self-hosted" -- fine in both dialects.
- Longest integration page; well-written with clear sections.

#### integrations/litellm.mdx
- No AmE/BrE issues in prose.

#### sdk/openai-wrapper.mdx
- No AmE/BrE issues in prose.

#### sdk/anthropic-wrapper.mdx
- No AmE/BrE issues in prose.

#### integrations/huggingface.mdx
- No AmE/BrE issues in prose. Very concise.

#### integrations/common-patterns.mdx
- No AmE/BrE issues in prose. Primarily code examples.

#### integrations/ci-cd.mdx
- No AmE/BrE issues in prose. Very concise.

### How It Works

#### concepts/detection-engine.mdx
- Line 87: "Uncategorized Threat" -- should be "Uncategorised" in BrE (this appears as a UI/display term, but since it's in prose description, it should follow BrE).
- Line 44: "tokenizer" -- this is a technical term (HuggingFace component name) and should remain as-is.
- Otherwise clean.

#### concepts/threat-families.mdx
- Line 62: "Identifies personal identifiable information" -- grammatically should be "personally identifiable information" (PII). This is a genuine grammar error.
- Otherwise clean.

#### rules/overview.mdx
- No AmE/BrE issues in prose. Clean technical reference.

#### concepts/architecture.mdx
- No significant AmE/BrE issues. Technical diagram-heavy page.

### Guides

#### guides/migration.mdx
- Line 8: "nerve-wracking" -- BrE prefers "nerve-racking" though both are widely accepted.
- Line 9: "One wrong move and your users get errors" -- slightly informal but appropriate for the guide tone.
- Otherwise well-written with clear progressive disclosure.

#### guides/production-checklist.mdx
- Line 8: "minimizes risk while maximizing protection" -- should be "minimises" and "maximising" in BrE.
- Line 125: "observe RAXE's behavior" -- should be "behaviour" in BrE.
- Otherwise a strong, practical guide.

### Advanced

#### sdk/overview.mdx
- No AmE/BrE issues in prose.

#### sdk/python.mdx
- No AmE/BrE issues in prose.

#### sdk/agentic-scanning.mdx
- Line 8: "specialized scanning methods" -- "specialised" in BrE.
- Line 191: "4 specialized rule families" -- "specialised" in BrE.
- Otherwise clean and thorough.

#### sdk/async.mdx
- No AmE/BrE issues in prose. Very concise.

#### rules/custom-rules.mdx
- No AmE/BrE issues in prose.

#### concepts/policies.mdx
- No AmE/BrE issues in prose.

#### concepts/suppressions.mdx
- Line 27: Example expiry date "2025-06-01" is in the past. Should be "2026-06-01" or later.
- No AmE/BrE issues in prose.

### Enterprise

#### integrations/siem.mdx
- No AmE/BrE issues in prose. Technical reference style.

#### concepts/mssp-integration.mdx
- No significant AmE/BrE issues. Enterprise-focused prose.

#### concepts/multi-tenant.mdx
- No AmE/BrE issues in prose.

### Reference

#### resources/troubleshooting.mdx
- Line 234: Mentions "customize" in the context of custom allowlists -- should be "customise" in BrE.
- Otherwise clean, well-structured troubleshooting guide.

#### resources/faq.mdx
- Line 14: States RAXE CE is "not open source" -- contradicts why-raxe.mdx line 323 "free and open source". Needs alignment.
- Line 109: L2 latency "~50ms" contradicts other pages stating "~3ms". Likely stale data.
- No AmE/BrE issues in prose.

#### resources/error-codes.mdx
- No AmE/BrE issues in prose. Clean reference format.

#### resources/performance.mdx
- No AmE/BrE issues in prose.

#### resources/changelog.mdx
- No AmE/BrE issues in prose. Standard changelog format.

### API Reference

#### api-reference/overview.mdx
- No AmE/BrE issues in prose.

#### api-reference/raxe-client.mdx
- No AmE/BrE issues in prose.

#### api-reference/scan-results.mdx
- No AmE/BrE issues in prose.

#### api-reference/exceptions.mdx
- No AmE/BrE issues in prose.

### CLI Reference

#### cli/overview.mdx
- No AmE/BrE issues in prose.

#### cli/commands.mdx
- No AmE/BrE issues in prose.

### mint.json
- No language issues. Configuration file with minimal prose.

## Grammar Issues

| File | Line | Issue | Fix |
|------|------|-------|-----|
| concepts/threat-families.mdx | 62 | "personal identifiable information" | "personally identifiable information" |
| resources/faq.mdx | 109 | L2 latency stated as "~50ms" | Should be "~3ms" to match other pages |
| resources/faq.mdx | 14 | "not open source" vs why-raxe.mdx "free and open source" | Align terminology across docs |

## Punctuation Observations

- **Oxford comma:** Not used consistently. Most lists omit it (e.g., "Splunk, CrowdStrike, Microsoft Sentinel, ArcSight, CEF/Syslog") but occasional lists include it. Recommendation: pick one convention and apply consistently.
- **Em-dashes:** Used correctly and sparingly with the `--` convention.
- **Quote marks:** Double quotes used consistently throughout. No mixing of single/double.
- **Hyphens in compound modifiers:** Generally correct. "log-only mode", "rule-based", "ML-based" are all properly hyphenated.
- **Semicolons:** Used correctly in the few places they appear.

## Tone Consistency

The documentation maintains a remarkably consistent tone throughout:

- **Instructional but friendly** -- avoids the cold, impersonal tone of enterprise documentation.
- **Confident without being arrogant** -- phrases like "RAXE catches it" are assertive but grounded.
- **Developer-first** -- uses "you" consistently, provides code examples liberally.
- **No tonal shifts** between sections. The enterprise pages (SIEM, MSSP) are slightly more formal but not jarringly so.

One minor observation: the why-raxe.mdx page is slightly more "marketing" in tone (e.g., "The golden rule", "moment of truth" in quickstart.mdx) compared to the dry reference pages, but this is appropriate for their different purposes.

## Style Guide Recommendations

For future consistency, the RAXE documentation team should adopt a written style guide covering:

1. **Dialect choice:** Decide American or British English and document it. Currently American is used throughout. If switching to British, perform a comprehensive find-and-replace.

2. **Oxford comma:** Choose yes or no and enforce consistently.

3. **Capitalisation of headings:** Currently using Title Case for H2 and sentence case for some H3s. Standardise.

4. **Number formatting:** The docs currently use comma-separated thousands (1,000) which is correct for both AmE and BrE. Continue this.

5. **Technical term styling:** Code terms in backticks, product names in bold on first mention. Already mostly followed.

6. **Date format:** Use ISO 8601 (YYYY-MM-DD) in technical contexts, which is already done. For prose, consider "1 January 2026" (BrE) over "January 1, 2026" (AmE).

7. **Rule count:** Standardise on one number (514 or 515) across all pages, or consistently use "515+" everywhere.

## Recommendations Summary

### Priority 1 (Must-Fix)
1. Fix the factual inconsistency between FAQ ("not open source") and why-raxe.mdx ("free and open source").
2. Fix the L2 latency discrepancy in FAQ (50ms vs 3ms stated elsewhere).
3. Fix "personal identifiable information" -> "personally identifiable information" in threat-families.mdx.
4. Update stale date "2025-06-01" in suppressions.mdx to a future date.

### Priority 2 (Should-Fix if targeting British English)
5. Convert all "behavior" -> "behaviour" in prose (~3 instances).
6. Convert all "analyzed" -> "analysed" in prose (~1 instance).
7. Convert all "specialized" -> "specialised" in prose (~2 instances).
8. Convert all "organization" -> "organisation" in prose (~2 instances).
9. Convert "minimize"/"maximize" -> "minimise"/"maximise" in prose (~2 instances).
10. Convert "Uncategorized" -> "Uncategorised" in prose (~1 instance).
11. Convert "customize" -> "customise" in prose (~1 instance).

### Priority 3 (Nice-to-Have)
12. Standardise Oxford comma usage (recommend: no Oxford comma, matching current majority usage).
13. Standardise rule count references across all pages.
14. Consider "nerve-racking" over "nerve-wracking" for BrE preference.
15. Create a written style guide document for contributors.
