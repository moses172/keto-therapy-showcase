# Keto Therapy

**A bilingual (English ⇄ العربية) metabolic cancer-support platform, built solo in Lebanon.**

A patient enters their cancer type. A deterministic engine builds a personalized metabolic-support protocol (ketogenic diet targets, exercise, supplements, and prescriber-gated off-label options) mapped to the metabolic pathways that cancer depends on. The protocol updates as new labs and scans come in, drives a daily care dashboard, and produces a clinical medical report. A separate clinician portal puts every protocol through a dietitian → oncologist → physician sign-off before it reaches a patient.

> Educational decision-support, not a diagnosis or prescription. Every treatment change goes through the patient's oncology team.

🔒 **The source code is private.** This repo is a public overview. I'm happy to walk reviewers through the code or grant read access on request.

🌐 Live site: https://myketotherapy.netlify.app/

---

## The core design rule

**AI explains, transcribes and chats. It never doses or decides.**

Every clinical decision comes from a pure, deterministic TypeScript engine with no IO and no LLM inside it, so it can be read, tested and audited. Claude is used around the engine for reading lab reports, plain-language explanations and a nutrition assistant. Each of those uses has a deterministic fallback and hard rails that stop it from changing a protocol or a safety gate.

## What it does

- **Protocol engine:** 28 cancer types plus a generic fallback, with mutation and hormone-receptor modifiers, mapped across 3 metabolic "doors" and 19 pathways plus a five-hallmarks layer. Safety gates run on every output, for example suspending carb restriction in favour of an anabolic plan when cachexia is flagged, and enforcing drug-combination rules.
- **Labs and scans:** upload any lab, imaging or pathology report, including iPhone HEIC photos. The AI detects the document type and extracts values. Any value it can't map is queued for human review and never silently dropped. Duplicate uploads are caught by SHA-256 checksum. A background worker finishes reads even if the tab closes, but a human always confirms before anything is written to the clinical record.
- **Medical report:** alerts, lab trends, imaging and pathology findings, a prioritized action plan and a protocol summary, regenerated as data changes.
- **Daily care:** a deterministic 0–100 Daily Care Score from check-ins, with streaks, trends and Care-Engine alerts.
- **Nutrition:** a 28-day plate-method meal plan, foods to favour and avoid for each cancer type, and an AI oncology-dietitian assistant that knows the patient's full clinical picture.
- **Clinical trials:** live recruiting trials from ClinicalTrials.gov, with flags on trials that test an agent already in the patient's protocol.
- **Clinician portal:** role-based access (dietitian, oncologist, physician, reviewer, admin), a 14-state patient workflow, staged e-signature approvals, threaded review comments, tasks and an audit-log viewer.
- **Fully bilingual:** English and Arabic with real right-to-left layout, including the engine's clinical content. A build-time check confirms translation coverage across all cancer types.
- **Built for Lebanon:** cash-voucher billing through Whish Money (server-priced and admin-verified), because card payments barely work there.

## Engineering

| | |
|---|---|
| Stack | Next.js 14 (App Router), React, strict TypeScript, Tailwind, Supabase (Postgres + RLS, Auth, Storage, Edge Functions), Anthropic Claude |
| Architecture | pnpm + Turborepo monorepo: `core` (pure engine), `ai` (Claude wrapper), `i18n`, patient web app, clinician portal |
| Database | 72 migrations, row-level security on every health-data table, hash-chained append-only audit log |
| Testing | 102 pgTAP database tests · ~200 unit tests · Playwright E2E in English and Arabic on desktop and mobile |
| Ops | Sentry with a PHI scrubber, email delivery tracking, 5 automated ops alarms, rehearsed disaster-recovery drill (RTO ≤ 4h / RPO ≤ 2 min) |
| Privacy | GDPR data export and account erasure, including stored files |
| Status | Engineering sign-off complete: a from-zero rebuild passes every migration, test and build |

## About

Built by **Mousa Khalife**, a Certified Ketogenic Nutrition Specialist with 11 years in finance across Lebanon, Zambia and Oman. I built the whole platform myself.

📧 mousa.khalife@gmail.com · 📸 [@my.keto.therapy](https://www.instagram.com/my.keto.therapy)
