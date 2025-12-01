1. ERD — Entity Relationship Diagram
+---------------------+        +---------------------+
|       users         |        |   social_accounts   |
+---------------------+        +---------------------+
| id (uuid PK)        |<------>| id (uuid PK)        |
| email               |        | user_id (FK)        |
| password_hash       |        | platform            |
| created_at          |        | access_token        |
+---------------------+        | refresh_token       |
                               | page_id             |
                               | page_name           |
                               | expires_at          |
                               +---------------------+

+---------------------+        +----------------------+
|      listings       |        |        posts         |
+---------------------+        +----------------------+
| id (uuid PK)        |<------>| id (uuid PK)         |
| user_id (FK)        |        | listing_id (FK)      |
| title               |        | platform             |
| address             |        | external_post_id     |
| rent                |        | url                  |
| description         |        | status               |
| images (json[])     |        | posted_at            |
| created_at          |        +----------------------+
+---------------------+

+----------------------+
|    automation_logs   |
+----------------------+
| id (uuid PK)         |
| listing_id (FK)      |
| platform             |
| operation            |
| status               |
| message              |
| created_at           |
+----------------------+
# 🟧 2. SYSTEM FLOWCHARTS
2.1 Login + OAuth Flow
[User] → Login
     ↓
[Supabase Auth]
     ↓ success
[Dashboard]
     ↓ click "Connect FB/IG"
[Meta OAuth Popup]
     ↓
[Meta returns tokens]
     ↓
[Supabase stores encrypted tokens]
     ↓
[User ready to post]
2.2 Post Everywhere Flow
User
 ↓
Clicks "Post Everywhere"
 ↓
[Python API]
 ↓ sends payload
[n8n Workflow Trigger]
 ↓
 ├── Facebook API (post)
 ├── Instagram API (post)
 ├── Playwright Worker (TikTok)
 ├── Playwright Worker (Kijiji)
 ├── Playwright Worker (Craigslist)
 ↓
[n8n returns results]
 ↓
[Python updates DB]
 ↓
Vue fetches status
 ↓
Dashboard updates in real time
2.3 Renewal Engine Flow
[n8n Daily Cron]
      ↓
Check all posts with `next_renew_at`
      ↓
For each expiring post:
    ├── Renew on Kijiji
    ├── Renew on Craigslist
    ├── Refresh IG (optional)
    ├── Repost FB
      ↓
Log results → update Supabase
      ↓
Dashboard shows upcoming renewals
# 🟦 3. 90-DAY GANTT CHART TIMELINE
PHASE A — Sellable MVP (Days 1–30)
Week 1:
  - Supabase setup
  - Backend skeleton (Python)
  - Vue project setup
  - Auth system

Week 2:
  - Listing form
  - AI caption generator
  - Image upload
  - Meta OAuth integration

Week 3:
  - Facebook posting (API)
  - Instagram posting (API)
  - Store post status

Week 4:
  - Dashboard v1
  - Logging system
  - Final testing
  - First internal demo
PHASE B — Scalable Automation (Days 31–60)
Week 5:
  - Playwright automation setup
  - Kijiji posting automation

Week 6:
  - Craigslist posting automation
  - TikTok posting automation

Week 7:
  - Renewal engine (n8n)
  - Reposting logic
  - Failure/retry system

Week 8:
  - Multi-platform UI
  - Status pages
  - Analytics v1
PHASE C — Full Portal (Days 61–90)
Week 9:
  - Workspace system
  - Roles & permissions
  - Team members

Week 10:
  - Pricing pages
  - Billing integration (Stripe)
  - Invoices

Week 11:
  - Full dashboard
  - Filters, search, sorting

Week 12:
  - QA
  - Load testing
  - Beta launch with 10 PMs
# 🟩 4. PITCH DECK (TEXT VERSION)
Slide 1 — Title
OmniList
The Automation Engine for Property Managers
Slide 2 — Problem
Property managers waste:
6–12 minutes posting per platform
30–60 minutes renewing listings
10–20 hours/week handling repetitive listing tasks
Platforms:
FB, IG, Kijiji, Craigslist, TikTok → all separate, all manual.
Slide 3 — Solution
Create listing once → post everywhere → renew automatically.
OmniList automates:
Posting
Reposting
Renewing
Updating
Tracking
Across all major platforms.
Slide 4 — Why Now
34% of PMs already adopting AI
Social platform posting is more fragmented than ever
Small PMs overlooked by enterprise PMS
Automation is exploding in boring industries
No existing tool automates cross-platform rental posting
Slide 5 — Product
FB/IG API integration
Kijiji/Craigslist/TikTok automation
Renewal engine
Dashboard
AI captions
Logs + analytics
Slide 6 — Business Model
$49 Starter
$129 Pro
$299 Agency
Add-ons:
TikTok automation
Email syndication
Templates
Slide 7 — Competition
Buildium → No automation
AppFolio → Too heavy
RentManager → No AI
PhantomBuster → Not PM-focused
OmniList is the only PM-focused automation engine.
Slide 8 — Roadmap
Phase A → Sell
Phase B → Scale
Phase C → Full Portal
Slide 9 — Ask
Looking for 10 early PMs to join private beta.
# 🟧 5. UI WIREFRAMES (ASCII Layouts)
Dashboard
+--------------------------------------------------+
| OmniList Dashboard                                |
+--------------------------------------------------+
| Stats:  Total Listings | Active Posts | Renewals  |
+--------------------------------------------------+
| RECENT ACTIVITY                                     |
| [FB] 123 Main St posted ✔  1m ago                   |
| [IG] 456 Oak Ave failed ✖  Retry                    |
| [Kijiji] 22 Pine St scheduled for renewal           |
+--------------------------------------------------+
| BUTTONS:  [Add Listing]  [Post Everywhere]          |
+--------------------------------------------------+
Create Listing
+--------------------------------------------------+
| Create Listing                                   |
+--------------------------------------------------+
| Title: [.........................]                |
| Address: [......................]                |
| Rent: [....]                                     |
| Description: [textarea + "AI Write"]             |
| Images: [Upload]                                 |
+--------------------------------------------------+
| BUTTON:  [Post Everywhere]                       |
+--------------------------------------------------+
Platform Status
+------------------------------+
| Listing: 123 Main Street     |
+------------------------------+
| Platform      Status         |
|---------------------------------------------|
| Facebook      Posted ✔ link                 |
| Instagram     Posted ✔ link                 |
| Kijiji        Pending...                    |
| Craigslist    Renew in 2 days               |
| TikTok        Failed (Login required)       |
+------------------------------+
# 🟩 6. DB MIGRATIONS (SUPABASE SQL)
users
create table users (
  id uuid primary key default gen_random_uuid(),
  email text unique not null,
  created_at timestamptz default now()
);
social_accounts
create table social_accounts (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references users(id),
  platform text not null,
  access_token text,
  refresh_token text,
  page_id text,
  page_name text,
  expires_at timestamptz,
  created_at timestamptz default now()
);
listings
create table listings (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references users(id),
  title text,
  address text,
  rent numeric,
  description text,
  images jsonb,
  status text default 'draft',
  created_at timestamptz default now()
);
posts
create table posts (
  id uuid primary key default gen_random_uuid(),
  listing_id uuid references listings(id),
  platform text,
  external_post_id text,
  url text,
  status text,
  posted_at timestamptz default now()
);
automation_logs
create table automation_logs (
  id uuid primary key default gen_random_uuid(),
  listing_id uuid references listings(id),
  platform text,
  operation text,
  status text,
  message text,
  created_at timestamptz default now()
);
# 🟪 7. GIT REPOSITORY STRUCTURE
omnilist/
│
├── frontend/ (Vue)
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── stores/ (Pinia)
│   │   ├── layouts/
│   │   ├── utils/
│   │   └── api/
│   └── package.json
│
├── backend/ (Python)
│   ├── app/
│   │   ├── main.py
│   │   ├── routes/
│   │   ├── services/
│   │   ├── models/
│   │   ├── workers/
│   │   └── utils/
│   └── requirements.txt
│
├── playwright-workers/
│   ├── kijiji_bot.py
│   ├── craigslist_bot.py
│   ├── tiktok_bot.py
│   └── utils/
│
├── n8n/
│   ├── workflows/
│   ├── triggers/
│   └── credentials/
│
├── supabase/
│   ├── migrations/
│   ├── policies/
│   └── seed.sql
│
└── docs/
    ├── architecture.md
    ├── api-spec.md
    ├── flowcharts.md
    ├── roadmap.md
    └── pitch-deck.md
