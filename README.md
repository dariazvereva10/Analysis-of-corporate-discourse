## Analysis of Public Corporate Discourse: Identification of the Relationship between Planned and Implemented

This repository contains the code and data for a computational linguistics study analyzing the relationship between strategic discourse (planned) and performance discourse (implemented) in corporate annual reports (Form 10-K) of Fortune 500 companies.

## Overview

This research applies Natural Language Processing (NLP) methods to examine how publicly traded companies articulate their strategic intentions, risk factors, and operational results. The study analyzes semantic similarity, thematic overlap, and linguistic modality markers across different sections of annual reports.

### Key Research Questions

1. How semantically similar are strategic descriptions, risk disclosures, and performance results?
2. Do companies' reported outcomes align more closely with strategic claims or risk warnings?
3. What linguistic patterns distinguish risk discourse from performance discourse?

### Methods Implemented

| Method | Description |
|--------|-------------|
| **Semantic Similarity Analysis** | Cosine similarity using Sentence-BERT embeddings (all-MiniLM-L6-v2) |
| **Topic Overlap Analysis** | Keyword extraction with KeyBERT, Jaccard similarity for thematic overlap |
| **Linguistic Marker Analysis** | Frequency analysis of modality markers (uncertainty) and resultative markers (achievement) |



## Repository Structure

```
parsing_reports.ipynb          # HTML parsing of 10-K reports 
cosine_similarity.ipynb        # Semantic similarity analysis
thematic_analysis.ipynb        # Keyword extraction, overlap
modality_analysis.ipynb        # Linguistic marker frequency analysis
requirements.txt               # Python dependencies
README.md
```



## Data

### Source
- **SEC EDGAR database** – Annual reports (Form 10-K) of 33 Fortune 500 companies
- **Time period** – Fiscal years 2023, 2024, 2025 (99 documents total)

### Extracted Sections
| Section | Content |
|---------|---------|
| Item 1 | Business description (strategic discourse) |
| Item 1A | Risk factors (uncertainty discourse) |
| Item 7 | MD&A – Management Discussion & Analysis (performance discourse) |

### Companies Included (sample)
UnitedHealth Group, Apple, Amazon, Tesla, Johnson & Johnson, Nvidia, Walmart, Meta, Goldman Sachs, Ford, Comcast, and 22 others.



## Input Format

### For Parsing (`parsing_reports.ipynb`)

**Input:** HTML files of 10-K reports from EDGAR

```
REPORTS_DIR = "/path/to/html/files/"
OUTPUT_DIR = "/path/to/output/json/"
```

**Output:** JSON file per report
```json
{
  "company": "apple",
  "fiscal_year": 2024,
  "file_name": "aapl-20240928",
  "section_item_1": "business description text...",
  "section_item_1a": "risk factors text...",
  "section_item_7": "MD&A text..."
}
```

### For Analysis (`cosine_similarity.ipynb`, `thematic_analysis.ipynb`, `modality_analysis.ipynb`)

**Input:** JSON files in the sample directory

```python
DATA_DIR = "/path/to/JSON/sample/"
OUTPUT_CSV = "/path/to/results.csv"
```

**Required JSON fields:**
- `company` – Company identifier (string)
- `fiscal_year` – Reporting year (int)
- `section_item_1` – Business description (string)
- `section_item_1a` – Risk factors (string)
- `section_item_7` – MD&A (string)



## Output Format

### Semantic Similarity Results (`cosine_similarity_results.csv`)

| Column | Description |
|--------|-------------|
| `company` | Company identifier |
| `year` | Fiscal year |
| `similarity_1_7` | Cosine similarity (Item 1 vs Item 7) |
| `similarity_1A_7` | Cosine similarity (Item 1A vs Item 7) |
| `gap` | `similarity_1_7 - similarity_1A_7` |

### Topic Overlap Results (`extracted_keywords.csv`)

| Column | Description |
|--------|-------------|
| `company` | Company identifier |
| `year` | Fiscal year |
| `keywords_1` | JSON list of keywords from Item 1 |
| `keywords_1A` | JSON list of keywords from Item 1A |
| `keywords_7` | JSON list of keywords from Item 7 |

### Modality Analysis Results (`linguistic_markers_res.csv`)

| Column | Description |
|--------|-------------|
| `company` | Company identifier |
| `year` | Fiscal year |
| `modal_ratio_1A` | Uncertainty marker ratio in Item 1A |
| `modal_ratio_7` | Uncertainty marker ratio in Item 7 |
| `result_ratio_1A` | Achievement marker ratio in Item 1A |
| `result_ratio_7` | Achievement marker ratio in Item 7 |



## Installation & Requirements

### Prerequisites
- Python 3.9+
- Google Colab (notebooks are designed for Colab with drive mounting)



## Usage Instructions

### Step 1: Parse HTML Reports to JSON

Run `parsing_reports.ipynb`:
1. Set `REPORTS_DIR` to folder with downloaded 10-K HTML files
2. Set `OUTPUT_DIR` for JSON output
3. Execute all cells – each HTML file will be parsed into a JSON file

### Step 2: Compute Cosine Similarity

Run `cosine_similarity.ipynb`:
1. Mount Google Drive (if using Colab)
2. Set `DATA_DIR` to JSON folder
3. Set `OUTPUT_CSV` for results
4. Run cells – outputs similarity scores and gap metric

### Step 3: Extract Keywords & Topic Overlap

Run `thematic_analysis.ipynb`:
1. Set `JSON_FOLDER_PATH` to JSON folder
2. Set `OUTPUT_KEYWORDS_PATH` and `KEYWORDS_FILE_PATH`
3. Run cells – extracts keywords using KeyBERT, calculates Jaccard overlap

### Step 4: Analyze Linguistic Markers

Run `modality_analysis.ipynb`:
1. Set `JSON_FOLDER`, `OUTPUT_FILE`, `INPUT_FILE`, `SIMILARITY_FILE`
2. Run cells – calculates marker ratios and quadrant classifications

### Step 5: Merge Results

The final notebook (`modality_analysis.ipynb`) merges modality metrics with similarity data for comprehensive company-level analysis.


## Key Metrics Definitions

### Cosine Similarity (Semantic)
```
cos_sim(A,B) = (A·B) / (||A|| × ||B||)
```
- Range: 0 (no similarity) to 1 (identical)
- Calculated on Sentence-BERT embeddings (384 dimensions)

### Jaccard Index (Topic Overlap)
```
J(A,B) = |A ∩ B| / |A ∪ B|
```
- Where A, B are sets of extracted keywords

### Marker Ratios (Linguistic)
```
ratio = (marker_count) / (total_tokens)
```
- `modal_ratio` – Uncertainty markers (may, could, risk, uncertain, etc.)
- `result_ratio` – Achievement markers (increased, growth, profit, etc.)

### Gap Metrics
```
semantic_gap = similarity_1_7 - similarity_1A_7
topic_gap = J(1,7) - J(1A,7)
```

- **Positive gap** → Results align more with strategic discourse
- **Negative gap** → Results align more with risk discourse


## References

- SEC EDGAR database: https://www.sec.gov/edgar
- Fortune 500 ranking: https://fortune.com/ranking/fortune500/
- Sentence-BERT: Reimers & Gurevych (2019)
- KeyBERT: keyword extraction with BERT embeddings
