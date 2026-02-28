# Code-Plagiarism-Detector-with-AST-Diffing
# ASTect — Structural Code Plagiarism Detector

> Detect code plagiarism at the **logical structure level** using Abstract Syntax Trees — not surface-level string matching.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python) ![Status](https://img.shields.io/badge/status-In%20Development-orange) ![License](https://img.shields.io/badge/license-MIT-green)

---

## The Problem

Traditional plagiarism tools use string comparison or token matching. A student can easily fool them by:
- Renaming all variables and functions
- Changing comments or whitespace
- Reordering independent statements
- Switching between equivalent constructs (`for` vs `while`)

The **logical structure** of copied code stays the same even after these changes. Existing tools miss this entirely.

## Our Solution

ASTect parses student submissions into **Abstract Syntax Trees** and compares their **structure** — not their text. Two submissions can look completely different on the surface while being structurally identical.

**How it works:**
1. Parse each file into an AST
2. Normalize all identifier names to generic tokens (`VAR_0`, `FUNC_1`, etc.)
3. Compute structural fingerprints using subtree hashing
4. Compare all submission pairs using Jaccard similarity + Winnowing
5. Flag pairs above a configurable threshold as suspicious

---

## Demo Output

```
Comparing 5 files → 10 pairs...

=================================================================
  ASTECT — SIMILARITY REPORT
=================================================================
  Total pairs checked : 10
  SUSPICIOUS (>75%)   : 1
  SIMILAR (45-75%)    : 2
=================================================================

  ⚠  SUSPICIOUS PAIRS (possible plagiarism):

  [100.0%]  alice.py  <-->  bob.py

  ~  SIMILAR PAIRS (worth reviewing):

  [ 71.0%]  alice.py  <-->  carol.py
  [ 71.0%]  bob.py   <-->  carol.py
=================================================================
```

alice.py and bob.py implement the same bubble sort — with every variable renamed. String matchers would miss this. We catch it at **100% similarity**.

---

## Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Core Parser | Python `ast` module | Converts source code into Abstract Syntax Trees(AST) for structural analysis |
| Multi-language | `tree-sitter` (planned) | Parse non-Python languages like Java, C, and JavaScript |
| Algorithms | Custom (Jaccard + Winnowing) | Compute structural similarity between submissions |
| Backend API | FastAPI | Provides REST API endpoints (upload files, run comparisons, fetch results) |
| Frontend | HTML + CSS + Vanilla JS | Dashboard UI + Handling API calls, file uploads, and dynamic rendering |
| Visualization | D3.js | Generates similarity cluster graphs and interactive visualizations |
| Storage | SQLite | Result persistence |
| Desktop wrapper | Electron.js (planned) | Wraps the web app into a cross-platform desktop application |

---

## Project Structure

```
astect/
├── poc/
│   ├── ast_parser.py      # AST parsing + identifier normalization
│   ├── similarity.py      # Jaccard + Winnowing scoring engine
│   └── demo.py            # Self-contained demo with fake submissions
├── backend/               # FastAPI server (WIP)
├── frontend/              # HTML/CSS/JS dashboard (WIP)
├── tests/                 # pytest test suite
└── README.md
```

---

## Algorithms

### 1. Subtree Hashing (Jaccard Similarity)
Every node in the normalized AST gets an MD5 hash computed from its type and all its children's hashes. Two files are compared by the Jaccard index of their hash sets:

```
similarity = |hashes_A ∩ hashes_B| / |hashes_A ∪ hashes_B|
```

### 2. Winnowing (Token n-gram Fingerprinting)
The AST is traversed depth-first to produce an ordered token sequence. We extract k-grams (k=5), hash each, then apply the Winnowing algorithm (Schleimer et al., 2003) to select a stable minimum fingerprint per window.

### Combined Score
```
final_score = 0.60 × jaccard + 0.40 × winnowing
```
- **≥ 75%** → SUSPICIOUS (flagged for review)
- **45–75%** → SIMILAR (worth checking)
- **< 45%** → DIFFERENT

---

## Getting Started

### Requirements
- Python 3.10+

### Run the PoC demo
```bash
git clone https://github.com/<your-team>/astect.git
cd astect/poc
python demo.py
```

### Compare two specific files
```bash
python similarity.py file_a.py file_b.py
```

### Compare a whole submission folder
```bash
python similarity.py submissions/
```

---

## Roadmap

- [x] Python AST parser + normalizer
- [x] Jaccard subtree similarity
- [x] Winnowing fingerprinting
- [x] All-pairs batch comparison
- [ ] FastAPI REST backend
- [ ] HTML/CSS/JS dashboard
- [ ] D3.js cluster visualization
- [ ] Side-by-side diff viewer
- [ ] Multi-language support (JS, Java, C)
- [ ] Electron desktop wrapper

---

## Team

Built during a 24-hour hackathon.

---

## License

MIT
