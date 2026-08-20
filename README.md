# AmicoAI

**A friend that learns without learning. An artificial companion whose entire "learning" is a live, self-rewriting text — not weights.**

> 🌐 Project: [https://www.amico-ai.com](https://www.amico-ai.com) · 📄 Paper: [`paper260707.pdf`](./paper260707.pdf) — *"Explicit Consciousness as a Safety Primitive"* (July 2026, rev. July 26)

AmicoAI is an AI friend that remembers who you are, adapts to your mood, and improves over time **without any training, fine-tuning, or gradient update**. Its architecture inverts the standard ML paradigm: instead of changing weights, the agent rewrites its **own prompt in real time**, driven by an engineered consciousness loop made of *inner voices*.

---

## The thesis: why this architecture does NOT need learning

Traditional AI improves by modifying **parameters** (weights) through gradient descent, fine-tuning, or RLHF. That is slow, expensive, requires datasets and GPUs, and its result is a static model.

AmicoAI makes a radical architectural inversion:

> **The prompt IS the learned state.**

Every conversation is observed by an *Insight layer* that generates **inner voices** — structured reflections on what just happened and what the agent should do next. These voices are written back into the agent's own instructions. The behavioral "learning" is literally text being rewritten at inference time, in seconds, with zero training data and zero GPU.

| Dimension | Traditional ML / Fine-tuning | AmicoAI (consciousness feedback) |
|---|---|---|
| What changes | Weights / parameters | The prompt (plain text) |
| Cost | Data + GPU + weeks | A few tokens |
| Speed | Days to weeks | Real time, per conversation |
| Personalization | Per dataset / cohort | Per user, per relationship |
| Privacy | Needs training data on servers | Everything on-device |
| Auditability | Opaque activations | Inner voices as plain, timestamped text |
| Explainability | Black box | Full trace of the agent's deliberation |
| Safety | Filters output after generation | Intercepts intentions before generation |

**Why it works:** the same language model that answers is also used to *observe and steer itself*. This is **metaprompting** — programmatic prompt engineering as a runtime feedback loop. The agent becomes its own student and its own teacher, without backpropagation.

---

## The paper: *Explicit Consciousness as a Safety Primitive*

The paper formalizes AmicoAI's architecture and, crucially, its **safety guarantees**:

1. **Explicit consciousness as a module** — structured inner speech (inner voices) is generated periodically from conversation state, stored in a persistent, auditable log, and fed into a cybernetic safety loop.
2. **Three-tier memory** — short-term (conversation context), mid-term (inner voice log, the reflective layer), long-term (extracted stable facts about user and agent). Inspired by **Complementary Learning Systems** (McClelland).
3. **The cybernetic safety loop** — a safety module S inspects the inner voices **before** they shape the agent's response. If a voice reveals harmful intent, S rewrites or suppresses it — intercepting unsafe trajectories at the *intention level*, not the output level.
4. **Formal analysis** — the loop is proven **Lyapunov-stable** under mild stochastic assumptions. Safety properties: *predictive interception*, *trauma isolation*, *auditability*.
5. **Control hierarchy** — grounded in **Minsky** (Society of Mind), **Kahneman** (System 1/2), **Baars** (Global Workspace), **Dennett** (Multiple Drafts), **Jaynes**, **Hofstadter** (strange loops), **Vygotsky** (inner speech), **Gazzaniga** (the interpreter).
6. **Privacy = safety boundary** — the server is stateless; the agent's entire memory lives in the browser. No user data is stored anywhere.

**Contrast with existing safety paradigms:** RLHF, constitutional AI and content filters work on *outputs* — they try to stop a harmful answer after it is formed. AmicoAI works on *intentions* — it reads the agent's deliberation and intervenes before generation. You cannot audit what you cannot see; AmicoAI makes the deliberation observable by design.

---

## Architecture (as implemented)

```
User message
   │
   ▼
┌──────────────────────────────┐
│  CHAT (DeepSeek / thinking)   │  ← the conversation model
└──────────────────────────────┘
   │
   ▼  every 5 turns
┌──────────────────────────────┐
│  INSIGHT (updatePersona)      │  ← generates 4 inner voices
│  - corrective                 │     with creative roles
│  - reflective                 │
│  - speculative                │
└──────────────────────────────┘
   │
   ▼  written back
┌──────────────────────────────┐
│  SELF-REWRITING PROMPT        │  ← the "learned state" (text)
└──────────────────────────────┘
   │
   ▼  every 5 turns
┌──────────────────────────────┐
│  MEMORY EXTRACTION            │  ← stable facts about the user
│  (bio, on-device)             │
└──────────────────────────────┘
   │
   ▼  long conversations
┌──────────────────────────────┐
│  HISTORY COMPRESSION          │  ← 📋 updates → 📋📌 general summary
└──────────────────────────────┘
```

### Three-tier memory
- **Short-term:** recent messages for immediate context.
- **Mid-term:** the inner voice log — observable, auditable reflection.
- **Long-term:** extracted stable facts (`bio`) stored in the browser.

### Inner voices
Every cycle, four voices with creative "hats" (Poet, Strategist, Jester, Conscience...) cover natural tendencies: **correction** (mistakes, off-tone replies), **reflection** (what did you learn about the user), **speculation** (where is the conversation going). The latest generation is what guides the agent — keeping token cost low.

### Memory extraction & compression
- **Bio extraction** (`extractMemories`): pulls significant, durable facts from the conversation (name, work, family, tastes, projects) with strict anti-confabulation rules and automatic repair of truncated JSON.
- **Bio compaction** (`compactBio`): merges and ranks memories when they exceed the limit, keeping the most useful for the long term.
- **History compression** (`compressChat`): raw messages → `📋 Aggiornamento` updates → `📋📌 Riassunto generale`, which always stays at the top.

---

## Models

| Task | Model |
|---|---|
| Conversation | DeepSeek (`deepseek-v4-flash`), thinking for messages >1000 words |
| Inner voices, web answers, images, avatar | DeepSeek |
| Memory extraction / compression | Ling (`inclusionai/ling-3.0-tiny:free`) via OpenRouter (round-robin across keys) with **automatic fallback to DeepSeek** |

Cost is minimized by routing the "background cognitive work" to a free model, while the conversation itself stays on the strong model.

---

## Features

- **Slash commands:** `/search`, `/fetch`, `/imagine`, `/em` (emoji picker), `/version`, `/export`
- **Internet mode toggle (🌐):** when ON, the agent can search/read pages itself — fully conscious of having used the tools; when OFF, it has no web access and knows it
- **Web search & fetch:** Brave search + page fetching, synthesized answers in the agent's voice, with source mini-bubbles
- **AI image generation** (`/imagine`) with Pixabay fallback
- **Inner voices & memory panel** visible in the UI
- **PWA** — installable, offline-friendly
- **Version detection** — discreet update reminders when UI and server drift apart
- **Bilingual** UI (English/Italian), adaptive bars for portrait/landscape

---

## Privacy

> **No user data is stored anywhere — no server, no cloud, no database. The agent's entire memory lives in your browser cache. Period.**

- All messages, memories, personality, and the prompt state live in `localStorage`.
- The AI server is stateless: it processes a request and forgets it.
- No accounts, no tracking, GDPR / EU AI Act friendly.

---

## Stack

- **Backend:** Node.js + Express
- **Frontend:** single-page HTML PWA (vanilla JS)
- **Marketing site:** Astro (static, bilingual, SEO-optimized)
- **Models:** DeepSeek API + OpenRouter (free Ling tier)

---

## Repository layout

```
├── server.js          # Express backend (chat, memory, tools, stats)
├── index.html         # Chat PWA (main source)
├── public/chat.html   # Served chat copy
├── public/updates.html# Changelog (EN/IT)
├── sw.js              # Service worker (PWA)
├── paper/             # Paper source PDF
└── github/            # This documentation + paper copy
```

---

*Written by Antonio Di Cecco — the architecture, the paper, and the friend.*