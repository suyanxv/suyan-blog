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

Quorum is an AI-native event management platform built for volunteer-led community organizations — alumni networks, professional clubs, nonprofits, and hobby groups. It handles the full event lifecycle: planning, AI-powered content generation across five channels, RSVP tracking, partner management, and team coordination from a single interface.

The initial use case is the CMU Seattle Alumni Network, but the architecture supports any number of organizations from day one via multi-tenant isolation.

---

## Features

### Multi-Channel AI Content Generation

Fill in one event form. Quorum generates ready-to-send content for all five channels simultaneously:

| Channel | Output |
|---|---|
| WhatsApp | Casual announcement, reminder, post-event follow-up |
| Email | Formatted announcement, RSVP reminder, recap |
| Instagram | Caption with hashtags |
| LinkedIn | Professional post |
| Luma | Event description for direct paste |

All content is editable and regeneratable with one click. Tone can be adjusted per channel.

**Implementation:** Claude Sonnet 4.6 with prompt caching — the event context is cached as a system prompt prefix shared across all five channel requests, reducing token cost by ~80% compared to five independent calls.

### Event Management

- Comprehensive event form with 20+ fields: name, date, time, location, speakers, sponsors, dress code, tone, custom fields
- Status lifecycle: draft → published → past/cancelled → archived
- Calendar views: year, month, and list
- Color-coded by category (internal / partnered / external)
- Bulk import from Google Sheets, CSV, or free text — Claude Sonnet parses messy data into structured events with best-effort field population

### RSVP & Attendance Tracking

- Manual RSVP entry and bulk paste from Google Sheets
- Public QR code check-in form with customizable dynamic fields
- Tracks RSVPed vs. walk-in attendance; flags no-shows
- Cross-event attendee lookup
- CSV export

### Broadcasting

- One-tap email broadcasts to confirmed RSVPs, all RSVPs, partners, or custom lists via Resend
- WhatsApp broadcasts via click-to-send deep links (sends from organizer's account, keeping it authentic)
- Broadcast history tracked per event

### Smart Reminders

- Assignable to specific board members with due dates
- AI can auto-generate a reminder schedule from event context
- Daily cron job scans due reminders and delivers via email
- Org-level reminder templates auto-applied to bulk-imported events

### Partner CRM

- Track sponsors, vendors, and co-hosts per event
- Inline edit, communication history, event linking
- AI-drafted partner outreach emails

### Public Shareable Calendar

- Token-based read-only calendar URL for members
- Shows only published/past events — hides drafts, RSVPs, internal notes, and partner contacts
- Token is rotatable and disableable

### Team & Permissions

- Multi-user access with Admin and Editor roles
- Clerk-based org sync with manual sync fallback
- Audit activity log (who edited what, when)

### PWA

Installable on iOS and Android with a fullscreen app experience and dynamically generated icon.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16 (App Router) + React 19 + Tailwind CSS v4 |
| Auth | Clerk (B2B multi-tenant) |
| Database | Neon Postgres (serverless, raw SQL — no ORM) |
| AI | Claude Sonnet 4.6 (content generation, bulk import) + Claude Haiku 4.5 (reminders, partner emails) |
| Email | Resend |
| Cron | Vercel Cron |
| Hosting | Vercel |

---

## Architecture Notes

**Multi-tenancy:** Every table is scoped by `org_id`. All API routes go through `requireOrgMember()` / `requireAdmin()` guards — no cross-org reads are possible.

**No ORM:** Raw SQL via Neon's serverless HTTP driver. 13 idempotent migrations with `schema.sql` as the canonical DDL.

**Prompt caching:** Event context assembled as an XML block and cached as a system prompt prefix. Each of the five channels is a separate user message — cache hits on channels 2–5 drive most of the cost savings.

**Custom fields:** Stored as JSONB on both events and check-in responses, allowing per-org form schemas without migrations. Claude Haiku parses existing Google Form / Monday / Luma URLs into a field schema automatically.

**Content guardrails:** System prompt enforces no em-dashes, no AI-giveaway verbs (leverage, delve, embark), and channel-specific length budgets (WhatsApp ≤1,024 chars, LinkedIn ≤3,000).

---

## Roadmap

- Direct API sends (Instagram Graph, LinkedIn, WhatsApp Business API)
- Image / file uploads for events
- Recurring event series
- Event templates (save-as-template for reuse)
- Analytics dashboard (attendance trends, channel engagement)
- Dark mode
