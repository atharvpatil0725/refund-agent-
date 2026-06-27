# ShopEase AI Customer Support Agent

A fully functional AI-powered customer support agent that processes or denies e-commerce refunds using Claude's tool-calling (function calling) capabilities, built with Next.js 15.

## 🎥 Demo Video
[Link to Loom video — record after setup]

## 🔗 Live Demo
[Deploy to Vercel — link here]

---

## ✨ Features

### Agent Backend
- **Raw Function Calling / Tool Orchestration** using Anthropic's Claude claude-sonnet-4-6
- **Agent Loop** that dynamically calls 6 tools in sequence:
  1. `lookup_customer` — Fetch CRM profile by email or ID
  2. `lookup_order` — Retrieve order details, dates, and delivery status
  3. `check_customer_flags` — Evaluate fraud/abuse risk level
  4. `check_refund_eligibility` — Validate against policy rules (return windows, digital products, etc.)
  5. `calculate_refund_amount` — Apply deductions based on condition & reason
  6. `process_refund_decision` — Finalize approve / deny / escalate decision
- **Real-time streaming** via Server-Sent Events (SSE)
- **Policy enforcement** — every decision traces back to a specific policy section

### Mock CRM Database
- **15 customer profiles** spanning all tiers: Bronze, Silver, Gold, Platinum
- Realistic order history, refund history, and flagged accounts
- Edge cases built in: flagged customers, digital products, expired return windows, undelivered orders

### Refund Policy Engine
- Category-specific return windows (15 days electronics, 30 days clothing, 0 days digital, etc.)
- Non-returnable items list
- Fraud prevention: auto-flag accounts with 3+ refunds in 12 months
- Change-of-mind vs defective/wrong-item refund calculations
- Condition-based partial refund deductions (25% for damaged items)

### Frontend UI
- **Customer Chat Interface** with quick-scenario buttons for demo purposes
- **Real-time Agent Reasoning Logs** — expand any assistant message to see every tool call and result
- **Admin Dashboard** with live CRM stats, tier distribution, flagged account alerts, and clickable customer profiles
- Dark, modern design with smooth transitions

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- An [Anthropic API key](https://console.anthropic.com)

### Installation

```bash
git clone <your-repo-url>
cd refund-agent
npm install
```

### Environment Setup

```bash
cp .env.local.example .env.local
```

Edit `.env.local`:
```
ANTHROPIC_API_KEY=sk-ant-...
```

### Run Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

---

## 🏗️ Architecture

```
refund-agent/
├── app/
│   ├── api/
│   │   ├── agent/route.ts       # Agent loop + SSE streaming
│   │   └── customers/route.ts   # CRM stats API
│   ├── page.tsx                 # Main page with tab navigation
│   ├── layout.tsx
│   └── globals.css
├── components/
│   ├── ChatInterface.tsx        # Customer chat + real-time logs
│   └── AdminDashboard.tsx       # CRM stats + customer table
└── lib/
    ├── crm-database.ts          # 15 mock customer profiles
    ├── refund-policy.ts         # Policy document + rules config
    └── agent-tools.ts           # Tool definitions + implementations
```

### Agent Flow

```
User Message
     │
     ▼
System Prompt (Policy + Instructions)
     │
     ▼
Claude claude-sonnet-4-6 (tool_use loop)
     │
     ├── lookup_customer ──────► CRM Database
     ├── lookup_order ─────────► CRM Database
     ├── check_customer_flags ─► Risk Engine
     ├── check_refund_eligibility► Policy Engine
     ├── calculate_refund_amount► Pricing Engine
     └── process_refund_decision► Final Decision
                    │
                    ▼
          SSE Stream → Frontend
         (each step logged live)
```

---

## 🧪 Demo Scenarios

Use the **quick prompt buttons** in the UI to test:

| Scenario | Customer | Order | Expected Result |
|----------|----------|-------|-----------------|
| ✅ Valid Refund | Alice Johnson | ORD-10021 | **APPROVED** — Defective headphones within 15-day electronics window |
| ❌ Digital Product | Emma Davis | ORD-10025 | **DENIED** — Software licenses non-refundable (Section 1.5) |
| ⚠️ Flagged Account | Henry Wilson | ORD-10028 | **ESCALATED** — Flagged for review, no auto-approval |
| ⏱️ Expired Window | Bob Martinez | ORD-10022 | **DENIED** — 57 days since delivery, 15-day electronics window expired |

---

## 📋 Refund Policy Summary

| Category | Return Window | Notes |
|----------|--------------|-------|
| Electronics / Gaming | 15 days | From delivery date |
| Clothing / Footwear | 30 days | Unworn, tags attached |
| Health & Beauty | 14 days | Sealed/unopened only |
| Furniture / Appliances | 14 days | Customer pays return shipping |
| Digital Products | 0 days | Non-refundable once activated |
| Perishables | 0 days | Non-refundable |
| Standard Products | 30 days | Original condition required |

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 15 (App Router) |
| AI | Anthropic claude-sonnet-4-6 via `@anthropic-ai/sdk` |
| Agent Pattern | Raw tool calling / function calling loop |
| Streaming | Server-Sent Events (SSE) |
| Styling | Tailwind CSS v4 |
| Language | TypeScript |

---

## 📦 Deployment (Vercel)

```bash
npm install -g vercel
vercel
```

Add `ANTHROPIC_API_KEY` in Vercel project environment variables.

---

## 🔮 Bonus / Extensions

- [ ] Voice pipeline with ElevenLabs / OpenAI Realtime API
- [ ] Persistent refund request history (database)
- [ ] Email notifications on decision
- [ ] Multi-agent: escalation agent handoff

---

*Built for the Jobform Automator Next.js Developer assignment*
