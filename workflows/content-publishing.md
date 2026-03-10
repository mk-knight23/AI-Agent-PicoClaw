# Content Publishing — PicoClaw

Automated technical blog + docs publishing pipeline for PicoClaw hardware tutorials and edge AI guides. Adapted from OpenClaw's content-publishing workflow.

## Trigger
- **Scheduled**: Every Wednesday 8:00 AM
- **Manual**: `@PicoClaw content-publishing --post <draft_file>`

## Pipeline

### Phase 1: Draft Discovery
Scans `drafts/` for Markdown files with `status: ready` in frontmatter:
```yaml
---
title: "Run Claude on a $10 RISC-V Board"
status: ready
tags: [picoclaw, edge-ai, risc-v]
---
```

### Phase 2: Quality Gate
```bash
# Each draft goes through:
@PicoClaw code-reviewer --file draft.md --type docs   # Technical accuracy
# Plus:
# - Broken link check
# - Code block syntax validation
# - Image alt-text presence
# - Reading time estimate
```

### Phase 3: SEO Enhancement
AI pass adds:
- Meta description (auto-generated from first paragraph)
- Keyword optimization for "edge AI", "RISC-V", "Raspberry Pi"
- Structured data (JSON-LD)
- Internal linking to related PicoClaw docs

### Phase 4: Multi-Platform Publish
```
draft.md → GitHub Pages (picoclaw.io/blog)
         → Dev.to (via API)
         → Hashnode (via API)
         → Telegram channel @picoclaw_news
```

### Phase 5: Analytics Tracking
- UTM parameters per platform
- Click tracking via Supabase
- Weekly digest: views, engagement, top posts

## Configuration
```yaml
platforms:
  - github-pages
  - dev-to
  - hashnode
channels:
  - telegram/@picoclaw_news
analytics: supabase
```
