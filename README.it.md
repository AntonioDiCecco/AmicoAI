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


---

*Scritto da Antonio Di Cecco — l'architettura, il paper, e l'amico.*
