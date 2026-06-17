# SEO Brief Pipeline

A Qoder skill that takes any search query and generates a production-ready SEO brief + HTML article through a 4-stage pipeline.

## Pipeline Stages

```
Query → Stage 1 (Rule-Based Analysis)
        → Stage 2 (LLM Synthesis — 15-section brief)
        → Stage 3 (Fact-Check + Scoring)
        → Stage 4 (HTML Article Generation)
```

| Stage | What it does | Output |
|-------|-------------|--------|
| **1. Rule-Based Analysis** | Determines page archetype, YMYL flag, entity map, outline | `stage1_output` block |
| **2. LLM Synthesis** | Fills 15-section brief template with SEO triplets, content plan, UX blocks | `brief-<slug>.md` |
| **3. Fact-Check + Score** | Verifies facts via WebSearch, scores on 20-criteria rubric (max 40) | Verified brief + GATE block |
| **4. HTML Generation** | Generates responsive HTML article with Schema.org markup | `article-<slug>.html` |

## Features

- **15-section brief template** covering intent, query clusters, SEO triplets, competitive analysis, content plan, UX blocks, sources, schema, editorial rules, and acceptance checklist
- **20-criteria scoring rubric** with production-ready threshold (≥32/40)
- **Fact-checking protocol** with source priority (official > ConsultantPlus/Garant > specialized media)
- **YMYL handling** — mandatory disclaimers, date verification, source links
- **Adaptive briefs** — not all 15 sections required for simple queries (minimum 11)
- **Editorial rules by niche** — separate rule sets for legal and general content
- **Responsive HTML output** — desktop + tablet + mobile breakpoints, sidebar TOC, scroll animations
- **Choice-cards** UI pattern for decision branches (not ASCII trees)

## Installation

### Quick Setup

Copy the `.qoder/skills/seo-brief-pipeline/` directory into your project:

```bash
# Clone this repo
git clone <your-repo-url> seo-brief-pipeline

# Copy skill into your project
cp -r seo-brief-pipeline/.qoder/skills/seo-brief-pipeline/ your-project/.qoder/skills/seo-brief-pipeline/
```

### Directory Structure

```
your-project/
├── .qoder/
│   └── skills/
│       └── seo-brief-pipeline/
│           ├── SKILL.md                          # Main skill orchestration (Qoder)
│           └── references/
│               ├── article-html-guide.md          # HTML generation rules
│               ├── brief-template.md              # 15-section brief template
│               ├── editorial-rules-general.md     # Editorial rules (non-YMYL)
│               ├── editorial-rules-legal.md       # Editorial rules (legal niche)
│               └── stage2-instructions.md         # LLM synthesis instructions
├── PROMPT.md                                      # Universal prompt (any LLM)
├── output/                                        # Generated briefs + HTML articles
└── ...
```

## Usage

### Universal (any LLM — ChatGPT, Gemini, Claude, etc.)

The main file is **`PROMPT.md`** — a self-contained system prompt that merges all pipeline stages, templates, and editorial rules.

**How to use:**
1. Open `PROMPT.md` and copy its entire content
2. Start a new conversation in any LLM (ChatGPT, Gemini, Claude, etc.)
3. Paste the prompt as the first message / system instructions / custom instructions
4. Then provide your query:
   ```
   Query: "госпошлина при разводе в 2026 году"
   ```

The LLM will execute all 4 stages and produce a brief + HTML article.

**Or as Custom Instructions (ChatGPT):**
- Settings → Personalization → Custom instructions → paste PROMPT.md into "How would you like ChatGPT to respond?"

**Or as Gemini Gems / System Instructions:**
- Create a new Gem → paste PROMPT.md as the instruction

### Qoder (slash command)

Copy `.qoder/skills/seo-brief-pipeline/` into your project. Then invoke:

```
/seo-brief-pipeline "госпошлина при разводе в 2026 году"
```

### Optional Inputs

| Input | Purpose |
|-------|---------|
| SERP XML files | Richer competitive analysis (Stage 1) |
| `analysis-engine.mjs` | Automated rule-based analysis |
| `benchmark/golden-queries.json` | Score comparison against golden standard |
| `parsed-matrix.json` | SERP data enrichment |

If none of these are available, the pipeline runs in **offline mode** (`evidence_mode: knowledge_draft`).

## Output Examples

### Brief (`output/brief-<slug>.md`)
- 15 sections covering intent, query clusters, SEO triplets, content plan, UX blocks, schema, editorial rules
- Adaptive: minimum 11 sections for simple queries

### HTML Article (`output/article-<slug>.html`)
- Self-contained single-file HTML with inline CSS + JS
- Schema.org JSON-LD (Article + FAQPage/HowTo)
- Responsive: desktop (1100px grid), tablet (900px), mobile (600px)
- Sidebar TOC with scroll-spy
- Choice-cards for decision branches
- Scroll-triggered animations (IntersectionObserver)
- YMYL disclaimer

## Editorial Rules

| Niche | File |
|-------|------|
| Legal (law, courts, regulations) | `editorial-rules-legal.md` |
| General (how-to, reviews, guides) | `editorial-rules-general.md` |
| Medical, financial | Use `general` + add YMYL rules from `legal` |

## Error Handling

| Situation | Action |
|-----------|--------|
| WebSearch returns 0 results | Mark fact as `unverified`, warn user |
| Contradictory data | Show both values with date + source |
| Stage 2 takes >80% context | Degrade: trim sections 6 and 12 |
| Score < 32 | Return to Stage 2, improve weak sections |

## License

MIT
