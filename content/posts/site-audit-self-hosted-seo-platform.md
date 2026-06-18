---
title: "Site Audit: A Self-Hosted, Developer-Friendly SEO Platform I Built (and Open-Sourced)"
description: "Crawl your sites, triage issues, connect Search Console, and export client reports — on infrastructure you control."
date: 2026-06-18T00:00:00+00:00
draft: false
tags:
  - opensource
  - seo
  - webdev
  - selfhosted
  - nextjs
  - python
ShowToc: true
---

If you've worked in SEO or web engineering, you've probably felt the same tension I have: enterprise audit tools are powerful, but they're also expensive, opaque, and built around someone else's cloud. Your crawl data lives in their database. Exports are gated behind tiers. Integrations are black boxes. And if you're a developer who wants to script against audit data, wire it into CI, or ask an AI agent real questions about your crawl — you're mostly on your own.

That's why I built **[Site Audit](https://github.com/codefrydev/WebsiteProfiling)** — an open-source, self-hosted SEO audit platform for developers, agencies, and in-house teams who want full technical SEO workflows without vendor lock-in.

## The problem I wanted to solve

Most SEO tooling falls into two camps:

1. **Heavy SaaS dashboards** — great UX, but subscription fees, per-seat pricing, and data you don't own.
2. **CLI crawlers** — powerful and transparent, but thin on reporting, integrations, and team workflows.

I wanted something in between: **familiar audit workflows** (crawl maps, issue boards, Lighthouse scores, Search Console overlays) running on **your stack**, with **your PostgreSQL database**, and **no fabricated metrics** when Google isn't connected.

Site Audit is that middle ground.

## What Site Audit does

At its core, Site Audit is a **developer-friendly SEO audit platform** that:

- **Crawls** your sites (static HTML or JavaScript rendering via headless Chromium)
- **Audits** technical SEO issues, on-page elements, accessibility (axe), and performance (Lighthouse)
- **Integrates** with Google Search Console, GA4, and Bing Webmaster — using *your* OAuth credentials
- **Reports** with prioritized issues, health scores, compare runs, and HTML/PDF exports
- **Extends** via AI chat and **340 MCP tools** for programmatic access from Cursor, Claude Desktop, or any MCP client

Everything runs locally or in Docker. Your data stays yours.

## Key features

### Site crawl — map every URL

Think Screaming Frog, but in a modern web UI with PostgreSQL storage behind it.

- Spider or sitemap-based discovery
- Status codes, redirect chains, canonical analysis
- Crawl maps and path trees for large sites
- Static or JavaScript rendering (`auto` mode uses heuristics to decide when to spin up Chromium)
- Export sitemaps and compare audit runs over time

### Technical audit — prioritized fixes

- Issue boards grouped by severity (critical, high, medium, low)
- On-page checks: titles, meta descriptions, headings, content signals
- Lighthouse performance scores and Core Web Vitals
- Accessibility findings via axe
- Category health scores (0–100) — internal audit scores, not Google rankings

### Google integrations — real data, no fabrication

Connect Search Console and GA4 via OAuth in the UI. When Google isn't connected, you see empty states with provenance labels — **not made-up numbers**.

- Search Performance: clicks, impressions, top queries from GSC
- GA4 sessions and users per property
- Keywords Explorer blends on-site term frequency with Search Console data

### Content Studio — write with live SEO scoring

An experimental but useful workflow for content teams:

- Draft and optimize copy with **live SEO grading** as you type
- Recommended terms and coverage gaps highlighted in the editor
- Powered by Search Console data and on-page heuristics from your audit
- Drafts persist per property (title, meta, rich text or Markdown)

### AI Chat — ask your audit in plain English

Enable an LLM provider (Ollama locally, or OpenAI, Anthropic, Gemini, Groq) and chat with your crawl data:

> "What are my top critical issues?"  
> "Show me pages with missing meta descriptions."  
> "Export a PDF report."

The assistant calls **real tools** against your database — charts, tables, and download buttons appear in the thread. It doesn't invent URLs or scores.

The same **340 read-only MCP tools** power programmatic access from your IDE. Domain-scoped bundles (`crawl`, `google`, `links`, `core`) let you load only what you need.

### Compare runs and client exports

Run a second crawl after fixes? **Compare audits** shows what improved and what regressed — category deltas, issue diffs, and Google metric changes when GSC is connected.

Export **HTML or PDF** reports. No upgrade tiers. No gated exports.

### Agency portfolio management

Manage multiple client properties from one dashboard. Track crawl history, compare runs, and ship reports without per-seat SaaS fees.

## Who it's for

| Audience | Workflow |
|----------|----------|
| **Agencies & consultants** | Portfolio of client sites, compare runs, HTML/PDF exports |
| **In-house SEO teams** | GSC + GA4 per property, issue tracking over time, data on your infra |
| **Developers & platform teams** | Docker deploy, PostgreSQL storage, MCP tools in Cursor/Claude |

## Tech stack

Site Audit is built with tools developers already know:

| Layer | Technology |
|-------|------------|
| **Frontend** | Next.js (App Router), React |
| **Backend / engine** | Python — crawl, analysis, reporting, Lighthouse, integrations |
| **Database** | PostgreSQL (Alembic migrations) |
| **Deploy** | Docker Compose (dev + production configs) |
| **AI** | MCP server, multi-provider LLM support |
| **License** | MIT — fork, modify, deploy freely |

Architecture at a glance:

```
WebsiteProfiling/
├── src/website_profiling/   # Python audit engine (CLI + MCP)
│   ├── crawl/               # Crawler, fetchers, JS rendering
│   ├── reporting/           # Report builder, issue categories
│   ├── integrations/        # GSC, GA4, Bing, CrUX
│   ├── llm/                 # AI enrich + chat agent
│   └── mcp/                 # 340 read-only MCP tools
├── web/                     # Next.js UI + API routes
├── alembic/                 # PostgreSQL migrations
└── docker-compose.yml       # One-command local stack
```

## Honest limitations (on purpose)

I believe in **transparent scope**. Site Audit is not trying to be every paid SEO SaaS product rolled into one. Here's what it **doesn't** do:

- **No live backlink index** — backlinks come from GSC Links CSV imports (and optional third-party CSV overlays), not Ahrefs/Semrush/Moz APIs
- **No proprietary rank tracker** — keyword positions come from GSC snapshots, not a daily SERP database
- **No live AI citation checks** — GEO/AEO tools use on-site heuristics, not real-time queries to ChatGPT or Perplexity
- **No third-party keyword volume APIs** — difficulty and SERP overlays are estimated unless you supply your own data
- **No managed cloud** — you run it; this isn't a hosted multi-tenant SaaS

That honesty is a feature. You know exactly what you're getting — and what to pair it with from your existing stack.

## Get started in minutes

**Docker (fastest path):**

```bash
git clone https://github.com/codefrydev/WebsiteProfiling.git
cd WebsiteProfiling
docker compose up --build
```

Open [http://localhost:3000/home](http://localhost:3000/home) and run your first audit.

**Local development:**

```bash
./local-run setup   # Postgres, Python venv, migrations, npm deps
./local-run         # Start DB + Next.js → http://localhost:3000/home
```

Connect Google Search Console and Analytics from the **Integrations** panel (gear icon). Optional: enable JavaScript crawl rendering, AI chat, and Content Studio from audit settings.

## Why open source?

SEO audits shouldn't require trusting a black box with your site data. By open-sourcing Site Audit under the **MIT license**, I wanted to give teams:

- **Data ownership** — PostgreSQL, your server, your backups
- **Transparency** — see how issues are detected and scored
- **Extensibility** — MCP tools, CLI pipeline, Docker deploy
- **No subscription tax** — especially valuable for agencies managing many properties

If you've been looking for a **self-hosted alternative** to enterprise crawl tools — with modern UI, real Google integrations, and AI-native workflows — I'd love for you to try it.

## Links

- **GitHub:** [github.com/codefrydev/WebsiteProfiling](https://github.com/codefrydev/WebsiteProfiling)
- **License:** MIT
- **Docs:** MCP setup, ops guide, and glossary in the repo's `docs/` folder

⭐ Star the repo if you find it useful — and issues/PRs are welcome.
