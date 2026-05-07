# 🍎 Malam AI — WhatsApp Sales Agent
### Dr. Apple Phone Store · Farm Centre Market, Kano, Nigeria

<p align="center">
  <img src="https://img.shields.io/badge/n8n-2.16.0-orange?style=for-the-badge&logo=n8n" />
  <img src="https://img.shields.io/badge/Claude-Sonnet_4-purple?style=for-the-badge&logo=anthropic" />
  <img src="https://img.shields.io/badge/Supabase-Postgres-green?style=for-the-badge&logo=supabase" />
  <img src="https://img.shields.io/badge/WhatsApp-Meta_Cloud_API-brightgreen?style=for-the-badge&logo=whatsapp" />
  <img src="https://img.shields.io/badge/Hosted_on-Render-blue?style=for-the-badge&logo=render" />
</p>

<p align="center">
  A production-ready, AI-powered WhatsApp sales agent for a phone retail business in Kano, Nigeria.<br>
  Speaks <strong>Hausa</strong>, <strong>English</strong>, and <strong>Nigerian Pidgin</strong>.<br>
  Built entirely on <strong>n8n</strong> + <strong>Claude Sonnet 4</strong> + <strong>Supabase</strong> + <strong>Meta Cloud API</strong>.
</p>

---

## 📱 What Is This?

**Malam AI** is a fully automated WhatsApp sales assistant that handles customer inquiries, presents products, logs requests, escalates to human staff when needed, and gives the business CEO full command and control — all through WhatsApp messages.

No mobile app. No website. Just WhatsApp.

---

## ✨ Features

### Customer-Facing (Malam AI)
- 🌍 **Multilingual** — auto-detects and replies in Hausa, English, or Pidgin
- 📱 **Product search** — presents catalog with price, specs, colors, storage
- 🛒 **Buy intent flow** — guides customer to order confirmation
- 📦 **Out of stock handling** — notifies CEO, suggests alternatives
- 🧾 **Invoice retrieval** — customer can request past receipts
- 🚨 **Smart escalation** — routes negotiations, payments, complaints to human staff
- 🧠 **Conversation memory** — remembers context across sessions (last 20 messages)
- ⏸️ **Agent pause/resume** — CEO can stop/start AI responses instantly

### CEO Command Center
| Command | What Happens |
|---|---|
| `blast new product` | DMs every customer about latest product |
| `/invoice_gen Name \| Product \| Qty \| Price` | Generates PDF invoice → sends to customer |
| `resend invoice [name]` | Fetches and resends last invoice |
| `check requests` | Returns open/escalated requests report |
| `pause agent` | Stops all AI responses immediately |
| `resume agent` | Restores AI responses |
| `orders today` | Returns today's buy intents |
| `Available [date]` | Tells waiting customer when stock arrives |
| `Suggest [product]` | Offers customer an alternative product |
| `Take over` | CEO takes over conversation directly |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────┐
│                  WHATSAPP INTERFACE                  │
│         Customer Messages ←→ CEO Commands           │
└──────────────┬──────────────────────┬───────────────┘
               │                      │
      ┌────────▼──────┐      ┌────────▼────────┐
      │  WF1           │      │  WF2             │
      │  Customer      │      │  CEO Command     │
      │  Sales Agent   │      │  Center          │
      └────────┬──────┘      └────────┬────────┘
               │                      │
      ┌────────▼──────────────────────▼────────┐
      │            n8n AUTOMATION ENGINE        │
      │  ┌──────────────────────────────────┐  │
      │  │     Malam AI (Claude Sonnet 4)   │  │
      │  │   + Memory + Products + Tools    │  │
      │  └──────────────────────────────────┘  │
      └────────────────┬────────────────────────┘
                       │
      ┌────────────────▼────────────────────────┐
      │              SUPABASE                    │
      │  customers · products · orders           │
      │  invoices · requests · conversation_memory│
      │  agent_config                            │
      └──────────────────────────────────────────┘
```

---

## 🔧 Tech Stack

| Layer | Technology |
|---|---|
| Automation Engine | [n8n](https://n8n.io) 2.16.0 Cloud |
| AI Model | [Claude Sonnet 4](https://anthropic.com) via Anthropic API |
| Database | [Supabase](https://supabase.com) (Postgres) |
| WhatsApp API | [Meta WhatsApp Cloud API](https://developers.facebook.com) |
| PDF Generation | [API2PDF](https://api2pdf.com) |
| Hosting | [Render](https://render.com) |

---

## 🗄️ Database Schema

7 tables in Supabase:

```
customers          — WhatsApp contacts (phone, name)
products           — Catalog (name, brand, category, price, color, memory, specs)
orders             — Purchase records
invoices           — Invoice records with items JSON
requests           — Every customer inquiry and interaction log
conversation_memory — Chat history per customer (last 50 messages)
agent_config       — System control (agent_active flag)
```

---

## 📂 Workflow Structure

```
n8n/
├── WF1 - Customer Sales Agent
│   ├── WhatsApp Webhook (GET + POST)
│   ├── Meta Verify Gate
│   ├── Message Extractor
│   ├── Agent Active Check (Supabase)
│   ├── CEO Phone Gate
│   ├── Load Conversation Memory (Supabase)
│   ├── Load Product Catalog (Supabase)
│   ├── Format Context (Code)
│   ├── Upsert Customer (Supabase)
│   ├── Build AI Context (Set)
│   ├── Malam AI Agent (Claude Sonnet 4)
│   │   ├── Tool: log_request
│   │   └── Tool: get_invoice
│   ├── Escalation Detector (IF)
│   ├── Tag Parser (Code)
│   ├── CEO Stock Alert (HTTP)
│   ├── Sales Team Escalation (HTTP)
│   ├── Strip Tags (Set)
│   ├── Sanitize Reply (Code)
│   ├── Send WA Reply (HTTP → Meta API)
│   └── Save Memory x2 (Supabase)
│
├── WF2 - CEO Command Center
│   ├── CEO Command Webhook
│   ├── Normalize Message
│   ├── IF Chain Router (8 routes)
│   ├── Blast → Get Customers → Loop → Send DM
│   ├── Invoice → Parse → Lookup → Insert → Build HTML → PDF → Send
│   ├── Resend Invoice → Fetch → Format → Send
│   ├── Check Requests → Fetch → Format → Send Report
│   ├── Pause Agent → Update agent_config
│   ├── Resume Agent → Update agent_config
│   ├── Orders Today → Fetch → Format → Send Report
│   └── Stock Reply → Available / Suggest / Take Over
│
└── WF3 - Stock Alert Reply Handler
    └── (Integrated into WF2 Stock Reply branch)
```

---

## 🚀 Setup Guide

### Prerequisites

- n8n Cloud or self-hosted (v2.16+)
- Supabase account (free tier works)
- Meta Developer account with WhatsApp Business API
- Anthropic API key
- API2PDF account (for PDF invoices)
- Render account (or any public HTTPS host for n8n)

---

### Step 1 — Clone This Repo

```bash
git clone https://github.com/yourusername/malam-ai-whatsapp-agent.git
cd malam-ai-whatsapp-agent
```

---

### Step 2 — Set Up Supabase

1. Create a new Supabase project at [supabase.com](https://supabase.com)
2. Go to **SQL Editor** and run the complete schema:

```bash
# Copy contents of schema.sql and run in Supabase SQL Editor
```

3. Copy your **Project URL** and **service_role key** from Settings → API

---

### Step 3 — Configure Meta WhatsApp API

```
1. developers.facebook.com → Create App → Business type
2. Add product: WhatsApp
3. API Setup → copy Phone Number ID
4. Business Settings → System Users → Generate permanent token
   Permissions: whatsapp_business_messaging, whatsapp_business_management
5. Set Webhook URL: https://your-n8n.onrender.com/webhook/whatsapp
6. Subscribe to: messages (only)
```

---

### Step 4 — Import Workflows into n8n

```
n8n → Workflows → New → Import from File

Import in this order:
1. WF2_DrApple_CEOCommands.json     (activate first)
2. WF1_DrApple_CustomerAgent.json   (activate second)
```

---

### Step 5 — Set Up Credentials in n8n

Go to **Settings → Credentials** and create:

**Supabase API:**
```
Host:                 https://YOUR-PROJECT.supabase.co
Service Role Secret:  YOUR_SERVICE_ROLE_KEY
```

**HTTP Header Auth (Meta Token):**
```
Name:   Authorization
Value:  Bearer YOUR_PERMANENT_META_TOKEN
```

**Anthropic API:**
```
API Key:  YOUR_ANTHROPIC_KEY
```

---

### Step 6 — Replace Placeholders

Search and replace these in both workflow JSONs before importing:

| Placeholder | Replace With |
|---|---|
| `YOUR_PHONE_NUMBER_ID` | Meta Phone Number ID (15 digits) |
| `CEO_PHONE_NUMBER` | CEO WhatsApp number (e.g. `2348012345678`) |
| `SALES_REP_PHONE_NUMBER` | Sales rep WhatsApp number |
| `YOUR_SUPABASE_URL` | `https://abcxyz.supabase.co` |
| `YOUR_SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key |
| `YOUR_ANTHROPIC_API_KEY` | Anthropic API key |
| `YOUR_API2PDF_KEY` | API2PDF API key |
| `2348XXXXXXXXX` | CEO's real WhatsApp number |

---

### Step 7 — Seed Your Products

Add your products to the `products` table in Supabase:

```sql
INSERT INTO products (name, brand, category, price, color, memory, description, in_stock)
VALUES
  ('iPhone 15 Pro Max', 'Apple', 'phone', 850000, 'Black Titanium, White Titanium, Blue', '256GB,512GB,1TB', '6.7 inch, A17 Pro chip, 48MP camera', true),
  ('Samsung Galaxy S24', 'Samsung', 'phone', 620000, 'Onyx Black, Marble Gray', '128GB,256GB', '6.2 inch, AI features, 50MP camera', true),
  ('MacBook Air M3', 'Apple', 'laptop', 1200000, 'Midnight, Starlight, Silver', '8GB RAM 256GB', '13.6 inch, M3 chip, 18hr battery', true);
```

---

### Step 8 — Activate and Test

```
1. Activate WF2 first (green toggle)
2. Activate WF1
3. Add your phone as a Meta test recipient
4. Send a WhatsApp message to the Meta test number
5. Watch n8n Executions — should complete in < 10 seconds
6. You should receive Malam AI's reply on your WhatsApp
```

---

## 💬 Sample Conversations

**Hausa:**
```
Customer: Sannu, ina wayar iPhone?
Malam AI: Sannu da zuwa Dr. Apple Phone Store! 🍎
          Yaya zan taimake ku yau, Alhaji?
          Muna da iPhone 15, 15 Pro, da 15 Pro Max.
          Wane model kuke so?
```

**Pidgin:**
```
Customer: Abeg how much be iPhone 15?
Malam AI: Welcome boss! 🍎
          📱 iPhone 15 (128GB) — ₦680,000
          📱 iPhone 15 (256GB) — ₦750,000
          Which one you want? And wetin color you like?
```

**English:**
```
Customer: Do you have Samsung S24?
Malam AI: Hello! 🍎 Yes we do!
          📱 Samsung Galaxy S24
          💰 Price: ₦620,000
          🎨 Colors: Onyx Black, Marble Gray
          💾 Storage: 128GB / 256GB
          ✅ In Stock — Ready for pickup!
          Which storage would you prefer?
```

---

## 🔐 Escalation Logic

Malam AI automatically escalates these to the sales team:

| Trigger | Escalation Route |
|---|---|
| Price negotiation or discount request | Sales Rep DM |
| Payment confirmation | Finance DM |
| Returns or refunds | Manager DM |
| Bulk order (10+ units) | CEO DM |
| Abuse or inappropriate language | Flag + pause |
| Customer confirms buy intent | Sales Rep DM |

---

## 📄 Invoice Generation

CEO types:
```
/invoice_gen Aminu Musa | iPhone 15 Pro 256GB | 1 | 850000 | Case | 1 | 5000
```

System automatically:
1. Parses customer name, items, quantities, prices
2. Looks up customer in database
3. Creates invoice record in Supabase
4. Generates professional branded PDF (via API2PDF)
5. Sends PDF as WhatsApp document to customer
6. Confirms to CEO with total and PDF link

---

## ⚙️ Agent Control

CEO controls the agent entirely through WhatsApp:

```
"pause agent"   → Malam AI stops responding instantly
"resume agent"  → Malam AI resumes responding
"check requests" → CEO receives full report of open inquiries
"orders today"  → CEO sees all buy intents from today
```

---

## 🌍 Language Support

| Language | Detection Keywords | Greeting |
|---|---|---|
| Hausa | sannu, ina, nawa, wayar, yaya, don allah | Sannu da zuwa Dr. Apple! |
| Pidgin | wetin, abeg, oga, how much, e dey | Welcome boss! Wetin I fit do? |
| English | everything else | Welcome! How can I help? |

---

## 📁 File Structure

```
malam-ai-whatsapp-agent/
├── README.md
├── schema.sql                          ← Complete Supabase schema
├── workflows/
│   ├── WF1_DrApple_CustomerAgent.json  ← Import into n8n
│   └── WF2_DrApple_CEOCommands.json    ← Import into n8n
├── code-nodes/
│   ├── format_context.js               ← Memory + products formatter
│   ├── parse_invoice_command.js        ← /invoice_gen parser
│   ├── build_invoice_html.js           ← PDF invoice HTML builder
│   ├── sanitize_reply.js               ← Reply cleaner before WhatsApp send
│   └── tag_parser.js                   ← Escalation tag extractor
└── docs/
    ├── setup-guide.md
    ├── ceo-commands.md
    └── troubleshooting.md
```

---

## 🐛 Troubleshooting

| Problem | Fix |
|---|---|
| Webhook not verified by Meta | Activate workflow first, use production URL not test URL |
| Workflow hangs at webhook | Subscribe only to `messages` field in Meta dashboard |
| `items.map is not a function` | Items arrived as string — use `JSON.parse(items)` |
| `Buffer is not defined` | Use `btoa()` or pass HTML directly to API2PDF |
| CEO commands not routing | Check `msgLower` field is set in Prepare CEO Data node |
| Empty data in WF2 | Use Webhook approach instead of Execute Workflow node |
| PDF shows no items | Check Parse Invoice Command output for `itemsJson` field |
| Agent not responding | Check `agent_config` table — `agent_active` must be `true` |

---

## 📊 Cost Estimate

| Service | Cost |
|---|---|
| n8n Cloud Starter | ~$20/month |
| Anthropic Claude | ~$0.003 per customer message |
| Supabase Free Tier | $0/month |
| Meta WhatsApp API | $0.0147 per business-initiated conversation |
| API2PDF | $0.001 per PDF (100 free/month) |
| Render Starter | $7/month |
| **Total estimate** | **~$30/month + usage** |

---

## 🤝 Contributing

Pull requests are welcome. For major changes please open an issue first.

1. Fork the repo
2. Create your feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 📜 License

MIT License — free to use, modify, and distribute.

---

## 🙏 Acknowledgements

- Built with [n8n](https://n8n.io) — the fair-code automation platform
- AI powered by [Anthropic Claude](https://anthropic.com)
- Database by [Supabase](https://supabase.com)
- WhatsApp API by [Meta for Developers](https://developers.facebook.com)
- PDF generation by [API2PDF](https://api2pdf.com)

---

<p align="center">
  Made with ❤️ for Dr. Apple Phone Store · Farm Centre Market, Kano, Nigeria 🇳🇬
</p>
