# MedFlow — Medical Practice Operations Engine

An n8n automation system designed to streamline the operational workflows of a medical practice.  
Built with a **Human-in-the-Loop** architecture to ensure clinical-grade decisions remain under human control.

> Self-hosted · 120 nodes · 8 modules · 18 integrations

---

## Overview

MedFlow covers the full operational lifecycle of a medical practice — from patient intake to revenue cycle management — through eight interconnected automation modules.

The system uses AI (GPT-4o) strictly for classification, scoring, and code suggestion tasks. **No Protected Health Information (PHI) is ever transmitted to external AI services.**

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                     MEDFLOW — 8 MODULES                      │
├────────────────────┬─────────────────────────────────────────┤
│  CORE PATIENT      │  PRACTICE OPERATIONS                    │
│  JOURNEY           │                                         │
│                    │                                         │
│  01 Patient Intake │  05 Reputation Shield                   │
│  02 Smart Schedule │  06 Supply Chain                        │
│  03 Communication  │  07 Practice Intelligence               │
│  04 Revenue Cycle  │  08 HIPAA Compliance                    │
└────────────────────┴─────────────────────────────────────────┘
```

---

## Modules

### 01 · Patient Intake Autopilot
Handles the full intake pipeline from form submission to EMR record creation.

**Flow:** Webhook → Validate → Insurance Verification → Provider Matching → Consent (DocuSign) → Record Creation  
**Human checkpoint:** Provider assignment review before record is finalized.

---

### 02 · No-Show Killer — Smart Scheduling Engine
Predicts appointment no-show risk and triggers appropriate follow-up actions.

**Inputs:** Appointment history · Weather (OpenWeather API) · Day-of-week · Patient travel distance (Google Maps)  
**Output:** Risk score (LOW / MEDIUM / HIGH) → automated reminders or overbooking suggestion sent to staff for approval.

---

### 03 · Patient Communication Hub
Handles both outbound scheduled communications and inbound message routing.

**Outbound:** Post-visit surveys · Prescription refill reminders · Annual recall notifications  
**Inbound:** GPT-4o classifies incoming messages into `ADMIN`, `CLINICAL`, `URGENT`, or `FEEDBACK` and routes to the appropriate queue.  
**Human checkpoint:** All URGENT and CLINICAL messages require provider action before any response is sent.

---

### 04 · Revenue Cycle Machine
Manages the full claims lifecycle — coding, submission, denial handling, and collections.

**Flow:** Visit notes → AI-suggested CPT/ICD codes → **Human validation required** → Submission to clearinghouse (Availity) → Denial analysis if rejected  
**Human checkpoint:** No claim is ever submitted without explicit provider code approval.

---

### 05 · Reputation Shield
Monitors patient satisfaction and Google Reviews. Triggers intervention workflows for negative feedback before it becomes a public review.

---

### 06 · Supply Chain & Inventory
Tracks medical supply levels, predicts consumption, and generates reorder requests. All purchase orders require staff approval before submission.

---

### 07 · Practice Intelligence
Aggregates weekly KPIs (appointments, revenue, satisfaction scores) and generates structured reports using AI. Monthly referral tracking and analysis.

---

### 08 · HIPAA Compliance & Data Protection
Runs hourly audit log scans with anomaly detection. Tracks staff credential expiry. Flags records for 6-year retention compliance review.

---

## Human-in-the-Loop Checkpoints

The system enforces **14 mandatory human checkpoints** across all modules. No clinical, financial, or public-facing action is taken without explicit human approval.

| Module | Checkpoint |
|--------|-----------|
| Intake | Provider assignment approval |
| Scheduling | Overbooking decision |
| Communication | Urgent / clinical message response |
| Revenue | CPT/ICD code validation before submission |
| Revenue | Denial appeal review |
| Revenue | Collections decision (90d+) |
| Reputation | Negative patient intervention |
| Reputation | Google review response approval |
| Supply | Purchase order approval |
| HIPAA | Anomaly response |
| HIPAA | Data purge approval |

---

## Data Protection Architecture

```
┌──────────────────────────────────────────────┐
│            ENCRYPTED EMR BOUNDARY             │
│                                              │
│  Patient records · Clinical notes            │
│  Prescriptions · Lab results                 │
│                                              │
│  AES-256 at rest · TLS 1.3 in transit        │
│  Self-hosted n8n · Role-based access         │
└────────────────────┬─────────────────────────┘
                     │
                     │  De-identified metadata only
                     │  (appointment types, scores,
                     │   categories — never PHI)
                     ▼
┌──────────────────────────────────────────────┐
│            AI PROCESSING LAYER               │
│                                              │
│  GPT-4o — classification & code suggestion   │
│                                              │
│  No BAA with OpenAI → strict PHI boundary    │
│  AI never receives patient identifiers       │
└──────────────────────────────────────────────┘
```

---

## Integrations

| Service | Role |
|---------|------|
| Airtable | Primary database / lightweight EMR |
| Twilio | SMS communications |
| Gmail (Google Workspace) | Email & billing communications |
| Slack | Internal team notifications |
| DocuSign | Patient consent e-signatures |
| Availity | Insurance verification & claims clearinghouse |
| OpenAI GPT-4o | Classification, scoring, code suggestion |
| OpenWeather API | No-show risk scoring input |
| Google Maps API | Travel distance for scheduling |
| Google Places API | Review monitoring |
| JotForm / Typeform | Patient intake forms |

---

## Setup

### Requirements
- n8n self-hosted instance (v1.0+)
- Credentials configured for the integrations you intend to use

### Import
1. Open your n8n editor
2. **+ New Workflow** → **Import from file**
3. Select `MedFlow-Workflow.json`
4. Set up credentials in **Settings → Credentials**
5. Enable modules incrementally — do not activate all triggers at once

### Required Credentials
```
AIRTABLE_API_KEY
TWILIO_ACCOUNT_SID
TWILIO_AUTH_TOKEN
OPENAI_API_KEY
GMAIL_CLIENT_ID / GMAIL_CLIENT_SECRET
SLACK_BOT_TOKEN
DOCUSIGN_INTEGRATION_KEY
OPENWEATHER_API_KEY
GOOGLE_MAPS_API_KEY
```

> Do not hardcode credentials in workflow nodes. Use n8n encrypted credential store exclusively.

---

## Workflow Stats

| Metric | Count |
|--------|-------|
| Functional nodes | 120 |
| Sticky note annotations | 9 |
| Node connections | 105 |
| Human checkpoints | 14 |
| Communication channels | 4 (Email, SMS, WhatsApp, Slack) |

---

## Files

| File | Description |
|------|-------------|
| `MedFlow-Workflow.json` | n8n workflow export — import directly |
| `assets/overview.png` | Architecture overview |
| `assets/canvas-full.png` | Full canvas screenshot |
| `assets/canvas-zoom-1.png` | Modules 01–04 detail |
| `assets/canvas-zoom-2.png` | Modules 05–08 detail |

---

## Author

**Amine Sellal** — Full Stack Developer & AI Automation Engineer  
[github.com/aminesellal](https://github.com/aminesellal) · [Portfolio](https://aminesellal.github.io/myportfolio)

---

*Built as a portfolio project. Review applicable healthcare data regulations before any production deployment.*
