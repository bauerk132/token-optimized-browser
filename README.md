# Token-Optimized AI Chromium Browser Controller 🚀

A lightweight, enterprise-grade Chromium browser automation engine engineered specifically for LLM and AI agent control. It slashes token consumption by **90% to 95%** compared to sending raw DOM trees or visual screenshots, while maintaining full accessibility and interaction capabilities.

---

## ⚡ Key Highlights

- **90%+ Token Reduction:** Directly distills 100k+ token HTML pages into ~2,000–3,000 token structured Set-of-Marks representations.
- **Set-of-Marks Visual Grounding:** Tags all interactive candidate elements with deterministic, numbered markers (e.g. `[12:button "Submit Application"]`).
- **Comprehensive Security & SSRF Protection:**
  - Strict scheme whitelist (`http`, `https`, `data:text/html`).
  - Pre-navigation DNS resolution to block DNS rebinding and loopback tricks.
  - Multi-format IP literal blocking (decimal, hexadecimal, octal notations).
  - Post-redirect URL validation to eliminate HTTP 3xx SSRF redirect bypasses.
  - Automatic credential and secret redaction (`***REDACTED***`) across attributes (`name`, `id`, `placeholder`, `aria-label`, `autocomplete`).
  - Escape-proof XML prompt encapsulation boundaries (`<untrusted_web_snapshot>`).
- **Multi-Provider Tool Schemas:** One-click JSON schema definitions ready for OpenAI Function Calling, Gemini Function Calling, and Anthropic Tools.
- **Interactive REPL & CLI:** Built-in terminal REPL and benchmarking mode for rapid local testing and development.

---

## 📦 Installation

```bash
git clone https://github.com/bauerk132/token-optimized-browser.git
cd token-optimized-browser
pip install -r requirements.txt
```

*Requirements:*
- Python 3.10+
- Chrome or Chromium installed on your system
- `selenium >= 4.20.0`

---

## 🚀 Quick Start

### 1. Python API

```python
from token_optimized_browser import TokenOptimizedBrowser

with TokenOptimizedBrowser(headless=True) as browser:
    # 1. Navigate to page
    observation = browser.goto("https://news.ycombinator.com")
    
    # 2. Inspect token savings and prompt
    print(observation.token_stats.summary())
    prompt_context = observation.to_agent_prompt()
    
    # 3. Execute actions by element ID
    # Click element with ID 4
    result = browser.execute_action({"action": "click", "id": 4})
    print(result["message"])
```

### 2. Command-Line Interface (CLI)

```bash
# Run local token savings benchmark demo
python token_optimized_browser.py --demo

# Inspect live URL
python token_optimized_browser.py --url "https://example.com"

# Launch interactive terminal REPL
python token_optimized_browser.py --interactive

# Print tool schema for Gemini or OpenAI
python token_optimized_browser.py --schema --format gemini
```

---

## 🛡️ Action Schema Reference

LLM agents interact using uniform action dictionaries:

| Action | Parameters | Example Payload |
|---|---|---|
| `goto` | `url` | `{"action": "goto", "url": "https://example.com"}` |
| `click` | `id` | `{"action": "click", "id": 12}` |
| `type` | `id`, `text`, `enter` (optional), `clear` (optional) | `{"action": "type", "id": 5, "text": "search query", "enter": true}` |
| `select` | `id`, `value` | `{"action": "select", "id": 7, "value": "Option 2"}` |
| `checkbox`| `id`, `checked` | `{"action": "checkbox", "id": 9, "checked": true}` |
| `scroll` | `direction` (`down`/`up`), `amount` | `{"action": "scroll", "direction": "down", "amount": 600}` |
| `key` | `key` (`enter`, `tab`, `esc`, `arrowdown`) | `{"action": "key", "key": "enter"}` |

---

## 🧪 Running Tests

```bash
python -m unittest discover -s tests
```

---

## 🤖 Jules Autonomous Optimization Roadmap

This repository includes pre-configured Jules memory (`.jules/bolt.md`) tracking 30 targeted design, performance, and security enhancements across:
- Chrome DevTools Protocol (CDP) direct execution
- Differential DOM diffing for 98%+ multi-turn savings
- Pluggable driver backends (Playwright / Selenium / CDP)
- AsyncIO native execution pipeline
- Shadow DOM & iframe deep piercing

---

## 📄 License

MIT License.
