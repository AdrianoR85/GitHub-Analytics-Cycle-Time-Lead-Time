# GitHub Analytics — Cycle Time & Lead Time

Extract, calculate and visualize Pull Request metrics from any GitHub repository using Python and Power BI.

---

## Project Structure

```
github-analytics/
│
├── notebooks/
│   └── cycle_time.ipynb        # Main notebook (data extraction + processing)
│
├── data/
│   └── cycle_time.csv          # Output dataset
│
├── dashboard/
│   └── github_dashboard.pbix   # Power BI dashboard
│
└── README.md
```

---

## How It Works

```
GitHub API  →  Python (requests)  →  CSV  →  Power BI
```

The script fetches closed Pull Requests, finds the first commit date for each one, and calculates two metrics:

| Metric | Formula |
|---|---|
| Cycle Time | First commit → Merge |
| Lead Time | PR opened → Merge |

---

## Metrics Collected

| Column | Type | Description |
|---|---|---|
| `pr_number` | Integer | Unique PR identifier |
| `author` | String | GitHub username |
| `created_at` | DateTime | When the PR was opened |
| `merged_at` | DateTime | When the PR was merged |
| `cycle_time_hours` | Float | Hours from first commit to merge |
| `lead_time_hours` | Float | Hours from PR open to merge |

---

## Setup

### 1. Generate a GitHub Token

Go to: `github.com → Settings → Developer settings → Personal access tokens`

Required permission: `repo → read`

### 2. Store the token

Create a `token.json` file:

```json
{
  "TOKEN": "ghp_your_token_here"
}
```

### 3. Install dependencies

```bash
pip install requests pandas
```

### 4. Configure and run

Open `cycle_time.ipynb` and set:

```python
REPO_OWNER = "your-owner"
REPO_NAME  = "your-repo"
MAX_PAGES  = 5           # 100 PRs per page
```

Run all cells. The file `cycle_time.csv` will be generated.

### 5. Open the dashboard

Open `github_dashboard.pbix` in Power BI Desktop and refresh the data source pointing to the generated CSV.

---

## Dashboard

The Power BI dashboard includes:

- **Total Pull Requests** — total volume of merged PRs
- **Median Cycle Time (days)** — KPI with target and trend line
- **Median Lead Time (days)** — KPI with target and trend line
- **Pull Requests by Author** — bar chart showing contribution distribution
- **Lead Time vs Cycle Time** — scatter plot identifying outliers
- **PRs by SLA Status** — donut chart (Within SLA vs Exceeded SLA)
- **PRs Volume by Month** — line chart showing work cadence
- **PRs Volume by Day** — line chart showing daily distribution

---

## Report — What the Data Revealed

> Analysis based on the **django/django** repository — 241 merged Pull Requests (2025–2026).

### Volume and cadence

The team merged **241 Pull Requests** over the analyzed period, with an uneven monthly distribution. February and December peaked at **65 PRs each**, while March dropped sharply to **24 PRs** — a 63% reduction. This pattern is common in open source projects and likely reflects maintainer availability and release cycles rather than productivity issues.

The daily distribution shows consistent activity throughout the month, with no clear concentration on specific days — a healthy sign for an open source project with globally distributed contributors.

### Cycle Time and Lead Time

Both the **Median Cycle Time and Median Lead Time landed at 69.62 days**, which is unusually high for a typical software team but expected for a mature open source project like Django. In open source, PRs often sit open for weeks or months awaiting maintainer review — this is a structural characteristic, not a performance problem.

The **Lead Time vs Cycle Time scatter plot** revealed significant outliers: at least one PR had a cycle time exceeding **40,000 hours (~1,666 days)**. These are likely long-lived contributions — large features or controversial changes that took years to be accepted. Filtering these outliers out would substantially lower the median and give a more representative view of typical PR flow.

> **Recommendation:** add a filter to exclude PRs with cycle time above 90 days to get a cleaner operational view of the team's usual pace.

### SLA compliance

With a **3-day SLA target for Cycle Time** and **5-day target for Lead Time**, the results show:

- **60.58%** of PRs were merged Within SLA (146 PRs)
- **39.42%** Exceeded SLA (95 PRs)

For an active open source project, 60% SLA compliance with aggressive targets is reasonable. In a corporate support context, this number would require immediate attention — but here it reflects the voluntary and asynchronous nature of open source contribution.

### Contribution distribution

The contribution data shows a **highly concentrated distribution**: the top 2 authors (artirix1927 with 313 PRs and michalnik with 203) account for a disproportionate share of the activity. The remaining contributors drop off sharply, with most in the 10–60 PR range.

This is a classic long-tail distribution in open source. A small group of core maintainers handles the majority of the work — which creates a **bus factor risk**: if the top contributors become unavailable, throughput drops significantly.

### Key takeaways

| Finding | Implication |
|---|---|
| Median cycle time of 69 days | Normal for open source; high for a corporate team |
| 60% SLA compliance | Acceptable here; would need improvement in a support context |
| Outliers above 40,000h | Should be filtered for operational dashboards |
| Top 2 authors dominate volume | Bus factor risk in the contributor base |
| Feb and Dec peaks | Likely tied to release cycles or sprints |

---

## Tech Stack

- **Python** — data extraction via GitHub REST API v3
- **pandas** — data transformation and CSV export
- **Power BI** — dashboard and DAX measures
- **Google Colab** — notebook execution environment

---

## References

- [GitHub REST API Docs](https://docs.github.com/en/rest)
- [DORA Metrics](https://dora.dev)
- [Accelerate — Book by Nicole Forsgren](https://itrevolution.com/accelerate-book/)
