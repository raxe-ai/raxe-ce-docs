# Technical Architecture Review: RAXE CE Documentation

**Reviewer:** tech-reviewer (Technical Architecture Specialist)
**Date:** 2026-02-06
**Scope:** All 47 MDX documentation pages
**Repository:** raxe-ce-docs (main branch, commit 1ef56a8)

---

## Overall Grade: C+

The documentation covers an impressive breadth of features -- MCP gateway, 10+ framework integrations, multi-tenant policies, MSSP workflows, SIEM integration, and a full CLI. However, the technical accuracy is severely undermined by **pervasive cross-page inconsistencies** in API surfaces, performance numbers, import paths, exception class names, and property names. A developer following one page's examples will hit runtime errors when combining with patterns from another page. The documentation reads as though multiple authors wrote sections independently without a shared API contract or style guide.

---

## Executive Summary

The RAXE CE documentation provides broad coverage of a sophisticated on-device AI security scanner. The architecture (dual-layer L1 regex + L2 ONNX ML detection, clean/hexagonal design) is well-explained. However, the documentation suffers from **three systemic problems**:

1. **API surface inconsistency**: Import paths, exception class names, result object properties, and method signatures vary across pages with no single authoritative source.
2. **Contradictory metrics**: Rule counts, latency numbers, scan limits, and licensing terms contradict each other across pages.
3. **Stale content**: Changelog roadmap, REPL version numbers, and example dates are outdated relative to the current v0.9.1 release.

These issues would cause developer frustration during integration, erode trust in the documentation, and increase support burden.

---

## Top 5 Critical Findings

### 1. Exception Class Name Chaos (CRITICAL)
**Impact:** Runtime NameError for developers copying examples across integration pages.

Three different exception class names are used for the same concept (blocking on threat detection):

| Exception Name | Used In |
|----------------|---------|
| `RaxeBlockedError` | sdk/python.mdx:116, sdk/overview.mdx, api-reference/exceptions.mdx, openai-wrapper.mdx:77, crewai.mdx:168, langchain.mdx:229 |
| `SecurityException` | integrations/autogen.mdx:117, integrations/llamaindex.mdx (multiple), integrations/portkey.mdx, integrations/common-patterns.mdx |
| `ThreatDetectedError` | integrations/dspy.mdx:109, integrations/litellm.mdx |

Additionally, `RaxeError` (sdk/python.mdx:124) vs `RaxeException` (api-reference/exceptions.mdx:9, common-patterns.mdx) appear to be the same base class with different names.

**The canonical hierarchy** (from api-reference/exceptions.mdx) is:
```
RaxeException (base)
  -> RaxeBlockedError
  -> ValidationError
  -> AuthenticationError
  -> ConfigurationError
  -> RuleError
  -> DatabaseError
  -> NetworkError
```

But integration pages reference `SecurityException` and `ThreatDetectedError` which do not appear in this hierarchy. Either these are framework-specific wrappers that should be documented in the exceptions page, or they are errors in the integration docs.

**Recommendation:** Standardize on the canonical exception hierarchy across ALL pages. If framework-specific exceptions exist (e.g., `SecurityException` is a LlamaIndex-specific wrapper), document the mapping explicitly.

---

### 2. Result Object Property Inconsistency (CRITICAL)
**Impact:** AttributeError at runtime when developers mix patterns from different pages.

The scan result object has conflicting property names across pages:

| Property Variant | Used In | Canonical (sdk/python.mdx, scan-results.mdx) |
|-----------------|---------|----------------------------------------------|
| `result.is_safe` | api-reference/overview.mdx, api-reference/exceptions.mdx:96,311,359, resources/faq.mdx | NOT in canonical SDK reference |
| `result.has_threats` | sdk/python.mdx:44, scan-results.mdx:21 | YES - canonical |
| `result.severity` | sdk/python.mdx:46, scan-results.mdx:22 | YES - canonical |
| `result.highest_severity` | api-reference/overview.mdx, api-reference/exceptions.mdx:81,103 | NOT in canonical SDK reference |
| `e.scan_result` | api-reference/exceptions.mdx:65 (RaxeBlockedError property) | Conflicts with `e.result` in sdk/python.mdx:181 |
| `e.scan_result.detection_count` | api-reference/exceptions.mdx:82 | Should be `total_detections` per scan-results.mdx:23 |
| `e.severity` / `e.rule_id` | sdk/python.mdx:117-118 (on RaxeBlockedError) | Not shown in exceptions.mdx class definition |

**Recommendation:** Establish a single source of truth for the `ScanPipelineResult` and `RaxeBlockedError` API surface. Update all 47 pages to use identical property names.

---

### 3. Import Path Fragmentation (HIGH)
**Impact:** ImportError for developers; unclear which import is correct.

The `RaxeOpenAI` class has three different import paths across pages:

| Import Path | Used In |
|-------------|---------|
| `from raxe import RaxeOpenAI` | sdk/openai-wrapper.mdx:15, sdk/overview.mdx |
| `from raxe.wrappers import RaxeOpenAI` | integrations/index.mdx |
| `from raxe.sdk.wrappers import RaxeOpenAI` | api-reference/overview.mdx |

Similarly, `Detection` import varies:
- `from raxe import Detection` (scan-results.mdx:111)
- `from raxe.domain.rules.models import Severity` (sdk/python.mdx:193, scan-results.mdx:173)

The `Raxe` import also varies:
- `from raxe import Raxe` (most pages)
- `from raxe import Raxe, RaxeBlockedError, RaxeError` (sdk/python.mdx)

**Recommendation:** Document the canonical import for each class in ONE place (api-reference), and use re-exports from `raxe.__init__` so that `from raxe import X` works for all public classes. Update all pages to use the top-level import.

---

### 4. Contradictory Performance Metrics (HIGH)
**Impact:** Developers cannot trust performance claims; makes capacity planning impossible.

Latency numbers vary wildly across pages:

| Metric | introduction.mdx | why-raxe.mdx | detection-engine.mdx | configuration.mdx | resources/faq.mdx | resources/performance.mdx |
|--------|-----------------|--------------|---------------------|-------------------|-------------------|--------------------------|
| L1 only | ~1ms | ~3ms | ~0.4ms | ~0.3ms (fast mode) | - | P50=0.37ms, P95=0.49ms |
| L2 only | - | ~7ms | ~3ms | - | ~50ms | - |
| L1+L2 | ~10ms | ~10ms | - | ~2ms (balanced) | - | - |
| Full scan | ~10ms | ~10ms | - | ~5ms (thorough) | - | - |

The FAQ's claim of "~50ms" for L2 is 16x higher than detection-engine.mdx's "~3ms". The introduction's "~1ms" for L1 is 2.5x higher than the performance page's benchmarked "0.37ms P50".

**Recommendation:** Run actual benchmarks, establish authoritative numbers, and reference them consistently. Use the performance.mdx page as the single source of truth with specific hardware/environment details.

---

### 5. Contradictory Licensing and Limits (HIGH)
**Impact:** Users cannot determine what they're getting with CE; trust erosion.

| Claim | Source |
|-------|--------|
| "Free forever, no usage limits" | why-raxe.mdx |
| "1,000 scans/day" limit for CE | resources/faq.mdx |
| "Not open source" | resources/faq.mdx |
| Implied "free and open source" | why-raxe.mdx ("100% on-device", "no API keys required for CE") |

These directly contradict each other. A user reading why-raxe.mdx expects unlimited free scanning; the FAQ then tells them there's a 1,000/day cap.

**Recommendation:** Define the CE tier limits clearly in ONE place and reference it everywhere. Remove contradictory claims.

---

## Top 5 Quick Wins

### 1. Standardize Rule Count
Currently oscillates between "514", "515", and "515+" across pages. Pick one number (or use "500+" as an approximation) and use it everywhere.

**Files to update:** introduction.mdx:15, why-raxe.mdx, integrations/index.mdx, detection-engine.mdx, cli/commands.mdx:150

### 2. Fix Broken Internal Links
Two confirmed broken links:
- `integrations/openclaw.mdx` links to `/rules/custom` -- should be `/rules/custom-rules`
- `integrations/huggingface.mdx` links to `/wrappers/openai` -- should be `/sdk/openai-wrapper`

### 3. Update Stale Dates and Versions
- `cli/overview.mdx:129` shows REPL version `v0.0.1` -- should be current version
- `cli/commands.mdx:494` uses suppression expiry `2025-06-01` -- already past
- `resources/changelog.mdx` has "Coming Soon" roadmap for v0.3 features that shipped months ago (current is v0.9.1)

### 4. Fix Category Code Inconsistency
Detection category values differ:
- `detection.category == "PI"` (sdk/python.mdx:198) -- short code
- `detection.category == "prompt_injection"` (scan-results.mdx:158,243) -- long form

Pick one format and use it everywhere. The detection-engine.mdx and threat-families.mdx pages use short codes (PI, JB, PII, etc.).

### 5. Remove MCP Troubleshooting Reference to Non-Existent Package
`resources/troubleshooting.mdx` references `@anthropic-ai/raxe-mcp` npm package in the MCP Gateway troubleshooting section. This package name doesn't match any usage in the MCP documentation (which uses `raxe mcp gateway` CLI command). Either this is a leftover from a different architecture or needs to be updated.

---

## Detailed Page-by-Page Notes

### Getting Started

#### introduction.mdx
- **Line 15:** Claims "514+ rules" but other pages say 515+
- **Line ~20:** Claims "11 threat families" -- this is correct for L1 (7 core + 4 agentic), but L2 has 14 different families. Should clarify "11 L1 families"
- **Line ~25:** Claims "~1ms" L1 latency, but performance.mdx benchmarks show 0.37ms P50. Not wrong but inconsistent precision
- Code examples are clean and correct for basic usage
- Good use of the overview architecture diagram

#### why-raxe.mdx
- Claims "515+ rules" (differs from introduction's "514+")
- Claims "~3ms pattern matching" for L1 which is 8x higher than benchmarked 0.37ms
- Claims "free forever, no usage limits" -- contradicts FAQ's 1,000/day limit
- Claims "14 L2 threat families" and "35 attack techniques" -- consistent with detection-engine.mdx
- Otherwise well-written value proposition page

#### quickstart.mdx
- Good progressive disclosure from CLI to SDK to integrations
- Import `from raxe.sdk.integrations.langchain import create_callback_handler` is correct
- Shows both `raxe scan` CLI and Python SDK usage
- Missing: No mention of `pip install raxe[ml]` for L2 support in quickstart (only `pip install raxe`)

#### installation.mdx
- Good coverage of extras: `raxe[ml]`, `raxe[wrappers]`, `raxe[repl]`, `raxe[all]`
- Framework-specific extras well-documented: `raxe[langchain]`, `raxe[crewai]`, etc.
- Python 3.10+ requirement clearly stated
- Missing: No mention of system dependencies (e.g., does ONNX runtime need specific system libs?)

#### configuration.mdx
- Performance modes well-explained: fast (~0.3ms), balanced (~2ms), thorough (~5ms)
- YAML config location `~/.raxe/config.yaml` is clear
- Notes "Telemetry requires Pro+ to disable" -- important CE limitation
- Latency numbers for modes don't align with other pages

### Integrations

#### integrations/index.mdx
- Claims "515+ rules" (ok, matches why-raxe)
- Uses `from raxe.wrappers import RaxeOpenAI` (unique import path, differs from other pages)
- Good hub page linking to all integrations
- Missing: No mention of which integrations are CE vs Pro/Enterprise

#### integrations/mcp.mdx
- Comprehensive coverage of both Gateway and Server modes
- Architecture diagram (ASCII art) is clear and accurate
- Claude Desktop and Cursor setup configs are well-structured
- Gateway YAML config example is production-quality
- Python API section (`from raxe.mcp import create_gateway`) is clean
- CLI reference (`raxe mcp gateway`, `serve`, `audit`, `generate-config`) matches cli/commands.mdx
- Performance expectations table (startup ~5s, L1 <5ms, L1+L2 <15ms, overhead <2ms) is useful but L1 number differs from other pages

#### integrations/langchain.mdx
- Agentic security methods are well-documented: `validate_agent_goal_change()`, `validate_tool_chain()`, `scan_agent_handoff()`, `scan_memory_before_save()`
- `ToolPolicy.block_tools()` and `ToolPolicy.allow_tools()` are clear
- Supported versions table (0.1.x through 0.3.x) is good
- OWASP ASI alignment table is valuable
- Error handling uses `RaxeBlockedError` (consistent with canonical)
- Code examples are well-structured and production-ready

#### integrations/crewai.mdx
- `RaxeCrewGuard` with `ScanMode` enum is well-designed
- Both `protect_crew()` wrapper and manual callbacks documented
- `CrewGuardConfig` options are comprehensive
- Tool wrapping (`wrap_tool`, `wrap_tools`) clearly explained
- Error handling uses `RaxeBlockedError` (consistent)
- Missing: No OWASP alignment table (LangChain has one)

#### integrations/autogen.mdx
- `RaxeConversationGuard` with hook-based registration is clear
- v0.4+ migration with `wrap_agent()` is helpful
- **ERROR:** Uses `SecurityException` (line ~117) instead of `RaxeBlockedError`
- Good coverage of both v0.2 and v0.4 API differences

#### integrations/llamaindex.mdx
- Three callback types well-differentiated
- Instrumentation API via `RaxeSpanHandler` for v0.10.20+ is forward-looking
- **ERROR:** Uses `SecurityException` in error handling examples
- Good version compatibility table

#### integrations/dspy.mdx
- `RaxeDSPyCallback` and `RaxeModuleGuard` are clean abstractions
- **ERROR:** Uses `ThreatDetectedError` instead of `RaxeBlockedError`
- DSPy 2.4.x+ version requirement noted

#### integrations/portkey.mdx
- Webhook guardrail pattern for Portkey is well-documented
- 3-second timeout consideration is a good production note
- **ERROR:** Error handling uses `SecurityException`
- Good dual-mode documentation (webhook vs client wrapper)

#### integrations/openclaw.mdx
- Honest about limitations: "message event hooks NOT implemented in OpenClaw (Feb 2026)"
- Recommends MCPorter integration as workaround
- **ERROR:** Links to `/rules/custom` (should be `/rules/custom-rules`)

#### integrations/litellm.mdx
- `RaxeLiteLLMCallback` and `create_litellm_handler()` factory are well-documented
- **ERROR:** Uses `ThreatDetectedError` instead of `RaxeBlockedError`
- Good version compatibility (1.0.x and 1.40.x+)

#### sdk/openai-wrapper.mdx
- Drop-in replacement pattern clearly explained
- Config params (`raxe_l1_enabled`, `raxe_l2_enabled`, `raxe_block_on_threat`) are clean
- Streaming support documented
- Multi-turn message scanning explained
- Migration guide (one-line change) is excellent
- `AsyncRaxeOpenAI` documented
- Error handling uses `RaxeBlockedError` (consistent)

#### sdk/anthropic-wrapper.mdx (not separately read but referenced in summary)
- Parallel structure to OpenAI wrapper
- Same config pattern

#### integrations/huggingface.mdx
- `RaxePipeline` wrapper documented
- **ERROR:** Links to `/wrappers/openai` (should be `/sdk/openai-wrapper`)

#### integrations/common-patterns.mdx
- FastAPI, Flask, Django, DRF patterns are all production-quality
- Async queue processing with `AsyncRaxe` is valuable
- Batch processing with `scan_batch()` documented
- **ERROR:** Uses `SecurityException` and `RaxeException` (should be `RaxeBlockedError` and `RaxeException` respectively -- `RaxeException` is actually correct per exceptions.mdx, but `SecurityException` is not)

#### integrations/ci-cd.mdx
- GitHub Actions and GitLab CI examples are production-ready
- Pre-commit hook configuration is well-structured
- `--ci` flag for JSON output documented
- Exit codes (0/1/2) consistent with cli/overview.mdx
- Docker example included
- Missing: No Bitbucket Pipelines or Jenkins examples

#### integrations/siem.mdx
- Comprehensive SIEM coverage: Splunk HEC, CrowdStrike, Sentinel, ArcSight, CEF
- Multi-customer routing via `SIEMDispatcher` is advanced
- CEF field mapping and severity mapping tables are thorough
- Event batching configuration included
- This is one of the best-written pages in the docs

### How It Works

#### concepts/detection-engine.mdx
- L1/L2 architecture clearly explained
- L2 5-head ONNX classifier well-documented: binary, threat family (14), severity (3), technique (35), harm types (10)
- BinaryFirstEngine voting logic with decision zones explained
- Voting presets (balanced, high_recall, low_fp) with TPR/FPR numbers
- 512 token limit for L2 noted
- Latency claims: ~0.4ms L1, ~3ms L2 (most credible numbers, closest to benchmarks)

#### concepts/threat-families.mdx
- L1 families with rule counts: PI:59, JB:77, PII:112, CMD:65, ENC:70, HC:65, RAG:12 = 460 total
- Agentic families: AGENT:15, TOOL:15, MEM:12, MULTI:12 = 54 total
- Total: 460 + 54 = 514 rules (matches introduction.mdx but not why-raxe's 515+)
- L2 14 families + benign well-documented
- 35 attack techniques listed
- 10 harm types (multilabel) documented
- This is the most detailed threat taxonomy page

#### rules/overview.mdx
- Rule YAML structure well-documented
- CLI commands `raxe rules list`, `raxe rules show` match cli/commands.mdx
- Custom rule structure explained
- Examples with `true_positives` and `false_positives` test cases

### Guides

#### concepts/architecture.mdx
- Clean/Hexagonal architecture diagram is excellent
- 4 layers clearly delineated: CLI/SDK, Application, Domain (pure), Infrastructure
- Mermaid scan pipeline diagram is helpful
- Telemetry payload structure documented (privacy-preserving)
- Good explanation of why domain layer has no I/O

#### guides/migration.mdx
- 3-phase approach (Shadow, Wrapper, Blocking) is production-proven
- Rollback patterns: env var toggle, feature flag, percentage rollout
- Verification script included
- Well-structured for risk-averse enterprise adoption

#### guides/production-checklist.mdx
- Week-by-week gradual rollout plan
- Shadow mode metrics collection
- Fail-open vs fail-closed patterns
- Circuit breaker implementation
- Policy YAML examples for each phase
- This is an excellent operations guide

### Advanced / Enterprise

#### sdk/overview.mdx
- Import reference `from raxe import Raxe, AsyncRaxe, RaxeOpenAI, RaxeBlockedError`
- Decorator `@raxe.protect` and context manager documented
- Clean overview linking to detailed pages

#### sdk/python.mdx
- `ScanPipelineResult` properties fully documented (canonical reference)
- `Detection` and `Match` objects well-specified
- Multi-tenant scanning with `tenant_id`, `app_id` documented
- Thread safety explicitly stated
- Filtering examples by severity, confidence, category
- This should be THE authoritative SDK reference

#### sdk/agentic-scanning.mdx
- `AgentScanner` with 6 methods well-documented
- 12 scan types listed
- OWASP ASI alignment is valuable
- Good separation from basic scanning docs

#### sdk/async.mdx
- `AsyncRaxe` with caching (`cache_size`, `cache_ttl`) documented
- `scan_batch(max_concurrency)` for high-throughput
- FastAPI and aiohttp integration examples
- Missing: No mention of connection pooling or resource cleanup

#### rules/custom-rules.mdx
- Custom rule YAML format well-documented
- CLI validation `raxe validate-rule` included
- Tier limits: CE=50, Pro=500, Enterprise=Unlimited
- Missing: No guidance on regex performance / catastrophic backtracking avoidance

#### concepts/policies.mdx
- ALLOW/FLAG/BLOCK/LOG actions well-explained
- YAML config in `~/.raxe/policies.yaml`
- Priority-based resolution (0-1000) documented
- Targeting by severity, family, rule_id, confidence

#### concepts/suppressions.mdx
- YAML in `.raxe/suppressions.yaml`
- Wildcard patterns with family prefix
- Actions: SUPPRESS/FLAG/LOG
- SDK: `scan(suppress=[...])`, `with raxe.suppressed(...)`
- CLI: `raxe suppress list/add/remove/audit`
- Expiration support documented

#### concepts/mssp-integration.mdx
- MSSP -> Customer -> Agent hierarchy clear
- Webhook with HMAC-SHA256 signing well-documented
- Privacy modes: full vs privacy_safe
- Agent heartbeat monitoring
- Tiers: Starter(10), Professional(50), Enterprise(unlimited)
- One of the more complex pages, well-organized

#### concepts/multi-tenant.mdx
- Tenant -> App -> Request policy resolution chain
- 3 presets: Monitor, Balanced, Strict
- CLI: `raxe tenant create`, `raxe app create`, `raxe policy set`
- Tier limits: CE=5 tenants, 10 apps/tenant, 3 custom policies/tenant
- Missing: No diagram of resolution chain

### Reference

#### resources/troubleshooting.mdx
- Quick diagnosis table is useful
- Covers: No Detections, Performance, False Positives, API Key, Installation, Dependencies, SIEM, MCP, CLI, Database
- **ERROR:** MCP Gateway section references `@anthropic-ai/raxe-mcp` npm package (does not match actual CLI usage `raxe mcp gateway`)
- Some solutions reference `raxe doctor` which is helpful

#### resources/faq.mdx
- **ERROR:** Claims CE is "not open source" (contradicts why-raxe.mdx "free and open source" implications)
- **ERROR:** Claims "1,000 scans/day" limit (contradicts why-raxe.mdx "no usage limits")
- **ERROR:** Claims L2 is "~50ms" (contradicts detection-engine.mdx "~3ms")
- **ERROR:** Uses `result.is_safe` property (not in canonical SDK reference)
- Otherwise good FAQ structure covering common questions

#### resources/error-codes.mdx
- Structured error codes: AUTH (001-005), SCAN (001-005), RULE (001-006), CONFIG (001-005), DB (001-004), NET (001-004), ML (001-004)
- CLI exit codes (0/1/2) match cli/overview.mdx
- Good troubleshooting cross-references

#### resources/performance.mdx
- Most credible performance numbers: P50=0.37ms, P95=0.49ms (L1 only)
- Memory: ~60MB peak
- Performance modes: fast/balanced/thorough
- Hardware recommendations included
- `scan_fast()`, `scan_thorough()` convenience methods documented
- `--profile` CLI flag for profiling
- **NOTE:** `scan_fast()` documented as "< 3ms" in api-reference/raxe-client.mdx but should be sub-millisecond since it's L1-only

#### resources/changelog.mdx
- Version history from v0.0.1 (Dec 2025) through v0.9.1 (Feb 2026)
- **ERROR:** Roadmap section has "Coming Soon" for v0.3 features that already shipped (current is v0.9.1)
- Good release cadence documentation

### API Reference

#### api-reference/overview.mdx
- Import reference shows `from raxe.sdk.wrappers import RaxeOpenAI` (third unique import path)
- **ERROR:** Uses `result.is_safe` and `result.highest_severity` properties (not in canonical scan-results.mdx)
- API stability table (stable/beta/alpha) is useful
- Good linking to detailed API pages

#### api-reference/raxe-client.mdx
- Full `Raxe` constructor and `scan()` method signatures
- `scan()` has 16+ parameters documented
- `AsyncRaxe` with `cache_size` and `cache_ttl`
- `scan_batch()` with `max_concurrency`
- Thread safety documented
- **NOTE:** `scan_fast()` says "< 3ms" but should be sub-ms for L1-only
- **NOTE:** `scan_thorough()` uses `mode="accurate"` but performance.mdx uses `mode="thorough"` -- naming inconsistency

#### api-reference/scan-results.mdx
- Most complete `ScanPipelineResult` reference
- Includes `policy_decision`, `action_taken`, `metadata` fields not in sdk/python.mdx
- Detection object has additional fields: `message`, `explanation`, `risk_explanation`, `remediation_advice`, `detection_layer`, `layer_latency_ms`, `is_flagged`, `suppression_reason`
- Computed properties: `match_count`, `threat_summary`, `versioned_rule_id`
- **ERROR:** `detection.category` shown as `"prompt_injection"` (line 158) but other pages use `"PI"` short code
- `from raxe import Detection` import differs from `from raxe.domain.rules.models` used elsewhere
- Severity enum with comparison operators is well-documented

#### api-reference/exceptions.mdx
- Full exception hierarchy documented
- `RaxeException` base with `code` and `message` attributes
- `RaxeBlockedError` with `scan_result` property
- **ERROR:** Uses `result.is_safe` (lines 96, 311, 359) instead of `not result.has_threats`
- **ERROR:** Uses `e.scan_result.highest_severity` instead of `e.scan_result.severity`
- **ERROR:** Uses `e.scan_result.detection_count` instead of `e.scan_result.total_detections`
- Error codes per exception type are valuable
- Web application and async handler examples are production-quality

### CLI Reference

#### cli/overview.mdx
- Quick reference covering all commands
- Global options (`--log-level`, `--format`, `--ci`)
- Environment variables (`RAXE_API_KEY`, `RAXE_LOG_LEVEL`, `RAXE_CI`)
- Exit codes (0/1/2) documented
- **ERROR:** REPL shows version `v0.0.1` (line 129) -- should reflect current version

#### cli/commands.mdx
- Comprehensive command reference covering: scan, batch, repl, rules, doctor, stats, config, init, mcp (gateway/serve/audit/generate-config), export, tenant, app, policy, suppress, validate-rule, mssp, customer, agent
- Multi-tenant CLI well-documented (tenant, app, policy subcommands)
- MSSP CLI well-documented (mssp, customer, agent subcommands)
- SIEM subcommands under customer are thorough
- **ERROR:** Suppression example uses `--expires 2025-06-01` which is in the past
- `raxe doctor` output shows "514 rules" -- matches introduction but not why-raxe's 515+

---

## Cross-Cutting Consistency Issues

### Issue Matrix

| Issue | Severity | Pages Affected | Fix Effort |
|-------|----------|----------------|------------|
| Exception class names (3 variants) | CRITICAL | 12+ pages | Medium - search/replace + verify |
| Result property names (is_safe vs has_threats) | CRITICAL | 8+ pages | Medium - search/replace |
| Import paths (3 variants for RaxeOpenAI) | HIGH | 5+ pages | Low - pick one, update |
| Rule count (514 vs 515 vs 515+) | MEDIUM | 6+ pages | Low - pick one number |
| Latency numbers (5+ variants) | HIGH | 7+ pages | Medium - benchmark and standardize |
| Category codes (PI vs prompt_injection) | MEDIUM | 3+ pages | Low - pick one format |
| Licensing/limits contradiction | HIGH | 2 pages | Low - clarify CE terms |
| Stale dates/versions | LOW | 3 pages | Low - update values |
| Broken internal links | MEDIUM | 2 pages | Low - fix hrefs |
| Mode naming (accurate vs thorough) | MEDIUM | 2 pages | Low - pick one |

### Threat Family Count Reconciliation

The documentation uses different counts in different contexts:
- **11 families** (introduction.mdx) -- correct for L1: 7 core + 4 agentic
- **7+4 families** (detection-engine.mdx) -- correct, same as above broken out
- **14 families** (why-raxe.mdx, detection-engine.mdx L2 section) -- correct for L2 ML classifier
- **8 families** (MCP server mode, mcp.mdx:248-258) -- only lists PI, JB, DE, RP, CS, SE, TA, CI

The MCP page lists a DIFFERENT set of families than either L1 or L2. For example, it lists "DE" (Data Exfiltration), "RP" (Role Play), "CS" (Context Switching), "SE" (Social Engineering), "TA" (Token Abuse), "CI" (Code Injection) -- some of these don't appear in the canonical L1 families list (which uses PII, CMD, ENC, HC, RAG).

**Recommendation:** Create a master threat family reference table and link to it from all pages. Clearly distinguish L1 families, L2 families, and MCP tool-exposed families.

---

## Missing Technical Depth

### Areas Needing More Documentation

1. **L2 Model Details**: No documentation on model size, quantization, supported hardware accelerators (GPU/NPU), or how the 512-token limit is handled for longer inputs (truncation? sliding window? chunking?)

2. **Regex Engine Internals**: No documentation on which regex engine is used (Python `re`? `regex`? Rust-based?), how catastrophic backtracking is prevented in custom rules, or rule compilation/caching strategy.

3. **Database Schema**: `raxe.db` (SQLite) is mentioned but schema, migration strategy, and data lifecycle are undocumented.

4. **Telemetry Payload**: architecture.mdx shows the payload structure, but there's no documentation on data retention, deletion requests, or GDPR compliance.

5. **Cache Invalidation**: `AsyncRaxe` has `cache_size` and `cache_ttl` but no documentation on cache key generation, invalidation strategies, or memory impact.

6. **Error Recovery**: No documentation on what happens during partial failures (e.g., L2 model fails but L1 succeeds -- does it return partial results or error?).

7. **Concurrency Model**: Thread safety is stated but not explained. Is it lock-based? Are there per-thread caches? What's the overhead per thread?

8. **Rule Update Mechanism**: How are rules updated? Is there a `raxe update` command? Auto-update? Versioned rule packs?

---

## Recommendations Summary

### Priority 1: Fix Now (Blocks Developer Adoption)
1. **Standardize exception class names** across all 47 pages to match api-reference/exceptions.mdx hierarchy
2. **Standardize result property names** (`has_threats` not `is_safe`, `severity` not `highest_severity`, `total_detections` not `detection_count`)
3. **Standardize import paths** to use top-level `from raxe import X` everywhere
4. **Fix broken links** in openclaw.mdx and huggingface.mdx
5. **Resolve licensing/limits contradiction** between why-raxe.mdx and faq.mdx

### Priority 2: Fix Soon (Erodes Trust)
6. **Standardize performance numbers** using benchmarked values from performance.mdx
7. **Standardize rule count** to a single number
8. **Fix stale content**: changelog roadmap, REPL version, suppression expiry date
9. **Standardize category codes** (PI vs prompt_injection)
10. **Fix MCP troubleshooting** reference to non-existent npm package

### Priority 3: Enhance (Improves Completeness)
11. **Add L2 model technical details** (size, quantization, token handling)
12. **Add error recovery documentation** (partial failure behavior)
13. **Add rule update mechanism** documentation
14. **Create master threat family reference** linked from all pages
15. **Add OWASP alignment tables** to all integration pages (not just LangChain)
16. **Add Bitbucket/Jenkins CI/CD examples** to ci-cd.mdx
17. **Document `RaxeBlockedError` property access** consistently (is it `.result` or `.scan_result`?)

---

## Positive Highlights

Despite the consistency issues, several pages deserve recognition for technical quality:

1. **integrations/siem.mdx** -- Comprehensive multi-SIEM coverage with proper field mapping, batching, and multi-customer routing
2. **concepts/detection-engine.mdx** -- Excellent explanation of dual-layer architecture with voting engine details
3. **guides/production-checklist.mdx** -- Production-grade rollout plan with circuit breakers and gradual enforcement
4. **concepts/architecture.mdx** -- Clean architecture explanation with proper layer separation
5. **sdk/agentic-scanning.mdx** -- Forward-looking agentic security scanning with OWASP ASI alignment
6. **integrations/langchain.mdx** -- Most complete integration page with goal hijack, tool chain, handoff, and memory scanning
7. **cli/commands.mdx** -- Comprehensive CLI reference covering all command groups including MSSP and multi-tenant

---

*Review completed: 2026-02-06*
*Reviewer: tech-reviewer (Technical Architecture Specialist)*
*Pages reviewed: 47/47*
