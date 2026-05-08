# Velora · V0 Design &amp; Spec

> **Velora is a clinical intelligence layer for the multi-specialty hospital.** It reads across the hospital's complete data — clinical, billing, lab, pharmacy, imaging — and surfaces what cross-specialty attention requires, with every claim cited to a structured record or a published clinical guideline.
>
> *A librarian, not a clinician. It cites; it does not interpret.*

This repository is the **V0 design package** — the homepage, the five intent deep-dives, the Clinical Guidelines catalogue, the chat-UI mockup, and the underlying specs. Open `velora-home.html` in a browser to start.

---

## Quick start

```bash
# Just open the home page in any modern browser
open velora-home.html        # macOS
xdg-open velora-home.html    # Linux
start velora-home.html       # Windows
```

Or serve the folder over HTTP if you want clean inter-page navigation:

```bash
python3 -m http.server 8080
# then visit http://localhost:8080/velora-home.html
```

No build step, no dependencies — every HTML file is self-contained. Fonts load from Google Fonts CDN; everything else is inline.

---

## What's in here

### Pages (open in browser)

| File | What it is |
|---|---|
| [`velora-home.html`](./velora-home.html) | **Homepage.** Hero, story (3 patient archetypes), V0 cut, five intents, Intent × Patient matrix, link to Clinical Guidelines. |
| [`velora-intent-mdt.html`](./velora-intent-mdt.html) | **Intent ① — MDT brief.** Cross-team view with Ravi Shankar's case + Patient Journey timeline + drawer mock + same-patient/different-hospital config. |
| [`velora-intent-open-loops.html`](./velora-intent-open-loops.html) | **Intent ② — Open loops.** Lakshmi Pandey's 4 unclosed chains. |
| [`velora-intent-active-meds.html`](./velora-intent-active-meds.html) | **Intent ③ — Active meds &amp; safety.** Suresh Iyer's NSAID-on-apixaban case. |
| [`velora-intent-trends.html`](./velora-intent-trends.html) | **Intent ④ — Trends across visits.** Anita Mehta's HbA1c &amp; BP trajectory. |
| [`velora-intent-why-flagged.html`](./velora-intent-why-flagged.html) | **Intent ⑤ — Why flagged today.** Ramesh Kumar's morning brief after AKI discharge. |
| [`velora-guidelines.html`](./velora-guidelines.html) | **Clinical Guidelines.** Catalogue of 80+ guideline bodies across 15 specialties — global + India context, with source-of-truth on every entry. |
| [`velora-chat-ui.html`](./velora-chat-ui.html) | **Chat UI mockup.** Doctor-facing conversational interface (early exploration; not the canonical surface). |

### Specs (read alongside the pages)

| File | What it is |
|---|---|
| [`Velora_V0_Spec.docx`](./Velora_V0_Spec.docx) | V0 product spec — what gets built in the first 60 days (Zydus pilot, General Medicine pack). |
| [`Velora_V0_Intent_Spec.md`](./Velora_V0_Intent_Spec.md) | Markdown intent spec — five intents, suggestion logic, response logic, validation contract. |
| [`Velora_V0_Intent_Logic.docx`](./Velora_V0_Intent_Logic.docx) | Word version of the intent logic spec (paired with the MD). |

---

## The product, in 30 seconds

A clinician opens a patient. Velora surfaces **two stacks**, visually separated:

- **Stack 1 — Hospital records (`FACT`).** Verbatim from the hospital's data, attributed and verifiable. No AI authoring. Click any line → source.
- **Stack 2 — AI Interpretation (`SUGGESTED · informational only`).** Curated clinical groupings from published guidelines (KDIGO, ESC, ICMR, ISN-India, ADA, etc.) applied to this patient's data. AI picks *which* guideline panels apply; AI does **not** author clinical claims.

> *Stack 1 is always above Stack 2. A doctor with 5 seconds reads only Stack 1. A doctor with 15 seconds reads both.*

### Five intents in V0

1. **MDT brief** — cross-team view: what each specialty has said, where they collide, what's pending.
2. **Open loops** — chains that started but didn't close (labs, referrals, follow-ups, fills).
3. **Active meds &amp; safety** — what's on board, with cited DDI, allergy, and duplicate-order rules before action.
4. **Trends across visits** — direction of key metrics over time, with the cited target body.
5. **Why flagged today** — concrete events that fired since the patient's last contact.

### Three patient archetypes (internal taxonomy — never shown to the doctor)

1. **Already in your care** — returning patient with your own notes on file.
2. **New to your care, known here** — first visit with you, but the hospital has cross-specialty history.
3. **First visit to the hospital** — brand new, or referred in with paper records (Velora OCRs externals into Stack 1, tagged `external · unverified`).

### Why these five (V0 cut)

1. `Answerable from structured data` — no free-text speculation.
2. `Citable to a guideline body` — every claim has provenance.
3. `Renderable on one surface` — fits a single drawer in 10 seconds.

Get these five right with doctors first, then expand. New intents are config + content additions, not architecture changes.

---

## Configurability — Clinical Guidelines

The `velora-guidelines.html` catalogue lists every recognised guideline body per specialty. **The hospital chooses which body wins per condition** via `hospital.yaml`:

```yaml
cardiology.atrial_fibrillation: ESC 2024
cardiology.heart_failure:        AHA/ACC 2022
nephrology.pd_adequacy:          ISN-India 2020
endocrinology.t2dm:              ICMR 2018
icu.sepsis:                      SSC 2021
```

One hospital may use only ESC. Another mixes ICMR + AHA + KDIGO. A third runs everything off a single set. The same Velora codebase reads the chosen body and cites its section on every answer card.

Every body in the catalogue includes:
- Acronym, full organisation name, latest guideline year
- Conditions covered (chips)
- Region badge (India · US · EU · Global)
- Source-of-truth on hover — publishing organisation + URL where the latest version lives

15 specialties · 80+ bodies. Quick-jump nav at the top of the catalogue.

---

## File structure

```
Zyvelor/
├── velora-home.html                      ← start here
├── velora-intent-mdt.html
├── velora-intent-open-loops.html
├── velora-intent-active-meds.html
├── velora-intent-trends.html
├── velora-intent-why-flagged.html
├── velora-guidelines.html
├── velora-chat-ui.html
├── Velora_V0_Spec.docx
├── Velora_V0_Intent_Spec.md
├── Velora_V0_Intent_Logic.docx
├── README.md                             ← this file
└── .gitignore
```

---

## Status

- **V0 stage.** Design and spec only — no production code yet.
- **Pilot target.** Zydus Hospital, General Medicine pack, 60-day pilot.
- **Validation contract.** Five non-negotiables on every answer: (1) source per claim, (2) guideline per clinical claim, (3) executed query, (4) data freshness, (5) exclusion log.

> Velora may not state a fact it cannot point to.

---

## License &amp; ownership

© 2026 Zyvelor Labs · zyvelorlabs@tatvacare.in
Internal design package · all-rights-reserved unless and until otherwise noted.
