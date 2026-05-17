# Robots.txt Monitor

A stateful, diff-based `robots.txt` monitoring Actor designed for autonomous scrapers, RAG compliance engines, and automated website governance. 

Unlike standard stateless parsers that throw false positives over minor spacing edits, this tool utilizes a **versioned stable snapshot contract (v1)**. It tracks structural directive changes over time and ignores superficial formatting modifications, ensuring you only receive actionable operational alerts.

## 🚀 Key Features

- **Stateful Baseline Awareness:** Establishes a historical baseline on the first run and generates a delta/diff payload on all subsequent executions.
- **Strict Severity Contract:** Classifies policy changes into clear operational tiers (`critical`, `warning`, `info`) to eliminate alert fatigue.
- **Structural Integrity Tracking:** Monitors user-agent rules, `Allow`/`Disallow` blocks, crawl-delays, and sitemap directive changes.
- **Formatting Insulation:** Intentionally ignores comment-only edits, line reordering, and whitespace shifts.

## 🛠 Usage & Integration

This tool runs as a managed Actor on the Apify platform.
**[Run on Apify Store →](https://apify.com/datawinder/robots-txt-monitor)**

### Inputs & Execution Strategy
The Actor is completely automated. It accepts a target base URL, locates the root `robots.txt` file, parses its directive tree, and handles network or fetch timeouts cleanly by logging a `httpStatus = 0` failure without breaking down-line automations.

---

## 📊 Alert Semantics (Severity Contract)

To make this actor completely safe for automated webhooks and PagerDuty/Log integrations, it follows a strict operational contract:

| Severity Tier | Operational Meaning | System Trigger Examples | Downstream Action |
| :--- | :--- | :--- | :--- |
| **🔴 Critical** | Access restriction or loss of reachability. | File becomes unreachable; Global `Disallow: /` added. | **Page / Halt Scraping** |
| **🟠 Warning** | Policy change requiring manual review. | Specific User-Agent blocks added; Crawl-delay changed. | **Notify / Log Warning** |
| **🔵 Info** | Non-blocking informational updates. | New sitemaps discovered; Formatting/comment edits. | **Silent Log Audit** |

---

## 📦 Output Contract Example

Subsequent runs output a highly optimized JSON delta payload designed for instant machine consumption:

```json
{
  "baseline": false,
  "unchanged": false,
  "summary": {
    "critical": 1,
    "warning": 0,
    "info": 0
  },
  "changes": [
    {
      "type": "disallow_added",
      "severity": "critical",
      "description": "Disallow added for *: /private/"
    }
  ]
}
```
💡 Recommended Integration Architecture
Interval: Automate via Apify schedulers to execute daily or hourly depending on target volatility.

Pre-Flight Filter: Wire this upstream from active web scraping loops or enterprise RAG data ingestion streams to treat robots.txt as a real-time compliance signal.
