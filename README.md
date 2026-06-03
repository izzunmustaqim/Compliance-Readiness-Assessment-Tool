# ⚡ EECA Compliance & Readiness Assessment Tool

A production-ready AI-powered chatbot that assesses organisational compliance readiness with Malaysia's **Energy Efficiency and Conservation Act (EECA) 2024**. Built with **Next.js 16**, **Vercel AI SDK v6**, **OpenAI GPT-5.4-mini**, and **Supabase**.

Developed by **Sandhurst Advisory** in collaboration with **Enerlytic Intelligence**.

🔗 **Live Demo:** [ai-customer-support-chatbot-dun.vercel.app](https://ai-customer-support-chatbot-dun.vercel.app/)
🔗 **Live:** [ai-customer-support-chatbot-dun.vercel.app](https://eeca.sandhurstadvisory.com.my/)

![Next.js](https://img.shields.io/badge/Next.js-16-black?logo=next.js)
![AI SDK](https://img.shields.io/badge/AI%20SDK-v6-blue)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--5.4--mini-green)
![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?logo=typescript)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react)
![License](https://img.shields.io/badge/License-MIT-green)

---

## ✨ Features

- 📋 **Structured EECA Readiness Assessment** — 1 awareness section + 10 scored questions + user info collection
- 🤖 **AI-Powered Conversational Chat** — Streaming responses via OpenAI GPT-5.4-mini
- 🔘 **Interactive UI** — Clickable option buttons & checkboxes for quiz responses
- 📊 **Scoring & Reporting** — 0–100 score with readiness bands and gap analysis
- 📄 **DOCX Report Generation** — One-click Microsoft Word compliance report with AI-generated analysis
- 📖 **EECA Regulations Built-In** — Full PUA466 2024 regulations embedded for legal reference
- 🔒 **Password-Protected Admin Dashboard** — View assessment results, manage report statuses, and generate reports
- 📇 **Automatic Lead Capture** — Contact info auto-extracted from chat and saved with assessment results
- ⭐ **Feedback System** — Star ratings with optional comments
- 🔄 **Rate Limiting & Retry Logic** — Per-IP rate limiting with exponential backoff for upstream 429s
- 🎨 **Sandhurst Corporate Theme** — Clean light theme with navy & green branding
- 📱 **Fully Responsive** — Works on desktop, tablet, and mobile
- 🐳 **Docker-Ready** — Multi-stage Dockerfile + Compose for VPS deployment

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| **Framework** | Next.js 16 (App Router) |
| **Language** | TypeScript 5 |
| **Runtime** | React 19 |
| **AI Provider** | OpenAI GPT-5.4-mini (swappable to Groq / Gemini) |
| **AI SDK** | Vercel AI SDK v6 (`ai`, `@ai-sdk/openai`, `@ai-sdk/react`) |
| **Database** | Supabase (PostgreSQL) |
| **Report Generation** | docxtemplater + PizZip (DOCX) |
| **Data Table** | TanStack Table v8 |
| **Styling** | CSS Modules + Vanilla CSS — Sandhurst corporate light theme |
| **Fonts** | Inter + Poppins (Google Fonts) |
| **Hosting** | VPS (Docker) or Vercel |
| **CI/CD** | GitHub Actions (lint → test → build → deploy) |

---

## 📁 Project Structure

```
src/
├── app/
│   ├── api/
│   │   ├── chat/route.ts               # AI chat endpoint (streaming + score extraction)
│   │   ├── feedback/route.ts           # Feedback submission
│   │   ├── analytics/route.ts          # Dashboard data aggregation
│   │   ├── dashboard-auth/route.ts     # Dashboard login/logout (cookie-based)
│   │   ├── assessment-status/route.ts  # Admin: update report status (PATCH)
│   │   └── send-report/route.ts        # Admin: generate & download DOCX report
│   ├── assessment/
│   │   ├── page.tsx                    # Full-page assessment chat
│   │   └── assessment.css              # Assessment styles
│   ├── dashboard/
│   │   ├── page.tsx                    # Admin analytics dashboard
│   │   ├── dashboard.css               # Dashboard styles
│   │   └── login/
│   │       ├── page.tsx                # Dashboard login page
│   │       └── login.css               # Login styles
│   ├── globals.css                     # Design system (Sandhurst theme)
│   ├── layout.tsx                      # Root layout
│   ├── page.tsx                        # Landing page
│   ├── error.tsx                       # Error boundary
│   ├── not-found.tsx                   # 404 page
│   └── global-error.tsx                # Global error boundary
├── components/
│   ├── chat/
│   │   ├── chat-widget.tsx             # Main chat container with session management
│   │   ├── message-list.tsx            # Message renderer with auto-scroll
│   │   ├── message-bubble.tsx          # Message bubble + option button parser
│   │   ├── chat-input.tsx              # Input area with disabled state
│   │   └── feedback-modal.tsx          # Star rating modal
│   └── dashboard/
│       ├── assessment-table.tsx        # TanStack-powered data table (filter, sort, paginate)
│       ├── generate-btn.tsx            # DOCX report generate & download button
│       ├── status-select.tsx           # Report status dropdown (admin)
│       ├── logout-btn.tsx              # Dashboard logout button
│       └── stat-card.tsx               # Metric card component
├── lib/
│   ├── ai/
│   │   └── system-prompt.ts            # EECA questionnaire + full PUA466 regulations
│   ├── docx/
│   │   └── build-report-docx.ts        # Programmatic DOCX builder (docxtemplater)
│   ├── pdf/
│   │   ├── build-report.ts             # PDF report builder (jsPDF)
│   │   └── generate-report.ts          # Report orchestration
│   ├── supabase/
│   │   ├── client.ts                   # Browser client (anon key)
│   │   └── server.ts                   # Server admin client (service role)
│   ├── dashboard-auth.ts              # Cookie-based auth utilities
│   └── rate-limit.ts                  # In-memory per-IP rate limiter
└── __tests__/                          # Jest + React Testing Library tests
    ├── api/
    ├── components/
    └── lib/
```

---

## 📋 Assessment Flow

### Greeting & Introduction
The AI greets the user, explains this is an EECA Readiness Assessment Tool by Sandhurst Advisory & Enerlytic Intelligence, then asks for name and designation.

### Section A — EECA Awareness (Not Scored)

**Q. What is your current level of awareness or exposure to the EECA requirements?**

Multiple choice (checkboxes — select all that apply):
- I have attended an ST briefing or session on EECA
- I have attended a SEDA briefing or session on EECA
- An ESCO / consultant has briefed us on EECA
- I have personally read or studied the EECA Act or guidelines
- My company has discussed EECA internally
- I have heard of EECA, but I do not know the details
- This is my first time exploring EECA requirement

### Section B — EECA Readiness (Q1–Q10, Scored)

| # | Question | Type | Max Score |
|---|----------|------|-----------|
| Q1 | Which best describes your facility? | Single choice | 10 |
| Q2 | Is your facility within the scope of EECA? (≥21,600 GJ threshold) | Single choice | 10 |
| Q3 | Do you have 12 months of organized energy data? | Single choice | 10 |
| Q4 | Has your company appointed a Registered Energy Manager (REM)? | Single choice | 10 |
| Q5 | Do you have an Energy Management System (EnMS) in place? | Single choice | 10 |
| Q6 | Which EnMS elements are already in place? | Multiple choice (checkboxes) | 10 |
| Q7 | Which energy management activities are being carried out? | Multiple choice (checkboxes) | 10 |
| Q8 | Is your facility prepared for the first EE&C Report submission? | Single choice | 10 |
| Q9 | What is your energy audit readiness status? | Single choice | 10 |
| Q10 | Are you prepared for the Energy Intensity Label (EIL) by end 2026? | Single choice | 10 |

### Readiness Bands
| Score | Band |
|-------|------|
| 80–100 | 🟢 High Readiness |
| 60–79 | 🟡 Moderate Readiness |
| 40–59 | 🟠 Low Readiness |
| 0–39 | 🔴 Critical Readiness Gap |

### After Scoring
1. AI presents the full score breakdown table and readiness band
2. AI asks: "Would you like to receive the detailed EECA Compliance Report by email?"
3. **If Yes** → Proceed to Section C (user info collection)
4. **If No** → Thank you and close the session

### Section C — User Info (Not Scored)
If user wants the detailed report, AI collects:
- Name (compulsory)
- Designation
- Email (compulsory)
- Mobile Number (compulsory)

Contact info is **automatically extracted** from the chat and saved to the `assessment_results` table. The chat input is then disabled to signal session completion.

---

## 🔒 Admin Dashboard

The dashboard at `/dashboard` is protected by a password gate (set via `DASHBOARD_PASSWORD` env var).

**Features:**
- **Stat Cards** — Total assessments, average score, readiness band distribution
- **Assessment Table** — Filterable, sortable, paginated (TanStack Table) showing all completed assessments
- **Report Status Management** — Update each assessment's report status (`pending` → `generating` → `sent` → `failed`)
- **DOCX Report Generation** — One-click "Generate Report" button that builds a branded Microsoft Word report with:
  - AI-generated compliance analysis (via GPT-4o-mini)
  - Per-question score breakdown
  - Readiness band classification
  - Sandhurst Advisory branding

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** 20+ installed
- **API Key** — one of the following:
  - OpenAI API key (paid) — [platform.openai.com](https://platform.openai.com/api-keys)
  - Groq API key (free) — [console.groq.com](https://console.groq.com/keys)
  - Google Gemini key (free) — [aistudio.google.com](https://aistudio.google.com/apikey)

### 1. Clone & Install

```bash
git clone https://github.com/izzunmustaqim/Compliance-Readiness-Assessment-Tool.git
cd Compliance-Readiness-Assessment-Tool
npm install
```

### 2. Set Up Environment Variables

Create a `.env.local` file in the root directory:

```env
# AI Provider (use one)
OPENAI_API_KEY=your_openai_api_key
# GROQ_API_KEY=your_groq_api_key
# GOOGLE_GENERATIVE_AI_API_KEY=your_gemini_api_key

# Supabase (for database features)
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key

# Dashboard admin password
DASHBOARD_PASSWORD=your_secure_password
```

> **Note:** The chatbot works with just an AI API key. Supabase enables conversation logging, assessment persistence, report generation, and the admin dashboard.

### 3. Run the App

```bash
npm run dev
```

Open **http://localhost:3000** in your browser.

---

## 🎨 Theme & Branding

The app uses **Sandhurst Advisory's corporate branding**:

| Element | Value |
|---------|-------|
| Primary (headers, nav) | Navy `#001d39` |
| Accent (buttons, options) | Green `#4CAF50` |
| Background | White `#ffffff` |
| Text | Dark `#1a1a2e` |
| Font (headings) | Poppins |
| Font (body) | Inter |

The design system is defined in `src/app/globals.css` using CSS custom properties. To customise colours, edit the `:root` variables.

---

## 🔄 Switching AI Providers

Change 2 lines in `src/app/api/chat/route.ts`:

```diff
# Switch to Groq (free)
- import { openai } from '@ai-sdk/openai';
+ import { groq } from '@ai-sdk/groq';
- model: openai('gpt-5.4-mini'),
+ model: groq('llama-3.3-70b-versatile'),

# Switch to Gemini (free)
- import { openai } from '@ai-sdk/openai';
+ import { google } from '@ai-sdk/google';
- model: openai('gpt-5.4-mini'),
+ model: google('gemini-2.0-flash'),
```

### Supported Providers

| Provider | Package | Env Variable | Cost |
|----------|---------|-------------|------|
| OpenAI | `@ai-sdk/openai` | `OPENAI_API_KEY` | Paid |
| Groq | `@ai-sdk/groq` | `GROQ_API_KEY` | Free (30 req/min) |
| Google Gemini | `@ai-sdk/google` | `GOOGLE_GENERATIVE_AI_API_KEY` | Free (15 req/min) |

---

## 📦 Deployment

### Option 1: Self-Host with Docker (Recommended)

```bash
# 1. Create .env.local with your API keys
cp .env.example .env.local
nano .env.local

# 2. Build & run
docker compose up -d --build
```

The Docker Compose stack includes:
- **Next.js app** (standalone output, multi-stage build)
- **PostgreSQL 16** (Alpine, with health checks and persistent volume)

> See [DEPLOY.md](DEPLOY.md) for the full VPS deployment guide with security hardening and Cloudflare Tunnel setup.

### Option 2: Vercel

1. Push code to GitHub
2. Go to [vercel.com](https://vercel.com) → Import repo
3. Add environment variables in Settings
4. Deploy — auto-deploys on every `git push`

> **Note:** Vercel Free is for personal use only. For commercial use, upgrade to Pro ($20/mo) or use a VPS.

### Option 3: Self-Host + Cloudflare Tunnel

```bash
# 1. Run app
docker compose up -d --build

# 2. Create Cloudflare Tunnel
cloudflared tunnel create eeca-chatbot
cloudflared tunnel route dns eeca-chatbot eeca.yourdomain.com

# 3. Start tunnel
cloudflared tunnel run eeca-chatbot
```

---

## 🧪 Testing

```bash
npm test                # Run all tests
npm run test:coverage   # Run with coverage report
```

---

## 🗄️ Database Schema

| Table | Purpose |
|-------|---------|
| `conversations` | Chat sessions with metadata (session ID, status, timestamps) |
| `messages` | All user and assistant messages (linked to conversation) |
| `assessment_results` | Scored outcomes: total score, per-question scores (Q1–Q10), readiness band, user contact info, report status |
| `feedback` | User ratings (1–5 stars) with optional comments |

### Data Flow

```
User starts chat → conversation created → messages saved per turn
                                       → scores auto-extracted from AI report
                                       → contact info auto-extracted (Section C)
                                       → feedback saved (star rating)
                                       → admin generates DOCX report from dashboard
```

### Row-Level Security

- **Anonymous inserts** allowed for `conversations`, `messages`, `feedback`, `assessment_results` (chatbot users aren't authenticated)
- **Full access** restricted to `service_role` only (API routes and admin dashboard)

---

## ⚙️ CI/CD Pipeline

The GitHub Actions workflow (`.github/workflows/ci.yml`) runs on every push and PR to `main`:

| Stage | Description |
|-------|-------------|
| 🔍 **Lint & Type Check** | ESLint + `tsc --noEmit` |
| 🧪 **Test** | Jest test suite with coverage upload |
| 🏗️ **Build** | Production build with env secrets |
| 🚀 **Deploy** | Auto-deploy to Vercel (if `VERCEL_TOKEN` is configured) |

---

## 📖 Legal Reference

This app includes the full text of **P.U. (A) 466 — Energy Efficiency and Conservation Regulations 2024** embedded in the system prompt, covering:

- Energy consumption threshold (21,600 GJ)
- REM appointment requirements and qualifications
- Energy Management System (EnMS) requirements
- Energy audit and reporting obligations
- Building energy intensity labels
- Fee schedule (28 items)

---

## 📝 License

MIT License — feel free to use this for personal or commercial projects.

---

## 🙏 Acknowledgments

- [Sandhurst Advisory](https://sandhurstadvisory.com.my/) — Domain expertise & branding
- [Enerlytic Intelligence](https://enerlytic.ai/) — Energy intelligence partner
- [Vercel AI SDK](https://sdk.vercel.ai/) — AI streaming framework
- [OpenAI](https://openai.com/) — GPT model provider
- [Supabase](https://supabase.com/) — Open-source Firebase alternative
- [Next.js](https://nextjs.org/) — React framework
- [TanStack Table](https://tanstack.com/table) — Headless table UI
