# Devil's Advocate Review: RAXE CE Documentation

**Reviewer perspective:** A skeptical engineering lead evaluating RAXE CE for production use, reading every page looking for reasons NOT to adopt it.

**Scope:** All 47 documentation pages across 9 sections.

**Overall Grade: B-** (Good bones, but trust gap is real)

---

## Top 5 Critical Findings (Must-Fix)

### 1. "Free Forever, No Usage Limits" Is Misleading (TRUST-BREAKING)

**Pages affected:** `why-raxe.mdx`, `faq.mdx`, `configuration.mdx`

The `why-raxe.mdx` page prominently states **"Free forever. No usage limits."** in a hero card. But buried in the FAQ:

- CE has a **1,000 scans/day limit**
- CE **cannot disable telemetry** (requires Pro+)
- CE is limited to **50 custom rules, 5 tenants, 10 MSSP customers**

This is the single most damaging credibility issue in the entire documentation. A skeptic who finds this contradiction will question everything else. The word "forever" combined with "no limits" reads as a promise that the FAQ directly contradicts.

**Fix:** Replace "No usage limits" with "Generous free tier" or "1,000 scans/day free" and link to a tier comparison. Honesty here builds more trust than marketing language.

---

### 2. Zero Social Proof or Third-Party Validation

**Pages affected:** Every page, especially `why-raxe.mdx`, `introduction.mdx`

Across all 47 pages there are:
- **Zero** customer testimonials or quotes
- **Zero** case studies or deployment stories
- **Zero** third-party benchmarks or audits
- **Zero** logos of companies using RAXE
- **Zero** links to independent reviews or security assessments
- **Zero** academic citations or peer review

The 95%+ detection rate claim (`why-raxe.mdx`) has no methodology, test corpus, or reproducible benchmark. A security product making unverified detection claims is a red flag for any serious buyer.

**Fix:** At minimum, provide a reproducible benchmark methodology. Even a self-published benchmark with a known test corpus (e.g., "tested against BIPIA dataset, HackAPrompt submissions") would be more credible than a bare percentage.

---

### 3. "Not Open Source" Buried in FAQ

**Pages affected:** `faq.mdx`, `why-raxe.mdx`, all marketing language

The FAQ explicitly states RAXE CE is **"not open source"** but rather "community-driven." Yet the entire documentation reads like an open-source project:
- Called "Community Edition"
- GitHub links throughout
- `pip install` distribution
- No mention of proprietary license terms anywhere

A developer who assumes this is OSS (a reasonable assumption given the naming and distribution) will feel deceived when they discover it is not. Enterprise legal teams will flag the licensing ambiguity immediately.

**Fix:** Add a clear "License" section to the introduction or installation page. State the license type explicitly. If it is source-available, say so. If there are usage restrictions, list them upfront.

---

### 4. API Key Requirement Contradicts "100% Local" Narrative

**Pages affected:** `quickstart.mdx`, `configuration.mdx`, `architecture.mdx`, `faq.mdx`

The quickstart requires `raxe init` with an API key. The configuration page mentions "initial API key validation." Yet the core selling point is **"100% on-device"** and **"zero network dependency."**

A skeptic immediately asks:
- If everything is local, why do I need an API key?
- What happens if RAXE's servers go down during init?
- Is the API key validating my identity, licensing, or something else?
- What data is sent during validation?
- Can I use this in air-gapped environments?

The `architecture.mdx` page mentions "Offline Mode" but the exact behavior of the API key requirement in offline scenarios is unclear.

**Fix:** Add a clear section explaining exactly what the API key does, what network calls are made and when, and how to use RAXE in fully air-gapped environments (if possible).

---

### 5. Performance Claims Are Internally Inconsistent

**Pages affected:** `introduction.mdx`, `why-raxe.mdx`, `performance.mdx`, `faq.mdx`, `mcp.mdx`

Latency claims across the documentation:
- Introduction hero: **"<1ms"** for L1
- Why RAXE page: **"~3ms"** per scan
- Performance page: **"~0.4ms"** L1, **"~3.5ms"** L1+L2
- FAQ: **"~50ms"** for L2 (!)
- MCP page: **"<5ms"** for L1, **"<15ms"** for L1+L2

The L2 claim ranges from 3ms to 50ms depending on which page you read. An engineer benchmarking during evaluation will notice these discrepancies and question the reliability of all numbers.

**Fix:** Audit every performance claim across all pages. Use consistent numbers with clear context (e.g., hardware used, text length, cold vs. warm). Ideally, one canonical "Performance" page with all numbers, and other pages linking to it.

---

## Top 5 Quick Wins (Easy Improvements)

### 1. Add a License/Pricing Clarity Section

Add a single page or prominent section that honestly states:
- CE license type (proprietary? source-available? what terms?)
- CE limitations (1K scans/day, 50 rules, telemetry required)
- What Pro/Enterprise costs (or "contact us" with a ballpark range)
- Upgrade path

Currently, Pro/Enterprise are mentioned on 10+ pages but there is zero pricing information. Enterprise buyers cannot even begin procurement without this.

---

### 2. Fix Rule Count Inconsistencies

Multiple pages reference different rule counts:
- `introduction.mdx`: "514 rules"
- `why-raxe.mdx`: "515+ patterns"
- `mcp.mdx`: "514 rules"
- `detection-engine.mdx`: "514+ rules"
- `quickstart.mdx`: "Loaded 514 rules"

Pick one number and use it consistently, or use "500+" as a round figure. Small inconsistencies erode trust disproportionately.

---

### 3. Remove or Update Stale Roadmap Items

The changelog (`changelog.mdx`) shows roadmap items for v0.3 marked "Coming Soon" while the current version is v0.9.1. This makes the project look abandoned or unmaintained.

Either update the roadmap to reflect current plans, or remove it entirely. A stale roadmap is worse than no roadmap.

---

### 4. Add "What Gets Sent" Privacy Transparency

The telemetry section in `configuration.mdx` lists what is NOT sent but is vague about what IS sent. Add a concrete example of a telemetry payload (redacted as needed). For a security product, maximum transparency about data handling is table stakes.

Example: "Here is a sample telemetry event: `{scan_id: 'uuid', duration_ms: 3.2, l1_detections: 1, l2_detections: 0, severity: 'high', rule_ids: ['pi-001'], timestamp: '...'}`"

---

### 5. Clarify "Coming Soon" Items Across the Docs

Several pages list features as "Coming soon":
- `quickstart.mdx` scan points table: response scanning, tool I/O scanning marked "Coming soon"
- `changelog.mdx` roadmap: v0.3 features still "coming"
- Various integrations reference Pro-only features without clarity

Each "Coming soon" is a potential dealbreaker for someone evaluating RAXE for that specific use case. Either give a timeline or remove the mention. Listing unbuilt features alongside real ones blurs the line between product and vaporware.

---

## Detailed Page-by-Page Notes

### Getting Started Section

**introduction.mdx**
- Hero code sample is excellent, best-in-class onboarding UX
- "Zero-day detection" claim in detection table is bold and unsubstantiated. How do you detect zero-days with pattern matching?
- Threat family accordion uses "14 threat families plus benign output" for L2 but lists only specific families for L1. The mapping between L1 families (7) and L2 families (14) is confusing
- Privacy card says "RAXE never transmits, stores, or logs your prompts" but telemetry IS mandatory for CE

**why-raxe.mdx**
- "77% of LLM applications vulnerable" - no source cited. What study? What year?
- "$4.2M average breach cost" - is this LLM-specific or general? Feels like general IBM data repurposed
- Comparison table vs. cloud solutions is self-serving (expected) but lists "Accuracy" as "Varies" for competitors with no specifics
- "Detect sophisticated attacks that cloud solutions miss" - evidence?
- Free vs. Pro comparison table buries the CE limitations in a way that feels like misdirection

**quickstart.mdx**
- Clean 60-second experience, good
- `raxe init` step does not explain what network calls happen or what the API key is for
- Scan points table has 3 of 6 rows marked "Coming soon" - that is 50% of the claimed scan surface NOT built yet

**installation.mdx**
- Python 3.10+ requirement may exclude some enterprise environments still on 3.8/3.9
- No mention of system dependencies (ONNX runtime, etc.)
- No Docker image for quick evaluation
- Windows support not explicitly confirmed

**configuration.mdx**
- Performance modes (fast/balanced/thorough) are well-designed
- Telemetry section buries the "CE requires telemetry" fact in a Tip callout rather than stating it prominently
- `raxe_api_key` is listed as an env var but its purpose and scope are never fully explained

### Integrations Section

**integrations/index.mdx**
- Good decision guide with comparison table
- "Transparent Proxy" for MCP is the standout differentiator
- Missing: which integrations are production-ready vs. experimental?

**integrations/mcp.mdx**
- Strongest integration page. The gateway architecture diagram is clear
- Security audit feature (`raxe mcp audit`) is a genuine value-add
- Performance expectations table at the bottom is useful
- Startup time of ~5s could be a problem for serverless/lambda use cases

**integrations/langchain.mdx**
- Most feature-complete integration page
- Goal hijack detection, tool chain validation, memory scanning - these are genuinely differentiated features
- But: are these features available in CE or only Pro? Not stated
- OWASP ASI alignment table is a good trust signal

**integrations/crewai.mdx**
- Clean API design with ScanMode enum
- Missing: real-world example of a threat being caught in a CrewAI workflow

**integrations/autogen.mdx**
- Good that it covers both v0.2 and v0.4+ APIs
- The two different API surfaces (hooks vs. wrappers) adds cognitive load

**integrations/llamaindex.mdx**
- Dual API (callback vs. instrumentation) mirrors LlamaIndex's own evolution, sensible
- Version requirement (0.10.20+) may exclude some users

**integrations/dspy.mdx**
- Thin page. DSPy integration feels like a checkbox item rather than deep integration
- Only covers basic callback and module guard

**integrations/portkey.mdx**
- 3-second timeout constraint for Portkey webhooks is a real concern. What if L2 takes 50ms on slow hardware? That is fine, but users need to understand the full chain
- Good that it warns about the timeout issue

**integrations/openclaw.mdx**
- **Critical:** The primary integration (message hooks) is NOT IMPLEMENTED. This is confirmed as of Feb 2026
- The MCPorter workaround is presented but this page should be labeled "Limited Support" or "Preview" rather than sitting alongside fully working integrations
- A skeptic sees this and wonders what else might not actually work

**integrations/litellm.mdx**
- Straightforward callback pattern, nothing controversial
- Good breadth (100+ providers through one integration)

**integrations/huggingface.mdx**
- Good that it lists supported task types explicitly
- Missing: what models were tested? Performance impact on local inference?

**integrations/common-patterns.mdx**
- Practical and well-organized (FastAPI, Flask, Django)
- The batch processing pattern is useful for high-volume scenarios
- Missing: any mention of caching scan results for repeated prompts

**integrations/ci-cd.mdx**
- GitHub Actions and GitLab CI examples are copy-paste ready
- Pre-commit hook pattern is clever for catching injection in committed data
- Missing: how does CI/CD scanning interact with the 1K scans/day CE limit?

### SDK Section

**sdk/overview.mdx**
- Good import reference table
- API stability table is a nice touch (scan = Stable, MCP gateway = Beta)
- Some items marked "Alpha" - what does that mean for production use?

**sdk/python.mdx**
- Comprehensive reference with good examples
- Multi-tenant scanning section references Pro features without clear delineation
- Thread safety guarantee is important for production use

**sdk/openai-wrapper.mdx**
- Drop-in replacement approach is the most frictionless integration possible
- "Just change the import" is a compelling pitch
- Missing: overhead per request? Connection pooling?

**sdk/anthropic-wrapper.mdx**
- Same pattern as OpenAI wrapper, consistent
- System prompt scanning is a good differentiator

**sdk/agentic-scanning.mdx**
- 12 scan types is impressive breadth
- AgentScanner API is well-designed
- But: are all 12 scan types available in CE? Several seem enterprise-grade
- OWASP ASI alignment is valuable for compliance conversations

**sdk/async.mdx**
- Good that async is a first-class citizen
- Batch scanning with concurrency control is production-ready
- Missing: what is the actual throughput? How many scans/sec on reference hardware?

### Rules Section

**rules/overview.mdx**
- Rule structure (YAML) is clean and understandable
- Severity/confidence model is standard and sensible
- The 4-tier severity with 5-level confidence feels over-engineered for the CE tier

**rules/custom-rules.mdx**
- 50-rule CE limit is reasonable but should be stated in the first paragraph, not buried
- YAML validation CLI is a nice touch
- Missing: examples of when you would NEED custom rules (i.e., what does the default ruleset miss?)

### Concepts Section

**concepts/detection-engine.mdx**
- Best technical page in the docs. Clear explanation of L1/L2 pipeline
- BinaryFirstEngine voting strategy is well-explained
- Voting presets (balanced, high_security, low_fp) are a good UX pattern
- But: what is the false positive rate for each preset? No data provided

**concepts/threat-families.mdx**
- 7 L1 families + 4 agentic families + 14 L2 families is confusing
- The relationship between L1 families and L2 families is unclear. Are they the same taxonomy? Different?
- Attack technique tables are valuable reference material
- Missing: which attacks are caught by L1 only, L2 only, or both?

**concepts/architecture.mdx**
- Clean architecture diagram builds engineering confidence
- Privacy model section is good but should be more prominent (linked from intro)
- Offline mode mentioned but not fully documented

**concepts/policies.mdx**
- ALLOW/FLAG/BLOCK/LOG action model is clean
- Priority resolution chain is well-explained
- CE limit of 10 policies is reasonable but should be upfront

**concepts/suppressions.mdx**
- Necessary feature for production use (reducing false positives)
- Pattern wildcards are powerful
- Audit trail mention is good for compliance

### Enterprise Section

**integrations/siem.mdx**
- Covers the big 4 (Splunk, CrowdStrike, Sentinel, ArcSight) plus generic CEF
- Event batching and multi-customer routing show production maturity
- But: is SIEM integration available in CE? The page does not say

**concepts/mssp-integration.mdx**
- Clearly targets a specific enterprise buyer persona
- Privacy modes (full, anonymized, metadata-only) are well-thought-out
- CE limit of 10 customers is very restrictive for any real MSSP
- Missing: actual webhook payload examples that would help integration

**concepts/multi-tenant.mdx**
- Tenant/App/Request hierarchy is clean
- Policy resolution chain is well-documented
- CE limit of 5 tenants is severely limiting - even a small SaaS has more than 5 customers
- Missing: performance impact of multi-tenant policy resolution

### Resources Section

**resources/troubleshooting.mdx**
- Quick diagnosis table is excellent UX
- Solutions are practical and actionable
- Missing: "RAXE is blocking legitimate requests" troubleshooting path (false positive handling)

**resources/faq.mdx**
- Contains the most honest answers in the entire documentation
- "Not open source" admission is here
- 1K scans/day limit is here
- Telemetry requirement is here
- This page should be required reading but it is buried in Resources
- The honesty here actually builds trust - the problem is that other pages contradict it

**resources/error-codes.mdx**
- Comprehensive error code reference
- AUTH, SCAN, RULE, CONFIG, DB, NET, ML categories are well-organized
- Good that it includes remediation steps

**resources/performance.mdx**
- Most detailed performance data in the docs
- Hardware recommendations are useful
- But: "Your results may vary" disclaimer undermines the specific numbers on other pages
- Missing: comparison with competitor performance (even approximate)

**resources/changelog.mdx**
- Good release cadence visible (v0.0.1 through v0.9.1)
- Stale roadmap items (v0.3 "coming soon") damage credibility
- No dates on entries - when were these releases?

### Reference Section

**api-reference/overview.mdx**
- Clean import reference
- API stability table is useful
- Alpha/Beta labels should link to a stability policy

**cli/overview.mdx**
- Comprehensive command reference
- Exit codes for CI/CD integration are a thoughtful touch
- Common workflow section is practical

---

## Skeptic's Unanswered Questions

1. **Who is actually using this in production?** Not a single company or use case is named.

2. **What is the false positive rate?** Detection rate is claimed but FP rate is never mentioned. For a security tool, FP rate matters more than detection rate for adoption.

3. **How does this compare to Lakera Guard, Prompt Armor, Rebuff, or LLM Guard?** The comparison table only compares against generic "cloud solutions" without naming competitors.

4. **What happens when I hit the 1K scans/day limit?** Does it fail open? Fail closed? Return an error? This is critical operational information that is completely missing.

5. **Why is telemetry mandatory for CE?** What is RAXE getting from my telemetry data? If the tool is local-first, why can't it run without phoning home?

6. **What is the actual license?** MIT? Apache? Proprietary? BSL? No license is specified anywhere.

7. **Is there a company behind this?** No "About" page, no team page, no company information. Who do I escalate to if I find a security vulnerability in the security tool?

8. **What is the model architecture for L2?** "ONNX neural classifier" is vague. How big is the model? What was it trained on? What are its known weaknesses?

9. **How do I evaluate if RAXE is actually catching attacks?** No test corpus, no benchmark suite, no red-team report is provided. How do I verify the tool works before trusting it with production traffic?

10. **What is the upgrade/migration path from CE to Pro?** If I build on CE and hit limits, is it a config change or a painful migration?

---

## Enterprise Buyer Objections Not Addressed

| Objection | Where It Should Be Answered |
|-----------|----------------------------|
| "What is your SOC 2 / ISO 27001 status?" | Security/compliance page (missing) |
| "Can we get an SLA?" | Pricing/enterprise page (missing) |
| "Who do we call at 3 AM?" | Support page (missing) |
| "What is your vulnerability disclosure process?" | Security page (missing) |
| "Can we run this without any network connectivity?" | Architecture page (partially) |
| "What happens if your company shuts down?" | FAQ (not addressed) |
| "Can we audit the detection rules?" | Rules overview (partially) |
| "Is there a BAA for healthcare?" | Compliance (missing) |
| "What is the total cost of ownership?" | Pricing page (missing) |

---

## What Would Make Me Choose a Competitor Instead

1. **Proven track record:** A competitor with named customers and case studies wins on trust alone.
2. **Open source license:** If LLM Guard or Rebuff offers similar features under MIT/Apache, the licensing clarity wins.
3. **Published benchmarks:** Any competitor with reproducible, third-party validated benchmarks wins on technical credibility.
4. **Transparent pricing:** If I cannot even get a ballpark price for Pro/Enterprise, I cannot start procurement. A competitor with clear pricing removes friction.
5. **Better documentation honesty:** Ironically, a competitor that says "we catch 80% of attacks, here is how we measured it" would be more trustworthy than RAXE's uncited "95%+."

---

## Summary Score Card

| Dimension | Score | Notes |
|-----------|-------|-------|
| Technical depth | A- | Excellent architecture docs, clean APIs |
| Code examples | A | Copy-paste ready, multi-framework |
| Honesty/transparency | C | Contradictions between marketing and FAQ |
| Social proof | F | Zero external validation |
| Enterprise readiness | C+ | SIEM/MSSP docs exist but key pages missing |
| Competitive positioning | C | No named competitors, self-serving comparisons |
| Pricing clarity | F | No pricing anywhere |
| Consistency | C- | Numbers, rule counts, latency claims vary |
| Production guidance | B+ | Migration guide and production checklist are strong |
| Completeness | B | 50% of scan points "coming soon," OpenClaw broken |

**Overall: B-**

The technical documentation is genuinely strong. The API design is clean, the integration breadth is impressive, and the architecture is well-thought-out. But the trust gap between "what RAXE claims" and "what RAXE proves" is the single biggest barrier to adoption. A skeptical evaluator will find the contradictions within 30 minutes of reading and walk away. The fix is not more features or more pages - it is more honesty, more evidence, and more transparency about limitations.
