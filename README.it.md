# AmicoAI

**Un amico che impara senza apprendere. Un compagno artificiale il cui intero "apprendimento" è un testo vivo che si riscrive da solo — non pesi.**

> 🌐 Progetto: [https://www.amico-ai.com](https://www.amico-ai.com) · 📄 Paper: [`paper260707.pdf`](./paper260707.pdf) — *"Explicit Consciousness as a Safety Primitive"* (luglio 2026, rev. 26 luglio)

AmicoAI è un amico artificiale che ricorda chi sei, si adatta al tuo umore e migliora nel tempo **senza alcun training, fine-tuning o aggiornamento dei pesi**. La sua architettura inverte il paradigma ML standard: invece di modificare i pesi, l'agente riscrive **il proprio prompt in tempo reale**, guidato da un loop di coscienza ingegnerizzata fatto di *voci interiori*.

---

## La tesi: perché questa architettura NON ha bisogno di apprendimento

L'AI tradizionale migliora modificando i **parametri** (pesi) tramite discesa del gradiente, fine-tuning o RLHF. È lento, costoso, richiede dataset e GPU, e il risultato è un modello statico.

AmicoAI fa un'inversione architetturale radicale:

> **Il prompt È lo stato appreso.**

Ogni conversazione viene osservata da uno strato *Insight* che genera **voci interiori** — riflessioni strutturate su ciò che è appena successo e su cosa l'agente dovrebbe fare dopo. Queste voci vengono riscritte nelle istruzioni dell'agente stesso. L'"apprendimento" comportamentale è letteralmente testo riscritto a tempo di inferenza, in pochi secondi, con zero dati di training e zero GPU.

| Dimensione | ML tradizionale / Fine-tuning | AmicoAI (feedback di coscienza) |
|---|---|---|
| Cosa cambia | Pesi / parametri | Il prompt (testo semplice) |
| Costo | Dati + GPU + settimane | Pochi token |
| Velocità | Giorni/settimane | Tempo reale, per conversazione |
| Personalizzazione | Per dataset / coorte | Per utente, per relazione |
| Privacy | Richiede dati di training sui server | Tutto on-device |
| Auditabilità | Attivazioni opache | Voci interiori come testo con timestamp |
| Spiegabilità | Scatola nera | Traccia completa della deliberazione |
| Sicurezza | Filtra l'output dopo la generazione | Intercetta le intenzioni prima della generazione |

**Perché funziona:** lo stesso modello linguistico che risponde viene usato anche per *osservare e guidare se stesso*. Questo è **metaprompting** — ingegneria del prompt programmatica come loop di feedback a runtime. L'agente diventa allo stesso tempo suo studente e suo insegnante, senza retropropagazione.

---

## Il paper: *Explicit Consciousness as a Safety Primitive*

Il paper formalizza l'architettura di AmicoAI e, soprattutto, le sue **garanzie di sicurezza**:

1. **Coscienza esplicita come modulo** — un discorso interiore strutturato (le voci interiori) viene generato periodicamente dallo stato della conversazione, memorizzato in un registro persistente e auditabile, e immesso in un ciclo di sicurezza cibernetico.
2. **Memoria a tre livelli** — breve termine (contesto conversazionale), medio termine (registro delle voci interiori, lo strato riflessivo), lungo termine (fatti stabili estratti su utente e agente). Ispirata ai **Complementary Learning Systems** (McClelland).
3. **Il ciclo di sicurezza cibernetico** — un modulo di sicurezza S ispeziona le voci interiori **prima** che modellino la risposta. Se una voce rivela un intento dannoso, S la riscrive o la sopprime — intercettando le traiettorie non sicure a livello di *intenzione*, non di output.
4. **Analisi formale** — il loop è dimostrato **stabile in senso di Lyapunov** sotto ipotesi stocastiche deboli. Proprietà di sicurezza: *intercettazione predittiva*, *isolamento del trauma*, *auditabilità*.
5. **Gerarchia di controllo** — fondata su **Minsky** (Society of Mind), **Kahneman** (Sistema 1/2), **Baars** (Global Workspace), **Dennett** (Multiple Drafts), **Jaynes**, **Hofstadter** (strange loops), **Vygotsky** (discorso interiore), **Gazzaniga** (l'interprete).
6. **Privacy come confine di sicurezza** — il server è stateless; l'intera memoria dell'agente vive nel browser. Nessun dato dell'utente viene memorizzato da nessuna parte.

**Contrasto con i paradigmi di sicurezza esistenti:** RLHF, AI costituzionale e filtri di contenuto lavorano sugli *output* — cercano di fermare una risposta dannosa dopo che si è formata. AmicoAI lavora sulle *intenzioni* — legge la deliberazione dell'agente e interviene prima della generazione. Non puoi controllare ciò che non puoi vedere; AmicoAI rende la deliberazione osservabile per design.

---

## Architettura (come implementata)

```
Messaggio utente
   │
   ▼
┌──────────────────────────────┐
│  CHAT (DeepSeek / thinking)   │  ← il modello conversazionale
└──────────────────────────────┘
   │
   ▼  ogni 5 turni
┌──────────────────────────────┐
│  INSIGHT (updatePersona)      │  ← genera 4 voci interiori
│  - correttiva                 │     con ruoli creativi
│  - riflessiva                 │
│  - speculativa                │
└──────────────────────────────┘
   │
   ▼  riscritte nel prompt
┌──────────────────────────────┐
│  PROMPT CHE SI RISCRIVE       │  ← lo "stato appreso" (testo)
└──────────────────────────────┘
   │
   ▼  ogni 5 turni
┌──────────────────────────────┐
│  ESTRAZIONE MEMORIE           │  ← fatti stabili sull'utente
│  (bio, on-device)             │
└──────────────────────────────┘
   │
   ▼  conversazioni lunghe
┌──────────────────────────────┐
│  COMPRESSIONE STORICO         │  ← 📋 aggiornamenti → 📋📌 riassunto
└──────────────────────────────┘
```

### Memoria a tre livelli
- **Breve termine:** messaggi recenti per il contesto immediato.
- **Medio termine:** il registro delle voci interiori — riflessione osservabile e auditabile.
- **Lungo termine:** fatti stabili estratti (`bio`) salvati nel browser.

### Voci interiori
Ogni ciclo, quattro voci con "cappelli" creativi (Poeta, Stratega, Giullare, Coscienza...) coprono tendenze naturali: **correzione** (errori, risposte fuori tono), **riflessione** (cosa hai imparato sull'utente), **speculazione** (dove sta andando la conversazione). La generazione più recente è quella che guida l'agente — mantenendo basso il costo in token.

### Estrazione e compressione della memoria
- **Estrazione bio** (`extractMemories`): ricava fatti significativi e duraturi dalla conversazione (nome, lavoro, famiglia, gusti, progetti) con regole anti-confabulazione e riparazione automatica dei JSON troncati.
- **Compressione bio** (`compactBio`): fonde e ranka le memorie quando superano il limite, tenendo le più utili nel lungo termine.
- **Compressione storico** (`compressChat`): messaggi raw → `📋 Aggiornamento` → `📋📌 Riassunto generale`, che resta sempre in cima.

---

## Modelli

| Compito | Modello |
|---|---|
| Conversazione | DeepSeek (`deepseek-v4-flash`), thinking per messaggi >1000 parole |
| Voci interiori, web-answer, immagini, avatar | DeepSeek |
| Estrazione / compressione memoria | Ling (`inclusionai/ling-3.0-tiny:free`) via OpenRouter (round-robin su più chiavi) con **fallback automatico a DeepSeek** |

Il costo è minimizzato instradando il "lavoro cognitivo di sottofondo" a un modello gratuito, mentre la conversazione resta sul modello forte.

---

## Funzionalità

- **Comandi slash:** `/search`, `/fetch`, `/imagine`, `/em` (emoji picker), `/version`, `/export`
- **Toggle modalità internet (🌐):** quando è ON, l'agente può cercare/leggere pagine da solo — pienamente consapevole di aver usato i tool; quando è OFF, non ha accesso web e lo sa
- **Ricerca e fetch web:** Brave + lettura pagine, risposte sintetizzate nella voce dell'agente, con mini-bolle fonti
- **Generazione immagini AI** (`/imagine`) con fallback Pixabay
- **Voci interiori e pannello memoria** visibili nell'interfaccia
- **PWA** — installabile, offline-friendly
- **Rilevamento versione** — promemoria discreto quando UI e server divergono
- **UI bilingue** (italiano/inglese), barre adattive portrait/landscape

---

## Privacy

> **Nessun dato dell'utente viene memorizzato da nessuna parte — né server, né cloud, né database. L'intera memoria dell'agente vive nella cache del tuo browser. Punto.**

- Messaggi, ricordi, personalità e stato del prompt vivono tutti in `localStorage`.
- Il server AI è stateless: elabora una richiesta e la dimentica.
- Nessun account, nessun tracking, GDPR / EU AI Act friendly.

---

## Stack

- **Backend:** Node.js + Express
- **Frontend:** PWA a pagina singola HTML (vanilla JS)
- **Sito marketing:** Astro (statico, bilingue, ottimizzato SEO)
- **Modelli:** API DeepSeek + OpenRouter (tier gratuito Ling)

---

## Struttura del repository

```
├── server.js          # Backend Express (chat, memoria, tool, statistiche)
├── index.html         # Chat PWA (sorgente principale)
├── public/chat.html   # Copia servita della chat
├── public/updates.html# Changelog (EN/IT)
├── sw.js              # Service worker (PWA)
├── paper/             # PDF del paper
└── github/            # Questa documentazione + copia del paper
```

---

*Scritto da Antonio Di Cecco — l'architettura, il paper, e l'amico.*