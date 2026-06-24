<div align="center">

# 🔍 AI Fact Checker Agent

### Verify any claim in seconds — powered by LLaMA 3.3, Groq, n8n & Next.js

[![Next.js](https://img.shields.io/badge/Next.js-14-black?style=for-the-badge&logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3-38B2AC?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com)
[![n8n](https://img.shields.io/badge/n8n-Workflow-EA4B71?style=for-the-badge&logo=n8n)](https://n8n.io)
[![Groq](https://img.shields.io/badge/Groq-LLaMA_3.3_70B-orange?style=for-the-badge)](https://groq.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

[![Live Demo](https://img.shields.io/badge/Live_Demo-Vercel-000000?style=for-the-badge&logo=vercel)](https://ai-fact-checker-agent.vercel.app/)
[![Demo Video](https://img.shields.io/badge/Demo_Video-Google_Drive-4285F4?style=for-the-badge&logo=google-drive)](https://drive.google.com/file/d/1rYs3NrmEGKAXNIzBoGEjYPdH3kYz_7QK/view?usp=sharing)

</div>

---

## 🌐 Live Demo & Video

| | Link |
|---|---|
| 🚀 **Deployed App** | [ai-fact-checker-agent.vercel.app](https://ai-fact-checker-agent.vercel.app/) |
| 🎬 **Demo Video** | [Watch on Google Drive](https://drive.google.com/file/d/1rYs3NrmEGKAXNIzBoGEjYPdH3kYz_7QK/view?usp=sharing) |

> [!WARNING]
> **The deployed app may not work end-to-end online.** The n8n AI pipeline runs locally and is exposed via ngrok, which means the tunnel goes offline when the machine is not running. If the app shows an error or hangs, please **run it locally** following the [Getting Started](#-getting-started) steps below — it works perfectly in a local setup.

---

## 🚀 What is This?

**AI Fact Checker Agent** is a full-stack AI application that takes any user claim — like *"Boat earbuds have connectivity issues"* or *"Protein powder is harmful"* — and returns a structured verdict in seconds:

- ✅ **SUPPORTED** — evidence backs the claim
- ❌ **REFUTED** — evidence contradicts the claim
- 🟡 **MIXED** — conflicting signals found

The system scrapes real-time data from Reddit, Quora, YouTube, and news sources, runs it through a custom scoring engine, and then uses **LLaMA 3.3 70B** (via Groq) to generate a human-readable analysis.

---

## ✨ Key Features

| Feature | Description |
|--------|-------------|
| 🧠 **AI-Powered Analysis** | LLaMA 3.3 70B via Groq API for fast, accurate natural language reasoning |
| 🔎 **Real-Time Web Search** | Serper API (Google Search) fetches live results for every claim |
| 📊 **Custom Scoring Engine** | Deterministic keyword + weight-based confidence scoring (no hallucination risk) |
| 🗂️ **Source Classification** | Auto-sorts results into Reddit, Quora, YouTube, News, Blogs, and Other |
| 📈 **Analytics Dashboard** | Custom SVG donut chart, sentiment analysis, confidence breakdown |
| ⚡ **Smooth Animations** | Framer Motion page transitions, spring animations, animated progress bars |
| 🔒 **BFF Pattern** | Backend-for-Frontend keeps API keys secure; no secrets exposed to the browser |
| 🎨 **Premium UI** | Glassmorphism, animated blobs, tri-gradient design system |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        USER BROWSER                         │
│   Next.js Frontend (React + TypeScript + Tailwind + Framer) │
└────────────────────────┬────────────────────────────────────┘
                         │  POST /api/fact-check
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              Next.js API Route (Backend-for-Frontend)        │
│         Validates input → Forwards to n8n webhook           │
└────────────────────────┬────────────────────────────────────┘
                         │  POST /webhook/fact-check-v2
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                     n8n WORKFLOW (9 Nodes)                   │
│                                                             │
│  [1] Webhook Trigger                                        │
│       ↓                                                     │
│  [2] Entity Extraction (JS) — extract entities + query      │
│       ↓                                                     │
│  [3] Serper Search — Google results via API                 │
│       ↓                                                     │
│  [4] Source Classification (JS) — sort by platform         │
│       ↓                                                     │
│  [5] Confidence Scoring Engine (JS) — keyword + weights     │
│       ↓                                                     │
│  [6] Edit Fields — flatten sources into AI context          │
│       ↓                                                     │
│  [7] Groq API → LLaMA 3.3 70B — analysis + reasoning       │
│       ↓                                                     │
│  [8] Parse AI Response — strip markdown, parse JSON         │
│       ↓                                                     │
│  [9] Respond to Webhook — structured JSON response          │
└─────────────────────────────────────────────────────────────┘
```

---

## 🧩 Tech Stack

### Frontend
- **[Next.js 14](https://nextjs.org)** — App Router, file-based routing, server components
- **[TypeScript](https://www.typescriptlang.org)** — Full type safety across components and API
- **[Tailwind CSS](https://tailwindcss.com)** — Utility-first styling
- **[Framer Motion](https://www.framer.com/motion/)** — Page transitions, spring animations, animated SVGs
- **[Lucide React](https://lucide.dev)** — Icon library
- **[Radix UI](https://www.radix-ui.com)** — Accessible headless UI primitives

### Backend
- **Next.js API Routes** — Serverless backend with BFF pattern
- **[n8n](https://n8n.io)** — Self-hosted workflow automation engine (9-node AI pipeline)

### AI & Data
- **[Groq API](https://groq.com)** — Ultra-fast LLM inference (LPU hardware)
- **[LLaMA 3.3 70B](https://ai.meta.com/blog/llama-3/)** — Open-source LLM for reasoning and analysis
- **[Serper API](https://serper.dev)** — Google Search API for real-time web results

---

## 🔬 How the n8n Scoring Engine Works

The verdict is **not decided by the AI** — it is calculated deterministically by a custom scoring engine:

```
Positive signals → +1 each:  "good", "best", "excellent", "recommended", "great"
Negative signals → -2 each:  "issue", "problem", "avoid", "not worth", "bad"

Source Weights:
  News    → weight 3   (most trusted)
  Blogs   → weight 2
  Other   → weight 2
  Reddit  → weight 1
  Quora   → weight 1
  YouTube → weight 1

Final Score:
  score > 2   → SUPPORTED
  score < -2  → REFUTED
  else        → MIXED

Confidence capped at 75% when multiple sources exist
(prevents false 100% confidence)
```

> **Why?** LLMs can hallucinate. By separating verdict calculation (deterministic) from verdict explanation (AI), the system is both reliable and interpretable.

---

## 📁 Project Structure

```
ai-fact-checker-agent/
├── app/
│   ├── layout.tsx              # Root layout, Inter font, metadata
│   ├── page.tsx                # Landing page ("Verify a claim?")
│   ├── globals.css             # Global styles + blob animations
│   ├── check/
│   │   └── page.tsx            # Main analysis page (1031 lines)
│   └── api/
│       └── fact-check/
│           └── route.ts        # Next.js API route (BFF layer)
├── components/
│   └── ui/
│       ├── button.tsx          # Gradient button component
│       ├── textarea.tsx        # Styled textarea
│       └── progress.tsx        # Progress bar (Radix UI)
├── lib/
│   └── utils.ts                # Utility functions (cn helper)
├── public/                     # Static assets
├── tailwind.config.js          # Tailwind + animation config
└── package.json
```

---

## 🚦 Getting Started

### Prerequisites
- Node.js 18+
- n8n (self-hosted or cloud)
- Groq API key → [console.groq.com](https://console.groq.com)
- Serper API key → [serper.dev](https://serper.dev)
- ngrok (for exposing local n8n) → [ngrok.com](https://ngrok.com)

### 1. Clone the Repository

```bash
git clone https://github.com/AasthaKapoor27/ai-fact-checker-agent.git
cd ai-fact-checker-agent
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Set Up Environment Variables

Create a `.env.local` file:

```env
N8N_WEBHOOK_URL=https://your-ngrok-url/webhook/fact-check-v2
```

### 4. Set Up n8n

1. Install n8n: `npm install -g n8n`
2. Start n8n: `n8n start`
3. Import the workflow JSON from `/n8n-workflow/` (if included)
4. Add your **Groq** and **Serper** API keys inside n8n nodes
5. Expose n8n with ngrok: `ngrok http 5678`
6. Copy the ngrok URL into your `.env.local`

### 5. Run the Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) 🚀

---

## 📸 UI Preview

### Landing Page
> Minimal, animated landing — click to enter the analysis screen.

### Analysis Screen
> Glassmorphic input card with animated blob backgrounds, example claim chips, and a gradient "Analyze Claim →" button.

### Results Card
- **Verdict badge** — color-coded (green/red/amber)
- **Confidence bar** — animated gradient progress bar
- **AI Analysis** — full reasoning from LLaMA
- **Supporting ✅ / Opposing ❌ points** — two-column grid
- **Source cards** — clickable links grouped by platform

### Analytics Dashboard
- 🍩 Custom SVG Donut Chart (built from scratch — no Chart.js)
- 📊 Signal breakdown bars (supporting vs opposing)
- 🛡️ Source reliability scores
- ⚡ Sentiment badge (Positive / Mixed / Negative)

---

## 🧠 Design Decisions

### Why n8n for the AI pipeline?
Putting all the scraping + AI logic inside a single Next.js function would hit Vercel's 10-second timeout. n8n runs as a persistent service, has a visual debugger, and lets me inspect data at every node — much faster to iterate.

### Why not let the AI decide the verdict?
LLMs can be confidently wrong. My scoring engine uses deterministic keyword + weight math to produce the verdict. The AI then explains that verdict — it cannot override it. This makes the system auditable and removes hallucination risk from the most critical output.

### Why Groq instead of OpenAI?
Groq's LPU hardware runs LLaMA 3.3 70B at hundreds of tokens/second — significantly faster than OpenAI for real-time applications. For a fact-checker where speed is UX, this matters a lot.

### Why a custom Donut Chart?
Libraries like Chart.js add ~150KB to the bundle. Since I only needed one chart with specific Framer Motion animations, I built it in pure SVG with arc path math (`Math.cos` / `Math.sin`). Zero extra dependencies, full control.

---

## 🗺️ Roadmap

- [ ] User authentication & personal claim history
- [ ] Redis caching for repeated claims
- [ ] Claim confidence trend over time
- [ ] Support for image-based claims (multimodal)
- [ ] Browser extension for inline fact-checking
- [ ] Deploy n8n to Railway / DigitalOcean

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**Built with ❤️ by [Aastha Kapoor](https://github.com/AasthaKapoor27)**

*If you found this useful, please ⭐ star the repository!*

</div>
