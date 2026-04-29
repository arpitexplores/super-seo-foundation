# Super SEO Foundation

<!-- super-series-intro -->
> Technical SEO foundation skill for audits, crawlability, indexing, schema, sitemaps, hreflang, and Google tooling.

[View the full SUPER Skills catalogue](https://github.com/arpitexplores/skills-super).
<!-- /super-series-intro -->

Audit and fix the technical SEO baseline: crawlability, indexing, schema, sitemaps, hreflang, and tooling.

## Install

Copy this folder into your agent's skills directory, then restart or reload the agent.

```bash
cp -R super-seo-foundation ~/.your-agent/skills/
```

Use it by name:

```text
Use $super-seo-foundation to help with this request.
```

## Best For

- technical SEO audits
- indexing diagnostics
- schema and sitemap checks
- Google Search Console workflows

## Outputs

- SEO health summary
- prioritised technical fixes
- validation checklist
- risk notes

## Modules

| Module | Purpose |
| --- | --- |
| `seo-audit.md` | Baseline technical SEO, crawlability, indexability, performance, and prioritised fixes |
| `seo-google.md` | Google Search Console, PageSpeed, CrUX, indexing, and reporting workflows |
| `seo-schema.md` | Structured data, schema validation, sitemaps, hreflang, and technical markup |

## Example Prompts

- `Use $super-seo-foundation to audit https://example.com`
- `Use $super-seo-foundation to check schema and sitemap issues.`
- `Use $super-seo-foundation to diagnose why these pages are not indexed.`

## Package Contents

- `SKILL.md` is the installable skill entry point.
- `references/modules/` contains detailed workflows loaded only when needed.
- `agents/` contains optional agent metadata where supported.
- `scripts/` and `assets/` are optional helpers when bundled.

## Compatibility

This skill is plain Markdown and is intended to be agent-agnostic. If a bundled helper mentions a specific tool path, translate that instruction to the equivalent path for your environment.

## SUPER Skills Series

This repository is part of the **SUPER Skills** series: standalone, installable agent skills that can be used independently or together.

### Published Series Repositories

| Repository | Purpose |
| --- | --- |
| [skills-super](https://github.com/arpitexplores/skills-super) | Master catalogue for the full SUPER Skills collection. |
| [super-seo-growth](https://github.com/arpitexplores/super-seo-growth) | SEO growth, AI/GEO visibility, content optimisation, and programmatic SEO. |
| [super-seo-foundation](https://github.com/arpitexplores/super-seo-foundation) | Technical SEO audits, schema, indexing, sitemaps, hreflang, and tooling. |
| [super-marketing-execution](https://github.com/arpitexplores/super-marketing-execution) | Campaign orchestration, copywriting, CRO, analytics, email, social, and paid ads. |
| [super-design-core](https://github.com/arpitexplores/super-design-core) | UI/UX, product design, IA, flows, visual systems, and UI patterns. |
| [super-ai-ml-agents](https://github.com/arpitexplores/super-ai-ml-agents) | Agent architecture, tools, memory, orchestration, and multi-agent patterns. |

Start with the skill that matches the task. Use the catalogue when you want to browse the full collection or install multiple skills.

## Version

See `VERSION` and `CHANGELOG.md`.

## Licence

MIT. See the root repository `LICENSE`.
