+++
title = "Quorum"
description = "AI-native event management platform for volunteer-led community organizations."
date = 2026-04-22

[extra]
lang = "en"
toc = true
copy = false
comment = false
+++

**Status:** Live · **MVP:** [quorum.suyan.dev](https://quorum.suyan.dev) · **Repo:** [github.com/suyanxv/cmu-community-os](https://github.com/suyanxv/cmu-community-os)

---

## Overview

Quorum is an AI-native event OS for volunteer-led community organizations — alumni networks, professional clubs, nonprofits, and hobby groups. Fill out one event form and get ready-to-send content across WhatsApp, email, Instagram, LinkedIn, and Luma. Plus RSVPs, QR check-in, partner CRM, smart reminders, email broadcasts, and a shareable public calendar.

The initial use case is the CMU Seattle Alumni Network, but the architecture supports any number of organizations from day one via multi-tenant isolation.

---

## Screenshots

{{ figure(src="/img/quorum/year-view.png", alt="Year calendar view", caption="Year view — 12 months at a glance, color-coded by category (internal · partnered · external · cancelled)") }}

{{ figure(src="/img/quorum/events-list.png", alt="Events list", caption="Events list — category stripe, hosts, and channels surfaced on each card") }}

{{ figure(src="/img/quorum/event-detail.png", alt="Event detail page", caption="Event detail — stats, check-in, broadcasts (email + WhatsApp), partners") }}

{{ figure(src="/img/quorum/content-generation.png", alt="AI content generation", caption="AI content generation — 5 channels from one event context, prompt-cached") }}

{{ figure(src="/img/quorum/public-share.png", alt="Public shareable calendar", caption="Public share URL — board members view upcoming events without a login") }}

---

## How It Works

1. **Fill out one event form** — core fields plus any custom fields your org uses. Form template is AI-parsed from your existing Google / Monday / Luma form URL.
2. **Generate channel content** — Claude Sonnet 4.6 drafts WhatsApp, email, Instagram, LinkedIn, and Luma copy from a single cached event context. Five channels cost roughly what one does.
3. **Copy and send** — one tap per channel, or use built-in broadcasts: email via Resend, WhatsApp via click-to-send deep links (ships from your account — no business badge).
4. **Track the lifecycle** — RSVPs (manual, paste-from-sheets, or CSV), public QR check-in with dynamic custom fields, attendance tracking (RSVPed vs. walk-in, no-show flagging), assignable reminders with daily automated email delivery.
5. **Plan ahead** — ideas backlog, year-grid calendar with color-coded categories, co-host picker synced to the Partner CRM.
6. **Share with the board** — rotatable public calendar URL so board members see upcoming programming without a Quorum login.

---

## Features

### Multi-Channel AI Content Generation

| Channel | Output |
|---|---|
| WhatsApp | Casual announcement, reminder, post-event follow-up |
| Email | Formatted announcement, RSVP reminder, recap |
| Instagram | Caption with hashtags |
| LinkedIn | Professional post |
| Luma | Event description for direct paste |

All content is editable and regeneratable. Tone can be adjusted per channel. Content guardrails in the system prompt enforce no em-dashes, no AI-tell verbs (leverage, delve, embark), and channel-specific length budgets (WhatsApp ≤1,024 · Instagram ≤2,200 · LinkedIn ≤3,000 · Luma ≤500).

### Event Management

- 20+ field event form with status lifecycle: draft → published → past/cancelled → archived
- Calendar views: year, month, list — color-coded by category (internal / partnered / external)
- Bulk import from Google Sheets, CSV, or free text via Claude Sonnet

### RSVP & Attendance

- Manual entry, paste-from-sheets, CSV upload
- Public QR code check-in with dynamic custom fields (JSONB)
- Tracks RSVPed vs. walk-in; flags no-shows
- Cross-event attendee lookup and CSV export

### Broadcasting

- Email broadcasts to confirmed RSVPs, all RSVPs, partners, or custom lists via Resend
- WhatsApp click-to-send deep links — pre-fills message and opens WhatsApp, sends from your account
- Broadcast history per event

### Smart Reminders

- Assignable to board members with due dates
- AI-generated reminder schedules from event context
- Daily cron job (Vercel Cron, 14:00 UTC) scans and delivers via email

### Partner CRM

- Track sponsors, vendors, and co-hosts per event
- Inline edit, communication history, event linking
- AI-drafted outreach emails via Claude Haiku

### Public Calendar

- Token-based read-only URL — shows published/past events only
- Hides drafts, RSVPs, internal notes, partner contacts
- Rotatable and fully disableable

### PWA

Installable on iOS and Android with a fullscreen experience and dynamically generated "Q" icon.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16 (App Router) + React 19 + Tailwind CSS v4 |
| Auth | Clerk (B2B multi-tenant organizations mode) |
| Database | Neon Postgres (serverless HTTP driver, raw SQL — no ORM) |
| AI | Claude Sonnet 4.6 (content generation, bulk imports) + Claude Haiku 4.5 (reminders, partner emails, template parsing) with prompt caching |
| Email | Resend (broadcasts + daily reminder emails) |
| Cron | Vercel Cron — `/api/cron/reminders` daily at 14:00 UTC |
| Hosting | Vercel |
| PWA | Next.js file-based manifest + dynamic icons via `next/og` |

---

## Architecture Notes

**Multi-tenancy:** Every table is scoped by `org_id`. All API routes go through `requireOrgMember()` / `requireAdmin()` guards — no cross-org reads are possible.

**No ORM:** Raw SQL via Neon's serverless HTTP driver. 13 idempotent migrations with `schema.sql` as the canonical DDL.

**Prompt caching:** Event context assembled as an XML block and cached as a system prompt prefix. Each of the five channels is a separate user message — cache hits on channels 2–5 drive most of the cost savings.

**Custom fields:** Stored as JSONB on events and check-in responses, allowing per-org form schemas without migrations. Claude Haiku parses existing form URLs into a field schema automatically.

**Graceful degradation:** Missing `RESEND_API_KEY` → broadcasts and reminder emails skip silently. Rate limits on generation (10/hour) and parsing (5/hour) protect API costs.

---

## Roadmap

- Direct API sends (Instagram Graph, LinkedIn, WhatsApp Business API)
- Image / file uploads for events
- Recurring event series
- Event templates (save-as-template for reuse)
- Analytics dashboard (attendance trends, channel engagement)
- Dark mode
