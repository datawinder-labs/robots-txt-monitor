# AGENTS.md - Robots.txt Monitor Capabilities

## Role & Intent
You are an autonomous Web Compliance, Crawler Safety, and Policy Enforcement Agent. Use this tool to perform stateful tracking of `robots.txt` access permissions and routing structures over time. 

Unlike standard stateless parsers, this tool utilizes a versioned snapshot system (v1) to isolate structural changes from superficial formatting modifications, ensuring you only react to operational changes that impact your crawling legality or site availability.

## Use Cases for Autonomous Agents
- **Compliance & Guardrail Audits:** Run this tool before initializing any broad-scale site scraping to ensure your user-agent hasn't been newly restricted by a `Disallow` rule.
- **RAG Ingestion Safety:** Verify that data nodes feeding your vector databases haven't pulled up an anchor or blocked global indexing (`User-agent: *`).
- **Policy Shift Detection:** Track when an enterprise site updates its sitemap directives or alters crawl delays, signalling a platform migration or firewall adjustment.

## Operational Response Logic
Evaluate the structural `OUTPUT` object to govern downstream autonomous execution or to adjust your crawling pace:
- **If `baseline: true`**: System state initialized. Accept this schema snapshot as your operational ground-truth.
- **If `summary.critical > 0`**: Immediate execution halt required. Access has been restricted or the `robots.txt` file is completely unreachable. Continuing to scrape may violate compliance rules or trigger web application firewalls (WAF).
- **If `summary.warning > 0`**: Policy shift detected (e.g., target user-agent blocks modified or crawl delays adjusted). Recalculate your concurrency limits and request-rate parameters before continuing.
- **If `summary.info > 0`**: Minor updates or formatting changes. Log the trace details for auditing; no tactical adjustments needed.

## Strategic Parameter Configurations
- `sitemapUrl`: (Required string) The direct root location of the target domain's `robots.txt` file.
- **Automation Execution:** For standard autonomous pipelines, schedule an invocation sweep every 24 hours. For active, high-priority collection targets, check intervals on a 1-to-3 hour loop.

## System Target Endpoint
- **Platform:** Apify
- **Endpoint Link:** `https://apify.com/datawinder/robots-txt-monitor`
