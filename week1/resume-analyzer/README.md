# Resume Analyzer vs. Job Description

An LLM-powered tool that parses your resume, scrapes a job description, and produces a full analysis report — including a match score, skill gap breakdown, and rewritten bullet points.

Built as an extension of the Day 5 Brochure project from the [LLM Engineering course](https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models/).

---

## What it does

| Step | Action |
|------|--------|
| 1 | Parses your resume PDF using `pdfplumber` |
| 2 | Fetches the job description (URL scrape or paste) |
| 3 | **LLM Call 1** — Scores the match (0–100) across 4 dimensions |
| 4 | **LLM Call 2** — Identifies missing skills, keywords, and experience gaps |
| 5 | **LLM Call 3** — Rewrites weak resume bullets to match the JD |
| 6 | Streams a full markdown report with all findings |

---

## Tech Stack

- **OpenAI API** (`gpt-4.1-mini`) — match scoring, gap analysis, bullet rewrites, report generation
- **pdfplumber** — PDF text extraction
- **BeautifulSoup + requests** — job description scraping
- **IPython streaming display** — typewriter-style report rendering
- **JSON structured outputs** — reliable parsing of all 3 LLM calls

---

## How to use

### 1. Install dependencies
```bash
uv add pdfplumber
```

### 2. Set your inputs
Open `resume_analyzer.ipynb` and edit the inputs cell:
```python
RESUME_PATH = "my_resume.pdf"
JOB_URL     = "https://example.com/jobs/123"
```

### 3. Add the job description

**Option A (recommended for LinkedIn / Indeed / Glassdoor):**
Paste the full job description text into the `JOB_DESCRIPTION_TEXT` variable.

**Option B:** If the company's career page is simple HTML, the URL scraper will work directly.

### 4. Run everything
Run all cells, or use the one-shot cell at the bottom:
```python
analyze_resume(RESUME_PATH, JOB_URL)
```

---

## Sample Output

### Match Score (Step 4)

Score against a Wipro Developer L3 role:

```json
{
  "overall_score": 72,
  "grade": "B",
  "section_scores": {
    "skills_match": 80,
    "experience_relevance": 65,
    "education_fit": 85,
    "keywords_coverage": 65
  },
  "summary": "The candidate has strong technical skills relevant to software development including
  programming languages, frontend and backend frameworks, and testing frameworks. Hands-on project
  experience demonstrates practical software design and deployment capabilities. However, the resume
  lacks explicit emphasis on formal QA process adherence, client communication, and detailed
  documentation practices."
}
```

![Match Score Output](screenshots/01_match_score.png)

---

### Gap Analysis (Step 5)

Key gaps identified:

**Missing Hard Skills**
- Software Validation Automation (Test Case/Scenario Design & Execution)
- Formal Documentation Standards (Flowcharts, Diagrams, Charts)
- Client Requirement Gathering and Clarification Processes
- SDLC in Client-Facing Deployments

**Missing Keywords**
- Quality Assurance Parameters, Root Cause Analysis, Fault Report Tracking, Operational Feasibility Evaluation

**Quick Wins**
- Add examples showcasing end-to-end software development for client deployment
- Incorporate QA keywords: `quality assurance`, `root cause analysis`, `test failure`, `coding standards`
- Highlight any client interaction or requirement gathering experience

![Gap Analysis Output](screenshots/02_gap_analysis.png)

---

### Bullet Rewrites (Step 6)

| Original | Improved |
|----------|----------|
| *"Engineered and refined prompts for LLM-powered automation workflows, improving output accuracy and consistency across use cases."* | *"Designed and optimized AI-driven automation workflows by refining prompt engineering techniques, enhancing system accuracy and reliability in alignment with operational requirements."* |
| *"Built AI automation tools integrating LLM APIs to streamline repetitive operational tasks and cut manual processing effort."* | *"Developed and deployed AI automation software by integrating LLM APIs to increase operational efficiency and reduce manual process time, ensuring seamless client-end deployment."* |
| *"Tested and iterated on prompt templates across model versions, maintaining a versioned prompt library."* | *"Conducted thorough testing and iterative improvement of prompt templates, establishing a version-controlled library to maintain code quality and facilitate reuse, adhering to software validation best practices."* |

![Bullet Rewrites Output](screenshots/03_bullet_rewrites.png)

---

### Full Streamed Report (Step 7)

The final report streams in real time with 7 sections:
1. Overall Match Score with unicode progress bar
2. Score Breakdown table
3. Summary Assessment
4. Skills & Keyword Gaps
5. Resume Bullet Rewrites (original → improved)
6. New Bullets to Add
7. Top 3 Quick Wins

![Full Report](screenshots/04_full_report.png)

---

## Project Structure

```
resume-analyzer/
├── resume_analyzer.ipynb   # Main notebook
├── screenshots/            # Output screenshots for README
└── README.md
```

`scraper.py` is shared from the parent `week1/` directory.

---

## Key design decisions

**Why 3 separate LLM calls instead of one big prompt?**
Each call uses `response_format={"type": "json_object"}` for reliable structured output. A single call trying to return all three outputs in one JSON object tends to hallucinate or mix up the structures.

**Why paste instead of scrape for job boards?**
LinkedIn, Indeed, and Glassdoor require login and JavaScript rendering — simple `requests` hits a cookie wall. The notebook auto-detects this and prompts you to paste instead.

---

## Author

**Karthik Pinneboyina** — built as part of the LLM Engineering course, Week 1.
