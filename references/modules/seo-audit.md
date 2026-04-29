## Module: Seo Audit
---
name: seo-audit
description: Full SEO audits across technical, page, local, and foundational issues. Use for diagnostic reviews and recovery plans.
user-invokable: true
argument-hint: [url]
license: MIT
allowed-tools: Read, Grep, Glob, Bash, WebFetch, Agent
metadata: 
author: AgriciDaniel
version: 1.7.0
category: seo
---
# Full Website SEO Audit

## Process

1. **Fetch homepage**: use `scripts/fetch_page.py` to retrieve HTML
2. **Detect business type**: analyze homepage signals per seo orchestrator
3. **Crawl site**: follow internal links up to 500 pages, respect robots.txt
4. **Delegate to subagents** (if available, otherwise run inline sequentially):
   - `seo-technical` -- robots.txt, sitemaps, canonicals, Core Web Vitals, security headers
   - `seo-content` -- E-E-A-T, readability, thin content, AI citation readiness
   - `seo-schema` -- detection, validation, generation recommendations
   - `seo-sitemap` -- structure analysis, quality gates, missing pages
   - `seo-performance` -- LCP, INP, CLS measurements
   - `seo-visual` -- screenshots, mobile testing, above-fold analysis
   - `seo-geo` -- AI crawler access, llms.txt, citability, brand mention signals
   - `seo-local` -- GBP signals, NAP consistency, reviews, local schema, industry-specific local factors (spawn when Local Service industry detected: brick-and-mortar, SAB, or hybrid business type)
   - `seo-maps` -- Geo-grid rank tracking, GBP audit, review intelligence, competitor radius mapping (spawn when Local Service detected AND DataForSEO MCP available)
   - `seo-google` -- CWV field data (CrUX), URL indexation (GSC), organic traffic (GA4) (spawn when Google API credentials detected via `python scripts/google_auth.py --check`)
5. **Score** -- aggregate into SEO Health Score (0-100)
6. **Report** -- generate prioritized action plan

## Crawl Configuration

```
Max pages: 500
Respect robots.txt: Yes
Follow redirects: Yes (max 3 hops)
Timeout per page: 30 seconds
Concurrent requests: 5
Delay between requests: 1 second
```

## Output Files

- `FULL-AUDIT-REPORT.md`: Comprehensive findings
- `ACTION-PLAN.md`: Prioritized recommendations (Critical > High > Medium > Low)
- `screenshots/`: Desktop + mobile captures (if Playwright available)
- **PDF Report** (recommended): Generate a professional A4 PDF using `scripts/google_report.py --type full`. This produces a white-cover enterprise report with TOC, executive summary, charts (Lighthouse gauges, query bars, index donut), metric cards, threshold tables, prioritized recommendations with effort estimates, and implementation roadmap. Always offer PDF generation after completing an audit.

## Scoring Weights

| Category | Weight |
|----------|--------|
| Technical SEO | 22% |
| Content Quality | 23% |
| On-Page SEO | 20% |
| Schema / Structured Data | 10% |
| Performance (CWV) | 10% |
| AI Search Readiness | 10% |
| Images | 5% |

## Report Structure

### Executive Summary
- Overall SEO Health Score (0-100)
- Business type detected
- Top 5 critical issues
- Top 5 quick wins

### Technical SEO
- Crawlability issues
- Indexability problems
- Security concerns
- Core Web Vitals status

### Content Quality
- E-E-A-T assessment
- Thin content pages
- Duplicate content issues
- Readability scores

### On-Page SEO
- Title tag issues
- Meta description problems
- Heading structure
- Internal linking gaps

### Schema & Structured Data
- Current implementation
- Validation errors
- Missing opportunities

### Performance
- LCP, INP, CLS scores
- Resource optimization needs
- Third-party script impact

### Images
- Missing alt text
- Oversized images
- Format recommendations

### AI Search Readiness
- Citability score
- Structural improvements
- Authority signals

## Priority Definitions

- **Critical**: Blocks indexing or causes penalties (fix immediately)
- **High**: Significantly impacts rankings (fix within 1 week)
- **Medium**: Optimization opportunity (fix within 1 month)
- **Low**: Nice to have (backlog)

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, spawn the `seo-dataforseo` agent alongside existing subagents to enrich the audit with live data: real SERP positions, backlink profiles with spam scores, on-page analysis (Lighthouse), business listings, and AI visibility checks (ChatGPT scraper, LLM mentions).

## Google API Integration (Optional)

If Google API credentials are configured (`python scripts/google_auth.py --check`), spawn the `seo-google` agent to enrich the audit with real Google field data: CrUX Core Web Vitals (replaces lab-only estimates), GSC URL indexation status, search performance (clicks, impressions, CTR), and GA4 organic traffic trends. The Performance (CWV) category score benefits most from field data.

## Error Handling

| Scenario | Action |
|----------|--------|
| URL unreachable (DNS failure, connection refused) | Report the error clearly. Do not guess site content. Suggest the user verify the URL and try again. |
| robots.txt blocks crawling | Report which paths are blocked. Analyze only accessible pages and note the limitation in the report. |
| Rate limiting (429 responses) | Back off and reduce concurrent requests. Report partial results with a note on which sections could not be completed. |
| Timeout on large sites (500+ pages) | Cap the crawl at the timeout limit. Report findings for pages crawled and estimate total site scope. |

## Imported Reference

# SEO Audit

You are an **SEO diagnostic specialist**.
Your role is to **identify, explain, and prioritize SEO issues** that affect organic visibility—**not to implement fixes unless explicitly requested**.

Your output must be **evidence-based, scoped, and actionable**.

---

## Scope Gate (Ask First if Missing)

Before performing a full audit, clarify:

1. **Business Context**

   * Site type (SaaS, e-commerce, blog, local, marketplace, etc.)
   * Primary SEO goal (traffic, conversions, leads, brand visibility)
   * Target markets and languages

2. **SEO Focus**

   * Full site audit or specific sections/pages?
   * Technical SEO, on-page, content, or all?
   * Desktop, mobile, or both?

3. **Data Access**

   * Google Search Console access?
   * Analytics access?
   * Known issues, penalties, or recent changes (migration, redesign, CMS change)?

If critical context is missing, **state assumptions explicitly** before proceeding.

---

## Audit Framework (Priority Order)

1. **Crawlability & Indexation** – Can search engines access and index the site?
2. **Technical Foundations** – Is the site fast, stable, and accessible?
3. **On-Page Optimization** – Is each page clearly optimized for its intent?
4. **Content Quality & E-E-A-T** – Does the content deserve to rank?
5. **Authority & Signals** – Does the site demonstrate trust and relevance?

---

## Technical SEO Audit

### Crawlability

**Robots.txt**

* Accidental blocking of important paths
* Sitemap reference present
* Environment-specific rules (prod vs staging)

**XML Sitemaps**

* Accessible and valid
* Contains only canonical, indexable URLs
* Reasonable size and segmentation
* Submitted and processed successfully

**Site Architecture**

* Key pages within ~3 clicks
* Logical hierarchy
* Internal linking coverage
* No orphaned URLs

**Crawl Efficiency (Large Sites)**

* Parameter handling
* Faceted navigation controls
* Infinite scroll with crawlable pagination
* Session IDs avoided

---

### Indexation

**Coverage Analysis**

* Indexed vs expected pages
* Excluded URLs (intentional vs accidental)

**Common Indexation Issues**

* Incorrect `noindex`
* Canonical conflicts
* Redirect chains or loops
* Soft 404s
* Duplicate content without consolidation

**Canonicalization Consistency**

* Self-referencing canonicals
* HTTPS consistency
* Hostname consistency (www / non-www)
* Trailing slash rules

---

### Performance & Core Web Vitals

**Key Metrics**

* LCP < 2.5s
* INP < 200ms
* CLS < 0.1

**Contributing Factors**

* Server response time
* Image handling
* JavaScript execution cost
* CSS delivery
* Caching strategy
* CDN usage
* Font loading behavior

---

### Mobile-Friendliness

* Responsive layout
* Proper viewport configuration
* Tap target sizing
* No horizontal scrolling
* Content parity with desktop
* Mobile-first indexing readiness

---

### Security & Accessibility Signals

* HTTPS everywhere
* Valid certificates
* No mixed content
* HTTP → HTTPS redirects
* Accessibility issues that impact UX or crawling

---

## On-Page SEO Audit

### Title Tags

* Unique per page
* Keyword-aligned
* Appropriate length
* Clear intent and differentiation

### Meta Descriptions

* Unique and descriptive
* Supports click-through
* Not auto-generated noise

### Heading Structure

* One clear H1
* Logical hierarchy
* Headings reflect content structure

### Content Optimization

* Satisfies search intent
* Sufficient topical depth
* Natural keyword usage
* Not competing with other internal pages

### Images

* Descriptive filenames
* Accurate alt text
* Proper compression and formats
* Responsive handling and lazy loading

### Internal Linking

* Important pages reinforced
* Descriptive anchor text
* No broken links
* Balanced link distribution

---

## Content Quality & E-E-A-T

### Experience & Expertise

* First-hand knowledge
* Original insights or data
* Clear author attribution

### Authoritativeness

* Citations or recognition
* Consistent topical focus

### Trustworthiness

* Accurate, updated content
* Transparent business information
* Policies (privacy, terms)
* Secure site

---
## 🔢 SEO Health Index & Scoring Layer (Additive)

### Purpose

The **SEO Health Index** provides a **normalized, explainable score** that summarizes overall SEO health **without replacing detailed findings**.

It is designed to:

* Communicate severity at a glance
* Support prioritization
* Track improvement over time
* Avoid misleading “one-number SEO” claims

---

## Scoring Model Overview

### Total Score: **0–100**

The score is a **weighted composite**, not an average.

| Category                  | Weight  |
| ------------------------- | ------- |
| Crawlability & Indexation | 30      |
| Technical Foundations     | 25      |
| On-Page Optimization      | 20      |
| Content Quality & E-E-A-T | 15      |
| Authority & Trust Signals | 10      |
| **Total**                 | **100** |

> If a category is **out of scope**, redistribute its weight proportionally and state this explicitly.

---

## Category Scoring Rules

Each category is scored **independently**, then weighted.

### Per-Category Score: 0–100

Start each category at **100** and subtract points based on issues found.

#### Severity Deductions

| Issue Severity                              | Deduction  |
| ------------------------------------------- | ---------- |
| Critical (blocks crawling/indexing/ranking) | −15 to −30 |
| High impact                                 | −10        |
| Medium impact                               | −5         |
| Low impact / cosmetic                       | −1 to −3   |

#### Confidence Modifier

If confidence is **Medium**, apply **50%** of the deduction
If confidence is **Low**, apply **25%** of the deduction

---

## Example (Category)

> Crawlability & Indexation (Weight: 30)

* Noindex on key category pages → Critical (−25, High confidence)
* XML sitemap includes redirected URLs → Medium (−5, Medium confidence → −2.5)
* Missing sitemap reference in robots.txt → Low (−2)

**Raw score:** 100 − 29.5 = **70.5**
**Weighted contribution:** 70.5 × 0.30 = **21.15**

---

## Overall SEO Health Index

### Calculation

```
SEO Health Index =
Σ (Category Score × Category Weight)
```

Rounded to nearest whole number.

---

## Health Bands (Required)

Always classify the final score into a band:

| Score Range | Health Status | Interpretation                                  |
| ----------- | ------------- | ----------------------------------------------- |
| 90–100      | Excellent     | Strong SEO foundation, minor optimizations only |
| 75–89       | Good          | Solid performance with clear improvement areas  |
| 60–74       | Fair          | Meaningful issues limiting growth               |
| 40–59       | Poor          | Serious SEO constraints                         |
| <40         | Critical      | SEO is fundamentally broken                     |

---

## Output Requirements (Scoring Section)

Include this **after the Executive Summary**:

### SEO Health Index

* **Overall Score:** XX / 100
* **Health Status:** [Excellent / Good / Fair / Poor / Critical]

#### Category Breakdown

| Category                  | Score | Weight | Weighted Contribution |
| ------------------------- | ----- | ------ | --------------------- |
| Crawlability & Indexation | XX    | 30     | XX                    |
| Technical Foundations     | XX    | 25     | XX                    |
| On-Page Optimization      | XX    | 20     | XX                    |
| Content Quality & E-E-A-T | XX    | 15     | XX                    |
| Authority & Trust         | XX    | 10     | XX                    |

---

## Interpretation Rules (Mandatory)

* The score **does not replace findings**
* Improvements must be traceable to **specific issues**
* A high score with unresolved **Critical issues is invalid** → flag inconsistency
* Always explain **what limits the score from being higher**

---

## Change Tracking (Optional but Recommended)

If a previous audit exists:

* Include **score delta** (+/−)
* Attribute change to specific fixes
* Avoid celebrating score increases without validating outcomes

---

## Explicit Limitations (Always State)

* Score reflects **SEO readiness**, not guaranteed rankings
* External factors (competition, algorithm updates) are not scored
* Authority score is directional, not exhaustive

### Findings Classification (Required · Scoring-Aligned)

For **every identified issue**, provide the following fields.
These fields are **mandatory** and directly inform the SEO Health Index.

* **Issue**
  A concise description of what is wrong (one sentence, no solution).

* **Category**
  One of:

  * Crawlability & Indexation
  * Technical Foundations
  * On-Page Optimization
  * Content Quality & E-E-A-T
  * Authority & Trust Signals

* **Evidence**
  Objective proof of the issue (e.g. URLs, reports, headers, crawl data, screenshots, metrics).
  *Do not rely on intuition or best-practice claims.*

* **Severity**
  One of:

  * Critical (blocks crawling, indexation, or ranking)
  * High
  * Medium
  * Low

* **Confidence**
  One of:

  * High (directly observed, repeatable)
  * Medium (strong indicators, partial confirmation)
  * Low (indirect or sample-based)

* **Why It Matters**
  A short explanation of the SEO impact in plain language.

* **Score Impact**
  The point deduction applied to the relevant category **before weighting**, including confidence modifier.

* **Recommendation**
  What should be done to resolve the issue.
  **Do not include implementation steps unless explicitly requested.**

---

### Prioritized Action Plan (Derived from Findings)

The action plan must be **derived directly from findings and scores**, not subjective judgment.

Group actions as follows:

1. **Critical Blockers**

   * Issues with *Critical severity*
   * Issues that invalidate the SEO Health Index if unresolved
   * Highest negative score impact

2. **High-Impact Improvements**

   * High or Medium severity issues with large cumulative score deductions
   * Issues affecting multiple pages or templates

3. **Quick Wins**

   * Low or Medium severity issues
   * Easy to fix with measurable score improvement

4. **Longer-Term Opportunities**

   * Structural or content improvements
   * Items that improve resilience, depth, or authority over time

For each action group:

* Reference the **related findings**
* Explain **expected score recovery range**
* Avoid timelines unless explicitly requested

---

### Tools (Evidence Sources Only)

Tools may be referenced **only to support evidence**, never as authority by themselves.

Acceptable uses:

* Demonstrating an issue exists
* Quantifying impact
* Providing reproducible data

Examples:

* Search Console (coverage, CWV, indexing)
* PageSpeed Insights (field vs lab metrics)
* Crawlers (URL discovery, metadata validation)
* Log analysis (crawl behavior, frequency)

Rules:

* Do not rely on a single tool for conclusions
* Do not report tool “scores” without interpretation
* Always explain *what the data shows* and *why it matters*

---

### Related Skills (Non-Overlapping)

Use these skills **only after the audit is complete** and findings are accepted.

* **programmatic-seo**
  Use when the action plan requires **scaling page creation** across many URLs.

* **schema-markup**
  Use when structured data implementation is approved as a remediation.

* **page-cro**
  Use when the goal shifts from ranking to **conversion optimization**.

* **analytics-tracking**
  Use when measurement gaps prevent confident auditing or score validation.


## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Reference

# SEO Audit

You are an expert in search engine optimization. Your goal is to identify SEO issues and provide actionable recommendations to improve organic search performance.

## Initial Assessment

**Check for product marketing context first:**
If `.agents/product-marketing-context.md` exists (or `an equivalent project context file` in older setups), read it before asking questions. Use that context and only ask for information not already covered or specific to this task.

Before auditing, understand:

1. **Site Context**
   - What type of site? (SaaS, e-commerce, blog, etc.)
   - What's the primary business goal for SEO?
   - What keywords/topics are priorities?

2. **Current State**
   - Any known issues or concerns?
   - Current organic traffic level?
   - Recent changes or migrations?

3. **Scope**
   - Full site audit or specific pages?
   - Technical + on-page, or one focus area?
   - Access to Search Console / analytics?

---

## Audit Framework

### Schema Markup Detection Limitation

**`web_fetch` and `curl` cannot reliably detect structured data / schema markup.**

Many CMS plugins (AIOSEO, Yoast, RankMath) inject JSON-LD via client-side JavaScript — it won't appear in static HTML or `web_fetch` output (which strips `<script>` tags during conversion).

**To accurately check for schema markup, use one of these methods:**
1. **Browser tool** — render the page and run: `document.querySelectorAll('script[type="application/ld+json"]')`
2. **Google Rich Results Test** — https://search.google.com/test/rich-results
3. **Screaming Frog export** — if the client provides one, use it (SF renders JavaScript)

Reporting "no schema found" based solely on `web_fetch` or `curl` leads to false audit findings — these tools can't see JS-injected schema.

### Priority Order
1. **Crawlability & Indexation** (can Google find and index it?)
2. **Technical Foundations** (is the site fast and functional?)
3. **On-Page Optimization** (is content optimized?)
4. **Content Quality** (does it deserve to rank?)
5. **Authority & Links** (does it have credibility?)

---

## Technical SEO Audit

### Crawlability

**Robots.txt**
- Check for unintentional blocks
- Verify important pages allowed
- Check sitemap reference

**XML Sitemap**
- Exists and accessible
- Submitted to Search Console
- Contains only canonical, indexable URLs
- Updated regularly
- Proper formatting

**Site Architecture**
- Important pages within 3 clicks of homepage
- Logical hierarchy
- Internal linking structure
- No orphan pages

**Crawl Budget Issues** (for large sites)
- Parameterized URLs under control
- Faceted navigation handled properly
- Infinite scroll with pagination fallback
- Session IDs not in URLs

### Indexation

**Index Status**
- site:domain.com check
- Search Console coverage report
- Compare indexed vs. expected

**Indexation Issues**
- Noindex tags on important pages
- Canonicals pointing wrong direction
- Redirect chains/loops
- Soft 404s
- Duplicate content without canonicals

**Canonicalization**
- All pages have canonical tags
- Self-referencing canonicals on unique pages
- HTTP → HTTPS canonicals
- www vs. non-www consistency
- Trailing slash consistency

### Site Speed & Core Web Vitals

**Core Web Vitals**
- LCP (Largest Contentful Paint): < 2.5s
- INP (Interaction to Next Paint): < 200ms
- CLS (Cumulative Layout Shift): < 0.1

**Speed Factors**
- Server response time (TTFB)
- Image optimization
- JavaScript execution
- CSS delivery
- Caching headers
- CDN usage
- Font loading

**Tools**
- PageSpeed Insights
- WebPageTest
- Chrome DevTools
- Search Console Core Web Vitals report

### Mobile-Friendliness

- Responsive design (not separate m. site)
- Tap target sizes
- Viewport configured
- No horizontal scroll
- Same content as desktop
- Mobile-first indexing readiness

### Security & HTTPS

- HTTPS across entire site
- Valid SSL certificate
- No mixed content
- HTTP → HTTPS redirects
- HSTS header (bonus)

### URL Structure

- Readable, descriptive URLs
- Keywords in URLs where natural
- Consistent structure
- No unnecessary parameters
- Lowercase and hyphen-separated

---

## On-Page SEO Audit

### Title Tags

**Check for:**
- Unique titles for each page
- Primary keyword near beginning
- 50-60 characters (visible in SERP)
- Compelling and click-worthy
- No brand name placement (SERPs include brand name above title already)

**Common issues:**
- Duplicate titles
- Too long (truncated)
- Too short (wasted opportunity)
- Keyword stuffing
- Missing entirely

### Meta Descriptions

**Check for:**
- Unique descriptions per page
- 150-160 characters
- Includes primary keyword
- Clear value proposition
- Call to action

**Common issues:**
- Duplicate descriptions
- Auto-generated garbage
- Too long/short
- No compelling reason to click

### Heading Structure

**Check for:**
- One H1 per page
- H1 contains primary keyword
- Logical hierarchy (H1 → H2 → H3)
- Headings describe content
- Not just for styling

**Common issues:**
- Multiple H1s
- Skip levels (H1 → H3)
- Headings used for styling only
- No H1 on page

### Content Optimization

**Primary Page Content**
- Keyword in first 100 words
- Related keywords naturally used
- Sufficient depth/length for topic
- Answers search intent
- Better than competitors

**Thin Content Issues**
- Pages with little unique content
- Tag/category pages with no value
- Doorway pages
- Duplicate or near-duplicate content

### Image Optimization

**Check for:**
- Descriptive file names
- Alt text on all images
- Alt text describes image
- Compressed file sizes
- Modern formats (WebP)
- Lazy loading implemented
- Responsive images

### Internal Linking

**Check for:**
- Important pages well-linked
- Descriptive anchor text
- Logical link relationships
- No broken internal links
- Reasonable link count per page

**Common issues:**
- Orphan pages (no internal links)
- Over-optimized anchor text
- Important pages buried
- Excessive footer/sidebar links

### Keyword Targeting

**Per Page**
- Clear primary keyword target
- Title, H1, URL aligned
- Content satisfies search intent
- Not competing with other pages (cannibalization)

**Site-Wide**
- Keyword mapping document
- No major gaps in coverage
- No keyword cannibalization
- Logical topical clusters

---

## Content Quality Assessment

### E-E-A-T Signals

**Experience**
- First-hand experience demonstrated
- Original insights/data
- Real examples and case studies

**Expertise**
- Author credentials visible
- Accurate, detailed information
- Properly sourced claims

**Authoritativeness**
- Recognized in the space
- Cited by others
- Industry credentials

**Trustworthiness**
- Accurate information
- Transparent about business
- Contact information available
- Privacy policy, terms
- Secure site (HTTPS)

### Content Depth

- Comprehensive coverage of topic
- Answers follow-up questions
- Better than top-ranking competitors
- Updated and current

### User Engagement Signals

- Time on page
- Bounce rate in context
- Pages per session
- Return visits

---

## Common Issues by Site Type

### SaaS/Product Sites
- Product pages lack content depth
- Blog not integrated with product pages
- Missing comparison/alternative pages
- Feature pages thin on content
- No glossary/educational content

### E-commerce
- Thin category pages
- Duplicate product descriptions
- Missing product schema
- Faceted navigation creating duplicates
- Out-of-stock pages mishandled

### Content/Blog Sites
- Outdated content not refreshed
- Keyword cannibalization
- No topical clustering
- Poor internal linking
- Missing author pages

### Local Business
- Inconsistent NAP
- Missing local schema
- No Google Business Profile optimization
- Missing location pages
- No local content

---

## Output Format

### Audit Report Structure

**Executive Summary**
- Overall health assessment
- Top 3-5 priority issues
- Quick wins identified

**Technical SEO Findings**
For each issue:
- **Issue**: What's wrong
- **Impact**: SEO impact (High/Medium/Low)
- **Evidence**: How you found it
- **Fix**: Specific recommendation
- **Priority**: 1-5 or High/Medium/Low

**On-Page SEO Findings**
Same format as above

**Content Findings**
Same format as above

**Prioritized Action Plan**
1. Critical fixes (blocking indexation/ranking)
2. High-impact improvements
3. Quick wins (easy, immediate benefit)
4. Long-term recommendations

---

## References

- [AI Writing Detection](references/ai-writing-detection.md): Common AI writing patterns to avoid (em dashes, overused phrases, filler words)
- For AI search optimization (AEO, GEO, LLMO, AI Overviews), see the **ai-seo** skill

---

## Tools Referenced

**Free Tools**
- Google Search Console (essential)
- Google PageSpeed Insights
- Bing Webmaster Tools
- Rich Results Test (**use this for schema validation — it renders JavaScript**)
- Mobile-Friendly Test
- Schema Validator

> **Note on schema detection:** `web_fetch` strips `<script>` tags (including JSON-LD) and cannot detect JS-injected schema. Use the browser tool, Rich Results Test, or Screaming Frog instead — they render JavaScript and capture dynamically-injected markup. See the Schema Markup Detection Limitation section above.

**Paid Tools** (if available)
- Screaming Frog
- Ahrefs / Semrush
- Sitebulb
- ContentKing

---

## Task-Specific Questions

1. What pages/keywords matter most?
2. Do you have Search Console access?
3. Any recent changes or migrations?
4. Who are your top organic competitors?
5. What's your current organic traffic baseline?

---

## Related Skills

- **ai-seo**: For optimizing content for AI search engines (AEO, GEO, LLMO)
- **programmatic-seo**: For building SEO pages at scale
- **site-architecture**: For page hierarchy, navigation design, and URL structure
- **schema-markup**: For implementing structured data
- **page-cro**: For optimizing pages for conversion (not just ranking)
- **analytics-tracking**: For measuring SEO performance

## Supplemental Guidance: Seo

# SEO: Universal SEO Analysis Skill

**Invocation:** `/seo $1 $2` where `$1` is the command and `$2` is the URL or argument.

**Scripts:** Located at the plugin root `scripts/` directory.

Comprehensive SEO analysis across all industries (SaaS, local services,
e-commerce, publishers, agencies). Orchestrates 15 specialized sub-skills and 10 subagents
(+ 2 optional extension sub-skills: seo-dataforseo and seo-image-gen).

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/seo audit <url>` | Full website audit with parallel subagent delegation |
| `/seo page <url>` | Deep single-page analysis |
| `/seo sitemap <url or generate>` | Analyze or generate XML sitemaps |
| `/seo schema <url>` | Detect, validate, and generate Schema.org markup |
| `/seo images <url>` | Image optimization analysis |
| `/seo technical <url>` | Technical SEO audit (9 categories) |
| `/seo content <url>` | E-E-A-T and content quality analysis |
| `/seo geo <url>` | AI Overviews / Generative Engine Optimization |
| `/seo plan <business-type>` | Strategic SEO planning |
| `/seo programmatic [url\|plan]` | Programmatic SEO analysis and planning |
| `/seo competitor-pages [url\|generate]` | Competitor comparison page generation |
| `/seo local <url>` | Local SEO analysis (GBP, citations, reviews, map pack) |
| `/seo maps [command] [args]` | Maps intelligence (geo-grid, GBP audit, reviews, competitors) |
| `/seo hreflang [url]` | Hreflang/i18n SEO audit and generation |
| `/seo google [command] [url]` | Google SEO APIs (GSC, PageSpeed, CrUX, Indexing, GA4) |
| `/seo dataforseo [command]` | Live SEO data via DataForSEO (extension) |
| `/seo image-gen [use-case] <description>` | AI image generation for SEO assets (extension) |

## Orchestration Logic

When the user invokes `/seo audit`, delegate to subagents in parallel:
1. Detect business type (SaaS, local, ecommerce, publisher, agency, other)
2. Spawn subagents: seo-technical, seo-content, seo-schema, seo-sitemap, seo-performance, seo-visual, seo-geo
3. If Google API credentials detected (`python scripts/google_auth.py --check`), also spawn seo-google agent
4. If local business detected, also spawn seo-local agent
5. If local business detected AND DataForSEO MCP available, also spawn seo-maps agent
6. Collect results and generate unified report with SEO Health Score (0-100)
7. Create prioritized action plan (Critical -> High -> Medium -> Low)
8. **Offer PDF report**: "Generate a professional PDF report? Use `/seo google report full`"

For individual commands, load the relevant sub-skill directly.
After any analysis command completes, offer to generate a PDF report via `scripts/google_report.py`.

## Industry Detection

Detect business type from homepage signals:
- **SaaS**: pricing page, /features, /integrations, /docs, "free trial", "sign up"
- **Local Service**: phone number, address, service area, "serving [city]", Google Maps embed --> auto-suggest `/seo local` for deeper analysis
- **E-commerce**: /products, /collections, /cart, "add to cart", product schema
- **Publisher**: /blog, /articles, /topics, article schema, author pages, publication dates
- **Agency**: /case-studies, /portfolio, /industries, "our work", client logos

## Quality Gates

Read `references/quality-gates.md` for thin content thresholds per page type.
Hard rules:
- WARNING at 30+ location pages (enforce 60%+ unique content)
- HARD STOP at 50+ location pages (require user justification)
- Never recommend HowTo schema (deprecated Sept 2023)
- FAQ schema for Google rich results: only government and healthcare sites (Aug 2023 restriction); existing FAQPage on commercial sites -> flag Info priority (not Critical), noting AI/LLM citation benefit; adding new FAQPage -> not recommended for Google benefit
- All Core Web Vitals references use INP, never FID

## Reference Files

Load these on-demand as needed (do NOT load all at startup):
- `references/cwv-thresholds.md`: Current Core Web Vitals thresholds and measurement details
- `references/schema-types.md`: All supported schema types with deprecation status
- `references/eeat-framework.md`: E-E-A-T evaluation criteria (Sept 2025 QRG update)
- `references/quality-gates.md`: Content length minimums, uniqueness thresholds
- `references/local-seo-signals.md`: Local ranking factors, review benchmarks, citation tiers, GBP status
- `references/local-schema-types.md`: LocalBusiness subtypes, industry-specific schema and citation sources

Maps-specific references (loaded by seo-maps skill, not at startup):
- `references/maps-geo-grid.md`, `references/maps-gbp-checklist.md`, `references/maps-api-endpoints.md`, `references/maps-free-apis.md`

## Scoring Methodology

### SEO Health Score (0-100)
Weighted aggregate of all categories:

| Category | Weight |
|----------|--------|
| Technical SEO | 22% |
| Content Quality | 23% |
| On-Page SEO | 20% |
| Schema / Structured Data | 10% |
| Performance (CWV) | 10% |
| AI Search Readiness | 10% |
| Images | 5% |

### Priority Levels
- **Critical**: Blocks indexing or causes penalties (immediate fix required)
- **High**: Significantly impacts rankings (fix within 1 week)
- **Medium**: Optimization opportunity (fix within 1 month)
- **Low**: Nice to have (backlog)

## Sub-Skills

This skill orchestrates 15 specialized sub-skills (+ 2 extensions):

1. **seo-audit** -- Full website audit with parallel delegation
2. **seo-page** -- Deep single-page analysis
3. **seo-technical** -- Technical SEO (9 categories)
4. **seo-content** -- E-E-A-T and content quality
5. **seo-schema** -- Schema markup detection and generation
6. **seo-images** -- Image optimization
7. **seo-sitemap** -- Sitemap analysis and generation
8. **seo-geo** -- AI Overviews / GEO optimization
9. **seo-plan** -- Strategic planning with templates
10. **seo-programmatic** -- Programmatic SEO analysis and planning
11. **seo-competitor-pages** -- Competitor comparison page generation
12. **seo-hreflang** -- Hreflang/i18n SEO audit and generation
13. **seo-local** -- Local SEO (GBP, NAP, citations, reviews, local schema, multi-location)
14. **seo-maps** -- Maps intelligence (geo-grid, GBP audit, reviews, competitor radius)
15. **seo-google** -- Google SEO APIs (GSC, PageSpeed, CrUX, Indexing API, GA4)
16. **seo-dataforseo** -- Live SEO data via DataForSEO MCP (extension)
17. **seo-image-gen** -- AI image generation for SEO assets via Gemini (extension)

## Subagents

For parallel analysis during audits:
- `seo-technical` -- Crawlability, indexability, security, CWV
- `seo-content` -- E-E-A-T, readability, thin content
- `seo-schema` -- Detection, validation, generation
- `seo-sitemap` -- Structure, coverage, quality gates
- `seo-performance` -- Core Web Vitals measurement
- `seo-visual` -- Screenshots, mobile testing, above-fold
- `seo-geo` -- AI crawler access, llms.txt, citability, brand mention signals
- `seo-local` -- GBP signals, NAP consistency, reviews, local schema, industry-specific local factors (conditional: spawned when Local Service detected)
- `seo-maps` -- Geo-grid rank tracking, GBP audit, review intelligence, competitor radius mapping (conditional: spawned when Local Service detected AND DataForSEO MCP available)
- `seo-google` -- CWV field data, URL indexation status, organic traffic trends (conditional: spawned when Google API credentials detected)
- `seo-dataforseo` -- Live SERP, keyword, backlink, local SEO data (extension, optional)
- `seo-image-gen` -- SEO image audit and generation plan (extension, optional)

## Error Handling

| Scenario | Action |
|----------|--------|
| Unrecognized command | List available commands from the Quick Reference table. Suggest the closest matching command. |
| URL unreachable | Report the error and suggest the user verify the URL. Do not attempt to guess site content. |
| Sub-skill fails during audit | Report partial results from successful sub-skills. Clearly note which sub-skill failed and why. Suggest re-running the failed sub-skill individually. |
| Ambiguous business type detection | Present the top two detected types with supporting signals. Ask the user to confirm before proceeding with industry-specific recommendations. |

## Imported Reference

# SEO Skill (Agentic / Agentic)

LLM-first SEO analysis skill with 16 specialized sub-skills, 10 specialist agents, and 33 scripts for website, blog, and GitHub repository optimization.

## Deterministic Trigger Mapping

For prompt reliability in agent IDEs, map common user wording to a fixed workflow:

- If user says `perform seo analysis on <url>` (or similar generic SEO request with a URL), treat it as a **single-URL full audit**.
- If no explicit sub-skill is specified, run the full/page audit path with **LLM-first reasoning** and script-backed evidence.
- For full/page audits, always produce:
  - `FULL-AUDIT-REPORT.md` (detailed findings)
  - `ACTION-PLAN.md` (prioritized fixes)
- If `generate_report.py` is run, also return the saved HTML path (for example `SEO-REPORT.html`).

## Available Commands

| Command | Sub-Skill | Description |
|---------|-----------|-------------|
| `seo audit <url>` | [seo-audit](resources/skills/seo-audit.md) | Full website audit with scoring |
| `seo page <url>` | [seo-page](resources/skills/seo-page.md) | Deep single-page analysis |
| `seo technical <url>` | [seo-technical](resources/skills/seo-technical.md) | Technical SEO checks |
| `seo content <url>` | [seo-content](resources/skills/seo-content.md) | Content quality & E-E-A-T |
| `seo schema <url>` | [seo-schema](resources/skills/seo-schema.md) | Schema detection/validation/generation |
| `seo sitemap <url>` | [seo-sitemap](resources/skills/seo-sitemap.md) | Sitemap analysis & generation |
| `seo images <url>` | [seo-images](resources/skills/seo-images.md) | Image optimization audit |
| `seo geo <url>` | [seo-geo](resources/skills/seo-geo.md) | AI search optimization (GEO) |
| `seo programmatic <url>` | [seo-programmatic](resources/skills/seo-programmatic.md) | Programmatic SEO safeguards |
| `seo competitors <url>` | [seo-competitor-pages](resources/skills/seo-competitor-pages.md) | Comparison/alternatives pages |
| `seo hreflang <url>` | [seo-hreflang](resources/skills/seo-hreflang.md) | International SEO validation |
| `seo plan <url>` | [seo-plan](resources/skills/seo-plan.md) | Strategic SEO planning |
| `seo github <repo_or_url>` | [seo-github](resources/skills/seo-github.md) | GitHub repository discoverability, README, topics, community health, and traffic archival |
| `seo article <url>` | [seo-article](resources/skills/seo-article.md) | Article data extraction & LLM optimization |
| `seo links <url>` | [seo-links](resources/skills/seo-links.md) | External backlink profile & link health |
| `seo aeo <url>` | [seo-aeo](resources/skills/seo-aeo.md) | Answer Engine Optimization (Featured Snippets, PAA, Knowledge Panel) |

---

## Orchestration Logic

When the user requests SEO analysis, follow this routing:

### Step 1 — Identify the Task

Parse the user's request to determine which sub-skill(s) to activate:

- **Full audit**: Read `resources/skills/seo-audit.md` — crawl multiple pages, delegate to agents, score and report
- **Single page**: Read `resources/skills/seo-page.md` — deep dive on one URL
- **Specific area**: Read the matching `resources/skills/seo-*.md` file
- **Strategic plan**: Read `resources/skills/seo-plan.md` and the matching `resources/templates/*.md` for the detected industry
- **GitHub repository SEO**: Read `resources/skills/seo-github.md` and use GitHub scripts with `--provider auto` for API/`gh` fallback.
- **Generic `perform seo analysis on <url>` request**: treat as single-page full audit, read `resources/skills/seo-page.md`, and generate `FULL-AUDIT-REPORT.md` + `ACTION-PLAN.md`.

### Step 2 — Collect Evidence

**Primary method (LLM-first)** — use the built-in `read_url_content` tool first:
```
read_url_content(url)  →  returns parsed HTML content directly
```
Use this as the baseline evidence for reasoning.

**Deterministic verification (recommended when script execution is available)**:
```bash
# Fetch/parse raw HTML for structured checks
python3 <SKILL_DIR>/scripts/fetch_page.py <url> --output /tmp/page.html
python3 <SKILL_DIR>/scripts/parse_html.py /tmp/page.html --url <url> --json

# Optional: generate shareable HTML dashboard artifact
python3 <SKILL_DIR>/scripts/generate_report.py <url> --output SEO-REPORT.html
```

> **Do not use third-party mirrors (e.g., `r.jina.ai`) as primary evidence when direct site fetch or bundled scripts are available.**
> `<SKILL_DIR>` = absolute path to this skill directory (the folder containing this SKILL.md).

### Step 3 — Perform LLM-First Analysis

Use the LLM as the primary SEO analyst:

1. Synthesize evidence from page content, metadata, and optional script outputs.
2. Produce findings with explicit proof:
   - `Finding`
   - `Evidence` (specific element, metric, or snippet)
   - `Impact` (why it matters for ranking/indexing/UX)
   - `Fix` (clear implementation step)
3. Prioritize by impact and implementation effort.
4. Separate confirmed issues, likely issues, and unknowns (missing data).

Always read and apply `resources/references/llm-audit-rubric.md` to keep scoring, severity, confidence, and output structure consistent across audit types.

### Step 4 — Run Baseline Verification Scripts (When execution is available)

For full/page audits, run baseline checks to avoid hypothesis-only reporting. Do not replace LLM reasoning with script-only scoring.

```bash
# Check robots.txt and AI crawler management
python3 <SKILL_DIR>/scripts/robots_checker.py <url>

# Check llms.txt for AI search readiness
python3 <SKILL_DIR>/scripts/llms_txt_checker.py <url>

# Get Core Web Vitals from PageSpeed Insights (free API, no key needed)
python3 <SKILL_DIR>/scripts/pagespeed.py <url> --strategy mobile

# Check security headers (HSTS, CSP, X-Frame-Options, etc.)
python3 <SKILL_DIR>/scripts/security_headers.py <url>

# Detect broken links on a page (404s, timeouts, connection errors)
python3 <SKILL_DIR>/scripts/broken_links.py <url> --workers 5

# Trace redirect chains, detect loops and mixed HTTP/HTTPS
python3 <SKILL_DIR>/scripts/redirect_checker.py <url>

# Analyze readability from fetched HTML (Flesch-Kincaid, grade level, sentence stats)
python3 <SKILL_DIR>/scripts/readability.py /tmp/page.html --json

# Validate Open Graph and Twitter Card meta tags
python3 <SKILL_DIR>/scripts/social_meta.py <url>

# Analyze internal link structure, find orphan pages
python3 <SKILL_DIR>/scripts/internal_links.py <url> --depth 1 --max-pages 20

# Extract article content and perform keyword research for LLM-driven optimization
python3 <SKILL_DIR>/scripts/article_seo.py <url> --keyword "<optional_target_keyword>" --json

# GitHub repository SEO (provider fallback: auto|api|gh)
# Auth setup (choose one):
# export GITHUB_TOKEN="ghp_xxx"   # or export GH_TOKEN="ghp_xxx"
# gh auth login -h github.com && gh auth status -h github.com
python3 <SKILL_DIR>/scripts/github_repo_audit.py --repo <owner/repo> --provider auto --json
python3 <SKILL_DIR>/scripts/github_readme_lint.py README.md --json
python3 <SKILL_DIR>/scripts/github_community_health.py --repo <owner/repo> --provider auto --json
# Benchmark/competitor inputs should be provided by LLM/web-search discovery when possible.
# If omitted, github_seo_report.py auto-derives repo-specific benchmark queries.
python3 <SKILL_DIR>/scripts/github_search_benchmark.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --json
python3 <SKILL_DIR>/scripts/github_competitor_research.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --top-n 6 --json
python3 <SKILL_DIR>/scripts/github_competitor_research.py --repo <owner/repo> --competitor <owner/repo> --competitor <owner/repo> --provider auto --json
python3 <SKILL_DIR>/scripts/github_traffic_archiver.py --repo <owner/repo> --provider auto --archive-dir .github-seo-data --json
python3 <SKILL_DIR>/scripts/github_seo_report.py --repo <owner/repo> --provider auto --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md --json
# Optional: increase/reduce auto-derived query volume (default: 6)
# python3 <SKILL_DIR>/scripts/github_seo_report.py --repo <owner/repo> --provider auto --auto-query-max 8 --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md --json
```

If a check fails due network, DNS, permissions, or API rate limits:
- Report it explicitly as an **environment limitation**, not a confirmed site issue.
- Keep confidence as `Hypothesis` for impacted categories.
- Continue with available evidence instead of stopping the audit.
- Do not enter repeated fallback loops. Retry a failed source at most once, then finalize the audit.
- Do not pivot into repeated web-search scraping loops for the same URL.

**Visual analysis** (requires Playwright — use `conda activate pentest` if available):
```bash
# Capture screenshots (desktop, laptop, tablet, mobile)
python3 <SKILL_DIR>/scripts/capture_screenshot.py <url> --all

# Analyze visual layout, above-the-fold, mobile responsiveness
python3 <SKILL_DIR>/scripts/analyze_visual.py <url> --json
```

**HTML Report Generator** — generates a self-contained interactive HTML dashboard:
```bash
# Generate full SEO report (runs scripts automatically, saves HTML to PWD)
python3 <SKILL_DIR>/scripts/generate_report.py <url>
python3 <SKILL_DIR>/scripts/generate_report.py <url> --output custom-report.html
```

### Step 5 — Delegate to Specialist Agents

For comprehensive audits, read the relevant agent file from `resources/agents/` to adopt the specialist role:

| Agent | File | Focus Area |
|-------|------|------------|
| Technical SEO | [seo-technical.md](resources/agents/seo-technical.md) | Crawlability, indexability, security, URLs, mobile, CWV, JS rendering |
| Content Quality | [seo-content.md](resources/agents/seo-content.md) | E-E-A-T assessment, content metrics, AI content detection |
| Performance | [seo-performance.md](resources/agents/seo-performance.md) | Core Web Vitals (LCP, INP, CLS), optimization recommendations |
| Schema Markup | [seo-schema.md](resources/agents/seo-schema.md) | Detection, validation, generation of JSON-LD structured data |
| Sitemap | [seo-sitemap.md](resources/agents/seo-sitemap.md) | XML sitemap validation, generation, quality gates |
| Visual Analysis | [seo-visual.md](resources/agents/seo-visual.md) | Screenshots, above-the-fold, responsiveness, layout |
| Verifier (global) | [seo-verifier.md](resources/agents/seo-verifier.md) | Deduplicate findings, suppress contradictions, and validate evidence relevance before final report |

### Step 6 — Apply Quality Gates

Reference the quality standards in `resources/references/`:

- **Content minimums**: Read [quality-gates.md](resources/references/quality-gates.md) for word counts, unique content %, title/meta requirements
- **Schema validation**: Read [schema-types.md](resources/references/schema-types.md) for active/deprecated/restricted types
- **Core Web Vitals**: Read [cwv-thresholds.md](resources/references/cwv-thresholds.md) for current metric thresholds
- **E-E-A-T framework**: Read [eeat-framework.md](resources/references/eeat-framework.md) for scoring criteria
- **Google reference**: Read [google-seo-reference.md](resources/references/google-seo-reference.md) for quick reference
- **LLM report rubric**: Read [llm-audit-rubric.md](resources/references/llm-audit-rubric.md) for mandatory evidence format, confidence labels, and output contract

### Step 6.5 — Verify Findings (All Workflows)

Before writing final reports, run verification:

```bash
python3 <SKILL_DIR>/scripts/finding_verifier.py --findings-json <raw_findings.json> --json
```

Use verified output for final report tables, not raw findings.

### Step 7 — Score and Report

Use numeric scores as guidance, not as a replacement for evidence quality and judgment.

#### Default Scoring Weights (Full Audit)

> **Canonical source of truth** — These weights are defined here and in `resources/skills/seo-audit.md`.
> Do not modify weights in individual sub-skill files; update only these two locations to keep scores consistent.

| Category | Weight |
|----------|--------|
| Technical SEO | 25% |
| Content Quality | 20% |
| On-Page SEO | 15% |
| Schema / Structured Data | 15% |
| Performance (CWV) | 10% |
| Image Optimization | 10% |
| AI Search Readiness (GEO) | 5% |

> If using `scripts/generate_report.py`, the automated dashboard uses script-level category weights defined in that script. Keep the narrative audit LLM-first and evidence-first.

### Step 8 — Mandatory Deliverables

For `seo audit`, `seo page`, and generic `perform seo analysis on <url>` flows:

1. Create `FULL-AUDIT-REPORT.md` in the current working directory at the start of the audit, then update it as evidence is collected.
2. Create `ACTION-PLAN.md` in the current working directory at the start of the audit, then update it with prioritized fixes.
3. If HTML dashboard was generated, include its exact saved path (for example `SEO-REPORT.html` or an absolute path).
4. In the final response, explicitly list generated artifacts and paths.
5. If technical checks are blocked by environment limits, still write both markdown files and include an "Environment Limitations" section.

#### Score Interpretation
| Score | Rating |
|-------|--------|
| 90-100 | Excellent |
| 70-89 | Good |
| 50-69 | Needs Improvement |
| 30-49 | Poor |
| 0-29 | Critical |

---

## Industry Detection

When running `seo plan`, detect the business type and load the matching template:

| Industry | Template File |
|----------|---------------|
| SaaS / Software | [saas.md](resources/templates/saas.md) |
| Local Service Business | [local-service.md](resources/templates/local-service.md) |
| E-commerce / Retail | [ecommerce.md](resources/templates/ecommerce.md) |
| Publisher / Media | [publisher.md](resources/templates/publisher.md) |
| Agency / Consultancy | [agency.md](resources/templates/agency.md) |
| Other / Generic | [generic.md](resources/templates/generic.md) |

**Detection signals:**
- SaaS: pricing page, feature pages, /docs, /api, trial/demo CTAs
- Local: address, phone, Google Business Profile, service area pages
- E-commerce: product pages, cart, checkout, /collections, /categories
- Publisher: article dates, author pages, /news, high content volume
- Agency: case studies, /work, /portfolio, team pages, service offerings

---

## Schema Templates

Pre-built JSON-LD templates are available in [templates.json](resources/schema/templates.json) for:
- **Common**: BlogPosting, Article, Organization, LocalBusiness, BreadcrumbList, WebSite (with SearchAction)
- **Video**: VideoObject, BroadcastEvent, Clip, SeekToAction
- **E-commerce**: ProductGroup (variants), OfferShippingDetails, Certification
- **Other**: SoftwareSourceCode, ProfilePage (E-E-A-T author pages)

---

## Validation Scripts

Two validation scripts are available for CI/CD integration:

### Pre-commit SEO Check
```bash
bash <SKILL_DIR>/scripts/pre_commit_seo_check.sh
```
Checks staged HTML files for: placeholder text in schema, title tag length, missing alt text, deprecated schema types, FID references (should be INP), meta description length.

### Schema Validator
```bash
python3 <SKILL_DIR>/scripts/validate_schema.py <file_path>
```
Validates JSON-LD blocks in HTML files: JSON syntax, @context/@type presence, placeholder text, deprecated/restricted types.

---

## Output Format

All sub-skill reports should use consistent severity levels:
- 🔴 **Critical** — Directly impacts rankings or indexing (fix immediately)
- ⚠️ **Warning** — Optimization opportunity (fix within 1 month)
- ✅ **Pass** — Meets or exceeds standards
- ℹ️ **Info** — Not applicable or informational only

Structure reports as:
1. Summary table with element, value, and severity
2. Detailed findings grouped by category
3. Actionable recommendations ordered by impact

---

## Critical Rules

1. **INP not FID** — FID was removed September 9, 2024. The sole interactivity metric is INP (Interaction to Next Paint). Never reference FID.
2. **FAQ schema is restricted** — FAQPage schema is limited to government and healthcare authority sites only (August 2023). Do NOT recommend for commercial sites.
3. **HowTo schema is deprecated** — Rich results fully removed September 2023. Never recommend.
4. **JSON-LD only** — Always use `<script type="application/ld+json">`. Never recommend Microdata or RDFa.
5. **E-E-A-T everywhere** — As of December 2025, E-E-A-T applies to ALL competitive queries, not just YMYL.
6. **Mobile-first is complete** — 100% mobile-first indexing since July 5, 2024.
7. **Location page limits** — Warning at 30+ pages, hard stop at 50+ pages. Enforce unique content requirements.
8. **AI crawler management** — Check robots.txt for GPTBot, provider-specific AI crawler, PerplexityBot, Applebot-Extended, Google-Extended, Bytespider, CCBot.
9. **LLM-first, resilient pipeline** — Start by reading the page with `read_url_content`, then always run relevant scripts for structured evidence. Scripts are the **preferred** evidence source — use them actively. However, if any script fails (timeout, network, parsing), the LLM MUST still produce a complete analysis using its own reasoning (confidence: `Likely`). Never block a report on a single script failure.
10. **Always produce file artifacts for audit flows** — `FULL-AUDIT-REPORT.md` and `ACTION-PLAN.md` are required outputs for full/page audit requests.
11. **Bound evidence retries** — Avoid long search/retry loops. If core checks fail due DNS/network, finalize promptly with confidence labels and file outputs.
12. **Avoid redundant web fallbacks** — If direct fetch/scripts fail and one fallback also fails, stop retrying and finish the report with explicit limitations.
13. **Signal freshness tracking** — Every reference file should contain a `<!-- Updated: YYYY-MM-DD -->` comment. Flag any reference file older than 90 days for review. When Google announces algorithm changes, verify affected reference files within 7 days. Key dates to track: core updates (quarterly), schema deprecations (schema-types.md), CWV threshold changes (cwv-thresholds.md).

---

## Dependencies

### Optional Script Dependencies
- Python 3.8+
- `requests` (for network analysis scripts)
- `beautifulsoup4` (for HTML parsing scripts)
- Playwright (for `capture_screenshot.py` and `analyze_visual.py`)
  ```bash
  pip install playwright && playwright install chromium
  ```
  Or if using conda: `conda activate pentest` (if Playwright is pre-installed)

### Install Script Dependencies
```bash
pip install requests beautifulsoup4
```

## Supplemental Guidance: Seo Page

# Single Page Analysis

## What to Analyze

### On-Page SEO
- Title tag: 50-60 characters, includes primary keyword, unique
- Meta description: 150-160 characters, compelling, includes keyword
- H1: exactly one, matches page intent, includes keyword
- H2-H6: logical hierarchy (no skipped levels), descriptive
- URL: short, descriptive, hyphenated, no parameters
- Internal links: sufficient, relevant anchor text, no orphan pages
- External links: to authoritative sources, reasonable count

### Content Quality
- Word count vs page type minimums (see quality-gates.md)
- Readability: Flesch Reading Ease score, grade level
- Keyword density: natural (1-3%), semantic variations present
- E-E-A-T signals: author bio, credentials, first-hand experience markers
- Content freshness: publication date, last updated date

### Technical Elements
- Canonical tag: present, self-referencing or correct
- Meta robots: index/follow unless intentionally blocked
- Open Graph: og:title, og:description, og:image, og:url
- Twitter Card: twitter:card, twitter:title, twitter:description
- Hreflang: if multi-language, correct implementation

### Schema Markup
- Detect all types (JSON-LD preferred)
- Validate required properties
- Identify missing opportunities
- NEVER recommend HowTo (deprecated) or FAQ (restricted to gov/health)

### Images
- Alt text: present, descriptive, includes keywords where natural
- File size: flag >200KB (warning), >500KB (critical)
- Format: recommend WebP/AVIF over JPEG/PNG
- Dimensions: width/height set for CLS prevention
- Lazy loading: loading="lazy" on below-fold images

### Core Web Vitals (reference only, not measurable from HTML alone)
- Flag potential LCP issues (huge hero images, render-blocking resources)
- Flag potential INP issues (heavy JS, no async/defer)
- Flag potential CLS issues (missing image dimensions, injected content)

## Output

### Page Score Card
```
Overall Score: XX/100

On-Page SEO:     XX/100  ████████░░
Content Quality: XX/100  ██████████
Technical:       XX/100  ███████░░░
Schema:          XX/100  █████░░░░░
Images:          XX/100  ████████░░
```

### Issues Found
Organized by priority: Critical -> High -> Medium -> Low

### Recommendations
Specific, actionable improvements with expected impact

### Schema Suggestions
Ready-to-use JSON-LD code for detected opportunities

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `serp_organic_live_advanced` for real SERP positions and `backlinks_summary` for backlink data and spam scores.

## Error Handling

| Scenario | Action |
|----------|--------|
| URL unreachable (DNS failure, connection refused) | Report the error clearly. Do not guess page content. Suggest the user verify the URL and try again. |
| Page requires authentication (401/403) | Report that the page is behind authentication. Suggest the user provide the rendered HTML directly or a publicly accessible URL. |
| JavaScript-rendered content (empty body in HTML) | Note that key content may be rendered client-side. Analyze the available HTML and flag that results may be incomplete. Suggest using a browser-rendered snapshot if available. |

## Supplemental Guidance: Seo Local

# Local SEO Analysis (March 2026)

## Key Statistics

| Metric | Value | Source |
|--------|-------|--------|
| GBP signals share of local pack weight | 32% | Whitespark 2026 |
| Proximity share of ranking variance | 55.2% | Search Atlas ML study |
| Review signals share (up from 16%) | ~20% | Whitespark 2026 |
| Google searches seeking local info | 46% | Industry data |
| Mobile "near me" searches leading to visit in 24h | 76% | Google confirmed |
| ChatGPT/AI usage for local recommendations | 45% (up from 6%) | BrightLocal LCRS 2026 |
| ChatGPT local conversion rate | 15.9% | Seer Interactive |
| Google organic local conversion rate | 1.76% | Seer Interactive |
| Local pack ads growth (Jan 2025 to Jan 2026) | 1% to 22% | Sterling Sky |

---

## Business Type Detection

Detect from page signals before analysis. This determines which checks apply.

### Brick-and-Mortar
- Physical street address visible in page content or footer
- Google Maps embed with pin/directions
- "Visit us at", "Located at", "Come see us"
- Structured address in LocalBusiness schema

### Service Area Business (SAB)
- No visible physical address
- Service area mentions: "serving [city/region]", "service area includes"
- "We come to you", "On-site service", "Mobile [service]"
- `areaServed` in schema without `address.streetAddress`

### Hybrid
- Both physical address AND service area language present
- "Visit our showroom" combined with "We also serve [areas]"

**Impact on checks**: SABs skip embedded map verification and physical address consistency. Brick-and-mortar gets full NAP + map checks.

---

## Industry Vertical Detection

Detect from page signals and GBP category patterns. Routes to industry-specific checks from `references/local-schema-types.md`.

| Vertical | Detection Signals |
|----------|------------------|
| **Restaurant** | /menu, menu items, reservations, cuisine types, food ordering, "dine-in", "takeout" |
| **Healthcare** | insurance accepted, patients, appointments, NPI, medical terms, "Dr.", HIPAA notice |
| **Legal** | attorney, lawyer, practice areas, bar admission, case results, "free consultation" |
| **Home Services** | service area, emergency service, "free estimate", licensed/insured/bonded, "24/7" |
| **Real Estate** | listings, MLS, properties for sale/rent, agent bio, brokerage, "open house" |
| **Automotive** | inventory, VIN, test drive, dealership, service department, "new/used/certified" |

If no vertical detected, use generic `LocalBusiness` analysis path.

---

## Analysis Dimensions

### 1. GBP Signals (25%)

Primary category is the **single most important local pack factor** (Whitespark #1, score: 193). Incorrect primary category is the **#1 negative factor** (score: 176).

**Check for:**
- GBP embed or reference detectable on page (Maps iframe, place ID, reviews widget)
- Primary category appropriateness (infer from page content vs visible GBP data)
- Evidence of secondary categories (optimal: 4 additional per BrightLocal)
- GBP posts presence (no direct ranking impact per WebFX, but triggers Post Justifications)
- Photos/video evidence (45% more direction requests with photos, Agency Jet)
- Q&A content (deprecated Dec 2025, replaced by Ask Maps Gemini AI -- recommend recreating Q&A content as FAQ sections on website; GBP removed existing Q&A with no export available)
- Google Verified badge eligibility (replaced Guaranteed/Screened in Oct 2025)
- GBP link URL strategy: do NOT link to strongest website page (Sterling Sky Diversity Update -- risks suppressing organic rankings)
- Business hours visibility on page (businesses open at search time rank higher, factor #5)

**Scoring guide:**
- Full: GBP embed present, category signals align, posts active, photos present
- Partial: Some GBP signals present but incomplete
- Low: No visible GBP integration on website

### 2. Reviews & Reputation (20%)

Review velocity matters more than total count. The **18-day rule** (Sterling Sky): rankings cliff if no new reviews for 3 weeks.

**Check for:**
- Total Google review count visible on page or schema (magic threshold: 10, Sterling Sky)
- Star rating (31% of consumers only use 4.5+, 68% only use 4+, BrightLocal 2026)
- Review recency indicators (74% only care about reviews in last 3 months)
- `aggregateRating` in schema (ratingValue, reviewCount, bestRating)
- Third-party review presence (consumers use average of 6 review sites, BrightLocal 2026)
- Owner response patterns (88% would use business that responds, BrightLocal)
- Review gating detection: any pre-screening of satisfaction before directing to review platform is prohibited by Google (fake engagement policy) and FTC ($53,088/violation)

**Industry-specific:**
- Healthcare: HIPAA prohibits confirming/denying reviewer is a patient in responses
- Legal: attorney-client privilege considerations in review responses

**Scoring guide:**
- Full: 10+ reviews, 4.5+ stars, recent activity, owner responses, multi-platform presence
- Partial: Some reviews but gaps in recency, rating, or response rate
- Low: <10 reviews, no recent activity, no responses, single platform only

### 3. Local On-Page SEO (20%)

Dedicated service pages = **#1 local organic factor AND #2 AI visibility factor** (Whitespark 2026).

**Check for:**
- Title tag contains city/service keywords
- H1 tag with local intent (city + service)
- NAP (Name, Address, Phone) visible in page HTML (footer, contact section, header)
- Dedicated service pages (one page per core service)
- Location page quality for multi-location sites:
  - **>60-70% unique content** minimum (industry consensus, no Google-confirmed threshold)
  - **Swap test**: if you can swap the city name and content still makes sense, it's a doorway page (RicketyRoo method). HVAC company lost 80% rankings + 63% traffic after March 2024 Core Update for this pattern
  - Local photos, area-specific testimonials, local FAQs
- Embedded Google Map (geographic signal reinforcement, not direct ranking factor -- lazy-load to mitigate speed impact)
- Click-to-call button (`tel:` link) and contact form above the fold
- Internal linking architecture: hub-and-spoke, every critical page within 3 clicks of homepage
- 2-5 contextual internal links per 1,000 words with descriptive anchor text

**Multi-location specific:**
- Store locator with individual crawlable URLs (SSR/SSG preferred over CSR)
- Subdirectory structure: `domain.com/locations/city-name/` (subdirectories consolidate link equity better, Bruce Clay: 50%+ traffic lift)
- Each location page has unique LocalBusiness schema with `@id`

**Scoring guide:**
- Full: City in title + H1, NAP visible, dedicated service pages, no doorway patterns, good internal linking
- Partial: Some local signals but missing service pages or doorway page risk
- Low: Generic title/H1, NAP not visible, thin location pages

### 4. NAP Consistency & Citations (15%)

Citations declining for traditional pack rankings but **3 of top 5 AI visibility factors are citation-related** (Whitespark 2026). Google's July 2025 documentation update removed "directories" from prominence definition.

**Check for:**
- NAP extraction: compare Name, Address, Phone from:
  1. Visible page HTML (footer, contact page)
  2. LocalBusiness JSON-LD schema
  3. Any visible GBP data
  - Flag any discrepancies between these three sources
- Citation presence on Tier 1 directories (check via WebFetch or site: search patterns):
  - Google Business Profile signals on page
  - Yelp: `site:yelp.com "Business Name"`
  - BBB: `site:bbb.org "Business Name"`
  - Facebook business page references
- Apple Business Connect awareness (usage doubled to 27%, BrightLocal 2026 -- recommend claiming)
- Bing Places awareness (powers ChatGPT, Copilot, Alexa -- recommend claiming and optimizing)
- Industry-specific directory recommendations: load `references/local-schema-types.md` for per-vertical citation sources
- Data aggregator awareness: Data Axle, Foursquare, Neustar/TransUnion (recommend submission for downstream distribution)

**Scoring guide:**
- Full: Consistent NAP across page/schema, Tier 1 citations detected, industry directories present
- Partial: NAP present but inconsistencies, some citations missing
- Low: NAP discrepancies, no detectable citations, no schema address

### 5. Local Schema Markup (10%)

Schema is NOT a direct ranking factor (John Mueller confirmed). But enables rich results (43% CTR increase, Webstix case study) and helps AI systems parse business information.

**Check for:**
- LocalBusiness schema presence (extract JSON-LD blocks)
- Required properties: `name`, `address` with PostalAddress sub-properties
- Recommended properties: `geo` (minimum 5 decimal places, Confirmed), `openingHoursSpecification`, `telephone`, `url`, `priceRange` (<100 chars), `image`, `aggregateRating`
- **Correct subtype for industry** -- load `references/local-schema-types.md`:
  - Restaurant using `Restaurant` not generic `LocalBusiness`
  - Legal using `LegalService` not deprecated `Attorney`
  - Auto dealer using `AutoDealer` not deprecated `VehicleListing`
  - Healthcare using `MedicalClinic`/`Hospital`/`Dentist` not generic `MedicalBusiness`
- SAB-specific: `areaServed` with named cities (recommended, not in Google's official list but Schema.org supported)
- Multi-location: each location page has own LocalBusiness with unique `@id`, linked via `branchOf` to Organization on homepage
- Industry-specific schema patterns (per `references/local-schema-types.md`):
  - Restaurant: Menu + MenuSection + MenuItem + ReserveAction
  - Healthcare: Physician (Person) + MedicalSpecialty + sameAs to NPI
  - Legal: LegalService + Person + Service (practice areas)
  - Home Services: Subtype + areaServed + Service
  - Real Estate: RealEstateAgent + Person + RealEstateListing
  - Automotive: AutoDealer + Car + Offer (separate dept schemas)

**Scoring guide:**
- Full: Correct subtype, all recommended properties, industry-specific patterns, valid JSON-LD
- Partial: LocalBusiness present but generic type or missing recommended properties
- Low: No local schema, or schema with errors/placeholder content

### 6. Local Link & Authority Signals (10%)

Links declining for local pack but remain **~26% of local organic ranking** (Whitespark 2026, #2 factor group). "Best of" list placements = **#1 AI visibility citation factor**.

**Check for:**
- Local backlink indicators detectable from page:
  - Chamber of Commerce mentions or links (high Trust Flow, ~80% more consumer visits, GlueUp)
  - BBB accreditation/badge (Google uses BBB for business verification)
  - Local news/press mentions
  - Community involvement signals (sponsorships, local events, partnerships)
- "Best of" list presence (top AI visibility factor per Whitespark 2026)
- Digital PR signals: 66.2% of PR practitioners now track AI citations as KPI (BuzzStream 2026)
- Brand mentions correlate **3x more strongly** with AI visibility than traditional backlinks (Ahrefs: 0.664 vs 0.218 correlation)
- Link velocity benchmark: 5-10 quality local links/month for small businesses (consensus)

**Scoring guide:**
- Full: Local authority signals visible (chamber, BBB, press), community involvement evident
- Partial: Some authority signals but limited local link indicators
- Low: No detectable local authority signals

---

## AI Search Impact on Local

**Do not duplicate seo-geo analysis.** Provide local-specific AI context and recommend `/seo geo <url>` for full analysis.

Key local AI facts:
- AI Overviews appear on up to 68% of local searches (Whitespark Q2 2025)
- ChatGPT converts at 15.9% vs Google organic at 1.76% (Seer Interactive)
- 3 of top 5 AI visibility factors are citation-related (Whitespark 2026)
- ChatGPT does NOT access GBP directly -- sources from Bing index, Yelp, TripAdvisor, BBB, Reddit
- Bing Places is critical: powers ChatGPT, Copilot, Alexa
- AI-powered local packs (mobile US) show only 1-2 businesses, 32% fewer shown (Sterling Sky)

**Recommendation**: Run `/seo geo <url>` for comprehensive AI search visibility analysis including citability scoring, llms.txt check, and brand mention audit.

---

## Reference Files

Load on-demand as needed:
- `references/local-seo-signals.md`: Ranking factors, review benchmarks, citation tiers, GBP feature status, algorithm updates
- `references/local-schema-types.md`: LocalBusiness subtypes by industry, schema patterns, citation sources per vertical

---

## Output

Generate `LOCAL-SEO-ANALYSIS-{domain}.md` with:

1. **Local SEO Score: XX/100** with dimension breakdown table
2. **Business type**: Brick-and-mortar / SAB / Hybrid
3. **Industry vertical detected** + industry-specific findings
4. **GBP optimization checklist** (detected signals vs missing)
5. **Review health snapshot** (rating, count, velocity indicators, response patterns)
6. **NAP consistency audit** (page vs schema discrepancies, cross-source comparison)
7. **Citation presence check** (Tier 1 directory status)
8. **Local schema status** (present/missing/malformed + ready-to-use fix)
9. **Location page quality** (if multi-location: unique content %, doorway risk, store locator)
10. **Top 10 prioritized actions** (Critical > High > Medium > Low)
11. **Limitations disclaimer**: What this analysis could NOT assess (geo-grid ranking, Domain Authority, comprehensive backlinks, GBP Insights data, real-time local pack position) and which paid tools can fill those gaps

---

## Quick Wins

1. Claim and optimize Apple Business Connect (usage doubled to 27%)
2. Claim and optimize Bing Places (powers ChatGPT, Copilot, Alexa)
3. Fix any NAP discrepancies between page, schema, and GBP
4. Add LocalBusiness schema with correct industry subtype
5. Add `geo` coordinates with 5+ decimal precision
6. Ensure phone number uses `tel:` link for click-to-call
7. Add city + service keyword to title tag and H1

## Medium Effort

1. Create dedicated page for each core service (Whitespark: #1 local organic factor)
2. Build review generation strategy maintaining 18-day minimum cadence
3. Submit to three data aggregators (Data Axle, Foursquare, Neustar/TransUnion) for downstream distribution
4. Claim industry-specific directory listings (per vertical recommendations)
5. Add industry-specific schema patterns (Menu for restaurants, Physician for healthcare, etc.)
6. Implement hub-and-spoke internal linking for service/location pages

## High Impact

1. Build local digital PR strategy targeting "best of" lists (#1 AI visibility factor)
2. Develop unique, non-swappable content for each location page (>60% unique)
3. Establish presence on platforms ChatGPT sources from (Yelp, TripAdvisor, BBB, Reddit)
4. Pursue Chamber of Commerce and BBB membership (authority + verification signals)
5. Create community involvement content (sponsorships, local events, partnerships)

---

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `local_business_data` for live GBP data extraction, `google_local_pack_serp` for real-time local pack positions, and `business_listings` for automated citation auditing across directories.

---

## Error Handling

| Scenario | Action |
|----------|--------|
| URL unreachable (DNS failure, connection refused) | Report the error clearly. Do not guess site content. Suggest the user verify the URL and try again. |
| No local signals detected on page | Report that no local business indicators were found. Suggest the user confirm this is a local business and provide the GBP listing URL if available. |
| NAP not found in page HTML | Check schema and meta tags. If still absent, flag as Critical issue. Recommend adding visible NAP to footer and contact page. |
| Industry vertical unclear | Present the top two detected verticals with supporting signals. Ask the user to confirm before applying industry-specific recommendations. |
| Multi-location with 50+ location pages | Apply the quality gates from seo orchestrator: WARNING at 30+ pages (enforce 60%+ unique), HARD STOP at 50+ pages (require user justification before continuing). |

## Supplemental Guidance: Seo Forensic Incident Response

# SEO Forensic Incident Response

You are an expert in forensic SEO incident response. Your goal is to investigate **sudden drops in organic traffic or rankings**, identify the most likely causes, and provide a prioritized remediation plan.

This skill is not a generic SEO audit. It is designed for **incident scenarios**: traffic crashes, suspected penalties, core update impacts, or major technical failures.

## When to Use

Use this skill when:
- You need to understand and resolve a sudden, significant drop in organic traffic or rankings.
- There are signs of a possible penalty, core update impact, major technical regression or other SEO incident.

Do **not** use this skill when:
- You need a routine SEO health check or prioritization of opportunities (use `seo-audit`).
- You are focused on long-term local visibility for legal/professional services (use `local-legal-seo-audit`).

## Initial Incident Triage

Before deep analysis, clarify the incident context:

1. **Incident Description**
   - When did you first notice the drop?
   - Was it sudden (1–3 days) or gradual (weeks)?
   - Which metrics are affected? (sessions, clicks, impressions, conversions)
   - Is the impact site-wide, specific sections, or specific pages?

2. **Data Access**
   - Do you have access to:
     - Google Search Console (GSC)?
     - Web analytics (GA4, Matomo, etc.)?
     - Server logs or CDN logs?
     - Deployment/change logs (Git, CI/CD, CMS release notes)?

3. **Recent Changes Checklist**
   Ask explicitly about the 30–60 days before the drop:
   - Site redesign or theme change
   - URL structure changes or migrations
   - CMS/plugin updates
   - Changes to hosting, CDN, or security tools (WAF, firewalls)
   - Changes to robots.txt, sitemap, canonical tags, or redirects
   - Bulk content edits or content pruning

4. **Business Context**
   - Is this a seasonal niche?
   - Any external events affecting demand?
   - Any previous manual actions or penalties?

---

## Incident Classification Framework

Classify the incident into one or more buckets to guide the investigation:

1. **Algorithm / Core Update Impact**
   - Drop coincides with known Google core update dates
   - Impact skewed toward certain types of queries or content
   - No major technical changes around the same time

2. **Technical / Infrastructure Failure**
   - Indexing/crawlability suddenly impaired
   - Widespread 5xx/4xx errors
   - Robots.txt or meta noindex changes
   - Broken redirects or canonicalization errors

3. **Manual Action / Policy Violation**
   - Manual action message in GSC
   - Sudden, severe drop in branded and non-branded queries
   - History of aggressive link building or spammy tactics

4. **Content / Quality Reassessment**
   - Specific sections or topics hit harder
   - Content thin, outdated, or heavily AI-generated
   - Competitors significantly improved content around the same topics

5. **Demand / Seasonality / External Factors**
   - Search demand drop in the niche (check industry trends)
   - Macro events, regulation changes, or market shifts

---

## Data-Driven Investigation Steps

When you have GSC and analytics access, structure the analysis like a forensic investigation:

### 1. Timeline Reconstruction

- Plot clicks, impressions, CTR, and average position over the last 6–12 months.
- Identify:
  - Exact start of the drop
  - Whether the drop is step-like (sudden) or gradual
  - Whether it affects all countries/devices or specific segments

Use this to narrow likely causes:
- **Step-like drop** → technical issue, manual action, deployment.
- **Gradual slide** → quality issues, competitor improvements, algorithmic re-evaluation.

### 2. Segment Analysis

Segment the impact by:

- **Device**: desktop vs. mobile
- **Country / region**
- **Query type**: branded vs. non-branded
- **Page type**: home, category, product, blog, docs, etc.

Look for patterns:
- Only mobile affected → potential mobile UX, CWV, or mobile-only indexing issue.
- Specific country affected → geo-targeting, hreflang, local factors.
- Non-branded hit harder than branded → often algorithm/quality-related.

### 3. Page-Level Impact

Identify:

- Top pages with largest drop in clicks and impressions.
- New 404s or heavily redirected URLs among previously high-traffic pages.
- Any pages that disappeared from the index or lost most of their ranking queries.

Check for:

- URL changes without proper redirects
- Canonical changes
- Noindex additions
- Template or content changes on those pages

### 4. Technical Integrity Checks

Focus on incident-related technical regressions:

- **Robots.txt**
  - Any recent changes?
  - Are key sections blocked unintentionally?

- **Indexation & Noindex**
  - Sudden spike in “Excluded” or “Noindexed” pages in GSC
  - Important pages with meta noindex or X-Robots-Tag set incorrectly

- **Redirects**
  - New redirect chains or loops
  - HTTP → HTTPS consistency
  - www vs. non-www consistency
  - Migrations without full redirect mapping

- **Server & Availability**
  - Increased 5xx/4xx in logs or GSC
  - Downtime or throttling by security tools
  - Rate-limiting or blocking of Googlebot

- **Core Web Vitals (CWV)**
  - Sudden degradation in CWV affecting large portions of the site
  - Especially on mobile

### 5. Content & Quality Reassessment

When technical is clean, analyze content factors:

- Which topics or content types were hit hardest?
- Is content:
  - Thin, generic, or outdated?
  - Over-optimized or keyword-stuffed?
  - Lacking original data, examples, or experience?

Evaluate against E-E-A-T:

- **Experience**: Does the content show first-hand experience?
- **Expertise**: Is the author qualified and clearly identified?
- **Authoritativeness**: Does the site have references, citations, recognition?
- **Trustworthiness**: Clear about who is behind the site, policies, contact info.

---

## Forensic Hypothesis Building

Use a hypothesis-driven approach instead of listing random issues.

For each plausible cause:

- **Hypothesis**: e.g., “A recent deployment introduced noindex tags on key templates.”
- **Evidence**: Data points from GSC, analytics, logs, code diffs, or screenshots.
- **Impact**: Which sections/pages are affected and by how much.
- **Test / Validation Step**: What check would confirm or refute this hypothesis.
- **Suggested Fix**: Concrete remediation action.

Prioritize hypotheses by:

1. Severity of impact
2. Ease of validation
3. Reversibility (how easy it is to roll back or adjust)

---

## Output Format

Structure your final forensic report clearly:

### Executive Incident Summary

- Incident type classification (technical, algorithmic, manual action, mixed)
- Date range of impact and severity (approximate % drop)
- Top 3–5 likely root causes
- Overall confidence level (Low/Medium/High)

### Evidence-Based Findings

For each key finding, include:

- **Finding**: Short description of what is wrong.
- **Evidence**: Specific metrics, screenshots, logs, or GSC/analytics segments.
- **Likely Cause**: How this could lead to the observed impact.
- **Impact**: High/Medium/Low.
- **Fix**: Concrete, implementable recommendation.

### Prioritized Action Plan

Break down into phases:

1. **Critical Immediate Fixes (0–3 days)**
   - Issues that block crawling, indexing, or basic site availability.
   - Reversals of harmful recent deployments.

2. **Stabilization (3–14 days)**
   - Clean up redirects, canonicals, internal links.
   - Restore or improve critical content and templates.

3. **Recovery & Hardening (2–8 weeks)**
   - Content quality improvements.
   - E-E-A-T enhancements.
   - Technical hardening to prevent recurrence.

4. **Monitoring Plan**
   - Metrics and dashboards to watch.
   - Checkpoints to assess partial recovery.
   - Criteria for closing the incident.

---

## Task-Specific Questions

When helping a user, ask:

1. When exactly did you notice the drop? Any change logs around that date?
2. Do you have GSC and analytics access, and can you share key screenshots or exports?
3. Was there any redesign, migration, or major plugin/CMS update in the last 30–60 days?
4. Is the impact site-wide or concentrated in certain sections, countries, or devices?
5. Have you ever received a manual action or used aggressive link building in the past?

---

## Related Skills

- **seo-audit**: For general SEO health checks outside of incident scenarios.
- **ai-seo**: For optimizing content for AI search experiences.
- **schema-markup**: For implementing structured data after stability is restored.
- **analytics-tracking**: For ensuring measurement is correct post-incident.

## Supplemental Guidance: Seo Fundamentals

---

# SEO Fundamentals

> **Foundational principles for sustainable search visibility.**
> This skill explains _how search engines evaluate quality_, not tactical shortcuts.

---

## 1. E-E-A-T (Quality Evaluation Framework)

E-E-A-T is **not a direct ranking factor**.
It is a framework used by search engines to **evaluate content quality**, especially for sensitive or high-impact topics.

| Dimension             | What It Represents                 | Common Signals                                      |
| --------------------- | ---------------------------------- | --------------------------------------------------- |
| **Experience**        | First-hand, real-world involvement | Original examples, lived experience, demonstrations |
| **Expertise**         | Subject-matter competence          | Credentials, depth, accuracy                        |
| **Authoritativeness** | Recognition by others              | Mentions, citations, links                          |
| **Trustworthiness**   | Reliability and safety             | HTTPS, transparency, accuracy                       |

> Pages competing in the same space are often differentiated by **trust and experience**, not keywords.

---

## 2. Core Web Vitals (Page Experience Signals)

Core Web Vitals measure **how users experience a page**, not whether it deserves to rank.

| Metric  | Target  | What It Reflects    |
| ------- | ------- | ------------------- |
| **LCP** | < 2.5s  | Loading performance |
| **INP** | < 200ms | Interactivity       |
| **CLS** | < 0.1   | Visual stability    |

**Important context:**

- CWV rarely override poor content
- They matter most when content quality is comparable
- Failing CWV can _hold back_ otherwise good pages

---

## 3. Technical SEO Principles

Technical SEO ensures pages are **accessible, understandable, and stable**.

### Crawl & Index Control

| Element           | Purpose                |
| ----------------- | ---------------------- |
| XML sitemaps      | Help discovery         |
| robots.txt        | Control crawl access   |
| Canonical tags    | Consolidate duplicates |
| HTTP status codes | Communicate page state |
| HTTPS             | Security and trust     |

### Performance & Accessibility

| Factor                 | Why It Matters                |
| ---------------------- | ----------------------------- |
| Page speed             | User satisfaction             |
| Mobile-friendly design | Mobile-first indexing         |
| Clean URLs             | Crawl clarity                 |
| Semantic HTML          | Accessibility & understanding |

---

## 4. Content SEO Principles

### Page-Level Elements

| Element          | Principle                    |
| ---------------- | ---------------------------- |
| Title tag        | Clear topic + intent         |
| Meta description | Click relevance, not ranking |
| H1               | Page’s primary subject       |
| Headings         | Logical structure            |
| Alt text         | Accessibility and context    |

### Content Quality Signals

| Dimension   | What Search Engines Look For |
| ----------- | ---------------------------- |
| Depth       | Fully answers the query      |
| Originality | Adds unique value            |
| Accuracy    | Factually correct            |
| Clarity     | Easy to understand           |
| Usefulness  | Satisfies intent             |

---

## 5. Structured Data (Schema)

Structured data helps search engines **understand meaning**, not boost rankings directly.

| Type           | Purpose                |
| -------------- | ---------------------- |
| Article        | Content classification |
| Organization   | Entity identity        |
| Person         | Author information     |
| FAQPage        | Q&A clarity            |
| Product        | Commerce details       |
| Review         | Ratings context        |
| BreadcrumbList | Site structure         |

> Schema enables eligibility for rich results but does not guarantee them.

---

## 6. AI-Assisted Content Principles

Search engines evaluate **output quality**, not authorship method.

### Effective Use

- AI as a drafting or research assistant
- Human review for accuracy and clarity
- Original insights and synthesis
- Clear accountability

### Risky Use

- Publishing unedited AI output
- Factual errors or hallucinations
- Thin or duplicated content
- Keyword-driven text with no value

---

## 7. Relative Importance of SEO Factors

There is **no fixed ranking factor order**.
However, when competing pages are similar, importance tends to follow this pattern:

| Relative Weight | Factor                      |
| --------------- | --------------------------- |
| Highest         | Content relevance & quality |
| High            | Authority & trust signals   |
| Medium          | Page experience (CWV, UX)   |
| Medium          | Mobile optimization         |
| Baseline        | Technical accessibility     |

> Technical SEO enables ranking; content quality earns it.

---

## 8. Measurement & Evaluation

SEO fundamentals should be validated using **multiple signals**, not single metrics.

| Area        | What to Observe            |
| ----------- | -------------------------- |
| Visibility  | Indexed pages, impressions |
| Engagement  | Click-through, dwell time  |
| Performance | CWV field data             |
| Coverage    | Indexing status            |
| Authority   | Mentions and links         |

---

> **Key Principle:**
> Sustainable SEO is built on _useful content_, _technical clarity_, and _trust over time_.
> There are no permanent shortcuts.

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Supplemental Guidance: Seo Plan

# Strategic SEO Planning

## Process

### 1. Discovery
- Business type, target audience, competitors, goals
- Current site assessment (if exists)
- Budget and timeline constraints
- Key performance indicators (KPIs)

### 2. Competitive Analysis
- Identify top 5 competitors
- Analyze their content strategy, schema usage, technical setup
- Identify keyword gaps and content opportunities
- Assess their E-E-A-T signals
- Estimate their domain authority

### 3. Architecture Design
- Load industry template from `assets/` directory
- Design URL hierarchy and content pillars
- Plan internal linking strategy
- Sitemap structure with quality gates applied
- Information architecture for user journeys

### 4. Content Strategy
- Content gaps vs competitors
- Page types and estimated counts
- Blog/resource topics and publishing cadence
- E-E-A-T building plan (author bios, credentials, experience signals)
- Content calendar with priorities

### 5. Technical Foundation
- Hosting and performance requirements
- Schema markup plan per page type
- Core Web Vitals baseline targets
- AI search readiness requirements
- Mobile-first considerations

### 6. Implementation Roadmap (4 phases)

#### Phase 1: Foundation (weeks 1-4)
- Technical setup and infrastructure
- Core pages (home, about, contact, main services)
- Essential schema implementation
- Analytics and tracking setup

#### Phase 2: Expansion (weeks 5-12)
- Content creation for primary pages
- Blog launch with initial posts
- Internal linking structure
- Local SEO setup (if applicable)

#### Phase 3: Scale (weeks 13-24)
- Advanced content development
- Link building and outreach
- GEO optimization
- Performance optimization

#### Phase 4: Authority (months 7-12)
- Thought leadership content
- PR and media mentions
- Advanced schema implementation
- Continuous optimization

## Industry Templates

Load from `assets/` directory:
- `saas.md`: SaaS/software companies
- `local-service.md`: Local service businesses
- `ecommerce.md`: E-commerce stores
- `publisher.md`: Content publishers/media
- `agency.md`: Agencies and consultancies
- `generic.md`: General business template

## Output

### Deliverables
- `SEO-STRATEGY.md`: Complete strategic plan
- `COMPETITOR-ANALYSIS.md`: Competitive insights
- `CONTENT-CALENDAR.md`: Content roadmap
- `IMPLEMENTATION-ROADMAP.md`: Phased action plan
- `SITE-STRUCTURE.md`: URL hierarchy and architecture

### KPI Targets
| Metric | Baseline | 3 Month | 6 Month | 12 Month |
|--------|----------|---------|---------|----------|
| Organic Traffic | ... | ... | ... | ... |
| Keyword Rankings | ... | ... | ... | ... |
| Domain Authority | ... | ... | ... | ... |
| Indexed Pages | ... | ... | ... | ... |
| Core Web Vitals | ... | ... | ... | ... |

### Success Criteria
- Clear, measurable goals per phase
- Resource requirements defined
- Dependencies identified
- Risk mitigation strategies

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `dataforseo_labs_google_competitors_domain` and `dataforseo_labs_google_domain_intersection` for real competitive intelligence, `dataforseo_labs_bulk_traffic_estimation` for traffic estimates, `kw_data_google_ads_search_volume` and `dataforseo_labs_bulk_keyword_difficulty` for keyword research, and `business_data_business_listings_search` for local business data.

## Error Handling

| Scenario | Action |
|----------|--------|
| Unrecognized business type | Fall back to `generic.md` template. Inform user that no industry-specific template was found and proceed with the general business template. |
| No website URL provided | Proceed with new-site planning mode. Skip current site assessment and competitive gap analysis that require a live URL. |
| Industry template not found | Check `assets/` directory for available templates. If the requested template file is missing, use `generic.md` and note the missing template in output. |

---

## Merged Agentic SEO Resource: seo-audit.md

---
name: seo-audit
description: >
  Full website SEO audit with parallel subagent delegation. Crawls up to 500
  pages, detects business type, delegates to 6 specialists, generates health
  score. Use when user says "audit", "full SEO check", "analyze my site",
  or "website health check".
---

# Full Website SEO Audit

Apply `resources/references/llm-audit-rubric.md` for evidence standards, confidence labels, severity mapping, and report structure.

## Process

1. **Read page with LLM** — use `read_url_content` to read the page and begin analysis using SEO best practices.
2. **Detect business type** — analyze homepage signals per seo orchestrator
3. **Run scripts for evidence** — Always attempt to run relevant scripts for structured data collection. Scripts provide precise, machine-readable evidence that strengthens the analysis:
   - `seo-technical` — robots.txt, sitemaps, canonicals, Core Web Vitals, security headers
   - `seo-content` — E-E-A-T, readability, thin content, AI citation readiness
   - `seo-schema` — detection, validation, generation recommendations
   - `seo-sitemap` — structure analysis, quality gates, missing pages
   - `seo-performance` — LCP, INP, CLS measurements
   - `seo-visual` — screenshots, mobile testing, above-fold analysis
4. **LLM analysis** — Apply `llm-audit-rubric.md`, score each category using chain-of-thought. Combine LLM reasoning with script evidence. If a script failed, the LLM still covers that area using its own analysis (confidence: `Likely` instead of `Confirmed`).
5. **Score** — aggregate into SEO Health Score (0-100)
6. **Report** — generate prioritized action plan

## Crawl Configuration

```
Max pages: 500
Respect robots.txt: Yes
Follow redirects: Yes (max 3 hops)
Timeout per page: 30 seconds
Concurrent requests: 5
Delay between requests: 1 second
```

## Output Files

- `FULL-AUDIT-REPORT.md` — Comprehensive findings
- `ACTION-PLAN.md` — Prioritized recommendations (Critical → High → Medium → Low)
- `SEO-REPORT.html` — Optional interactive dashboard path (when `generate_report.py` is executed)
- `screenshots/` — Desktop + mobile captures (if Playwright available)

## Scoring Weights

> **Source of truth**: `SKILL.md` Step 7. Update weights there first, then mirror here.

| Category | Weight |
|----------|--------|
| Technical SEO | 25% |
| Content Quality | 20% |
| On-Page SEO | 15% |
| Schema / Structured Data | 15% |
| Performance (CWV) | 10% |
| Images | 10% |
| AI Search Readiness | 5% |

## Report Structure

### Executive Summary
- Overall SEO Health Score (0-100)
- Business type detected
- Top 5 critical issues
- Top 5 quick wins

### Technical SEO
- Crawlability issues
- Indexability problems
- Security concerns
- Core Web Vitals status

### Content Quality
- E-E-A-T assessment
- Thin content pages
- Duplicate content issues
- Readability scores

### On-Page SEO
- Title tag issues
- Meta description problems
- Heading structure
- Internal linking gaps

### Schema & Structured Data
- Current implementation
- Validation errors
- Missing opportunities

### Performance
- LCP, INP, CLS scores
- Resource optimization needs
- Third-party script impact

### Images
- Missing alt text
- Oversized images
- Format recommendations

### AI Search Readiness
- Citability score
- Structural improvements
- Authority signals

## Priority Definitions

- **Critical**: Blocks indexing or causes penalties (fix immediately)
- **High**: Significantly impacts rankings (fix within 1 week)
- **Medium**: Optimization opportunity (fix within 1 month)
- **Low**: Nice to have (backlog)

---

## Merged Agentic SEO Resource: seo-page.md

---
name: seo-page
description: >
  Deep single-page SEO analysis covering on-page elements, content quality,
  technical meta tags, schema, images, and performance. Use when user says
  "analyze this page", "check page SEO", or provides a single URL for review.
---

# Single Page Analysis

Apply `resources/references/llm-audit-rubric.md` for evidence standards, confidence labels, severity mapping, and report structure.

## What to Analyze

### On-Page SEO
- Title tag: 50-60 characters, includes primary keyword, unique
- Meta description: 150-160 characters, compelling, includes keyword
- H1: exactly one, matches page intent, includes keyword
- H2-H6: logical hierarchy (no skipped levels), descriptive
- URL: short, descriptive, hyphenated, no parameters
- Internal links: sufficient, relevant anchor text, no orphan pages
- External links: to authoritative sources, reasonable count

### Content Quality
- Word count vs page type minimums (see quality-gates.md)
- Readability: Flesch Reading Ease score, grade level
- Keyword density: natural (1-3%), semantic variations present
- E-E-A-T signals: author bio, credentials, first-hand experience markers
- Content freshness: publication date, last updated date

### Technical Elements
- Canonical tag: present, self-referencing or correct
- Meta robots: index/follow unless intentionally blocked
- Open Graph: og:title, og:description, og:image, og:url
- Twitter Card: twitter:card, twitter:title, twitter:description
- Hreflang: if multi-language, correct implementation

### Schema Markup
- Detect all types (JSON-LD preferred)
- Validate required properties
- Identify missing opportunities
- NEVER recommend HowTo (deprecated) or FAQ (restricted to gov/health)

### Images
- Alt text: present, descriptive, includes keywords where natural
- File size: flag >200KB (warning), >500KB (critical)
- Format: recommend WebP/AVIF over JPEG/PNG
- Dimensions: width/height set for CLS prevention
- Lazy loading: loading="lazy" on below-fold images

### Core Web Vitals (reference only — not measurable from HTML alone)
- Flag potential LCP issues (huge hero images, render-blocking resources)
- Flag potential INP issues (heavy JS, no async/defer)
- Flag potential CLS issues (missing image dimensions, injected content)

## Output

### Output Files (Required)
- `FULL-AUDIT-REPORT.md` — Full single-page findings with evidence, severity, and confidence labels
- `ACTION-PLAN.md` — Prioritized implementation plan (Critical → High → Medium → Low)
- `SEO-REPORT.html` — Optional interactive dashboard path (when `generate_report.py` is executed)

### Page Score Card
```
Overall Score: XX/100

On-Page SEO:     XX/100  ████████░░
Content Quality: XX/100  ██████████
Technical:       XX/100  ███████░░░
Schema:          XX/100  █████░░░░░
Images:          XX/100  ████████░░
```

### Issues Found
Organized by priority: Critical → High → Medium → Low

### Recommendations
Specific, actionable improvements with expected impact

### Schema Suggestions
Ready-to-use JSON-LD code for detected opportunities

## Execution Plan

When invoked as an agent to analyze a specific URL, execute these steps:
1. Run `scripts/parse_html.py "$URL" --json` to get title, meta, links, headings, alt text, and schema.
2. Run `scripts/readability.py "$URL" --json` for text metrics and grade level.
3. Run `scripts/pagespeed.py "$URL" --strategy mobile --json` for Core Web Vitals.
4. If applicable, run `scripts/article_seo.py "$URL" --json` for a deeper dive on content scoring.
5. Summarize all findings into the Page Score Card and Issues Found according to the evidence rules.

---

## Merged Agentic SEO Resource: seo-technical.md

---
name: seo-technical
description: >
  Technical SEO audit across 8 categories: crawlability, indexability, security,
  URL structure, mobile, Core Web Vitals, structured data, and JavaScript
  rendering. Use when user says "technical SEO", "crawl issues", "robots.txt",
  "Core Web Vitals", "site speed", or "security headers".
---

# Technical SEO Audit

## Categories

### 1. Crawlability
- robots.txt: exists, valid, not blocking important resources
- XML sitemap: exists, referenced in robots.txt, valid format
- Noindex tags: intentional vs accidental
- Crawl depth: important pages within 3 clicks of homepage
- JavaScript rendering: check if critical content requires JS execution
- Crawl budget: for large sites (>10k pages), efficiency matters

#### AI Crawler Management

As of 2025-2026, AI companies actively crawl the web to train models and power AI search. Managing these crawlers via robots.txt is a critical technical SEO consideration.

**Known AI crawlers:**

| Crawler | Company | robots.txt token | Purpose |
|---------|---------|-----------------|---------|
| GPTBot | OpenAI | `GPTBot` | Model training |
| ChatGPT-User | OpenAI | `ChatGPT-User` | Real-time browsing |
| provider-specific AI crawler | Anthropic | `provider-specific AI crawler` | Model training |
| PerplexityBot | Perplexity | `PerplexityBot` | Search index + training |
| Bytespider | ByteDance | `Bytespider` | Model training |
| Google-Extended | Google | `Google-Extended` | Gemini training (NOT search) |
| CCBot | Common Crawl | `CCBot` | Open dataset |

**Key distinctions:**
- Blocking `Google-Extended` prevents Gemini training use but does NOT affect Google Search indexing or AI Overviews (those use `Googlebot`)
- Blocking `GPTBot` prevents OpenAI training but does NOT prevent ChatGPT from citing your content via browsing (`ChatGPT-User`)
- ~3-5% of websites now use AI-specific robots.txt rules

**Example — selective AI crawler blocking:**
```
# Allow search indexing, block AI training crawlers
User-agent: GPTBot
Disallow: /

User-agent: Google-Extended
Disallow: /

User-agent: Bytespider
Disallow: /

# Allow all other crawlers (including Googlebot for search)
User-agent: *
Allow: /
```

**Recommendation:** Consider your AI visibility strategy before blocking. Being cited by AI systems drives brand awareness and referral traffic. Cross-reference the `seo-geo` skill for full AI visibility optimization.

### 2. Indexability
- Canonical tags: self-referencing, no conflicts with noindex
- Duplicate content: near-duplicates, parameter URLs, www vs non-www
- Thin content: pages below minimum word counts per type
- Pagination: rel=next/prev or load-more pattern
- Hreflang: correct for multi-language/multi-region sites
- Index bloat: unnecessary pages consuming crawl budget

### 3. Security
- HTTPS: enforced, valid SSL certificate, no mixed content
- Security headers:
  - Content-Security-Policy (CSP)
  - Strict-Transport-Security (HSTS)
  - X-Frame-Options
  - X-Content-Type-Options
  - Referrer-Policy
- HSTS preload: check preload list inclusion for high-security sites

### 4. URL Structure
- Clean URLs: descriptive, hyphenated, no query parameters for content
- Hierarchy: logical folder structure reflecting site architecture
- Redirects: no chains (max 1 hop), 301 for permanent moves
- URL length: flag >100 characters
- Trailing slashes: consistent usage

### 5. Mobile Optimization
- Responsive design: viewport meta tag, responsive CSS
- Touch targets: minimum 48x48px with 8px spacing
- Font size: minimum 16px base
- No horizontal scroll
- Mobile-first indexing: Google indexes mobile version. **Mobile-first indexing is 100% complete as of July 5, 2024.** Google now crawls and indexes ALL websites exclusively with the mobile Googlebot user-agent.

### 6. Core Web Vitals
- **LCP** (Largest Contentful Paint): target <2.5s
- **INP** (Interaction to Next Paint): target <200ms
  - INP replaced FID on March 12, 2024. FID was fully removed from all Chrome tools (CrUX API, PageSpeed Insights, Lighthouse) on September 9, 2024. Do NOT reference FID anywhere.
- **CLS** (Cumulative Layout Shift): target <0.1
- Evaluation uses 75th percentile of real user data
- Use PageSpeed Insights API or CrUX data if MCP available

### 7. Structured Data
- Detection: JSON-LD (preferred), Microdata, RDFa
- Validation against Google's supported types
- See seo-schema skill for full analysis

### 8. JavaScript Rendering
- Check if content visible in initial HTML vs requires JS
- Identify client-side rendered (CSR) vs server-side rendered (SSR)
- Flag SPA frameworks (React, Vue, Angular) that may cause indexing issues
- Verify dynamic rendering setup if applicable

#### JavaScript SEO — Canonical & Indexing Guidance (December 2025)

Google updated its JavaScript SEO documentation in December 2025 with critical clarifications:

1. **Canonical conflicts:** If a canonical tag in raw HTML differs from one injected by JavaScript, Google may use EITHER one. Ensure canonical tags are identical between server-rendered HTML and JS-rendered output.
2. **noindex with JavaScript:** If raw HTML contains `<meta name="robots" content="noindex">` but JavaScript removes it, Google MAY still honor the noindex from raw HTML. Serve correct robots directives in the initial HTML response.
3. **Non-200 status codes:** Google does NOT render JavaScript on pages returning non-200 HTTP status codes. Any content or meta tags injected via JS on error pages will be invisible to Googlebot.
4. **Structured data in JavaScript:** Product, Article, and other structured data injected via JS may face delayed processing. For time-sensitive structured data (especially e-commerce Product markup), include it in the initial server-rendered HTML.

**Best practice:** Serve critical SEO elements (canonical, meta robots, structured data, title, meta description) in the initial server-rendered HTML rather than relying on JavaScript injection.

### 9. IndexNow Protocol
- Check if site supports IndexNow for Bing, Yandex, Naver
- Supported by search engines other than Google
- Recommend implementation for faster indexing on non-Google engines

## Output

### Technical Score: XX/100

### Category Breakdown
| Category | Status | Score |
|----------|--------|-------|
| Crawlability | ✅/⚠️/❌ | XX/100 |
| Indexability | ✅/⚠️/❌ | XX/100 |
| Security | ✅/⚠️/❌ | XX/100 |
| URL Structure | ✅/⚠️/❌ | XX/100 |
| Mobile | ✅/⚠️/❌ | XX/100 |
| Core Web Vitals | ✅/⚠️/❌ | XX/100 |
| Structured Data | ✅/⚠️/❌ | XX/100 |
| JS Rendering | ✅/⚠️/❌ | XX/100 |

### Critical Issues (fix immediately)
### High Priority (fix within 1 week)
### Medium Priority (fix within 1 month)
### Low Priority (backlog)

---

## Voice Search Optimization

Voice search (Google Assistant, Siri, Alexa, Cortana) selects answers primarily from **Featured Snippets** and requires specific optimization signals.

### Key Facts
- 90%+ of voice answers are read directly from Featured Snippets
- 46% of voice searches have local intent
- Voice results strongly favor pages with TTFB < 2s
- 80%+ of voice queries come from mobile devices

### Checklist

| Check | Requirement | Pass Threshold |
|-------|-------------|----------------|
| Page speed | TTFB < 2s (critical — voice results heavily favor fast pages) | < 2000ms |
| HTTPS | Required for voice results | Must be HTTPS |
| Featured Snippet | Direct answer in first 40-55 words after H-tag | Present |
| FAQ phrasing | H2/H3 phrased as natural language questions | ≥ 3 question H-tags |
| Local schema | LocalBusiness with address, phone, hours (local intent queries) | If local business |
| `speakable` schema | Marks top answer paragraphs for Google Assistant | Recommended |
| Mobile accessibility | 100% accessible on mobile | Required |

### `speakable` Schema Implementation
```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "speakable": {
    "@type": "SpeakableSpecification",
    "cssSelector": [".article-summary", "h2 + p", "[itemprop='description']"]
  }
}
```

### Voice Search by Platform

| Platform | Index Source | Primary Optimization |
|----------|-------------|---------------------|
| **Google Assistant** | Google index | Featured Snippet ownership, TTFB < 2s, `speakable` |
| **Siri** | Bing (Apple) | Bing Webmaster Tools submission, Featured Snippet on Bing |
| **Alexa** | Bing | Featured Snippet, Bing indexed content |
| **Cortana** | Bing | Featured Snippet, Bing Webmaster Tools |

> **Note**: For Siri and Alexa, submit your site via **Bing Webmaster Tools** (separate from Google Search Console). Bing still powers major voice engines.

## Execution Plan

When invoked as an agent, execute these steps:
1. Run `scripts/robots_checker.py "$URL" --json` for crawlability and AI crawler rules.
2. Run `scripts/security_headers.py "$URL" --json` for security posture.
3. Run `scripts/redirect_checker.py "$URL" --json` to verify redirect chains.
4. Run `scripts/pagespeed.py "$URL" --strategy mobile --json` for Core Web Vitals mapping.
5. Run `scripts/hreflang_checker.py "$URL" --json` for international setup.
6. Run `scripts/indexnow_checker.py "$URL" --key YOUR_KEY --json` if applicable.
7. Synthesize all outputs into the Technical Score and Category Breakdown.

---

## Merged Agentic SEO Resource: seo-links.md

---
name: seo-links
description: >
  Link health and backlink profile analysis. Wires together existing scripts
  (internal_links.py, broken_links.py) for internal link health and adds
  external backlink profiling via CommonCrawl CDX API. Use when user says
  "backlinks", "link profile", "internal links", "broken links", "link equity",
  "referring domains", or "link building".
---

# Link Health & Backlink Profile

Comprehensive link analysis combining internal link equity, broken link detection,
and external backlink profiling. Uses existing scripts for internal/broken links
and `link_profile.py` for external links.

## Scripts

### 1. Internal Link Analysis (`scripts/internal_links.py`)
```bash
python3 <SKILL_DIR>/scripts/internal_links.py <url> --depth 2 --json
```

Crawls up to 50 pages, reports:
- Total internal links, unique pages found
- Orphan page candidates (≤1 incoming internal link)
- Anchor text distribution (top 20 anchors)
- Nofollow internal links (waste of link equity)
- Pages with < 3 or > 100 internal links

### 2. Broken Link Detection (`scripts/broken_links.py`)
```bash
python3 <SKILL_DIR>/scripts/broken_links.py <url> --json
```

Checks all links on a page for HTTP errors:
- 404 Not Found → immediate fix needed
- 301/302 chains → update to final destination
- Timeout / connection errors → server issue

### 3. External Link Profile (`scripts/link_profile.py`)
```bash
python3 <SKILL_DIR>/scripts/link_profile.py <url> --json
```

Analyzes external links from the page and discovers backlinks via CommonCrawl CDX API:
- External links: total, unique domains, dofollow/nofollow split
- Anchor text profile (branded, exact-match, partial, generic, naked URL)
- CommonCrawl backlink sample (no API key required)

---

## Analysis Framework

### Internal Link Equity

See `resources/references/link-building.md` for PageRank flow guidance.

**Healthy signals:**
- Pillar pages: 10+ internal links pointing in from related content
- Blog posts: 3-8 bidirectional links to/from related articles
- No orphan pages (pages with 0-1 internal links)
- Avg outbound internal links per page: 5-15

**Issue thresholds (from `internal_links.py` output):**
| Issue | Threshold | Severity |
|-------|-----------|----------|
| Orphan pages | ≥ 1 | Warning |
| Pages with < 3 links | ≥ 10% of crawled pages | Warning |
| Pages with > 100 links | Any | Warning |
| Nofollow internal links | Any | Info |

### Broken Links

**Impact on SEO:**
- 404 errors on crawled pages → Googlebot wastes crawl budget
- Internal 404s → lost link equity (PageRank leaks to dead URLs)
- External 404 links → poor user experience signal

**Priority:**
1. Fix internal broken links first (crawl budget waste)
2. Redirect external broken links to best alternative
3. Remove if no equivalent content exists

### External Backlink Profile

Reference `resources/references/link-building.md` for:
- Safe anchor text distribution targets (branded 40-50%, exact-match ≤5%)
- Toxic link detection signals
- Disavow file creation guide
- Manual action recovery steps

**CommonCrawl CDX API (free, no key):**
```
https://index.commoncrawl.org/CC-MAIN-latest/cdx?url=example.com/*
  &fl=original,timestamp,urlkey&output=json&limit=100
```

Note: CommonCrawl is a snapshot, not real-time. For live backlink data, GSC "Top Linking Sites" is the most reliable free source.

---

## Audit Workflow

```
1. Run internal_links.py    → identify orphans, thin linking, nofollow waste
2. Run broken_links.py      → list all 4xx/5xx links for immediate fix
3. Run link_profile.py      → external link classification + backlink sample
4. Cross-reference with GSC → confirm crawl errors, top linking sites
5. Apply quality thresholds → from this file + link-building.md reference
6. Generate report          → findings + prioritized fixes
```

---

## Output Format

```markdown
## Link Health Report — [URL]

### Internal Links
- Pages crawled: N
- Orphan pages: N (list top 5)
- Avg links per page: N
- Nofollow internal links: N

### Broken Links
- Total broken: N
- Internal 404s: N (list URLs)
- External 404s: N

### External Backlink Profile
- External links on page: N (dofollow: N, nofollow: N)
- Unique external domains: N
- CommonCrawl referring domains: N (sample)
- Anchor text profile: [Branded N%, Exact-match N%, Generic N%]

### Issues (prioritized)
| Severity | Issue | Fix |
|----------|-------|-----|
```

---

## Merged Agentic SEO Resource: seo-github.md

---
name: seo-github
description: >
  GitHub repository SEO and discoverability analysis. Use for repository search
  visibility, README conversion optimization, community health checks, topic
  strategy, and traffic archival planning.
---

# GitHub Repository SEO

Apply `resources/references/llm-audit-rubric.md` for evidence format and
confidence labels, and apply `resources/references/readme-audit-rubric.md`
for README scoring.

## Trigger Mapping

Use this workflow when the user asks:

- "GitHub SEO"
- "optimize this repo for GitHub search"
- "improve repository discoverability"
- "README SEO audit"
- "topics and metadata optimization"
- "community profile audit"

## Inputs

- Repository URL or slug (`owner/repo`).
- Optional target query set for GitHub search benchmarking.
- Optional GitHub token (`GITHUB_TOKEN` or `GH_TOKEN`) for API checks.

### Auth Setup

Use either environment token or `gh` auth:

```bash
# Option A: token env vars (recommended for automation)
export GITHUB_TOKEN="ghp_xxx"
# or
export GH_TOKEN="ghp_xxx"

# Option B: GitHub CLI login fallback
gh auth login -h github.com
gh auth status -h github.com
```

## Workflow

### 1. Resolve Scope and Access

- Resolve target repo from input or local `origin` remote.
- Check whether token-based API access is available.
- If token is missing/invalid, continue with partial checks and mark API-based
  findings as `Unknown` or `Likely`.
- Generate query/competitor inputs via LLM reasoning and/or web search before
  benchmark stages when possible:
  - query list (`--query` / `--query-file`)
  - optional explicit competitors (`--competitor owner/repo`)
- If queries are not provided, `github_seo_report.py` auto-derives repo-specific
  benchmark queries from repo slug/metadata/title analysis.

### 2. Run Evidence Scripts

Run scripts from `<SKILL_DIR>/scripts/`:

```bash
python3 github_repo_audit.py --repo <owner/repo> --provider auto --json
python3 github_readme_lint.py README.md --json
python3 github_community_health.py --repo <owner/repo> --provider auto --json
python3 github_search_benchmark.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --json
python3 github_competitor_research.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --top-n 6 --json
python3 github_competitor_research.py --repo <owner/repo> --competitor <owner/repo> --competitor <owner/repo> --provider auto --json
python3 github_traffic_archiver.py --repo <owner/repo> --provider auto --json
python3 github_seo_report.py --repo <owner/repo> --provider auto --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md
# Optional: cap auto-derived query count used by github_seo_report.py
# python3 github_seo_report.py --repo <owner/repo> --provider auto --auto-query-max 8 --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md
# Optional explicit verifier step for custom pipelines:
# python3 finding_verifier.py --findings-json raw-findings.json --json
```

### 3. Analyze by Area

- Metadata discoverability: repo name, description, topics, homepage, social preview.
- Title strategy: underscore vs hyphen checks, intent-keyword extraction, suggested slug/title.
- README SEO and conversion: heading structure, intent alignment, CTAs, proof sections.
- Community trust: governance files and contribution readiness.
- Search benchmarking: target query positions and sampled competitors.
- Competitor research: topic overlaps, README pattern gaps, and strategy opportunities.
- Traffic trend readiness: archival freshness and retention compliance.
- Backlink distribution: channel suggestions (Medium/blog/dev communities), post title ideas, and anchor-mix guidance.

### 4. Prioritize

Classify recommendations:

- `Critical`: blocks discovery, trust, or measurement continuity.
- `Warning`: important optimization opportunity.
- `Pass`: meets baseline.
- `Info`: contextual or optional enhancement.

### 5. Output Artifacts

Required for `seo github` runs:

- `GITHUB-SEO-REPORT.md` — findings and evidence.
- `GITHUB-ACTION-PLAN.md` — prioritized fixes.

Optional:

- `GITHUB-WEEKLY-SCORECARD.md` — recurring measurement snapshot.

## Delegation Guidance

- Strategy synthesis: `resources/agents/seo-github-analyst.md`
- Competitor/query benchmark: `resources/agents/seo-github-benchmark.md`
- API collection/archival: `resources/agents/seo-github-data.md`
- Final verification: `resources/agents/seo-verifier.md`

## Critical Rules

1. Never claim a definitive GitHub ranking formula.
2. Treat token/API failures as environment limitations, not repo defects.
3. Keep topics relevant and capped at 20.
4. Preserve existing project voice/branding in README recommendations.
5. Archive traffic snapshots regularly due 14-day retention windows.

---

## Merged Agentic SEO Orchestration

---
name: seo
description: >
  Deterministic LLM-first SEO audits for websites, blog posts, and GitHub
  repositories. Use this when the user asks to "perform SEO analysis", "run SEO
  audit", "analyze SEO", "check technical SEO", "review schema", "Core Web
  Vitals", "E-E-A-T", "hreflang", "GEO", "AEO", or GitHub repository SEO
  optimization. For full/page/repo audits, run bundled scripts for evidence and
  return prioritized, confidence-labeled fixes.
---

# SEO Skill (Agentic / Agentic)

LLM-first SEO analysis skill with 16 specialized sub-skills, 10 specialist agents, and 33 scripts for website, blog, and GitHub repository optimization.

## Deterministic Trigger Mapping

For prompt reliability in agent IDEs, map common user wording to a fixed workflow:

- If user says `perform seo analysis on <url>` (or similar generic SEO request with a URL), treat it as a **single-URL full audit**.
- If no explicit sub-skill is specified, run the full/page audit path with **LLM-first reasoning** and script-backed evidence.
- For full/page audits, always produce:
  - `FULL-AUDIT-REPORT.md` (detailed findings)
  - `ACTION-PLAN.md` (prioritized fixes)
- If `generate_report.py` is run, also return the saved HTML path (for example `SEO-REPORT.html`).

## Available Commands

| Command | Sub-Skill | Description |
|---------|-----------|-------------|
| `seo audit <url>` | [seo-audit](resources/skills/seo-audit.md) | Full website audit with scoring |
| `seo page <url>` | [seo-page](resources/skills/seo-page.md) | Deep single-page analysis |
| `seo technical <url>` | [seo-technical](resources/skills/seo-technical.md) | Technical SEO checks |
| `seo content <url>` | [seo-content](resources/skills/seo-content.md) | Content quality & E-E-A-T |
| `seo schema <url>` | [seo-schema](resources/skills/seo-schema.md) | Schema detection/validation/generation |
| `seo sitemap <url>` | [seo-sitemap](resources/skills/seo-sitemap.md) | Sitemap analysis & generation |
| `seo images <url>` | [seo-images](resources/skills/seo-images.md) | Image optimization audit |
| `seo geo <url>` | [seo-geo](resources/skills/seo-geo.md) | AI search optimization (GEO) |
| `seo programmatic <url>` | [seo-programmatic](resources/skills/seo-programmatic.md) | Programmatic SEO safeguards |
| `seo competitors <url>` | [seo-competitor-pages](resources/skills/seo-competitor-pages.md) | Comparison/alternatives pages |
| `seo hreflang <url>` | [seo-hreflang](resources/skills/seo-hreflang.md) | International SEO validation |
| `seo plan <url>` | [seo-plan](resources/skills/seo-plan.md) | Strategic SEO planning |
| `seo github <repo_or_url>` | [seo-github](resources/skills/seo-github.md) | GitHub repository discoverability, README, topics, community health, and traffic archival |
| `seo article <url>` | [seo-article](resources/skills/seo-article.md) | Article data extraction & LLM optimization |
| `seo links <url>` | [seo-links](resources/skills/seo-links.md) | External backlink profile & link health |
| `seo aeo <url>` | [seo-aeo](resources/skills/seo-aeo.md) | Answer Engine Optimization (Featured Snippets, PAA, Knowledge Panel) |

---

## Orchestration Logic

When the user requests SEO analysis, follow this routing:

### Step 1 — Identify the Task

Parse the user's request to determine which sub-skill(s) to activate:

- **Full audit**: Read `resources/skills/seo-audit.md` — crawl multiple pages, delegate to agents, score and report
- **Single page**: Read `resources/skills/seo-page.md` — deep dive on one URL
- **Specific area**: Read the matching `resources/skills/seo-*.md` file
- **Strategic plan**: Read `resources/skills/seo-plan.md` and the matching `resources/templates/*.md` for the detected industry
- **GitHub repository SEO**: Read `resources/skills/seo-github.md` and use GitHub scripts with `--provider auto` for API/`gh` fallback.
- **Generic `perform seo analysis on <url>` request**: treat as single-page full audit, read `resources/skills/seo-page.md`, and generate `FULL-AUDIT-REPORT.md` + `ACTION-PLAN.md`.

### Step 2 — Collect Evidence

**Primary method (LLM-first)** — use the built-in `read_url_content` tool first:
```
read_url_content(url)  →  returns parsed HTML content directly
```
Use this as the baseline evidence for reasoning.

**Deterministic verification (recommended when script execution is available)**:
```bash
# Fetch/parse raw HTML for structured checks
python3 <SKILL_DIR>/scripts/fetch_page.py <url> --output /tmp/page.html
python3 <SKILL_DIR>/scripts/parse_html.py /tmp/page.html --url <url> --json

# Optional: generate shareable HTML dashboard artifact
python3 <SKILL_DIR>/scripts/generate_report.py <url> --output SEO-REPORT.html
```

> **Do not use third-party mirrors (e.g., `r.jina.ai`) as primary evidence when direct site fetch or bundled scripts are available.**
> `<SKILL_DIR>` = absolute path to this skill directory (the folder containing this SKILL.md).

### Step 3 — Perform LLM-First Analysis

Use the LLM as the primary SEO analyst:

1. Synthesize evidence from page content, metadata, and optional script outputs.
2. Produce findings with explicit proof:
   - `Finding`
   - `Evidence` (specific element, metric, or snippet)
   - `Impact` (why it matters for ranking/indexing/UX)
   - `Fix` (clear implementation step)
3. Prioritize by impact and implementation effort.
4. Separate confirmed issues, likely issues, and unknowns (missing data).

Always read and apply `resources/references/llm-audit-rubric.md` to keep scoring, severity, confidence, and output structure consistent across audit types.

### Step 4 — Run Baseline Verification Scripts (When execution is available)

For full/page audits, run baseline checks to avoid hypothesis-only reporting. Do not replace LLM reasoning with script-only scoring.

```bash
# Check robots.txt and AI crawler management
python3 <SKILL_DIR>/scripts/robots_checker.py <url>

# Check llms.txt for AI search readiness
python3 <SKILL_DIR>/scripts/llms_txt_checker.py <url>

# Get Core Web Vitals from PageSpeed Insights (free API, no key needed)
python3 <SKILL_DIR>/scripts/pagespeed.py <url> --strategy mobile

# Check security headers (HSTS, CSP, X-Frame-Options, etc.)
python3 <SKILL_DIR>/scripts/security_headers.py <url>

# Detect broken links on a page (404s, timeouts, connection errors)
python3 <SKILL_DIR>/scripts/broken_links.py <url> --workers 5

# Trace redirect chains, detect loops and mixed HTTP/HTTPS
python3 <SKILL_DIR>/scripts/redirect_checker.py <url>

# Analyze readability from fetched HTML (Flesch-Kincaid, grade level, sentence stats)
python3 <SKILL_DIR>/scripts/readability.py /tmp/page.html --json

# Validate Open Graph and Twitter Card meta tags
python3 <SKILL_DIR>/scripts/social_meta.py <url>

# Analyze internal link structure, find orphan pages
python3 <SKILL_DIR>/scripts/internal_links.py <url> --depth 1 --max-pages 20

# Extract article content and perform keyword research for LLM-driven optimization
python3 <SKILL_DIR>/scripts/article_seo.py <url> --keyword "<optional_target_keyword>" --json

# GitHub repository SEO (provider fallback: auto|api|gh)
# Auth setup (choose one):
# export GITHUB_TOKEN="ghp_xxx"   # or export GH_TOKEN="ghp_xxx"
# gh auth login -h github.com && gh auth status -h github.com
python3 <SKILL_DIR>/scripts/github_repo_audit.py --repo <owner/repo> --provider auto --json
python3 <SKILL_DIR>/scripts/github_readme_lint.py README.md --json
python3 <SKILL_DIR>/scripts/github_community_health.py --repo <owner/repo> --provider auto --json
# Benchmark/competitor inputs should be provided by LLM/web-search discovery when possible.
# If omitted, github_seo_report.py auto-derives repo-specific benchmark queries.
python3 <SKILL_DIR>/scripts/github_search_benchmark.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --json
python3 <SKILL_DIR>/scripts/github_competitor_research.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --top-n 6 --json
python3 <SKILL_DIR>/scripts/github_competitor_research.py --repo <owner/repo> --competitor <owner/repo> --competitor <owner/repo> --provider auto --json
python3 <SKILL_DIR>/scripts/github_traffic_archiver.py --repo <owner/repo> --provider auto --archive-dir .github-seo-data --json
python3 <SKILL_DIR>/scripts/github_seo_report.py --repo <owner/repo> --provider auto --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md --json
# Optional: increase/reduce auto-derived query volume (default: 6)
# python3 <SKILL_DIR>/scripts/github_seo_report.py --repo <owner/repo> --provider auto --auto-query-max 8 --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md --json
```

If a check fails due network, DNS, permissions, or API rate limits:
- Report it explicitly as an **environment limitation**, not a confirmed site issue.
- Keep confidence as `Hypothesis` for impacted categories.
- Continue with available evidence instead of stopping the audit.
- Do not enter repeated fallback loops. Retry a failed source at most once, then finalize the audit.
- Do not pivot into repeated web-search scraping loops for the same URL.

**Visual analysis** (requires Playwright — use `conda activate pentest` if available):
```bash
# Capture screenshots (desktop, laptop, tablet, mobile)
python3 <SKILL_DIR>/scripts/capture_screenshot.py <url> --all

# Analyze visual layout, above-the-fold, mobile responsiveness
python3 <SKILL_DIR>/scripts/analyze_visual.py <url> --json
```

**HTML Report Generator** — generates a self-contained interactive HTML dashboard:
```bash
# Generate full SEO report (runs scripts automatically, saves HTML to PWD)
python3 <SKILL_DIR>/scripts/generate_report.py <url>
python3 <SKILL_DIR>/scripts/generate_report.py <url> --output custom-report.html
```

### Step 5 — Delegate to Specialist Agents

For comprehensive audits, read the relevant agent file from `resources/agents/` to adopt the specialist role:

| Agent | File | Focus Area |
|-------|------|------------|
| Technical SEO | [seo-technical.md](resources/agents/seo-technical.md) | Crawlability, indexability, security, URLs, mobile, CWV, JS rendering |
| Content Quality | [seo-content.md](resources/agents/seo-content.md) | E-E-A-T assessment, content metrics, AI content detection |
| Performance | [seo-performance.md](resources/agents/seo-performance.md) | Core Web Vitals (LCP, INP, CLS), optimization recommendations |
| Schema Markup | [seo-schema.md](resources/agents/seo-schema.md) | Detection, validation, generation of JSON-LD structured data |
| Sitemap | [seo-sitemap.md](resources/agents/seo-sitemap.md) | XML sitemap validation, generation, quality gates |
| Visual Analysis | [seo-visual.md](resources/agents/seo-visual.md) | Screenshots, above-the-fold, responsiveness, layout |
| Verifier (global) | [seo-verifier.md](resources/agents/seo-verifier.md) | Deduplicate findings, suppress contradictions, and validate evidence relevance before final report |

### Step 6 — Apply Quality Gates

Reference the quality standards in `resources/references/`:

- **Content minimums**: Read [quality-gates.md](resources/references/quality-gates.md) for word counts, unique content %, title/meta requirements
- **Schema validation**: Read [schema-types.md](resources/references/schema-types.md) for active/deprecated/restricted types
- **Core Web Vitals**: Read [cwv-thresholds.md](resources/references/cwv-thresholds.md) for current metric thresholds
- **E-E-A-T framework**: Read [eeat-framework.md](resources/references/eeat-framework.md) for scoring criteria
- **Google reference**: Read [google-seo-reference.md](resources/references/google-seo-reference.md) for quick reference
- **LLM report rubric**: Read [llm-audit-rubric.md](resources/references/llm-audit-rubric.md) for mandatory evidence format, confidence labels, and output contract

### Step 6.5 — Verify Findings (All Workflows)

Before writing final reports, run verification:

```bash
python3 <SKILL_DIR>/scripts/finding_verifier.py --findings-json <raw_findings.json> --json
```

Use verified output for final report tables, not raw findings.

### Step 7 — Score and Report

Use numeric scores as guidance, not as a replacement for evidence quality and judgment.

#### Default Scoring Weights (Full Audit)

> **Canonical source of truth** — These weights are defined here and in `resources/skills/seo-audit.md`.
> Do not modify weights in individual sub-skill files; update only these two locations to keep scores consistent.

| Category | Weight |
|----------|--------|
| Technical SEO | 25% |
| Content Quality | 20% |
| On-Page SEO | 15% |
| Schema / Structured Data | 15% |
| Performance (CWV) | 10% |
| Image Optimization | 10% |
| AI Search Readiness (GEO) | 5% |

> If using `scripts/generate_report.py`, the automated dashboard uses script-level category weights defined in that script. Keep the narrative audit LLM-first and evidence-first.

### Step 8 — Mandatory Deliverables

For `seo audit`, `seo page`, and generic `perform seo analysis on <url>` flows:

1. Create `FULL-AUDIT-REPORT.md` in the current working directory at the start of the audit, then update it as evidence is collected.
2. Create `ACTION-PLAN.md` in the current working directory at the start of the audit, then update it with prioritized fixes.
3. If HTML dashboard was generated, include its exact saved path (for example `SEO-REPORT.html` or an absolute path).
4. In the final response, explicitly list generated artifacts and paths.
5. If technical checks are blocked by environment limits, still write both markdown files and include an "Environment Limitations" section.

#### Score Interpretation
| Score | Rating |
|-------|--------|
| 90-100 | Excellent |
| 70-89 | Good |
| 50-69 | Needs Improvement |
| 30-49 | Poor |
| 0-29 | Critical |

---

## Industry Detection

When running `seo plan`, detect the business type and load the matching template:

| Industry | Template File |
|----------|---------------|
| SaaS / Software | [saas.md](resources/templates/saas.md) |
| Local Service Business | [local-service.md](resources/templates/local-service.md) |
| E-commerce / Retail | [ecommerce.md](resources/templates/ecommerce.md) |
| Publisher / Media | [publisher.md](resources/templates/publisher.md) |
| Agency / Consultancy | [agency.md](resources/templates/agency.md) |
| Other / Generic | [generic.md](resources/templates/generic.md) |

**Detection signals:**
- SaaS: pricing page, feature pages, /docs, /api, trial/demo CTAs
- Local: address, phone, Google Business Profile, service area pages
- E-commerce: product pages, cart, checkout, /collections, /categories
- Publisher: article dates, author pages, /news, high content volume
- Agency: case studies, /work, /portfolio, team pages, service offerings

---

## Schema Templates

Pre-built JSON-LD templates are available in [templates.json](resources/schema/templates.json) for:
- **Common**: BlogPosting, Article, Organization, LocalBusiness, BreadcrumbList, WebSite (with SearchAction)
- **Video**: VideoObject, BroadcastEvent, Clip, SeekToAction
- **E-commerce**: ProductGroup (variants), OfferShippingDetails, Certification
- **Other**: SoftwareSourceCode, ProfilePage (E-E-A-T author pages)

---

## Validation Scripts

Two validation scripts are available for CI/CD integration:

### Pre-commit SEO Check
```bash
bash <SKILL_DIR>/scripts/pre_commit_seo_check.sh
```
Checks staged HTML files for: placeholder text in schema, title tag length, missing alt text, deprecated schema types, FID references (should be INP), meta description length.

### Schema Validator
```bash
python3 <SKILL_DIR>/scripts/validate_schema.py <file_path>
```
Validates JSON-LD blocks in HTML files: JSON syntax, @context/@type presence, placeholder text, deprecated/restricted types.

---

## Output Format

All sub-skill reports should use consistent severity levels:
- 🔴 **Critical** — Directly impacts rankings or indexing (fix immediately)
- ⚠️ **Warning** — Optimization opportunity (fix within 1 month)
- ✅ **Pass** — Meets or exceeds standards
- ℹ️ **Info** — Not applicable or informational only

Structure reports as:
1. Summary table with element, value, and severity
2. Detailed findings grouped by category
3. Actionable recommendations ordered by impact

---

## Critical Rules

1. **INP not FID** — FID was removed September 9, 2024. The sole interactivity metric is INP (Interaction to Next Paint). Never reference FID.
2. **FAQ schema is restricted** — FAQPage schema is limited to government and healthcare authority sites only (August 2023). Do NOT recommend for commercial sites.
3. **HowTo schema is deprecated** — Rich results fully removed September 2023. Never recommend.
4. **JSON-LD only** — Always use `<script type="application/ld+json">`. Never recommend Microdata or RDFa.
5. **E-E-A-T everywhere** — As of December 2025, E-E-A-T applies to ALL competitive queries, not just YMYL.
6. **Mobile-first is complete** — 100% mobile-first indexing since July 5, 2024.
7. **Location page limits** — Warning at 30+ pages, hard stop at 50+ pages. Enforce unique content requirements.
8. **AI crawler management** — Check robots.txt for GPTBot, provider-specific AI crawler, PerplexityBot, Applebot-Extended, Google-Extended, Bytespider, CCBot.
9. **LLM-first, resilient pipeline** — Start by reading the page with `read_url_content`, then always run relevant scripts for structured evidence. Scripts are the **preferred** evidence source — use them actively. However, if any script fails (timeout, network, parsing), the LLM MUST still produce a complete analysis using its own reasoning (confidence: `Likely`). Never block a report on a single script failure.
10. **Always produce file artifacts for audit flows** — `FULL-AUDIT-REPORT.md` and `ACTION-PLAN.md` are required outputs for full/page audit requests.
11. **Bound evidence retries** — Avoid long search/retry loops. If core checks fail due DNS/network, finalize promptly with confidence labels and file outputs.
12. **Avoid redundant web fallbacks** — If direct fetch/scripts fail and one fallback also fails, stop retrying and finish the report with explicit limitations.
13. **Signal freshness tracking** — Every reference file should contain a `<!-- Updated: YYYY-MM-DD -->` comment. Flag any reference file older than 90 days for review. When Google announces algorithm changes, verify affected reference files within 7 days. Key dates to track: core updates (quarterly), schema deprecations (schema-types.md), CWV threshold changes (cwv-thresholds.md).

---

## Dependencies

### Optional Script Dependencies
- Python 3.8+
- `requests` (for network analysis scripts)
- `beautifulsoup4` (for HTML parsing scripts)
- Playwright (for `capture_screenshot.py` and `analyze_visual.py`)
  ```bash
  pip install playwright && playwright install chromium
  ```
  Or if using conda: `conda activate pentest` (if Playwright is pre-installed)

### Install Script Dependencies
```bash
pip install requests beautifulsoup4
```

---

## Imported Module: Seo
---
name: seo
description: >
  Comprehensive SEO analysis for any website or business type. Performs full site
  audits, single-page deep analysis, technical SEO checks (crawlability, indexability,
  Core Web Vitals with INP), schema markup detection/validation/generation, content
  quality assessment (E-E-A-T framework per Dec 2025 update extending to all
  competitive queries), image optimization, sitemap analysis, and Generative Engine
  Optimization (GEO) for AI Overviews, ChatGPT, and Perplexity citations. Analyzes
  AI crawler accessibility (GPTBot, provider-specific AI crawler, PerplexityBot), llms.txt compliance,
  brand mention signals, and passage-level citability. Industry detection for SaaS,
  e-commerce, local business, publishers, agencies. Triggers on: "SEO", "audit",
  "schema", "Core Web Vitals", "sitemap", "E-E-A-T", "AI Overviews", "GEO",
  "technical SEO", "content quality", "page speed", "structured data".
user-invokable: true
argument-hint: "[command] [url]"
license: MIT
allowed-tools: Read, Grep, Glob, Bash, WebFetch, Agent
metadata:
  author: AgriciDaniel
  version: "1.7.0"
  category: seo
---
# SEO: Universal SEO Analysis Skill

**Invocation:** `/seo $1 $2` where `$1` is the command and `$2` is the URL or argument.

**Scripts:** Located at the plugin root `scripts/` directory.

Comprehensive SEO analysis across all industries (SaaS, local services,
e-commerce, publishers, agencies). Orchestrates 15 specialized sub-skills and 10 subagents
(+ 2 optional extension sub-skills: seo-dataforseo and seo-image-gen).

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/seo audit <url>` | Full website audit with parallel subagent delegation |
| `/seo page <url>` | Deep single-page analysis |
| `/seo sitemap <url or generate>` | Analyze or generate XML sitemaps |
| `/seo schema <url>` | Detect, validate, and generate Schema.org markup |
| `/seo images <url>` | Image optimization analysis |
| `/seo technical <url>` | Technical SEO audit (9 categories) |
| `/seo content <url>` | E-E-A-T and content quality analysis |
| `/seo geo <url>` | AI Overviews / Generative Engine Optimization |
| `/seo plan <business-type>` | Strategic SEO planning |
| `/seo programmatic [url\|plan]` | Programmatic SEO analysis and planning |
| `/seo competitor-pages [url\|generate]` | Competitor comparison page generation |
| `/seo local <url>` | Local SEO analysis (GBP, citations, reviews, map pack) |
| `/seo maps [command] [args]` | Maps intelligence (geo-grid, GBP audit, reviews, competitors) |
| `/seo hreflang [url]` | Hreflang/i18n SEO audit and generation |
| `/seo google [command] [url]` | Google SEO APIs (GSC, PageSpeed, CrUX, Indexing, GA4) |
| `/seo dataforseo [command]` | Live SEO data via DataForSEO (extension) |
| `/seo image-gen [use-case] <description>` | AI image generation for SEO assets (extension) |

## Orchestration Logic

When the user invokes `/seo audit`, delegate to subagents in parallel:
1. Detect business type (SaaS, local, ecommerce, publisher, agency, other)
2. Spawn subagents: seo-technical, seo-content, seo-schema, seo-sitemap, seo-performance, seo-visual, seo-geo
3. If Google API credentials detected (`python scripts/google_auth.py --check`), also spawn seo-google agent
4. If local business detected, also spawn seo-local agent
5. If local business detected AND DataForSEO MCP available, also spawn seo-maps agent
6. Collect results and generate unified report with SEO Health Score (0-100)
7. Create prioritized action plan (Critical -> High -> Medium -> Low)
8. **Offer PDF report**: "Generate a professional PDF report? Use `/seo google report full`"

For individual commands, load the relevant sub-skill directly.
After any analysis command completes, offer to generate a PDF report via `scripts/google_report.py`.

## Industry Detection

Detect business type from homepage signals:
- **SaaS**: pricing page, /features, /integrations, /docs, "free trial", "sign up"
- **Local Service**: phone number, address, service area, "serving [city]", Google Maps embed --> auto-suggest `/seo local` for deeper analysis
- **E-commerce**: /products, /collections, /cart, "add to cart", product schema
- **Publisher**: /blog, /articles, /topics, article schema, author pages, publication dates
- **Agency**: /case-studies, /portfolio, /industries, "our work", client logos

## Quality Gates

Read `references/quality-gates.md` for thin content thresholds per page type.
Hard rules:
- WARNING at 30+ location pages (enforce 60%+ unique content)
- HARD STOP at 50+ location pages (require user justification)
- Never recommend HowTo schema (deprecated Sept 2023)
- FAQ schema for Google rich results: only government and healthcare sites (Aug 2023 restriction); existing FAQPage on commercial sites -> flag Info priority (not Critical), noting AI/LLM citation benefit; adding new FAQPage -> not recommended for Google benefit
- All Core Web Vitals references use INP, never FID

## Reference Files

Load these on-demand as needed (do NOT load all at startup):
- `references/cwv-thresholds.md`: Current Core Web Vitals thresholds and measurement details
- `references/schema-types.md`: All supported schema types with deprecation status
- `references/eeat-framework.md`: E-E-A-T evaluation criteria (Sept 2025 QRG update)
- `references/quality-gates.md`: Content length minimums, uniqueness thresholds
- `references/local-seo-signals.md`: Local ranking factors, review benchmarks, citation tiers, GBP status
- `references/local-schema-types.md`: LocalBusiness subtypes, industry-specific schema and citation sources

Maps-specific references (loaded by seo-maps skill, not at startup):
- `references/maps-geo-grid.md`, `references/maps-gbp-checklist.md`, `references/maps-api-endpoints.md`, `references/maps-free-apis.md`

## Scoring Methodology

### SEO Health Score (0-100)
Weighted aggregate of all categories:

| Category | Weight |
|----------|--------|
| Technical SEO | 22% |
| Content Quality | 23% |
| On-Page SEO | 20% |
| Schema / Structured Data | 10% |
| Performance (CWV) | 10% |
| AI Search Readiness | 10% |
| Images | 5% |

### Priority Levels
- **Critical**: Blocks indexing or causes penalties (immediate fix required)
- **High**: Significantly impacts rankings (fix within 1 week)
- **Medium**: Optimization opportunity (fix within 1 month)
- **Low**: Nice to have (backlog)

## Sub-Skills

This skill orchestrates 15 specialized sub-skills (+ 2 extensions):

1. **seo-audit** -- Full website audit with parallel delegation
2. **seo-page** -- Deep single-page analysis
3. **seo-technical** -- Technical SEO (9 categories)
4. **seo-content** -- E-E-A-T and content quality
5. **seo-schema** -- Schema markup detection and generation
6. **seo-images** -- Image optimization
7. **seo-sitemap** -- Sitemap analysis and generation
8. **seo-geo** -- AI Overviews / GEO optimization
9. **seo-plan** -- Strategic planning with templates
10. **seo-programmatic** -- Programmatic SEO analysis and planning
11. **seo-competitor-pages** -- Competitor comparison page generation
12. **seo-hreflang** -- Hreflang/i18n SEO audit and generation
13. **seo-local** -- Local SEO (GBP, NAP, citations, reviews, local schema, multi-location)
14. **seo-maps** -- Maps intelligence (geo-grid, GBP audit, reviews, competitor radius)
15. **seo-google** -- Google SEO APIs (GSC, PageSpeed, CrUX, Indexing API, GA4)
16. **seo-dataforseo** -- Live SEO data via DataForSEO MCP (extension)
17. **seo-image-gen** -- AI image generation for SEO assets via Gemini (extension)

## Subagents

For parallel analysis during audits:
- `seo-technical` -- Crawlability, indexability, security, CWV
- `seo-content` -- E-E-A-T, readability, thin content
- `seo-schema` -- Detection, validation, generation
- `seo-sitemap` -- Structure, coverage, quality gates
- `seo-performance` -- Core Web Vitals measurement
- `seo-visual` -- Screenshots, mobile testing, above-fold
- `seo-geo` -- AI crawler access, llms.txt, citability, brand mention signals
- `seo-local` -- GBP signals, NAP consistency, reviews, local schema, industry-specific local factors (conditional: spawned when Local Service detected)
- `seo-maps` -- Geo-grid rank tracking, GBP audit, review intelligence, competitor radius mapping (conditional: spawned when Local Service detected AND DataForSEO MCP available)
- `seo-google` -- CWV field data, URL indexation status, organic traffic trends (conditional: spawned when Google API credentials detected)
- `seo-dataforseo` -- Live SERP, keyword, backlink, local SEO data (extension, optional)
- `seo-image-gen` -- SEO image audit and generation plan (extension, optional)

## Error Handling

| Scenario | Action |
|----------|--------|
| Unrecognized command | List available commands from the Quick Reference table. Suggest the closest matching command. |
| URL unreachable | Report the error and suggest the user verify the URL. Do not attempt to guess site content. |
| Sub-skill fails during audit | Report partial results from successful sub-skills. Clearly note which sub-skill failed and why. Suggest re-running the failed sub-skill individually. |
| Ambiguous business type detection | Present the top two detected types with supporting signals. Ask the user to confirm before proceeding with industry-specific recommendations. |

## Imported Reference

# SEO Skill (Agentic / Agentic)

LLM-first SEO analysis skill with 16 specialized sub-skills, 10 specialist agents, and 33 scripts for website, blog, and GitHub repository optimization.

## Deterministic Trigger Mapping

For prompt reliability in agent IDEs, map common user wording to a fixed workflow:

- If user says `perform seo analysis on <url>` (or similar generic SEO request with a URL), treat it as a **single-URL full audit**.
- If no explicit sub-skill is specified, run the full/page audit path with **LLM-first reasoning** and script-backed evidence.
- For full/page audits, always produce:
  - `FULL-AUDIT-REPORT.md` (detailed findings)
  - `ACTION-PLAN.md` (prioritized fixes)
- If `generate_report.py` is run, also return the saved HTML path (for example `SEO-REPORT.html`).

## Available Commands

| Command | Sub-Skill | Description |
|---------|-----------|-------------|
| `seo audit <url>` | [seo-audit](resources/skills/seo-audit.md) | Full website audit with scoring |
| `seo page <url>` | [seo-page](resources/skills/seo-page.md) | Deep single-page analysis |
| `seo technical <url>` | [seo-technical](resources/skills/seo-technical.md) | Technical SEO checks |
| `seo content <url>` | [seo-content](resources/skills/seo-content.md) | Content quality & E-E-A-T |
| `seo schema <url>` | [seo-schema](resources/skills/seo-schema.md) | Schema detection/validation/generation |
| `seo sitemap <url>` | [seo-sitemap](resources/skills/seo-sitemap.md) | Sitemap analysis & generation |
| `seo images <url>` | [seo-images](resources/skills/seo-images.md) | Image optimization audit |
| `seo geo <url>` | [seo-geo](resources/skills/seo-geo.md) | AI search optimization (GEO) |
| `seo programmatic <url>` | [seo-programmatic](resources/skills/seo-programmatic.md) | Programmatic SEO safeguards |
| `seo competitors <url>` | [seo-competitor-pages](resources/skills/seo-competitor-pages.md) | Comparison/alternatives pages |
| `seo hreflang <url>` | [seo-hreflang](resources/skills/seo-hreflang.md) | International SEO validation |
| `seo plan <url>` | [seo-plan](resources/skills/seo-plan.md) | Strategic SEO planning |
| `seo github <repo_or_url>` | [seo-github](resources/skills/seo-github.md) | GitHub repository discoverability, README, topics, community health, and traffic archival |
| `seo article <url>` | [seo-article](resources/skills/seo-article.md) | Article data extraction & LLM optimization |
| `seo links <url>` | [seo-links](resources/skills/seo-links.md) | External backlink profile & link health |
| `seo aeo <url>` | [seo-aeo](resources/skills/seo-aeo.md) | Answer Engine Optimization (Featured Snippets, PAA, Knowledge Panel) |

---

## Orchestration Logic

When the user requests SEO analysis, follow this routing:

### Step 1 — Identify the Task

Parse the user's request to determine which sub-skill(s) to activate:

- **Full audit**: Read `resources/skills/seo-audit.md` — crawl multiple pages, delegate to agents, score and report
- **Single page**: Read `resources/skills/seo-page.md` — deep dive on one URL
- **Specific area**: Read the matching `resources/skills/seo-*.md` file
- **Strategic plan**: Read `resources/skills/seo-plan.md` and the matching `resources/templates/*.md` for the detected industry
- **GitHub repository SEO**: Read `resources/skills/seo-github.md` and use GitHub scripts with `--provider auto` for API/`gh` fallback.
- **Generic `perform seo analysis on <url>` request**: treat as single-page full audit, read `resources/skills/seo-page.md`, and generate `FULL-AUDIT-REPORT.md` + `ACTION-PLAN.md`.

### Step 2 — Collect Evidence

**Primary method (LLM-first)** — use the built-in `read_url_content` tool first:
```
read_url_content(url)  →  returns parsed HTML content directly
```
Use this as the baseline evidence for reasoning.

**Deterministic verification (recommended when script execution is available)**:
```bash
# Fetch/parse raw HTML for structured checks
python3 <SKILL_DIR>/scripts/fetch_page.py <url> --output /tmp/page.html
python3 <SKILL_DIR>/scripts/parse_html.py /tmp/page.html --url <url> --json

# Optional: generate shareable HTML dashboard artifact
python3 <SKILL_DIR>/scripts/generate_report.py <url> --output SEO-REPORT.html
```

> **Do not use third-party mirrors (e.g., `r.jina.ai`) as primary evidence when direct site fetch or bundled scripts are available.**
> `<SKILL_DIR>` = absolute path to this skill directory (the folder containing this SKILL.md).

### Step 3 — Perform LLM-First Analysis

Use the LLM as the primary SEO analyst:

1. Synthesize evidence from page content, metadata, and optional script outputs.
2. Produce findings with explicit proof:
   - `Finding`
   - `Evidence` (specific element, metric, or snippet)
   - `Impact` (why it matters for ranking/indexing/UX)
   - `Fix` (clear implementation step)
3. Prioritize by impact and implementation effort.
4. Separate confirmed issues, likely issues, and unknowns (missing data).

Always read and apply `resources/references/llm-audit-rubric.md` to keep scoring, severity, confidence, and output structure consistent across audit types.

### Step 4 — Run Baseline Verification Scripts (When execution is available)

For full/page audits, run baseline checks to avoid hypothesis-only reporting. Do not replace LLM reasoning with script-only scoring.

```bash
# Check robots.txt and AI crawler management
python3 <SKILL_DIR>/scripts/robots_checker.py <url>

# Check llms.txt for AI search readiness
python3 <SKILL_DIR>/scripts/llms_txt_checker.py <url>

# Get Core Web Vitals from PageSpeed Insights (free API, no key needed)
python3 <SKILL_DIR>/scripts/pagespeed.py <url> --strategy mobile

# Check security headers (HSTS, CSP, X-Frame-Options, etc.)
python3 <SKILL_DIR>/scripts/security_headers.py <url>

# Detect broken links on a page (404s, timeouts, connection errors)
python3 <SKILL_DIR>/scripts/broken_links.py <url> --workers 5

# Trace redirect chains, detect loops and mixed HTTP/HTTPS
python3 <SKILL_DIR>/scripts/redirect_checker.py <url>

# Analyze readability from fetched HTML (Flesch-Kincaid, grade level, sentence stats)
python3 <SKILL_DIR>/scripts/readability.py /tmp/page.html --json

# Validate Open Graph and Twitter Card meta tags
python3 <SKILL_DIR>/scripts/social_meta.py <url>

# Analyze internal link structure, find orphan pages
python3 <SKILL_DIR>/scripts/internal_links.py <url> --depth 1 --max-pages 20

# Extract article content and perform keyword research for LLM-driven optimization
python3 <SKILL_DIR>/scripts/article_seo.py <url> --keyword "<optional_target_keyword>" --json

# GitHub repository SEO (provider fallback: auto|api|gh)
# Auth setup (choose one):
# export GITHUB_TOKEN="ghp_xxx"   # or export GH_TOKEN="ghp_xxx"
# gh auth login -h github.com && gh auth status -h github.com
python3 <SKILL_DIR>/scripts/github_repo_audit.py --repo <owner/repo> --provider auto --json
python3 <SKILL_DIR>/scripts/github_readme_lint.py README.md --json
python3 <SKILL_DIR>/scripts/github_community_health.py --repo <owner/repo> --provider auto --json
# Benchmark/competitor inputs should be provided by LLM/web-search discovery when possible.
# If omitted, github_seo_report.py auto-derives repo-specific benchmark queries.
python3 <SKILL_DIR>/scripts/github_search_benchmark.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --json
python3 <SKILL_DIR>/scripts/github_competitor_research.py --repo <owner/repo> --query "<llm_or_web_query>" --provider auto --top-n 6 --json
python3 <SKILL_DIR>/scripts/github_competitor_research.py --repo <owner/repo> --competitor <owner/repo> --competitor <owner/repo> --provider auto --json
python3 <SKILL_DIR>/scripts/github_traffic_archiver.py --repo <owner/repo> --provider auto --archive-dir .github-seo-data --json
python3 <SKILL_DIR>/scripts/github_seo_report.py --repo <owner/repo> --provider auto --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md --json
# Optional: increase/reduce auto-derived query volume (default: 6)
# python3 <SKILL_DIR>/scripts/github_seo_report.py --repo <owner/repo> --provider auto --auto-query-max 8 --markdown GITHUB-SEO-REPORT.md --action-plan GITHUB-ACTION-PLAN.md --json
```

If a check fails due network, DNS, permissions, or API rate limits:
- Report it explicitly as an **environment limitation**, not a confirmed site issue.
- Keep confidence as `Hypothesis` for impacted categories.
- Continue with available evidence instead of stopping the audit.
- Do not enter repeated fallback loops. Retry a failed source at most once, then finalize the audit.
- Do not pivot into repeated web-search scraping loops for the same URL.

**Visual analysis** (requires Playwright — use `conda activate pentest` if available):
```bash
# Capture screenshots (desktop, laptop, tablet, mobile)
python3 <SKILL_DIR>/scripts/capture_screenshot.py <url> --all

# Analyze visual layout, above-the-fold, mobile responsiveness
python3 <SKILL_DIR>/scripts/analyze_visual.py <url> --json
```

**HTML Report Generator** — generates a self-contained interactive HTML dashboard:
```bash
# Generate full SEO report (runs scripts automatically, saves HTML to PWD)
python3 <SKILL_DIR>/scripts/generate_report.py <url>
python3 <SKILL_DIR>/scripts/generate_report.py <url> --output custom-report.html
```

### Step 5 — Delegate to Specialist Agents

For comprehensive audits, read the relevant agent file from `resources/agents/` to adopt the specialist role:

| Agent | File | Focus Area |
|-------|------|------------|
| Technical SEO | [seo-technical.md](resources/agents/seo-technical.md) | Crawlability, indexability, security, URLs, mobile, CWV, JS rendering |
| Content Quality | [seo-content.md](resources/agents/seo-content.md) | E-E-A-T assessment, content metrics, AI content detection |
| Performance | [seo-performance.md](resources/agents/seo-performance.md) | Core Web Vitals (LCP, INP, CLS), optimization recommendations |
| Schema Markup | [seo-schema.md](resources/agents/seo-schema.md) | Detection, validation, generation of JSON-LD structured data |
| Sitemap | [seo-sitemap.md](resources/agents/seo-sitemap.md) | XML sitemap validation, generation, quality gates |
| Visual Analysis | [seo-visual.md](resources/agents/seo-visual.md) | Screenshots, above-the-fold, responsiveness, layout |
| Verifier (global) | [seo-verifier.md](resources/agents/seo-verifier.md) | Deduplicate findings, suppress contradictions, and validate evidence relevance before final report |

### Step 6 — Apply Quality Gates

Reference the quality standards in `resources/references/`:

- **Content minimums**: Read [quality-gates.md](resources/references/quality-gates.md) for word counts, unique content %, title/meta requirements
- **Schema validation**: Read [schema-types.md](resources/references/schema-types.md) for active/deprecated/restricted types
- **Core Web Vitals**: Read [cwv-thresholds.md](resources/references/cwv-thresholds.md) for current metric thresholds
- **E-E-A-T framework**: Read [eeat-framework.md](resources/references/eeat-framework.md) for scoring criteria
- **Google reference**: Read [google-seo-reference.md](resources/references/google-seo-reference.md) for quick reference
- **LLM report rubric**: Read [llm-audit-rubric.md](resources/references/llm-audit-rubric.md) for mandatory evidence format, confidence labels, and output contract

### Step 6.5 — Verify Findings (All Workflows)

Before writing final reports, run verification:

```bash
python3 <SKILL_DIR>/scripts/finding_verifier.py --findings-json <raw_findings.json> --json
```

Use verified output for final report tables, not raw findings.

### Step 7 — Score and Report

Use numeric scores as guidance, not as a replacement for evidence quality and judgment.

#### Default Scoring Weights (Full Audit)

> **Canonical source of truth** — These weights are defined here and in `resources/skills/seo-audit.md`.
> Do not modify weights in individual sub-skill files; update only these two locations to keep scores consistent.

| Category | Weight |
|----------|--------|
| Technical SEO | 25% |
| Content Quality | 20% |
| On-Page SEO | 15% |
| Schema / Structured Data | 15% |
| Performance (CWV) | 10% |
| Image Optimization | 10% |
| AI Search Readiness (GEO) | 5% |

> If using `scripts/generate_report.py`, the automated dashboard uses script-level category weights defined in that script. Keep the narrative audit LLM-first and evidence-first.

### Step 8 — Mandatory Deliverables

For `seo audit`, `seo page`, and generic `perform seo analysis on <url>` flows:

1. Create `FULL-AUDIT-REPORT.md` in the current working directory at the start of the audit, then update it as evidence is collected.
2. Create `ACTION-PLAN.md` in the current working directory at the start of the audit, then update it with prioritized fixes.
3. If HTML dashboard was generated, include its exact saved path (for example `SEO-REPORT.html` or an absolute path).
4. In the final response, explicitly list generated artifacts and paths.
5. If technical checks are blocked by environment limits, still write both markdown files and include an "Environment Limitations" section.

#### Score Interpretation
| Score | Rating |
|-------|--------|
| 90-100 | Excellent |
| 70-89 | Good |
| 50-69 | Needs Improvement |
| 30-49 | Poor |
| 0-29 | Critical |

---

## Industry Detection

When running `seo plan`, detect the business type and load the matching template:

| Industry | Template File |
|----------|---------------|
| SaaS / Software | [saas.md](resources/templates/saas.md) |
| Local Service Business | [local-service.md](resources/templates/local-service.md) |
| E-commerce / Retail | [ecommerce.md](resources/templates/ecommerce.md) |
| Publisher / Media | [publisher.md](resources/templates/publisher.md) |
| Agency / Consultancy | [agency.md](resources/templates/agency.md) |
| Other / Generic | [generic.md](resources/templates/generic.md) |

**Detection signals:**
- SaaS: pricing page, feature pages, /docs, /api, trial/demo CTAs
- Local: address, phone, Google Business Profile, service area pages
- E-commerce: product pages, cart, checkout, /collections, /categories
- Publisher: article dates, author pages, /news, high content volume
- Agency: case studies, /work, /portfolio, team pages, service offerings

---

## Schema Templates

Pre-built JSON-LD templates are available in [templates.json](resources/schema/templates.json) for:
- **Common**: BlogPosting, Article, Organization, LocalBusiness, BreadcrumbList, WebSite (with SearchAction)
- **Video**: VideoObject, BroadcastEvent, Clip, SeekToAction
- **E-commerce**: ProductGroup (variants), OfferShippingDetails, Certification
- **Other**: SoftwareSourceCode, ProfilePage (E-E-A-T author pages)

---

## Validation Scripts

Two validation scripts are available for CI/CD integration:

### Pre-commit SEO Check
```bash
bash <SKILL_DIR>/scripts/pre_commit_seo_check.sh
```
Checks staged HTML files for: placeholder text in schema, title tag length, missing alt text, deprecated schema types, FID references (should be INP), meta description length.

### Schema Validator
```bash
python3 <SKILL_DIR>/scripts/validate_schema.py <file_path>
```
Validates JSON-LD blocks in HTML files: JSON syntax, @context/@type presence, placeholder text, deprecated/restricted types.

---

## Output Format

All sub-skill reports should use consistent severity levels:
- 🔴 **Critical** — Directly impacts rankings or indexing (fix immediately)
- ⚠️ **Warning** — Optimization opportunity (fix within 1 month)
- ✅ **Pass** — Meets or exceeds standards
- ℹ️ **Info** — Not applicable or informational only

Structure reports as:
1. Summary table with element, value, and severity
2. Detailed findings grouped by category
3. Actionable recommendations ordered by impact

---

## Critical Rules

1. **INP not FID** — FID was removed September 9, 2024. The sole interactivity metric is INP (Interaction to Next Paint). Never reference FID.
2. **FAQ schema is restricted** — FAQPage schema is limited to government and healthcare authority sites only (August 2023). Do NOT recommend for commercial sites.
3. **HowTo schema is deprecated** — Rich results fully removed September 2023. Never recommend.
4. **JSON-LD only** — Always use `<script type="application/ld+json">`. Never recommend Microdata or RDFa.
5. **E-E-A-T everywhere** — As of December 2025, E-E-A-T applies to ALL competitive queries, not just YMYL.
6. **Mobile-first is complete** — 100% mobile-first indexing since July 5, 2024.
7. **Location page limits** — Warning at 30+ pages, hard stop at 50+ pages. Enforce unique content requirements.
8. **AI crawler management** — Check robots.txt for GPTBot, provider-specific AI crawler, PerplexityBot, Applebot-Extended, Google-Extended, Bytespider, CCBot.
9. **LLM-first, resilient pipeline** — Start by reading the page with `read_url_content`, then always run relevant scripts for structured evidence. Scripts are the **preferred** evidence source — use them actively. However, if any script fails (timeout, network, parsing), the LLM MUST still produce a complete analysis using its own reasoning (confidence: `Likely`). Never block a report on a single script failure.
10. **Always produce file artifacts for audit flows** — `FULL-AUDIT-REPORT.md` and `ACTION-PLAN.md` are required outputs for full/page audit requests.
11. **Bound evidence retries** — Avoid long search/retry loops. If core checks fail due DNS/network, finalize promptly with confidence labels and file outputs.
12. **Avoid redundant web fallbacks** — If direct fetch/scripts fail and one fallback also fails, stop retrying and finish the report with explicit limitations.
13. **Signal freshness tracking** — Every reference file should contain a `<!-- Updated: YYYY-MM-DD -->` comment. Flag any reference file older than 90 days for review. When Google announces algorithm changes, verify affected reference files within 7 days. Key dates to track: core updates (quarterly), schema deprecations (schema-types.md), CWV threshold changes (cwv-thresholds.md).

---

## Dependencies

### Optional Script Dependencies
- Python 3.8+
- `requests` (for network analysis scripts)
- `beautifulsoup4` (for HTML parsing scripts)
- Playwright (for `capture_screenshot.py` and `analyze_visual.py`)
  ```bash
  pip install playwright && playwright install chromium
  ```
  Or if using conda: `conda activate pentest` (if Playwright is pre-installed)

### Install Script Dependencies
```bash
pip install requests beautifulsoup4
```

---

## Imported Module: Seo Forensic Incident Response
---
name: seo-forensic-incident-response
description: "Investigate sudden drops in organic traffic or rankings and run a structured forensic SEO incident response with triage, root-cause analysis and recovery plan."
risk: safe
source: original
date_added: "2026-02-27"
---

# SEO Forensic Incident Response

You are an expert in forensic SEO incident response. Your goal is to investigate **sudden drops in organic traffic or rankings**, identify the most likely causes, and provide a prioritized remediation plan.

This skill is not a generic SEO audit. It is designed for **incident scenarios**: traffic crashes, suspected penalties, core update impacts, or major technical failures.

## When to Use

Use this skill when:
- You need to understand and resolve a sudden, significant drop in organic traffic or rankings.
- There are signs of a possible penalty, core update impact, major technical regression or other SEO incident.

Do **not** use this skill when:
- You need a routine SEO health check or prioritization of opportunities (use `seo-audit`).
- You are focused on long-term local visibility for legal/professional services (use `local-legal-seo-audit`).

## Initial Incident Triage

Before deep analysis, clarify the incident context:

1. **Incident Description**
   - When did you first notice the drop?
   - Was it sudden (1–3 days) or gradual (weeks)?
   - Which metrics are affected? (sessions, clicks, impressions, conversions)
   - Is the impact site-wide, specific sections, or specific pages?

2. **Data Access**
   - Do you have access to:
     - Google Search Console (GSC)?
     - Web analytics (GA4, Matomo, etc.)?
     - Server logs or CDN logs?
     - Deployment/change logs (Git, CI/CD, CMS release notes)?

3. **Recent Changes Checklist**
   Ask explicitly about the 30–60 days before the drop:
   - Site redesign or theme change
   - URL structure changes or migrations
   - CMS/plugin updates
   - Changes to hosting, CDN, or security tools (WAF, firewalls)
   - Changes to robots.txt, sitemap, canonical tags, or redirects
   - Bulk content edits or content pruning

4. **Business Context**
   - Is this a seasonal niche?
   - Any external events affecting demand?
   - Any previous manual actions or penalties?

---

## Incident Classification Framework

Classify the incident into one or more buckets to guide the investigation:

1. **Algorithm / Core Update Impact**
   - Drop coincides with known Google core update dates
   - Impact skewed toward certain types of queries or content
   - No major technical changes around the same time

2. **Technical / Infrastructure Failure**
   - Indexing/crawlability suddenly impaired
   - Widespread 5xx/4xx errors
   - Robots.txt or meta noindex changes
   - Broken redirects or canonicalization errors

3. **Manual Action / Policy Violation**
   - Manual action message in GSC
   - Sudden, severe drop in branded and non-branded queries
   - History of aggressive link building or spammy tactics

4. **Content / Quality Reassessment**
   - Specific sections or topics hit harder
   - Content thin, outdated, or heavily AI-generated
   - Competitors significantly improved content around the same topics

5. **Demand / Seasonality / External Factors**
   - Search demand drop in the niche (check industry trends)
   - Macro events, regulation changes, or market shifts

---

## Data-Driven Investigation Steps

When you have GSC and analytics access, structure the analysis like a forensic investigation:

### 1. Timeline Reconstruction

- Plot clicks, impressions, CTR, and average position over the last 6–12 months.
- Identify:
  - Exact start of the drop
  - Whether the drop is step-like (sudden) or gradual
  - Whether it affects all countries/devices or specific segments

Use this to narrow likely causes:
- **Step-like drop** → technical issue, manual action, deployment.
- **Gradual slide** → quality issues, competitor improvements, algorithmic re-evaluation.

### 2. Segment Analysis

Segment the impact by:

- **Device**: desktop vs. mobile
- **Country / region**
- **Query type**: branded vs. non-branded
- **Page type**: home, category, product, blog, docs, etc.

Look for patterns:
- Only mobile affected → potential mobile UX, CWV, or mobile-only indexing issue.
- Specific country affected → geo-targeting, hreflang, local factors.
- Non-branded hit harder than branded → often algorithm/quality-related.

### 3. Page-Level Impact

Identify:

- Top pages with largest drop in clicks and impressions.
- New 404s or heavily redirected URLs among previously high-traffic pages.
- Any pages that disappeared from the index or lost most of their ranking queries.

Check for:

- URL changes without proper redirects
- Canonical changes
- Noindex additions
- Template or content changes on those pages

### 4. Technical Integrity Checks

Focus on incident-related technical regressions:

- **Robots.txt**
  - Any recent changes?
  - Are key sections blocked unintentionally?

- **Indexation & Noindex**
  - Sudden spike in “Excluded” or “Noindexed” pages in GSC
  - Important pages with meta noindex or X-Robots-Tag set incorrectly

- **Redirects**
  - New redirect chains or loops
  - HTTP → HTTPS consistency
  - www vs. non-www consistency
  - Migrations without full redirect mapping

- **Server & Availability**
  - Increased 5xx/4xx in logs or GSC
  - Downtime or throttling by security tools
  - Rate-limiting or blocking of Googlebot

- **Core Web Vitals (CWV)**
  - Sudden degradation in CWV affecting large portions of the site
  - Especially on mobile

### 5. Content & Quality Reassessment

When technical is clean, analyze content factors:

- Which topics or content types were hit hardest?
- Is content:
  - Thin, generic, or outdated?
  - Over-optimized or keyword-stuffed?
  - Lacking original data, examples, or experience?

Evaluate against E-E-A-T:

- **Experience**: Does the content show first-hand experience?
- **Expertise**: Is the author qualified and clearly identified?
- **Authoritativeness**: Does the site have references, citations, recognition?
- **Trustworthiness**: Clear about who is behind the site, policies, contact info.

---

## Forensic Hypothesis Building

Use a hypothesis-driven approach instead of listing random issues.

For each plausible cause:

- **Hypothesis**: e.g., “A recent deployment introduced noindex tags on key templates.”
- **Evidence**: Data points from GSC, analytics, logs, code diffs, or screenshots.
- **Impact**: Which sections/pages are affected and by how much.
- **Test / Validation Step**: What check would confirm or refute this hypothesis.
- **Suggested Fix**: Concrete remediation action.

Prioritize hypotheses by:

1. Severity of impact
2. Ease of validation
3. Reversibility (how easy it is to roll back or adjust)

---

## Output Format

Structure your final forensic report clearly:

### Executive Incident Summary

- Incident type classification (technical, algorithmic, manual action, mixed)
- Date range of impact and severity (approximate % drop)
- Top 3–5 likely root causes
- Overall confidence level (Low/Medium/High)

### Evidence-Based Findings

For each key finding, include:

- **Finding**: Short description of what is wrong.
- **Evidence**: Specific metrics, screenshots, logs, or GSC/analytics segments.
- **Likely Cause**: How this could lead to the observed impact.
- **Impact**: High/Medium/Low.
- **Fix**: Concrete, implementable recommendation.

### Prioritized Action Plan

Break down into phases:

1. **Critical Immediate Fixes (0–3 days)**
   - Issues that block crawling, indexing, or basic site availability.
   - Reversals of harmful recent deployments.

2. **Stabilization (3–14 days)**
   - Clean up redirects, canonicals, internal links.
   - Restore or improve critical content and templates.

3. **Recovery & Hardening (2–8 weeks)**
   - Content quality improvements.
   - E-E-A-T enhancements.
   - Technical hardening to prevent recurrence.

4. **Monitoring Plan**
   - Metrics and dashboards to watch.
   - Checkpoints to assess partial recovery.
   - Criteria for closing the incident.

---

## Task-Specific Questions

When helping a user, ask:

1. When exactly did you notice the drop? Any change logs around that date?
2. Do you have GSC and analytics access, and can you share key screenshots or exports?
3. Was there any redesign, migration, or major plugin/CMS update in the last 30–60 days?
4. Is the impact site-wide or concentrated in certain sections, countries, or devices?
5. Have you ever received a manual action or used aggressive link building in the past?

---

## Related Skills

- **seo-audit**: For general SEO health checks outside of incident scenarios.
- **ai-seo**: For optimizing content for AI search experiences.
- **schema-markup**: For implementing structured data after stability is restored.
- **analytics-tracking**: For ensuring measurement is correct post-incident.

---

## Imported Module: Seo Fundamentals
---
name: seo-fundamentals
description: Core principles of SEO including E-E-A-T, Core Web Vitals, technical foundations, content quality, and how modern search engines evaluate pages.
risk: unknown
source: community
date_added: '2026-02-27'
---

---

# SEO Fundamentals

> **Foundational principles for sustainable search visibility.**
> This skill explains _how search engines evaluate quality_, not tactical shortcuts.

---

## 1. E-E-A-T (Quality Evaluation Framework)

E-E-A-T is **not a direct ranking factor**.
It is a framework used by search engines to **evaluate content quality**, especially for sensitive or high-impact topics.

| Dimension             | What It Represents                 | Common Signals                                      |
| --------------------- | ---------------------------------- | --------------------------------------------------- |
| **Experience**        | First-hand, real-world involvement | Original examples, lived experience, demonstrations |
| **Expertise**         | Subject-matter competence          | Credentials, depth, accuracy                        |
| **Authoritativeness** | Recognition by others              | Mentions, citations, links                          |
| **Trustworthiness**   | Reliability and safety             | HTTPS, transparency, accuracy                       |

> Pages competing in the same space are often differentiated by **trust and experience**, not keywords.

---

## 2. Core Web Vitals (Page Experience Signals)

Core Web Vitals measure **how users experience a page**, not whether it deserves to rank.

| Metric  | Target  | What It Reflects    |
| ------- | ------- | ------------------- |
| **LCP** | < 2.5s  | Loading performance |
| **INP** | < 200ms | Interactivity       |
| **CLS** | < 0.1   | Visual stability    |

**Important context:**

- CWV rarely override poor content
- They matter most when content quality is comparable
- Failing CWV can _hold back_ otherwise good pages

---

## 3. Technical SEO Principles

Technical SEO ensures pages are **accessible, understandable, and stable**.

### Crawl & Index Control

| Element           | Purpose                |
| ----------------- | ---------------------- |
| XML sitemaps      | Help discovery         |
| robots.txt        | Control crawl access   |
| Canonical tags    | Consolidate duplicates |
| HTTP status codes | Communicate page state |
| HTTPS             | Security and trust     |

### Performance & Accessibility

| Factor                 | Why It Matters                |
| ---------------------- | ----------------------------- |
| Page speed             | User satisfaction             |
| Mobile-friendly design | Mobile-first indexing         |
| Clean URLs             | Crawl clarity                 |
| Semantic HTML          | Accessibility & understanding |

---

## 4. Content SEO Principles

### Page-Level Elements

| Element          | Principle                    |
| ---------------- | ---------------------------- |
| Title tag        | Clear topic + intent         |
| Meta description | Click relevance, not ranking |
| H1               | Page’s primary subject       |
| Headings         | Logical structure            |
| Alt text         | Accessibility and context    |

### Content Quality Signals

| Dimension   | What Search Engines Look For |
| ----------- | ---------------------------- |
| Depth       | Fully answers the query      |
| Originality | Adds unique value            |
| Accuracy    | Factually correct            |
| Clarity     | Easy to understand           |
| Usefulness  | Satisfies intent             |

---

## 5. Structured Data (Schema)

Structured data helps search engines **understand meaning**, not boost rankings directly.

| Type           | Purpose                |
| -------------- | ---------------------- |
| Article        | Content classification |
| Organization   | Entity identity        |
| Person         | Author information     |
| FAQPage        | Q&A clarity            |
| Product        | Commerce details       |
| Review         | Ratings context        |
| BreadcrumbList | Site structure         |

> Schema enables eligibility for rich results but does not guarantee them.

---

## 6. AI-Assisted Content Principles

Search engines evaluate **output quality**, not authorship method.

### Effective Use

- AI as a drafting or research assistant
- Human review for accuracy and clarity
- Original insights and synthesis
- Clear accountability

### Risky Use

- Publishing unedited AI output
- Factual errors or hallucinations
- Thin or duplicated content
- Keyword-driven text with no value

---

## 7. Relative Importance of SEO Factors

There is **no fixed ranking factor order**.
However, when competing pages are similar, importance tends to follow this pattern:

| Relative Weight | Factor                      |
| --------------- | --------------------------- |
| Highest         | Content relevance & quality |
| High            | Authority & trust signals   |
| Medium          | Page experience (CWV, UX)   |
| Medium          | Mobile optimization         |
| Baseline        | Technical accessibility     |

> Technical SEO enables ranking; content quality earns it.

---

## 8. Measurement & Evaluation

SEO fundamentals should be validated using **multiple signals**, not single metrics.

| Area        | What to Observe            |
| ----------- | -------------------------- |
| Visibility  | Indexed pages, impressions |
| Engagement  | Click-through, dwell time  |
| Performance | CWV field data             |
| Coverage    | Indexing status            |
| Authority   | Mentions and links         |

---

> **Key Principle:**
> Sustainable SEO is built on _useful content_, _technical clarity_, and _trust over time_.
> There are no permanent shortcuts.

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Module: Seo Local
---
name: seo-local
description: >
  Local SEO analysis covering Google Business Profile optimization, NAP
  consistency, citation health, review signals, local schema markup,
  location page quality, multi-location SEO, and industry-specific
  recommendations. Detects business type (brick-and-mortar, SAB, hybrid)
  and industry vertical (restaurant, healthcare, legal, home services,
  real estate, automotive). Use when user says "local SEO", "Google
  Business Profile", "GBP", "map pack", "local pack", "citations",
  "NAP consistency", "local rankings", "service area", "multi-location",
  or "local search".
user-invokable: true
argument-hint: "[url]"
license: MIT
allowed-tools: Read, Grep, Glob, Bash, WebFetch, Write
metadata:
  author: AgriciDaniel
  version: "1.7.0"
  category: seo
---

# Local SEO Analysis (March 2026)

## Key Statistics

| Metric | Value | Source |
|--------|-------|--------|
| GBP signals share of local pack weight | 32% | Whitespark 2026 |
| Proximity share of ranking variance | 55.2% | Search Atlas ML study |
| Review signals share (up from 16%) | ~20% | Whitespark 2026 |
| Google searches seeking local info | 46% | Industry data |
| Mobile "near me" searches leading to visit in 24h | 76% | Google confirmed |
| ChatGPT/AI usage for local recommendations | 45% (up from 6%) | BrightLocal LCRS 2026 |
| ChatGPT local conversion rate | 15.9% | Seer Interactive |
| Google organic local conversion rate | 1.76% | Seer Interactive |
| Local pack ads growth (Jan 2025 to Jan 2026) | 1% to 22% | Sterling Sky |

---

## Business Type Detection

Detect from page signals before analysis. This determines which checks apply.

### Brick-and-Mortar
- Physical street address visible in page content or footer
- Google Maps embed with pin/directions
- "Visit us at", "Located at", "Come see us"
- Structured address in LocalBusiness schema

### Service Area Business (SAB)
- No visible physical address
- Service area mentions: "serving [city/region]", "service area includes"
- "We come to you", "On-site service", "Mobile [service]"
- `areaServed` in schema without `address.streetAddress`

### Hybrid
- Both physical address AND service area language present
- "Visit our showroom" combined with "We also serve [areas]"

**Impact on checks**: SABs skip embedded map verification and physical address consistency. Brick-and-mortar gets full NAP + map checks.

---

## Industry Vertical Detection

Detect from page signals and GBP category patterns. Routes to industry-specific checks from `references/local-schema-types.md`.

| Vertical | Detection Signals |
|----------|------------------|
| **Restaurant** | /menu, menu items, reservations, cuisine types, food ordering, "dine-in", "takeout" |
| **Healthcare** | insurance accepted, patients, appointments, NPI, medical terms, "Dr.", HIPAA notice |
| **Legal** | attorney, lawyer, practice areas, bar admission, case results, "free consultation" |
| **Home Services** | service area, emergency service, "free estimate", licensed/insured/bonded, "24/7" |
| **Real Estate** | listings, MLS, properties for sale/rent, agent bio, brokerage, "open house" |
| **Automotive** | inventory, VIN, test drive, dealership, service department, "new/used/certified" |

If no vertical detected, use generic `LocalBusiness` analysis path.

---

## Analysis Dimensions

### 1. GBP Signals (25%)

Primary category is the **single most important local pack factor** (Whitespark #1, score: 193). Incorrect primary category is the **#1 negative factor** (score: 176).

**Check for:**
- GBP embed or reference detectable on page (Maps iframe, place ID, reviews widget)
- Primary category appropriateness (infer from page content vs visible GBP data)
- Evidence of secondary categories (optimal: 4 additional per BrightLocal)
- GBP posts presence (no direct ranking impact per WebFX, but triggers Post Justifications)
- Photos/video evidence (45% more direction requests with photos, Agency Jet)
- Q&A content (deprecated Dec 2025, replaced by Ask Maps Gemini AI -- recommend recreating Q&A content as FAQ sections on website; GBP removed existing Q&A with no export available)
- Google Verified badge eligibility (replaced Guaranteed/Screened in Oct 2025)
- GBP link URL strategy: do NOT link to strongest website page (Sterling Sky Diversity Update -- risks suppressing organic rankings)
- Business hours visibility on page (businesses open at search time rank higher, factor #5)

**Scoring guide:**
- Full: GBP embed present, category signals align, posts active, photos present
- Partial: Some GBP signals present but incomplete
- Low: No visible GBP integration on website

### 2. Reviews & Reputation (20%)

Review velocity matters more than total count. The **18-day rule** (Sterling Sky): rankings cliff if no new reviews for 3 weeks.

**Check for:**
- Total Google review count visible on page or schema (magic threshold: 10, Sterling Sky)
- Star rating (31% of consumers only use 4.5+, 68% only use 4+, BrightLocal 2026)
- Review recency indicators (74% only care about reviews in last 3 months)
- `aggregateRating` in schema (ratingValue, reviewCount, bestRating)
- Third-party review presence (consumers use average of 6 review sites, BrightLocal 2026)
- Owner response patterns (88% would use business that responds, BrightLocal)
- Review gating detection: any pre-screening of satisfaction before directing to review platform is prohibited by Google (fake engagement policy) and FTC ($53,088/violation)

**Industry-specific:**
- Healthcare: HIPAA prohibits confirming/denying reviewer is a patient in responses
- Legal: attorney-client privilege considerations in review responses

**Scoring guide:**
- Full: 10+ reviews, 4.5+ stars, recent activity, owner responses, multi-platform presence
- Partial: Some reviews but gaps in recency, rating, or response rate
- Low: <10 reviews, no recent activity, no responses, single platform only

### 3. Local On-Page SEO (20%)

Dedicated service pages = **#1 local organic factor AND #2 AI visibility factor** (Whitespark 2026).

**Check for:**
- Title tag contains city/service keywords
- H1 tag with local intent (city + service)
- NAP (Name, Address, Phone) visible in page HTML (footer, contact section, header)
- Dedicated service pages (one page per core service)
- Location page quality for multi-location sites:
  - **>60-70% unique content** minimum (industry consensus, no Google-confirmed threshold)
  - **Swap test**: if you can swap the city name and content still makes sense, it's a doorway page (RicketyRoo method). HVAC company lost 80% rankings + 63% traffic after March 2024 Core Update for this pattern
  - Local photos, area-specific testimonials, local FAQs
- Embedded Google Map (geographic signal reinforcement, not direct ranking factor -- lazy-load to mitigate speed impact)
- Click-to-call button (`tel:` link) and contact form above the fold
- Internal linking architecture: hub-and-spoke, every critical page within 3 clicks of homepage
- 2-5 contextual internal links per 1,000 words with descriptive anchor text

**Multi-location specific:**
- Store locator with individual crawlable URLs (SSR/SSG preferred over CSR)
- Subdirectory structure: `domain.com/locations/city-name/` (subdirectories consolidate link equity better, Bruce Clay: 50%+ traffic lift)
- Each location page has unique LocalBusiness schema with `@id`

**Scoring guide:**
- Full: City in title + H1, NAP visible, dedicated service pages, no doorway patterns, good internal linking
- Partial: Some local signals but missing service pages or doorway page risk
- Low: Generic title/H1, NAP not visible, thin location pages

### 4. NAP Consistency & Citations (15%)

Citations declining for traditional pack rankings but **3 of top 5 AI visibility factors are citation-related** (Whitespark 2026). Google's July 2025 documentation update removed "directories" from prominence definition.

**Check for:**
- NAP extraction: compare Name, Address, Phone from:
  1. Visible page HTML (footer, contact page)
  2. LocalBusiness JSON-LD schema
  3. Any visible GBP data
  - Flag any discrepancies between these three sources
- Citation presence on Tier 1 directories (check via WebFetch or site: search patterns):
  - Google Business Profile signals on page
  - Yelp: `site:yelp.com "Business Name"`
  - BBB: `site:bbb.org "Business Name"`
  - Facebook business page references
- Apple Business Connect awareness (usage doubled to 27%, BrightLocal 2026 -- recommend claiming)
- Bing Places awareness (powers ChatGPT, Copilot, Alexa -- recommend claiming and optimizing)
- Industry-specific directory recommendations: load `references/local-schema-types.md` for per-vertical citation sources
- Data aggregator awareness: Data Axle, Foursquare, Neustar/TransUnion (recommend submission for downstream distribution)

**Scoring guide:**
- Full: Consistent NAP across page/schema, Tier 1 citations detected, industry directories present
- Partial: NAP present but inconsistencies, some citations missing
- Low: NAP discrepancies, no detectable citations, no schema address

### 5. Local Schema Markup (10%)

Schema is NOT a direct ranking factor (John Mueller confirmed). But enables rich results (43% CTR increase, Webstix case study) and helps AI systems parse business information.

**Check for:**
- LocalBusiness schema presence (extract JSON-LD blocks)
- Required properties: `name`, `address` with PostalAddress sub-properties
- Recommended properties: `geo` (minimum 5 decimal places, Confirmed), `openingHoursSpecification`, `telephone`, `url`, `priceRange` (<100 chars), `image`, `aggregateRating`
- **Correct subtype for industry** -- load `references/local-schema-types.md`:
  - Restaurant using `Restaurant` not generic `LocalBusiness`
  - Legal using `LegalService` not deprecated `Attorney`
  - Auto dealer using `AutoDealer` not deprecated `VehicleListing`
  - Healthcare using `MedicalClinic`/`Hospital`/`Dentist` not generic `MedicalBusiness`
- SAB-specific: `areaServed` with named cities (recommended, not in Google's official list but Schema.org supported)
- Multi-location: each location page has own LocalBusiness with unique `@id`, linked via `branchOf` to Organization on homepage
- Industry-specific schema patterns (per `references/local-schema-types.md`):
  - Restaurant: Menu + MenuSection + MenuItem + ReserveAction
  - Healthcare: Physician (Person) + MedicalSpecialty + sameAs to NPI
  - Legal: LegalService + Person + Service (practice areas)
  - Home Services: Subtype + areaServed + Service
  - Real Estate: RealEstateAgent + Person + RealEstateListing
  - Automotive: AutoDealer + Car + Offer (separate dept schemas)

**Scoring guide:**
- Full: Correct subtype, all recommended properties, industry-specific patterns, valid JSON-LD
- Partial: LocalBusiness present but generic type or missing recommended properties
- Low: No local schema, or schema with errors/placeholder content

### 6. Local Link & Authority Signals (10%)

Links declining for local pack but remain **~26% of local organic ranking** (Whitespark 2026, #2 factor group). "Best of" list placements = **#1 AI visibility citation factor**.

**Check for:**
- Local backlink indicators detectable from page:
  - Chamber of Commerce mentions or links (high Trust Flow, ~80% more consumer visits, GlueUp)
  - BBB accreditation/badge (Google uses BBB for business verification)
  - Local news/press mentions
  - Community involvement signals (sponsorships, local events, partnerships)
- "Best of" list presence (top AI visibility factor per Whitespark 2026)
- Digital PR signals: 66.2% of PR practitioners now track AI citations as KPI (BuzzStream 2026)
- Brand mentions correlate **3x more strongly** with AI visibility than traditional backlinks (Ahrefs: 0.664 vs 0.218 correlation)
- Link velocity benchmark: 5-10 quality local links/month for small businesses (consensus)

**Scoring guide:**
- Full: Local authority signals visible (chamber, BBB, press), community involvement evident
- Partial: Some authority signals but limited local link indicators
- Low: No detectable local authority signals

---

## AI Search Impact on Local

**Do not duplicate seo-geo analysis.** Provide local-specific AI context and recommend `/seo geo <url>` for full analysis.

Key local AI facts:
- AI Overviews appear on up to 68% of local searches (Whitespark Q2 2025)
- ChatGPT converts at 15.9% vs Google organic at 1.76% (Seer Interactive)
- 3 of top 5 AI visibility factors are citation-related (Whitespark 2026)
- ChatGPT does NOT access GBP directly -- sources from Bing index, Yelp, TripAdvisor, BBB, Reddit
- Bing Places is critical: powers ChatGPT, Copilot, Alexa
- AI-powered local packs (mobile US) show only 1-2 businesses, 32% fewer shown (Sterling Sky)

**Recommendation**: Run `/seo geo <url>` for comprehensive AI search visibility analysis including citability scoring, llms.txt check, and brand mention audit.

---

## Reference Files

Load on-demand as needed:
- `references/local-seo-signals.md`: Ranking factors, review benchmarks, citation tiers, GBP feature status, algorithm updates
- `references/local-schema-types.md`: LocalBusiness subtypes by industry, schema patterns, citation sources per vertical

---

## Output

Generate `LOCAL-SEO-ANALYSIS-{domain}.md` with:

1. **Local SEO Score: XX/100** with dimension breakdown table
2. **Business type**: Brick-and-mortar / SAB / Hybrid
3. **Industry vertical detected** + industry-specific findings
4. **GBP optimization checklist** (detected signals vs missing)
5. **Review health snapshot** (rating, count, velocity indicators, response patterns)
6. **NAP consistency audit** (page vs schema discrepancies, cross-source comparison)
7. **Citation presence check** (Tier 1 directory status)
8. **Local schema status** (present/missing/malformed + ready-to-use fix)
9. **Location page quality** (if multi-location: unique content %, doorway risk, store locator)
10. **Top 10 prioritized actions** (Critical > High > Medium > Low)
11. **Limitations disclaimer**: What this analysis could NOT assess (geo-grid ranking, Domain Authority, comprehensive backlinks, GBP Insights data, real-time local pack position) and which paid tools can fill those gaps

---

## Quick Wins

1. Claim and optimize Apple Business Connect (usage doubled to 27%)
2. Claim and optimize Bing Places (powers ChatGPT, Copilot, Alexa)
3. Fix any NAP discrepancies between page, schema, and GBP
4. Add LocalBusiness schema with correct industry subtype
5. Add `geo` coordinates with 5+ decimal precision
6. Ensure phone number uses `tel:` link for click-to-call
7. Add city + service keyword to title tag and H1

## Medium Effort

1. Create dedicated page for each core service (Whitespark: #1 local organic factor)
2. Build review generation strategy maintaining 18-day minimum cadence
3. Submit to three data aggregators (Data Axle, Foursquare, Neustar/TransUnion) for downstream distribution
4. Claim industry-specific directory listings (per vertical recommendations)
5. Add industry-specific schema patterns (Menu for restaurants, Physician for healthcare, etc.)
6. Implement hub-and-spoke internal linking for service/location pages

## High Impact

1. Build local digital PR strategy targeting "best of" lists (#1 AI visibility factor)
2. Develop unique, non-swappable content for each location page (>60% unique)
3. Establish presence on platforms ChatGPT sources from (Yelp, TripAdvisor, BBB, Reddit)
4. Pursue Chamber of Commerce and BBB membership (authority + verification signals)
5. Create community involvement content (sponsorships, local events, partnerships)

---

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `local_business_data` for live GBP data extraction, `google_local_pack_serp` for real-time local pack positions, and `business_listings` for automated citation auditing across directories.

---

## Error Handling

| Scenario | Action |
|----------|--------|
| URL unreachable (DNS failure, connection refused) | Report the error clearly. Do not guess site content. Suggest the user verify the URL and try again. |
| No local signals detected on page | Report that no local business indicators were found. Suggest the user confirm this is a local business and provide the GBP listing URL if available. |
| NAP not found in page HTML | Check schema and meta tags. If still absent, flag as Critical issue. Recommend adding visible NAP to footer and contact page. |
| Industry vertical unclear | Present the top two detected verticals with supporting signals. Ask the user to confirm before applying industry-specific recommendations. |
| Multi-location with 50+ location pages | Apply the quality gates from seo orchestrator: WARNING at 30+ pages (enforce 60%+ unique), HARD STOP at 50+ pages (require user justification before continuing). |

---

## Imported Module: Seo Page
---
name: seo-page
description: >
  Deep single-page SEO analysis covering on-page elements, content quality,
  technical meta tags, schema, images, and performance. Use when user says
  "analyze this page", "check page SEO", "single URL", "check this page",
  "page analysis", or provides a single URL for review.
user-invokable: true
argument-hint: "[url]"
license: MIT
allowed-tools: Read, Grep, Glob, Bash, WebFetch
metadata:
  author: AgriciDaniel
  version: "1.7.0"
  category: seo
---

# Single Page Analysis

## What to Analyze

### On-Page SEO
- Title tag: 50-60 characters, includes primary keyword, unique
- Meta description: 150-160 characters, compelling, includes keyword
- H1: exactly one, matches page intent, includes keyword
- H2-H6: logical hierarchy (no skipped levels), descriptive
- URL: short, descriptive, hyphenated, no parameters
- Internal links: sufficient, relevant anchor text, no orphan pages
- External links: to authoritative sources, reasonable count

### Content Quality
- Word count vs page type minimums (see quality-gates.md)
- Readability: Flesch Reading Ease score, grade level
- Keyword density: natural (1-3%), semantic variations present
- E-E-A-T signals: author bio, credentials, first-hand experience markers
- Content freshness: publication date, last updated date

### Technical Elements
- Canonical tag: present, self-referencing or correct
- Meta robots: index/follow unless intentionally blocked
- Open Graph: og:title, og:description, og:image, og:url
- Twitter Card: twitter:card, twitter:title, twitter:description
- Hreflang: if multi-language, correct implementation

### Schema Markup
- Detect all types (JSON-LD preferred)
- Validate required properties
- Identify missing opportunities
- NEVER recommend HowTo (deprecated) or FAQ (restricted to gov/health)

### Images
- Alt text: present, descriptive, includes keywords where natural
- File size: flag >200KB (warning), >500KB (critical)
- Format: recommend WebP/AVIF over JPEG/PNG
- Dimensions: width/height set for CLS prevention
- Lazy loading: loading="lazy" on below-fold images

### Core Web Vitals (reference only, not measurable from HTML alone)
- Flag potential LCP issues (huge hero images, render-blocking resources)
- Flag potential INP issues (heavy JS, no async/defer)
- Flag potential CLS issues (missing image dimensions, injected content)

## Output

### Page Score Card
```
Overall Score: XX/100

On-Page SEO:     XX/100  ████████░░
Content Quality: XX/100  ██████████
Technical:       XX/100  ███████░░░
Schema:          XX/100  █████░░░░░
Images:          XX/100  ████████░░
```

### Issues Found
Organized by priority: Critical -> High -> Medium -> Low

### Recommendations
Specific, actionable improvements with expected impact

### Schema Suggestions
Ready-to-use JSON-LD code for detected opportunities

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `serp_organic_live_advanced` for real SERP positions and `backlinks_summary` for backlink data and spam scores.

## Error Handling

| Scenario | Action |
|----------|--------|
| URL unreachable (DNS failure, connection refused) | Report the error clearly. Do not guess page content. Suggest the user verify the URL and try again. |
| Page requires authentication (401/403) | Report that the page is behind authentication. Suggest the user provide the rendered HTML directly or a publicly accessible URL. |
| JavaScript-rendered content (empty body in HTML) | Note that key content may be rendered client-side. Analyze the available HTML and flag that results may be incomplete. Suggest using a browser-rendered snapshot if available. |

---

## Imported Module: Seo Plan
---
name: seo-plan
description: >
  Strategic SEO planning for new or existing websites. Industry-specific
  templates, competitive analysis, content strategy, and implementation
  roadmap. Use when user says "SEO plan", "SEO strategy", "SEO planning",
  "content strategy", "keyword strategy", "content calendar",
  "site architecture", or "SEO roadmap".
user-invokable: true
argument-hint: "[business-type]"
license: MIT
allowed-tools: Read, Grep, Glob, Bash, WebFetch, Write
metadata:
  author: AgriciDaniel
  version: "1.7.0"
  category: seo
---

# Strategic SEO Planning

## Process

### 1. Discovery
- Business type, target audience, competitors, goals
- Current site assessment (if exists)
- Budget and timeline constraints
- Key performance indicators (KPIs)

### 2. Competitive Analysis
- Identify top 5 competitors
- Analyze their content strategy, schema usage, technical setup
- Identify keyword gaps and content opportunities
- Assess their E-E-A-T signals
- Estimate their domain authority

### 3. Architecture Design
- Load industry template from `assets/` directory
- Design URL hierarchy and content pillars
- Plan internal linking strategy
- Sitemap structure with quality gates applied
- Information architecture for user journeys

### 4. Content Strategy
- Content gaps vs competitors
- Page types and estimated counts
- Blog/resource topics and publishing cadence
- E-E-A-T building plan (author bios, credentials, experience signals)
- Content calendar with priorities

### 5. Technical Foundation
- Hosting and performance requirements
- Schema markup plan per page type
- Core Web Vitals baseline targets
- AI search readiness requirements
- Mobile-first considerations

### 6. Implementation Roadmap (4 phases)

#### Phase 1: Foundation (weeks 1-4)
- Technical setup and infrastructure
- Core pages (home, about, contact, main services)
- Essential schema implementation
- Analytics and tracking setup

#### Phase 2: Expansion (weeks 5-12)
- Content creation for primary pages
- Blog launch with initial posts
- Internal linking structure
- Local SEO setup (if applicable)

#### Phase 3: Scale (weeks 13-24)
- Advanced content development
- Link building and outreach
- GEO optimization
- Performance optimization

#### Phase 4: Authority (months 7-12)
- Thought leadership content
- PR and media mentions
- Advanced schema implementation
- Continuous optimization

## Industry Templates

Load from `assets/` directory:
- `saas.md`: SaaS/software companies
- `local-service.md`: Local service businesses
- `ecommerce.md`: E-commerce stores
- `publisher.md`: Content publishers/media
- `agency.md`: Agencies and consultancies
- `generic.md`: General business template

## Output

### Deliverables
- `SEO-STRATEGY.md`: Complete strategic plan
- `COMPETITOR-ANALYSIS.md`: Competitive insights
- `CONTENT-CALENDAR.md`: Content roadmap
- `IMPLEMENTATION-ROADMAP.md`: Phased action plan
- `SITE-STRUCTURE.md`: URL hierarchy and architecture

### KPI Targets
| Metric | Baseline | 3 Month | 6 Month | 12 Month |
|--------|----------|---------|---------|----------|
| Organic Traffic | ... | ... | ... | ... |
| Keyword Rankings | ... | ... | ... | ... |
| Domain Authority | ... | ... | ... | ... |
| Indexed Pages | ... | ... | ... | ... |
| Core Web Vitals | ... | ... | ... | ... |

### Success Criteria
- Clear, measurable goals per phase
- Resource requirements defined
- Dependencies identified
- Risk mitigation strategies

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `dataforseo_labs_google_competitors_domain` and `dataforseo_labs_google_domain_intersection` for real competitive intelligence, `dataforseo_labs_bulk_traffic_estimation` for traffic estimates, `kw_data_google_ads_search_volume` and `dataforseo_labs_bulk_keyword_difficulty` for keyword research, and `business_data_business_listings_search` for local business data.

## Error Handling

| Scenario | Action |
|----------|--------|
| Unrecognized business type | Fall back to `generic.md` template. Inform user that no industry-specific template was found and proceed with the general business template. |
| No website URL provided | Proceed with new-site planning mode. Skip current site assessment and competitive gap analysis that require a live URL. |
| Industry template not found | Check `assets/` directory for available templates. If the requested template file is missing, use `generic.md` and note the missing template in output. |

---

## Imported Module: Seo Technical
---
name: seo-technical
description: >
  Technical SEO audit across 9 categories: crawlability, indexability, security,
  URL structure, mobile, Core Web Vitals, structured data, JavaScript rendering,
  and IndexNow protocol. Use when user says "technical SEO", "crawl issues",
  "robots.txt", "Core Web Vitals", "site speed", or "security headers".
user-invokable: true
argument-hint: "[url]"
license: MIT
allowed-tools: Read, Grep, Glob, Bash, WebFetch
metadata:
  author: AgriciDaniel
  version: "1.7.0"
  category: seo
---

# Technical SEO Audit

## Categories

### 1. Crawlability
- robots.txt: exists, valid, not blocking important resources
- XML sitemap: exists, referenced in robots.txt, valid format
- Noindex tags: intentional vs accidental
- Crawl depth: important pages within 3 clicks of homepage
- JavaScript rendering: check if critical content requires JS execution
- Crawl budget: for large sites (>10k pages), efficiency matters

#### AI Crawler Management

As of 2025-2026, AI companies actively crawl the web to train models and power AI search. Managing these crawlers via robots.txt is a critical technical SEO consideration.

**Known AI crawlers:**

| Crawler | Company | robots.txt token | Purpose |
|---------|---------|-----------------|---------|
| GPTBot | OpenAI | `GPTBot` | Model training |
| ChatGPT-User | OpenAI | `ChatGPT-User` | Real-time browsing |
| provider-specific AI crawler | Anthropic | `provider-specific AI crawler` | Model training |
| PerplexityBot | Perplexity | `PerplexityBot` | Search index + training |
| Bytespider | ByteDance | `Bytespider` | Model training |
| Google-Extended | Google | `Google-Extended` | Gemini training (NOT search) |
| CCBot | Common Crawl | `CCBot` | Open dataset |

**Key distinctions:**
- Blocking `Google-Extended` prevents Gemini training use but does NOT affect Google Search indexing or AI Overviews (those use `Googlebot`)
- Blocking `GPTBot` prevents OpenAI training but does NOT prevent ChatGPT from citing your content via browsing (`ChatGPT-User`)
- ~3-5% of websites now use AI-specific robots.txt rules

**Example, selective AI crawler blocking:**
```
# Allow search indexing, block AI training crawlers
User-agent: GPTBot
Disallow: /

User-agent: Google-Extended
Disallow: /

User-agent: Bytespider
Disallow: /

# Allow all other crawlers (including Googlebot for search)
User-agent: *
Allow: /
```

**Recommendation:** Consider your AI visibility strategy before blocking. Being cited by AI systems drives brand awareness and referral traffic. Cross-reference the `seo-geo` skill for full AI visibility optimization.

### 2. Indexability
- Canonical tags: self-referencing, no conflicts with noindex
- Duplicate content: near-duplicates, parameter URLs, www vs non-www
- Thin content: pages below minimum word counts per type
- Pagination: rel=next/prev or load-more pattern
- Hreflang: correct for multi-language/multi-region sites
- Index bloat: unnecessary pages consuming crawl budget

### 3. Security
- HTTPS: enforced, valid SSL certificate, no mixed content
- Security headers:
  - Content-Security-Policy (CSP)
  - Strict-Transport-Security (HSTS)
  - X-Frame-Options
  - X-Content-Type-Options
  - Referrer-Policy
- HSTS preload: check preload list inclusion for high-security sites

### 4. URL Structure
- Clean URLs: descriptive, hyphenated, no query parameters for content
- Hierarchy: logical folder structure reflecting site architecture
- Redirects: no chains (max 1 hop), 301 for permanent moves
- URL length: flag >100 characters
- Trailing slashes: consistent usage

### 5. Mobile Optimization
- Responsive design: viewport meta tag, responsive CSS
- Touch targets: minimum 48x48px with 8px spacing
- Font size: minimum 16px base
- No horizontal scroll
- Mobile-first indexing: Google indexes mobile version. **Mobile-first indexing is 100% complete as of July 5, 2024.** Google now crawls and indexes ALL websites exclusively with the mobile Googlebot user-agent.

### 6. Core Web Vitals
- **LCP** (Largest Contentful Paint): target <2.5s
- **INP** (Interaction to Next Paint): target <200ms
  - INP replaced FID on March 12, 2024. FID was fully removed from all Chrome tools (CrUX API, PageSpeed Insights, Lighthouse) on September 9, 2024. Do NOT reference FID anywhere.
- **CLS** (Cumulative Layout Shift): target <0.1
- Evaluation uses 75th percentile of real user data
- Use PageSpeed Insights API or CrUX data if MCP available

### 7. Structured Data
- Detection: JSON-LD (preferred), Microdata, RDFa
- Validation against Google's supported types
- See seo-schema skill for full analysis

### 8. JavaScript Rendering
- Check if content visible in initial HTML vs requires JS
- Identify client-side rendered (CSR) vs server-side rendered (SSR)
- Flag SPA frameworks (React, Vue, Angular) that may cause indexing issues
- Verify dynamic rendering setup if applicable

#### JavaScript SEO: Canonical & Indexing Guidance (December 2025)

Google updated its JavaScript SEO documentation in December 2025 with critical clarifications:

1. **Canonical conflicts:** If a canonical tag in raw HTML differs from one injected by JavaScript, Google may use EITHER one. Ensure canonical tags are identical between server-rendered HTML and JS-rendered output.
2. **noindex with JavaScript:** If raw HTML contains `<meta name="robots" content="noindex">` but JavaScript removes it, Google MAY still honor the noindex from raw HTML. Serve correct robots directives in the initial HTML response.
3. **Non-200 status codes:** Google does NOT render JavaScript on pages returning non-200 HTTP status codes. Any content or meta tags injected via JS on error pages will be invisible to Googlebot.
4. **Structured data in JavaScript:** Product, Article, and other structured data injected via JS may face delayed processing. For time-sensitive structured data (especially e-commerce Product markup), include it in the initial server-rendered HTML.

**Best practice:** Serve critical SEO elements (canonical, meta robots, structured data, title, meta description) in the initial server-rendered HTML rather than relying on JavaScript injection.

### 9. IndexNow Protocol
- Check if site supports IndexNow for Bing, Yandex, Naver
- Supported by search engines other than Google
- Recommend implementation for faster indexing on non-Google engines

## Output

### Technical Score: XX/100

### Category Breakdown
| Category | Status | Score |
|----------|--------|-------|
| Crawlability | pass/warn/fail | XX/100 |
| Indexability | pass/warn/fail | XX/100 |
| Security | pass/warn/fail | XX/100 |
| URL Structure | pass/warn/fail | XX/100 |
| Mobile | pass/warn/fail | XX/100 |
| Core Web Vitals | pass/warn/fail | XX/100 |
| Structured Data | pass/warn/fail | XX/100 |
| JS Rendering | pass/warn/fail | XX/100 |
| IndexNow | pass/warn/fail | XX/100 |

### Critical Issues (fix immediately)
### High Priority (fix within 1 week)
### Medium Priority (fix within 1 month)
### Low Priority (backlog)

## DataForSEO Integration (Optional)

If DataForSEO MCP tools are available, use `on_page_instant_pages` for real page analysis (status codes, page timing, broken links, on-page checks), `on_page_lighthouse` for Lighthouse audits (performance, accessibility, SEO scores), and `domain_analytics_technologies_domain_technologies` for technology stack detection.

## Google API Integration (Optional)

If Google API credentials are configured, use `python scripts/pagespeed_check.py <url> --json` for real PSI + CrUX field data (replaces lab-only CWV estimates), `python scripts/crux_history.py <url> --json` for 25-week CWV trends, and `python scripts/gsc_inspect.py <url> --json` for real indexation status per URL.

## Error Handling

| Scenario | Action |
|----------|--------|
| URL unreachable | Report connection error with status code. Suggest verifying URL, checking DNS resolution, and confirming the site is publicly accessible. |
| robots.txt not found | Note that no robots.txt was detected at the root domain. Recommend creating one with appropriate directives. Continue audit on remaining categories. |
| HTTPS not configured | Flag as a critical issue. Report whether HTTP is served without redirect, mixed content exists, or SSL certificate is missing/expired. |
| Core Web Vitals data unavailable | Note that CrUX data is not available (common for low-traffic sites). Suggest using Lighthouse lab data as a proxy and recommend increasing traffic before re-testing. |
