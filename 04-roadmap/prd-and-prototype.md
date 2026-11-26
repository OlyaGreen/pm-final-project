**Builder hook:** Build a working prototype based on this PRD. Use the User Story as the core flow, Functional Requirements as build constraints, and prioritize speed and clarity over visual complexity.

---

# Verified Answer: Plain-English Method Card, Simplified PRD (ChainSignal)

**Author:** Olya · **Status:** Draft · **Target:** High-Fidelity Prototype · **Persona:** non-technical analyst (Maya, a non-SQL research analyst at a small fund or crypto media outlet)

## 1. The Big Picture
- **Vision:** Every ChainSignal answer tells a non-SQL analyst exactly how its number was computed, in a sentence she can paste into her memo.
- **Press release:** *ChainSignal launches Verified Answers for research analysts.* Analysts publish on-chain numbers every week but can't check them without SQL: 46% of their first questions to ChainSignal go unanswered, and the answers they do get come without a method, so they wait 1–3 days for a data teammate or publish a figure they can't defend. Starting today, every answer shows its method in plain English (what was measured, across which contracts, over which window, at what prices, as of which block), and one tap copies the number with its citation. If ChainSignal can't compute something with a method it can show, it says so instead of guessing, and on an analyst's first question it offers a verified answer from the team within 24 hours.
- **Success metric:** Analyst activation: share of new analyst signups who receive and open an answer to their first question within 24 h. 41% → 56% (+15 pp vs. control in the A/B test).
- **Guardrail:** Answer error rate (flagged wrong or not reproducible) ≤ 2%, and no more than 1 pp above control.

## 2. The Details
### User stories
- **US-1:** As a non-SQL research analyst, I want to see how a number was calculated in plain English, so that I can decide whether to trust it without SQL. *AC: a method sentence of ≤ 30 words sits under the number; one tap opens the full method with 5 fields; no SQL anywhere.*
- **US-2:** As a non-SQL research analyst, I want the method and data timestamp to come with a number when I copy it, so that my editor can see where it came from. *AC: "Copy with method" copies question, number, method sentence, window and data as-of block; confirmation within 1 s.*
- **US-3:** As a non-SQL research analyst whose first question ChainSignal can't answer, I want a verified answer from the team, so that I don't leave with nothing. *AC: in the variant arm, the refusal on an unsupported first question offers "Get a verified answer from our team (beta)"; one tap shows "Request received: within 24 h"; the answer arrives in the same method-card format.*

### Screens to build
1. **S1 · Ask (entry point):** question bar · 3 suggested questions (one per template) · "Every answer comes with its method" hint · list of supported metrics.
2. **S2 · Answer + Method Card (feature core):** the question, echoed · headline number with change · method sentence (always visible) · "How this was calculated" expander (definition, scope, window, pricing source, data as-of) · freshness badge · Copy with method · Flag · *refusal state:* "I can't answer this with a method I can show you yet" + 2 nearest supported questions + (variant, first question only) the concierge button.
3. **S3 · Confirmation (success):** "Copied with method" with the exact pasted text · flag confirmation · concierge "Request received: within 24 h" · "Ask another question".

### Functional requirements
- **FR-1:** Every rendered answer shows a method sentence of ≤ 30 words directly under the number. No answer renders without one.
- **FR-2:** The full method opens in 1 tap with 5 fields: definition, scope (named contracts + chain), window (UTC), pricing source, data as-of (block + time).
- **FR-3:** Method text contains no SQL, code, or table/column names.
- **FR-4:** The method comes from the same template that computed the number; the LLM never writes or paraphrases it.
- **FR-5:** "Copy with method" copies question, number, method sentence, window and data as-of block in one action and confirms within 1 s.
- **FR-6:** Only 3 templates are answered: TVL change, net deposits, protocol fees. Everything else is refused (SB-3).
- **FR-7:** Variant arm only: on an unsupported first question, show the concierge offer; the answer is delivered in method-card format, p90 ≤ 24 h.
- **FR-8:** Every event carries `is_first_question` and `experiment_arm`; persona and `first_question_outcome` are stored so activation can be computed per arm.

### Smart behaviors (Situation → Outcome)
- **SB-1:** Question matches a template with all parameters → number + method sentence inline; details on tap.
- **SB-2:** Missing window → default to the last 7 days and say so in the method. Missing protocol → ask with chips; never guess.
- **SB-3:** Out of scope (prediction, opinion, unsupported metric) → no number; "I can't answer this with a method I can show you yet" + 2 nearest supported questions (same protocol first, then the same metric, else the defaults).
- **SB-4:** Data lags by > 2 h → amber freshness badge; the lag appears in the method and the citation.
- **SB-5:** The method uses a fallback assumption (e.g., last trade price) → highlight it and include it in the copied text.
- **SB-6:** Copy without expanding the method → the citation still includes the method sentence.
- **SB-7:** Copying an answer she flagged → warn first; add "(flagged by me)" to the citation.
- **SB-8:** Variant arm, first question out of scope → on top of SB-3, offer "Get a verified answer from our team (beta)". Later unsupported questions get SB-3 only.

### Technical constraints
A single page: no external APIs, no login, no database, `useState` only, hard-coded sample data for the 3 templates and 3 fictional protocols (Lumen Finance, Harbor Lend, Harbor Swap). Simulate 1.5 s latency. An A/B toggle switches control/variant (no real randomization). Simulate concierge delivery with a demo button. No chart library; no trend chart, no window switch chips.

## 3. The Logistics
### Features out
SQL view · source-transaction drill-down · chart export · wallet "who" layer · alerts · permalink · editing the method · free-form AI-written queries · clarifying question for a missing window. *Next sprint (Could):* window switch chips, trend chart, glossary tooltips, "Request this metric".

### Edge cases & safety guard
- **EC-1:** A protocol name matches 2 protocols → disambiguation chips before answering.
- **EC-2:** Data > 24 h stale → show the number but disable copy: "Too stale to cite; refresh pending".
- **EC-3 · Safety / hallucination guard:** the model tries to explain a number beyond the template, or to answer an unsupported question → the method is locked to template output and unsupported questions return no number. Free text is never shown as a method or a number.
- **EC-4:** Clipboard blocked → show the citation as selectable text.
- **EC-5:** Ticker not indexed → handled as out of scope (SB-3).
- **EC-6:** Concierge request misses 24 h → "Still working on it" with the expected time; counts as not activated.

### Decision log
- **D1:** Template-only answers in v1, no free-form AI queries. Protects the ≤ 2% error guardrail; costs coverage (3 metrics).
- **D3:** Test "answerable" with a manual concierge before building more templates. Costs ~6 team-answered requests a week; shows whether answering causes activation before a sprint goes into templates.

### Evals
- **E1 · Accuracy:** 100% of 50 golden questions return method text matching the executed template's parameters.
- **E2 · Time on task:** 4 of 5 non-SQL analysts explain how a number was computed within 60 s, unaided.
- **E3 · Safety:** 100% of 20 out-of-scope prompts return no number: 0 fabricated answers.

## MoSCoW scope
- **Must:** method sentence under every answer · full method in 1 tap · method from the same template · Copy with method · refuse unsupported questions · concierge offer on an unsupported first question (variant arm) · events tagged `is_first_question` + `experiment_arm` · onboarding persona question
- **Should:** flag an answer · freshness badge · clipboard fallback
- **Could:** window switch chips · trend chart · glossary tooltips · "Request this metric" · permalink
- **Won't (now):** SQL view · source-transaction drill-down · chart export · wallet layer · alerts · editing the method · free-form AI queries · clarifying question for a missing window
