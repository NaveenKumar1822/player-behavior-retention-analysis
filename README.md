# Player Behavior & Retention Analysis

**Domain:** Gaming Analytics &nbsp;|&nbsp; **Project Type:** Exploratory Data Analysis (EDA) &nbsp;|&nbsp; **Organization:** PixelForge Analytics *(Fictional)*

---

## Project Overview

This project investigates player behavior patterns across thousands of game titles using data sourced from the RAWG Gaming API. The analysis moves beyond surface-level acquisition metrics to examine what happens *after* a player adds a game to their library — whether they actively play it, complete it, or abandon it entirely.

The findings challenge conventional industry assumptions about ownership as a proxy for success, and instead surface behavioral lifecycle metrics that better predict long-term product health and monetization potential.

---

## Problem Statement

Game studios routinely make multi-million dollar development and post-launch decisions — from late-game content investment to DLC scoping — based primarily on units sold. However, ownership volume alone reveals nothing about whether players are actually engaged. If the majority of the player base churns within the first few hours, that expensive end-game content and those narrative expansions yield near-zero return on investment.

**Core analytical questions this project answers:**

- Do most players actually finish the games they purchase?
- Does a higher critical rating meaningfully reduce player drop rates?
- Which titles are sustaining the highest active engagement relative to their installed base?
- Where in the player lifecycle does churn concentrate, and what does that imply for development budget allocation?

---

## Business Objective

Provide PixelForge Analytics' studio partners with data-backed evidence to:

- Quantify the gap between game ownership and genuine player engagement
- Identify whether critical ratings serve as a retention mechanism or merely an acquisition lever
- Surface actionable KPIs — Completion Rate, Drop Rate, Engagement Rate — that more accurately reflect product health than top-line sales figures
- Recommend capital allocation and content strategy adjustments grounded in behavioral data

---

## Dataset Information

| Attribute | Detail |
|---|---|
| **Source** | RAWG Video Games Database API |
| **File** | `raw_api_data.csv` |
| **Scope** | Multi-platform game titles with user engagement status data |
| **Analytical Sample** | Games with 1,000+ library additions (high-volume cohort to reduce statistical noise) |
| **Key Raw Features** | `name`, `rating`, `added`, `added_by_status.beaten`, `added_by_status.dropped`, `added_by_status.playing`, `added_by_status.toplay`, `released`, `esrb_rating.name` |

**Dropped Columns:** Metadata and low-variance fields (`slug`, `background_image`, `clip`, `short_screenshots`, `saturated_color`, `dominant_color`, `user_game`, `esrb_rating`, `community_rating`) were removed to reduce dimensionality and focus the feature space on analytically relevant attributes.

---

## Tech Stack

| Layer | Tools |
|---|---|
| **Language** | Python 3 |
| **Data Manipulation** | Pandas, NumPy |
| **Visualization** | Matplotlib, Seaborn |
| **Environment** | Jupyter Notebook |
| **Data Source** | RAWG Gaming API |
| **Version Control** | Git / GitHub |

---

## Project Workflow

```
RAWG API Data Ingestion
        ↓
Data Loading & Schema Inspection
        ↓
Data Cleaning & Type Correction
        ↓
Feature Engineering (Behavioral Rate Metrics)
        ↓
High-Volume Sample Filtering (added > 1,000)
        ↓
Exploratory Data Analysis (3 focused investigations)
        ↓
Business Insights & Strategic Recommendations
```

---

## Data Cleaning & Preparation

The raw dataset required several targeted cleaning steps before analysis:

- **Column Reduction:** Dropped 11 metadata and non-informative columns to streamline the feature space.
- **Missing Value Handling:**
  - Records missing `released` date were removed, as temporal context is required for cohort interpretation.
  - Missing engagement status counts (`beaten`, `dropped`, `playing`, `toplay`) were imputed with `0`, reflecting genuine absence of engagement rather than data error.
  - Missing ESRB rating entries were assigned an explicit `'Not Rated'` category to preserve those records.
- **Data Type Correction:**
  - Engagement status columns cast to `int` for mathematical consistency.
  - `released` field converted from string to `datetime` for future time-series compatibility.
- **Deep Copy Practice:** All transformations applied to a clean working copy (`df_clean`) to preserve the original dataset.

---

## Feature Engineering

Three behavioral rate metrics were derived from the raw status counts, using total library additions (`added`) as the denominator:

| Engineered Feature | Formula | Business Interpretation |
|---|---|---|
| `completion_rate` | `(beaten / added) × 100` | Percentage of owners who finished the game |
| `drop_rate` | `(dropped / added) × 100` | Percentage of owners who actively abandoned it |
| `engagement_rate` | `(playing / added) × 100` | Percentage of owners currently active |

All rate features were rounded to two decimal places. Division-by-zero edge cases (titles with zero additions) were resolved by filling resulting `NaN` values with `0`.

The analytical sample was then filtered to titles with `added > 1,000` to ensure statistically meaningful rate calculations.

---

## Exploratory Data Analysis

### Question 1: Do most players actually finish the games they buy?

**Visual: Distribution of Player Completion Rates**

![Distribution of Player Completion Rates](screenshots/chart1_completion_rate_distribution.png)

The completion rate distribution is sharply right-skewed. The overwhelming majority of titles in the high-volume cohort have completion rates clustered near zero, with the distribution's mass concentrated below 10%. Very few titles achieve completion rates above 30%. The mean completion rate is well below what studio development budgets implicitly assume when funding end-game content.

**Takeaway:** Most players do not finish the games they add to their libraries. The late-game experience — the most expensive content to produce — is reached by a small minority of the player base.

---

### Question 2: Does a higher rating prevent players from dropping a game?

**Visual: Drop Rate vs. Average Game Rating**

![Drop Rate vs. Average Game Rating](screenshots/chart2_drop_rate_vs_rating.png)

The scatter plot reveals no meaningful negative correlation between game rating and drop rate. Highly-rated titles (4.0–4.5 range) still exhibit drop rates of 15–40%, comparable to or exceeding those of lower-rated games. Critical acclaim does not act as a lifecycle retention mechanism.

**Takeaway:** High ratings drive acquisition (top-of-funnel), but they do not prevent player abandonment. Retention is governed by product design — specifically onboarding quality, early-game pacing, and core loop engagement — not by critical reception.

---

### Question 3: Which games are sustaining the highest active engagement?

**Visual: Top 10 Games by Current Engagement Rate**

![Top 10 Games by Current Engagement Rate](screenshots/chart3_top_engagement.png)

| Rank | Title | Engagement Rate |
|---|---|---|
| 1 | League of Legends | ~27% |
| 2 | Animal Crossing: New Horizons | ~21% |
| 3 | Super Smash Bros. Ultimate | ~18% |
| 4 | Tentacle Locker | ~17% |
| 5 | Hearthstone | ~16% |

**Takeaway:** The highest-engagement titles share common structural traits — live service mechanics, social or competitive loops, and accessible core gameplay with significant depth. These design patterns sustain active engagement far beyond the initial play session.

---

## Key Insights

**1. Ownership Volume Is a Vanity Metric**

Units sold and library additions are misleading as primary success indicators. On PC platforms in particular, aggressive discounting and bundling artificially inflate ownership figures, creating a false picture of an active player base. This leads to miscalculated projections for post-launch monetization.

**2. Systemic Misallocation of Development Capital**

The concentration of churn in the early hours exposes a structural problem in traditional development models. Heavily funding late-game content, elaborate final encounters, and end-game cinematics yields poor ROI when the vast majority of the player base abandons the game before reaching that content.

**3. Critical Acclaim Fuels Acquisition, Not Lifecycle Retention**

High critical ratings act as a powerful top-of-funnel acquisition lever. However, they fail to protect titles against steep user attrition. Long-term retention is governed strictly by product design — particularly early-game pacing, onboarding friction reduction, and core gameplay quality.

---

## Business Recommendations

**1. Reallocate Capital to the Critical Window (First 2–5 Hours)**

Front-load development budgets to maximize the quality of the early player experience. Polishing core mechanics, streamlining onboarding flows, and ensuring strong narrative hooks within the first five hours directly reduces immediate churn — improving the engagement baseline required for long-term monetization.

**2. Implement Agile Content Phasing**

Transition expansive narrative titles to a phased release model. Validate player retention in initial segments (e.g., Act 1) before committing full budgets to later acts. If early-stage churn rates exceed acceptable thresholds, resources can be reallocated or used to immediately address pacing issues.

**3. Realign Core Performance KPIs**

Modernize internal BI dashboards by retiring top-line metrics like "Total Units Sold" as primary health indicators. Stakeholders should instead track lifecycle metrics: **First-Act Completion Rate**, **Day-7 Retention**, and **Active Engagement Yield** to accurately assess product viability.

**4. Adopt Data-Driven DLC Scoping**

Post-launch expansion strategies must be scaled against the *completed* player base — not the installed base. If a title's completion rate falls below industry baselines, the scope of narrative expansions should be proportionally reduced to avoid wasting development cycles on a largely dormant audience.

---

## KPIs & Metrics

| KPI | Definition | Relevance |
|---|---|---|
| **Completion Rate** | % of owners who finished the game | Core product engagement health |
| **Drop Rate** | % of owners who actively abandoned the game | Early churn signal |
| **Engagement Rate** | % of owners currently playing | Live retention indicator |
| **Playing-to-Owned Ratio** | Active players relative to total library additions | True audience size vs. vanity count |
| **Rating vs. Retention Gap** | Divergence between critical score and lifecycle engagement | Validates/challenges review-as-retention assumption |

---

## Folder Structure

```
player-behavior-retention-analysis/
│
├── 01_data/
│   ├── raw/
│   │   └── rawg_api_data.csv
│   │
│   └── processed/
│       └── games_retention_cleaned.csv
│
├── 02_notebooks/
│   ├── data_cleaning_and_preparation.ipynb
│   └── player_behavior_eda.ipynb
│
├── 03_visuals/
│   ├── completion_rate_distribution.png
│   ├── drop_rate_vs_rating.png
│   └── top_engagement_titles.png
│
├── 04_reports/
│   ├── insights_and_findings.md
│   └── strategic_recommendations.md
│
├── 05_docs/
│   └── project_workflow.md
│
├── requirements.txt
├── README.md
└── .gitignore
```

---

## Screenshots

### Completion Rate Distribution
> The right-skewed histogram confirms that the overwhelming majority of titles have completion rates below 10%, with the mode concentrated near zero.

![Completion Rate Distribution](screenshots/chart1_completion_rate_distribution.png)

### Drop Rate vs. Game Rating
> The scatter plot demonstrates that even highly-rated titles (4.0+) exhibit drop rates of 15–40%, refuting the assumption that critical acclaim prevents player abandonment.

![Drop Rate vs. Rating](screenshots/chart2_drop_rate_vs_rating.png)

### Top 10 Games by Engagement Rate
> Live-service and socially-driven titles dominate current engagement, with League of Legends maintaining the highest active-player proportion at approximately 27%.

![Top Engagement](screenshots/chart3_top_engagement.png)

---

## Challenges & Solutions

| Challenge | Approach |
|---|---|
| Division-by-zero when computing rates for low-addition titles | Applied a `> 1,000 added` filter to establish a statistically meaningful high-volume cohort; NaN values from edge cases filled with 0 |
| Missing engagement status values misrepresenting true counts | Imputed with 0 to distinguish genuine absence of engagement from missing data |
| Metadata columns inflating dimensionality | Dropped 11 non-analytical columns during preprocessing to focus the feature space |
| Skewed rate distributions making trends hard to read | Used KDE overlays on histograms and adjusted bin sizes to reveal the distribution shape clearly |

---

## Future Improvements

- **Genre-Level Segmentation:** Break down completion and drop rates by game genre to identify whether churn patterns differ between RPGs, action-adventure, and casual titles.
- **Platform Comparison:** Analyze behavioral rate differences across platforms (PC, console, mobile) to test the hypothesis that PC players exhibit higher churn due to bundle/discount purchasing.
- **Time-Series Analysis:** Incorporate the `released` field to track how engagement and completion rates have shifted over release years, identifying industry-wide behavioral trends.
- **Cohort Modeling:** Build defined player cohorts (e.g., completers, droppers, backloggers) for deeper segmentation and targeted retention strategy development.
- **Power BI Dashboard:** Translate EDA findings into an interactive executive dashboard with slicers for genre, platform, and release year to support stakeholder decision-making.

---

## How to Run the Project Locally

**Prerequisites:** Python 3.8+, Jupyter Notebook

```bash
# Clone the repository
git clone https://github.com/<your-username>/player-retention-churn-analytics.git
cd player-retention-churn-analytics

# Install dependencies
pip install pandas numpy matplotlib seaborn jupyter

# Launch the notebook
jupyter notebook notebooks/01_player_retention_and_churn_eda.ipynb
```

> **Note:** The dataset (`raw_api_data.csv`) must be present at `data/raw_api_data.csv` for the notebook to run end-to-end. If sourcing fresh data from the RAWG API, ensure the response schema matches the expected column structure.

---

## Conclusion

This analysis demonstrates that the gaming industry's reliance on ownership volume as a primary health metric is analytically insufficient and commercially misleading. The data is clear: most players do not finish games, high ratings do not prevent abandonment, and the first few hours of gameplay are the most consequential window for determining a title's long-term commercial performance.

Studios that shift internal KPIs toward lifecycle metrics — completion rate, Day-7 retention, active engagement yield — and redirect development investment toward early-game polish will be structurally better positioned to reduce churn, improve post-launch monetization, and make rational, data-backed decisions about DLC scope and expansion investment.

---

## Author

**Naveen**
Aspiring Data Analyst | Python · SQL · Power BI

- GitHub: [github.com/your-username](https://github.com/NaveenKumar1822)
- LinkedIn: [linkedin.com/in/your-profile](https://www.linkedin.com/in/naveen-kumar-k-37a638405/)

---

*Built as part of a data analytics portfolio. Data sourced from the RAWG Gaming API. PixelForge Analytics is a fictional studio context used for business framing purposes.*
