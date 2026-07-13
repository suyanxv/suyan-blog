+++
title = "Let That Bake"
description = "AI-powered baking planner for hobbyists, gift bakers, side hustlers, and home bakeries. Production scheduling, calendar-aware timing, and AI step-by-step guidance."
date = 2026-04-26
updated = 2026-07-13

[extra]
lang = "en"
toc = true
copy = false
comment = false
+++

**Status:** Live · **App:** [let-that-bake.suyan.dev](https://let-that-bake.suyan.dev) · **Repo:** [github.com/suyanxv/let-that-bake](https://github.com/suyanxv/let-that-bake)

---

## Overview

Let That Bake is an AI-powered baking planner for anyone juggling more than one bake at a time. Whether it's a side-hustle order, your sister's birthday cake, or a tray of cookies for a school bake sale, drop in what you're making and when it's due. The agent works backwards through the bake, chill, and decorate timeline, fits each step around your real calendar, and gives you a day-by-day plan.

There's also a public order form so customers (or friends) can submit requests directly, AI tips per step with passive reminders for chilling and freezing, and YouTube tutorials embedded inline for visual techniques like crumb coating or piping.

**Who it's for:**

- 🎨 **Hobby bakers** — anyone who bakes for the love of it and wants the timing handled
- 🎁 **Gift bakers** — birthday cakes, holiday cookies, hostess gifts
- 💼 **Side hustlers** — taking custom orders on the side of a day job
- 🏪 **Home bakeries** — running a real cottage food business

The first user is me. I bake because I enjoy it and because people in my life like it when I do. The pain point — losing track of what to start when — is the same whether you're charging for it or not.

It's also the first product spinout from my [Let That Bake YouTube channel](https://letthatbake.ai), which is where the brand and audience live.

---

## How It Works

1. **Capture an order** — customer + item + quantity + due date. Menu items can be backed by a saved recipe so the plan comes pre-tuned.
2. **Generate a production plan** — backwards-scheduling engine takes the due date, the recipe's timeline (or a default per category), and the baker's blocked days, then assigns each step to the nearest available date.
3. **Work the plan** — animated cupcake mascot rides the progress bar; tap a task to see Claude-generated "How to nail this step" tips, passive reminders ("freeze layers 30min before icing"), and embedded YouTube tutorials for visual techniques.
4. **Reschedule on the fly** — drag tasks between days on the calendar (works on mobile via touch sensors); the order's task graph stays intact.
5. **Save what you learned** — tweak the AI-drafted steps on a real order, hit "save as default," and those edits become the recipe's timeline. The next order of that item bakes from your tuned steps, not the generic template.
6. **Run the order lifecycle** — accept, decline, or request more info; request a deposit and mark it paid; Claude drafts a warm confirmation to copy into WhatsApp or email, or the app emails the customer directly.
7. **Take public orders** — share `/order/your-slug` so customers submit orders without an account, browse your menu and gallery, and attach inspo photos; submissions land as `pending` for you to review.
8. **Let customers self-serve status** — every order gets a private tracker link (`/track/...`) where the customer sees progress, payment status, and the pickup address once it's ready — no account, no back-and-forth texts.

---

## Features

### Production Intelligence

- Default timelines per category (layer cake, cupcakes, cookies, cheesecake, macarons, brownies)
- AI recipe parsing — paste a URL or text and Claude extracts a structured timeline with daysBeforeDue + duration per step
- **Recipe learning loop** — link a recipe to a menu item and its orders inherit that timeline; edit the steps on any real order and save them back as the item's new default, so the tool gets smarter every time you bake it
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

### Order Lifecycle & Payments

- Accept, decline, or request more info on any incoming order, with a status that flows through to the customer's tracker
- Request a deposit, mark it paid, then mark the balance paid in full — or flag an order as a gift with no payment
- Accept emails are deposit-aware ("baking starts once we receive your deposit")
- Private pickup address stays hidden until the order is marked ready
- Customer-facing tracker (`/track/<token>`) shows live progress, payment status, and pickup details without an account

### Menu & Gallery

- Public gallery grouped into sections (Cakes, Cookies & Bars, Pastries) with an "About" blurb
- Menu items with prices, an "Order this" prefill button, and in-stock vs made-to-order handling with quantity
- Multi-photo items — carousel with a card-stack cover so one item can show several angles
- Direct photo uploads to the gallery, plus private inspo/reference photos that never go public

### Ways to See Your Work

- **Kanban board** — orders as cards across pending → in progress → ready → done
- **Finance page** — receipts, budget vs earnings, and billing in one place
- **iCal export** — subscribe to your bakes in Google, Apple, or Outlook Calendar
- **Themes** — light and dark plus forest, ocean, grape, and zen palettes on a semantic token system
- **Guided setup wizard** — re-runnable and prefilled, so onboarding and later edits use the same flow

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
| AI | Anthropic Claude — recipe parsing, task tips, video query generation, customer confirmations |
| Calendar | node-ical for Google / Apple / Outlook iCal subscriptions |
| Video | YouTube Data API v3 |
| Email | Resend — order confirmations and status updates |
| Photos | Vercel Blob — gallery and inspo uploads |
| Hosting | Vercel |
| Fonts | Geist (UI) + Caveat (cursive logo) |

---

## Architecture Notes

**Backwards scheduling** is a pure function: `(dueDate, category, customTimeline?, blockedDates) → tasks[]`. Each step has a `daysBeforeDue` value; the scheduler subtracts that from the due date, then walks backwards day-by-day if the target lands on a blocked day. Conflicts are surfaced to the user with friendly suggestions.

**Calendar feed integration** uses iCal subscription URLs rather than OAuth, which means one parser handles all three providers (Google, Apple, Outlook) with no per-provider auth dance. The downside is read-only — we can't write back to the user's calendar — but for the "respect my busy days" use case that's the right tradeoff. webcal:// URLs are auto-normalized to https://. Events are cached in the database and synced on demand.

**Touch-first drag-and-drop** was the trickiest UX problem. Native HTML5 drag events don't fire on touch, so I migrated to @dnd-kit with a `TouchSensor` that has a 200ms activation delay — long enough to disambiguate from scroll, short enough to feel responsive. Quick tap still opens the order detail; long-press initiates a drag.

**AI cost guardrails** — every Claude call is cached per task in client state, so a baker who returns to the same task during a baking session pays the LLM cost once. Recipe parsing is one-shot at import time and stored in the database. Video search uses a quota-cheap analysis call (which decides whether video is needed) before spending a more expensive YouTube search call.

**The recipe learning loop** is the piece I'm happiest with. The scheduler already took an optional `customTimeline`, so wiring recipes to orders was mostly plumbing — but the write-back is what makes it feel alive. When a baker saves an edited order's steps, the app diffs each task's scheduled date against the due date to recover a relative `daysBeforeDue`, rebuilds the dependency chain, drops the per-order handoff task, and writes the result back to the item's recipe (creating and linking one if the item had none). The AI drafts the first version; one real bake tunes it; the item remembers. No model retraining, just a tight edit-and-persist loop over structured data.

**Multi-baker isolation** — every table is scoped by `baker_id`, which is keyed by Clerk user ID. The public order and tracker endpoints are the only unauthenticated routes; order lookup is by `public_slug`, and the tracker gates on an opaque per-order token with rate-limited verification.

**Build notes** — node-ical uses `BigInt` at module evaluation time which broke Vercel's Turbopack build collection. Fixed by lazy-importing it inside the request handler.

---

## Where It Sits in the Market

The existing baking software space splits cleanly — and skips the biggest segment entirely:

- **BakeMargin / Bake Boost** — recipe costing and pricing focus, paid bakers only
- **CakeBoss** — desktop-only, dated, paid bakers only
- **BakeSmart** — overbuilt for retail, $99–240/month
- **Recipe apps (Paprika, Plan to Eat)** — what to cook tonight, not how to time multi-day projects
- **Spreadsheets** — what most people actually use, regardless of why they're baking

None of them answer the question both pros and hobbyists are asking when they look at their queue on Wednesday morning: *"What do I need to start today?"* That's the lane Let That Bake fills.

The hobby segment matters more than the existing tools admit. A hobby baker juggling Mom's birthday cake on Saturday, cookies for the school bake sale on Sunday, and a friend's housewarming on Monday has the exact same multi-project scheduling problem as a side hustler with three customer orders that week. The category-defining insight is that *baking is project planning* — and that's true for anyone with more than one bake on the calendar.

Pro features (recipe costing, customer confirmations, public order forms with custom branding) sit behind a $9.99/mo paywall because they're meaningful only to people running it as a business. The free tier handles the planning loop in full so casual bakers get real value without a subscription.

---

## Roadmap

- Recipe costing + pricing suggestions
- Inventory and ingredient tracking
- Repeat order reminders
- Two-way Google Calendar sync (currently read-only)
- Multi-baker households (one account, multiple bakers)
- Print-friendly production sheets
- Online deposit/balance collection on the public order form (payment tracking is manual today)

**Shipped since first writing:** email order confirmations and status updates, the recipe learning loop, order lifecycle with deposits, a customer-facing order tracker, public menu + gallery, in-stock items, a kanban board, and a finance page.

---

## Why This Project

I'm a software engineer who bakes. I know the pain of juggling 3 cake orders due in the same week, a dentist appointment Friday, and a recipe I've made before but can't remember if I should freeze the layers overnight or just chill them. The product is for me first.

It's also a useful proving ground for the post-AI artisan economy thesis I've been chewing on for the [Let That Bake YouTube channel](https://letthatbake.ai). Software is getting cheaper to build; small businesses with real human relationships are getting more valuable. Tools that compress the operational overhead of an artisan business — without taking the craft out of it — are going to matter.

This is the first one. More coming.
