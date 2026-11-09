## 2026-11-08 - Architecture & Security Invariants
**Learning:** The Token-Optimized AI Chromium Browser Controller operates by running in-page JavaScript distillation (`JS_DISTILLATION_SCRIPT`) directly inside the browser DOM, converting raw verbose HTML (100k+ tokens) into high-signal Set-of-Marks notation (~3k tokens, 90%+ savings).
**Critical Invariants:**
1. **SSRF Guard:** All URLs must pass scheme verification (`http`, `https`, `data:text/html`), host blacklist inspection (cloud metadata, loopbacks, octal/hex IP notations), and pre-navigation DNS resolution. Post-redirect destinations (`driver.current_url`) must be re-verified before distillation.
2. **Credential Redaction:** Passwords, API keys, tokens, PAN, CVV, SSN, OTP/2FA, and one-time-code fields in `<input>`/`<textarea>` attributes (`name`, `id`, `placeholder`, `aria-label`, `autocomplete`) must be redacted as `***REDACTED***` in snapshots and masked in action execution outputs.
3. **Prompt Boundary Security:** Snapshots rendered into prompts must be enclosed in `<untrusted_web_snapshot>` tags with attributes escaped and any premature closing tags neutralized to prevent prompt injection breakouts.
4. **Set-of-Marks Marker Token Safety:** Labels and values inside `[ID:role "label"]` must have square brackets escaped or bracket-free redaction tokens (`***REDACTED***`) so downstream LLM regex token parsers do not fracture.

## 2026-11-08 - 30 Targeted Design, Performance, and Quality Passes
**Goal:** Complete 30 focused design and performance improvements to make this controller the premier open-source token-efficient browser automation framework:

### Performance Passes (10)
1. **CDP (Chrome DevTools Protocol) Direct Execution:** Bypass WebDriver HTTP round-trips for script injection and DOM queries using native CDP `Runtime.evaluate` to drop turn latency from ~80ms to <10ms.
2. **Streaming Tree Construction:** Instead of building monolithic string buffers in JS distillation, stream Set-of-Marks nodes using a lightweight tokenized binary or array buffer.
3. **Differential DOM Diffing:** Cache the previous distillation state and emit DOM delta updates (`+ added`, `- removed`, `~ changed`) on successive turns to achieve 98%+ token savings on incremental actions.
4. **Debounced/Event-Driven DOM Ready:** Replace polling sleep loops in `_wait_for_dom_ready` with `document.fonts.ready`, `requestIdleCallback`, and MutationObserver quiescence detection.
5. **Pre-warmed Headless Chromium Pool:** Implement lazy background browser process pooling to eliminate cold-start startup overhead (down from 1.5s to instant).
6. **Smart Scroll Chunking:** Compute optimal viewport bounding box subdivisions to avoid redundant visual rendering of off-screen DOM subtrees in viewport mode.
7. **Fast In-Memory CSS Selector Caching:** Cache resolved element locators and IDs across quick multi-action chains without DOM re-querying when the page has not mutated.
8. **Asynchronous Observation Pipelines:** Allow background snapshot distillation while the LLM client is parsing or planning the next reasoning step.
9. **Zero-Copy Serialization:** Eliminate intermediate Python dictionary copies when rendering prompts or JSON function schemas.
10. **Headless Memory Capping:** Configure Chromium flags (`--js-flags=--max-old-space-size=256`, `--disable-background-networking`) to prevent long-lived background browser memory bloat.

### Architecture & Design Passes (10)
11. **Type-Safe Multi-Provider Tool Definitions:** Refactor OpenAI, Gemini, and Anthropic tool schema generators into dedicated, strongly-typed Pydantic classes or schema builders.
12. **Pluggable Driver Backend:** Abstract the browser execution layer behind a clean `BrowserBackend` interface, allowing hot-swapping between Selenium, Playwright, and raw CDP/Pyppeteer.
13. **AsyncIO Native API:** Introduce an `AsyncTokenOptimizedBrowser` alongside the sync client for seamless integration with modern async agent runtimes (LangChain, AutoGen, AgentScope).
14. **Custom Accessibility Tree Traversal:** Support ARIA role trees (`role="treegrid"`, `role="listbox"`, `role="dialog"`) with hierarchical indentation in snapshot outputs.
15. **Shadow DOM & IFrame Deep Piercing:** Extend `JS_DISTILLATION_SCRIPT` to seamlessly traverse shadow roots (`element.shadowRoot`) and same-origin iframes without requiring manual context switches.
16. **Robust Multi-Tab / Window Management:** Add clean methods to list tabs, switch focus (`switch_to.window`), open new tabs, and track active tab observations.
17. **File Upload / File Chooser Handling:** Implement secure support for `<input type="file">` file attachment actions.
18. **Custom Action Middleware Pipeline:** Add pre- and post-action hooks (e.g. rate limiting, audit logging, screenshot capture on failure).
19. **Structured Error Hierarchy:** Replace generic `ValueError`/`RuntimeError` exceptions with granular domain errors (`SSRFSecurityError`, `ElementNotFoundError`, `BrowserCrashError`, `NavigationTimeoutError`).
20. **Configurable Snapshot Formatters:** Support multiple snapshot output formats (compact Set-of-Marks, YAML DOM, Markdown Outline, or JSON AST).

### Robustness, Accessibility & Security Passes (10)
21. **Content Security Policy (CSP) Bypass Resilience:** Ensure in-page script evaluation works reliably even when pages enforce strict CSP headers (`script-src 'self'`).
22. **Canvas and SVG Text Distillation:** Extract textual nodes and accessible labels inside complex `<svg>` and `<canvas>` UI widgets.
23. **Canvas / Visual Grounding Coordinates:** Provide optional bounding box coordinates `[x, y, w, h]` for hybrid multimodal models (e.g. Gemini 1.5 / 2.0 Flash multimodal).
24. **Multi-Domain SSRF Redirect Depth Tracking:** Track redirect chains to limit max redirect hops and prevent infinite redirect loops.
25. **Auto-Dismiss Unwanted Overlays:** Automatically detect and dismiss cookie consent banners, GDPR popups, and newsletter modals.
26. **Hover / Tooltip Trigger Support:** Add an explicit `hover(element_id)` action to reveal dynamic dropdowns and tooltips.
27. **Drag-and-Drop Action Support:** Provide simulated drag-and-drop primitives for kanban boards, sliders, and reorderable lists.
28. **Graceful Crash & OOM Recovery:** Automatically detect Chromium renderer crashes or tab disconnection and auto-restart the browser session with preserved history.
29. **Strict Secret Masking in REPL Logs:** Guarantee that terminal REPL logs and command histories never print raw sensitive inputs.
30. **Comprehensive Pytest Suite & CI Workflow:** Provide a mock HTTP server and full end-to-end integration test suite running in GitHub Actions across Ubuntu, Windows, and macOS.
