# Romanian Legal Plain Language Pipeline

A GenAI capstone project for the *GenAI for Humanists* course (SoSe 2026).

This pipeline takes Romanian legal PDFs from the *Monitorul Oficial* (Official Gazette), rewrites them in plain language using an AI agent, measures the readability improvement with lexical indices, and produces a packaged PDF for each document containing the summary, the comparison, and the simplified text.

---

## What it does

1. **Loads** legal PDFs from `data/`.
2. **Agent 1 — Simplificator**: rewrites the legal text in plain Romanian, following the criteria in `code/criteria.md`, while preserving all legal meaning (obligations, rights, deadlines, sanctions).
3. **Readability scoring**: computes five lexical indices (Gîfu et al. 2016, Sec. III.B.1) on both the original and simplified text using a Romanian spaCy model, then compares them.
4. **Agent 2 — Redactor rezumat**: extracts 4–7 key points from the original document for a reader summary.
5. **Packages** the results into a PDF in `simplified_docs/`, containing the disclaimer, the summary, the readability comparison table, and the full simplified text.

### Readability indices

| Index | What it captures |
|---|---|
| Average word length (chars) | Shorter words → simpler |
| Average words per sentence | Shorter sentences → simpler |
| Unique content words per sentence | Lower lexical load per sentence |
| Word entropy | Lower diversity → more consistent wording |
| Average word–lemma edit distance | Lighter morphological inflection |

A lower value on every index indicates simpler, more readable text.

---

## Repository structure

```
.
├── code/
│   ├── legal_plain_language_pipeline.ipynb   # Main pipeline notebook
│   └── criteria.md                           # Plain-language simplification rules (Romanian)
├── data/                                     # Input: Romanian Official Gazette PDFs
├── simplified_docs/                          # Output: simplified PDFs
├── untested_doc/                             # Extra input PDF not yet run through the pipeline
├── prompts/                                  # Prompt design documents and deep-report outputs
├── presentation/                             # Project presentation
├── questionnaires/                           # Evaluation forms (original vs. simplified)
├── .gitignore
└── README.md
```

---

## Setup

### 1. Clone and create a virtual environment

```bash
git clone <repo-url>
cd GenAI_Capstone_Project
python -m venv venv
```

Activate it:

- **Windows:** `.\venv\Scripts\activate`
- **macOS/Linux:** `source venv/bin/activate`

### 2. Install dependencies

```bash
pip install openai-agents pdfplumber spacy fpdf2 python-dotenv matplotlib ipykernel
python -m spacy download ro_core_news_lg
```

### 3. Add your OpenAI API key

Create `code/.env`:

```
OPENAI_API_KEY=sk-...
```

### 4. Register the venv as a Jupyter kernel

```bash
python -m ipykernel install --user --name genai_capstone --display-name "GenAI Capstone (venv)"
```

Then in Jupyter, select **Kernel → GenAI Capstone (venv)** before running.

---

## Usage

1. Place input PDFs in `data/`.
2. Open `code/legal_plain_language_pipeline.ipynb` in Jupyter.
3. Run all cells. Simplified PDFs are written to `simplified_docs/`.

### Input documents (included)

Six issues of the *Monitorul Oficial Partea I* from December 2025 – April 2026:

| File | Date |
|---|---|
| Monitorul Oficial nr. 1204 | 2025-12-29 |
| Monitorul Oficial nr. 10 | 2026-01-09 |
| Monitorul Oficial nr. 27 | 2026-01-15 |
| Monitorul Oficial nr. 151 | 2026-02-27 |
| Monitorul Oficial nr. 207 | 2026-03-17 |
| Monitorul Oficial nr. 282 | 2026-04-07 |

---

## Design notes

- **ReaderBench, lexical subset.** The five indices from Sec. III.B.1 are computed directly with the Romanian spaCy model rather than the `rbpy-rb` package, which pins outdated dependencies. Cohesion/semantic indices (LSA/LDA) are out of scope.
- **spaCy lemmas as stem proxy.** The paper's stem-distance indices are approximated with word–lemma edit distance, because spaCy exposes lemmas rather than raw Romanian stems.
- **Deterministic PDF writer.** The LLM produces text; a Python function renders the PDF with a DejaVu Unicode font so Romanian diacritics (`ă â î ș ț`) display correctly.
- **Legal fidelity disclaimer.** Every output PDF includes a notice that the simplified version is orientational and does not constitute legal advice.

---

## Bibliography

Gîfu, Daniela & Dascalu, Mihai & Trausan-Matu, Stefan & Allen, Laura. (2016). Time Evolution of Writing Styles in Romanian Language. 1048-1054. 10.1109/ICTAI.2016.0161. 


