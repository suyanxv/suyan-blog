+++
title = "Let That Bake"
description = "AI-powered home bakery operations assistant — production planning, calendar-aware scheduling, and customer order intake for solo bakers."
date = 2026-04-26

[extra]
lang = "en"
toc = true
copy = false
comment = false
+++

**Status:** Live · **App:** [let-that-bake.suyan.dev](https://let-that-bake.suyan.dev) · **Repo:** [github.com/suyanxv/let-that-bake](https://github.com/suyanxv/let-that-bake)

---

## Overview

Let That Bake is a production-planning OS for home bakers. Drop in an order with a due date and the agent works backwards through the bake / chill / decorate timeline, fits each step around your real calendar, and gives you a day-by-day plan. There's a public order form so customers can submit requests directly, AI tips per step (with passive reminders for chilling and freezing), and YouTube tutorials embedded inline for visual techniques like crumb coating or piping.

The first user is me. The category is small enough to be ignored by VC-scale software but large enough to support a real indie business — there are millions of cottage food businesses in the US and they're underserved by tools built for commercial kitchens.

It's also the first product spinout from my [Let That Bake YouTube channel](https://letthatbake.ai), which is where the brand and audience live.

---

## How It Works

1. **Capture an order** — customer + item + quantity + due date. Optionally link a saved recipe.
2. **Generate a production plan** — backwards-scheduling engine takes the due date, the recipe's timeline (or a default per category), and the baker's blocked days, then assigns each step to the nearest available date.
3. **Work the plan** — animated cupcake mascot rides the progress bar; tap a task to see Claude-generated "How to nail this step" tips, passive reminders ("freeze layers 30min before icing"), and embedded YouTube tutorials for visual techniques.
4. **Reschedule on the fly** — drag tasks between days on the calendar (works on mobile via touch sensors); the order's task graph stays intact.
5. **Confirm with the customer** — Claude drafts a warm message; copy and send via WhatsApp or email.
6. **Take public orders** — share `/order/your-slug` so customers submit orders without an account; submissions land as `pending` for you to review.

---

## Features

### Production Intelligence

- Default timelines per category (layer cake, cupcakes, cookies, cheesecake, macarons, brownies)
- AI recipe parsing — paste a URL or text and Claude extracts a structured timeline with daysBeforeDue + duration per step
- Conflict-aware scheduler unions manual blocked days with synced calendar events
- Editable per-task scheduled date and duration

### Calendar

| View | What it shows |
|---|---|
| Month | Day grid with task counts and event dots, tiny cupcake on today |
| Week (default) | Full-height columns showing every task and external event per day |
| List | Chronological feed of upcoming tasks and events |

- Drag tasks across days to reschedule (touch + mouse via @dnd-kit)
- Tap a task to open the order detail page
- Subscribe to Google, Apple, or Outlook calendars via iCal URL — events appear in their own color and are respected when scheduling new orders

### AI Layer

- **Recipe parser** — Claude reads a recipe and outputs structured `TimelineStep[]` with category detection
- **Task tips** — per-task summary, numbered steps, pro tip, and passive reminders for chill/freeze/defrost windows
- **Video search** — Claude decides whether a step benefits from video (skips routine prep, surfaces video for visual techniques), then writes the optimal beginner-friendly YouTube query
- **Customer confirmations** — friendly draft message tailored to the order
- **Cost control** — tips and videos cached per task in client state, one Claude call per task per session

### Public Order Intake

- Each baker gets a custom URL (`/order/<slug>`) with their bakery name, tagline, and color
- Mobile-first form: category picker, contact info, item description, due date, notes
- Toggle "accepting orders" on/off to pause submissions
- Submissions trigger the scheduler immediately so the baker sees a draft plan when they review

### Cute Details

- Cursive Caveat logo across all branded surfaces
- Animated cupcake on the progress bar that bounces and slides as steps complete
- Confetti burst on order creation, sparkles on task completion
- Time-of-day greeting with weather emoji (☀️ 🌅 🌙)
- Random celebration toasts ("Crushing it 🔥", "Sweet, that's done.")
- 404 page: "Oh crumbs."
- PWA manifest — installable to home screen on iOS and Android

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16 (App Router) + React 19 + Tailwind CSS v4 |
| UI motion | Framer Motion |
| Drag & drop | @dnd-kit (PointerSensor + TouchSensor) |
| Auth | Clerk |
| Database | Neon Postgres + Drizzle ORM |
| AI | Anthropic Claude (Opus 4.5) — recipe parsing, task tips, video query generation, customer confirmations |
| Calendar | node-ical for Google / Apple / Outlook iCal subscriptions |
| Video | YouTube Data API v3 |
| Hosting | Vercel |
| Fonts | Geist (UI) + Caveat (cursive logo) |

---

## Architecture Notes

**Backwards scheduling** is a pure function: `(dueDate, category, customTimeline?, blockedDates) → tasks[]`. Each step has a `daysBeforeDue` value; the scheduler subtracts that from the due date, then walks backwards day-by-day if the target lands on a blocked day. Conflicts are surfaced to the user with friendly suggestions.

**Calendar feed integration** uses iCal subscription URLs rather than OAuth, which means one parser handles all three providers (Google, Apple, Outlook) with no per-provider auth dance. The downside is read-only — we can't write back to the user's calendar — but for the "respect my busy days" use case that's the right tradeoff. webcal:// URLs are auto-normalized to https://. Events are cached in the database and synced on demand.

**Touch-first drag-and-drop** was the trickiest UX problem. Native HTML5 drag events don't fire on touch, so I migrated to @dnd-kit with a `TouchSensor` that has a 200ms activation delay — long enough to disambiguate from scroll, short enough to feel responsive. Quick tap still opens the order detail; long-press initiates a drag.

**AI cost guardrails** — every Claude call is cached per task in client state, so a baker who returns to the same task during a baking session pays the LLM cost once. Recipe parsing is one-shot at import time and stored in the database. Video search uses a quota-cheap analysis call (which decides whether video is needed) before spending a more expensive YouTube search call.

**Multi-baker isolation** — every table is scoped by `baker_id`, which is keyed by Clerk user ID. The public order endpoint is the only unauthenticated route and it looks up the baker by `public_slug`.

**Build notes** — node-ical uses `BigInt` at module evaluation time which broke Vercel's Turbopack build collection. Fixed by lazy-importing it inside the request handler.

---

## Where It Sits in the Market

The home bakery software space is established but stale. The existing tools split cleanly:

- **BakeMargin / Bake Boost** — recipe costing and pricing focus
- **CakeBoss** — desktop-only, dated, cheap long-term
- **BakeSmart** — overbuilt for retail, $99–240/month
- **Spreadsheets** — what most home bakers actually use

None of them answer the question that bakers are actually asking when they look at their order list on Wednesday morning: *"What do I need to do today to ship everything on time?"* That's the lane Let That Bake fills, and it's the part that genuinely benefits from agentic AI rather than just a CRUD app with an LLM bolted on.

The roadmap moves toward a true all-in-one (recipe costing, ingredient tracking, customer CRM, repeat order reminders) so a serious home baker can run their entire business in one tool.

---

## Roadmap

- Recipe costing + pricing suggestions
- Inventory and ingredient tracking
- Repeat order reminders
- Two-way Google Calendar sync (currently read-only)
- Multi-baker households (one account, multiple bakers)
- Print-friendly production sheets
- Email order confirmations directly from the app
- Stripe payments on the public order form

---

## Why This Project

I'm a software engineer who bakes. I know the pain of juggling 3 cake orders due in the same week, a dentist appointment Friday, and a recipe I've made before but can't remember if I should freeze the layers overnight or just chill them. The product is for me first.

It's also a useful proving ground for the post-AI artisan economy thesis I've been chewing on for the [Let That Bake YouTube channel](https://letthatbake.ai). Software is getting cheaper to build; small businesses with real human relationships are getting more valuable. Tools that compress the operational overhead of an artisan business — without taking the craft out of it — are going to matter.

This is the first one. More coming.
