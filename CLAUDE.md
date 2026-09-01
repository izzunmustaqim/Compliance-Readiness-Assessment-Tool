# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

EECA Compliance & Readiness Assessment Tool — an AI-powered chatbot that assesses organisational readiness for Malaysia's Energy Efficiency and Conservation Act (EECA) 2024. Built with Next.js 16 (App Router), Vercel AI SDK v6, and Supabase.

## Commands

```bash
npm run dev          # Start dev server (localhost:3000)
npm run build        # Production build
npm run lint         # ESLint (next/core-web-vitals + typescript)
npx tsc --noEmit     # Type check only
npm test             # Run Jest tests
npm run test:coverage # Jest with coverage report
```

Run a single test file:
```bash
npx jest src/__tests__/components/message-bubble.test.tsx
```

## Architecture

### AI Chat Flow
- **`src/app/api/chat/route.ts`** — Main chat endpoint. Uses Vercel AI SDK `streamText()` with OpenAI GPT-5.4-mini. Handles rate limiting (20 req/min per IP), Supabase conversation/message persistence, and auto-extraction of assessment scores and contact info from AI responses.
- **`src/lib/ai/system-prompt.ts`** — Contains the full EECA questionnaire (Section A awareness + 10 scored questions), scoring logic, and the complete P.U.(A) 466 regulations text. The AI follows a strict task flow: greeting → name collection → Section A → Section B (Q1-Q10) → results → report offer → optional Section C (user info).
- **`src/components/chat/chat-widget.tsx`** — Client component using `useChat()` from `@ai-sdk/react`. Manages session state, detects `[ASSESSMENT_COMPLETE]` tag in AI responses to end sessions, and triggers feedback modals every 5 messages.

### Score & Contact Extraction
Scores and contact info are extracted server-side in the `onFinish` callback of `streamText()`:
- Scores: regex matches `(\d{1,3})\s*[/\/]\s*100` in the AI response, then parses Q1-Q10 per-question scores from the breakdown table.
- Contact info: triggered when the AI response contains a "thank you for the information" pattern. Extracts name, designation, email, and phone from the last user message.

### Option Button System
The AI outputs `[OPTION]text[/OPTION]` for single-choice and `[CHECKBOX]text[/CHECKBOX]` for multiple-choice. The `message-bubble.tsx` component parses these tags and renders clickable buttons that send the selected text back as a user message.

### DOCX Report Generation
- **`src/lib/docx/build-report-docx.ts`** — Builds reports using docxtemplater + PizZip. Reads the template file `EECA Compliance Readiness Preliminary Report.docx` from project root (cached in memory). Handles Word XML quirks: merges split `<w:r>` runs so bracket placeholders match, converts newlines to `<w:br/>` elements.
- **`src/app/api/send-report/route.ts`** — API endpoint that fetches assessment data + messages from Supabase, calls OpenAI for analysis, then calls `buildReportDOCX()`.

### Database (Supabase)
Four tables: `conversations`, `messages`, `assessment_results`, `feedback`. Schema in `supabase/schema.sql`. Row-level security allows anonymous inserts (chatbot users aren't authenticated) but restricts full access to `service_role`.

The app works without Supabase — all DB calls are wrapped in try/catch and gracefully skip if env vars are missing.

### Admin Dashboard
- `/dashboard` — password-protected via cookie-based auth (`DASHBOARD_PASSWORD` env var)
- `/dashboard/login` — login page
- Uses TanStack Table v8 for the assessment results table with filtering, sorting, and pagination

### AI Provider Switching
Change the import and model in `src/app/api/chat/route.ts`. Supported: OpenAI (`@ai-sdk/openai`), Groq (`@ai-sdk/groq`), Google Gemini (`@ai-sdk/google`). All three packages are installed.

## Key Conventions

- **Styling**: CSS Modules for components, global CSS custom properties in `src/app/globals.css` (Sandhurst corporate theme: navy `#001d39`, green `#4CAF50`).
- **Path alias**: `@/` maps to `src/` (configured in tsconfig and jest).
- **Next.js 16**: This version may have breaking changes from training data. Check `node_modules/next/dist/docs/` if unsure about APIs (per AGENTS.md).
- **Standalone output**: `next.config.ts` sets `output: "standalone"` for Docker deployment. The Dockerfile is a multi-stage build.
- **Message format**: The chat uses Vercel AI SDK's `UIMessage` format with `parts` array (not flat `content` string). The API route converts parts to plain messages for the model.

## Environment Variables

Required for full functionality:
```
OPENAI_API_KEY              # Or GROQ_API_KEY / GOOGLE_GENERATIVE_AI_API_KEY
NEXT_PUBLIC_SUPABASE_URL    # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY   # Server-side only
DASHBOARD_PASSWORD          # Admin dashboard access
```

## CI/CD

GitHub Actions (`.github/workflows/ci.yml`): lint → type check → test → build → deploy to Vercel. Runs on push/PR to `main`. Deploy step requires `VERCEL_TOKEN` secret.
