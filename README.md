# NBA-Analytics-Project

# NBA Award Analytics Engine

A custom, percentile-based analytics platform that calculates "deserved" NBA awards across six
full seasons (2019-20 through 2024-25) — built end to end: PostgreSQL database design, a SQL-only
percentile and composite-scoring engine, seven Power BI dashboards, and a full methodology
write-up.

This is a data analytics portfolio project. The goal wasn't to finish quickly — it was to
demonstrate the full analytics pipeline: database engineering, statistical methodology, BI
visualization, and the engineering judgment to redesign or cut anything that produced
indefensible results.

---

## What's in this repo

| File | Contents |
|---|---|
| `NBA_Data_Project.sql` | Schema-only export of the PostgreSQL database — every table and view definition (no data) |
| `NBA_DATA_PROJECT_DASHBOARDS.pbix` | All seven Power BI dashboards |
| `NBA_Data_Project_Methodology-Report.Rmd` / `NBA_Data_Project_Methodology-Report.pdf` | The full methodology and decision report |
| `nba_analytics_deck.pptx` / `nba_analytics_deck.pdf` | A short stage-by-stage walkthrough deck |
| `Final_Award_Set.docx` / `.pdf` | The final award list, award teams, and cut-award reasoning |
| `Formula_Reference_Final.docx` / `.pdf` | Every formula used in the project, in full |
| `player_season.png`, `team_season.png`, `season_awards.png`, `player_comparison.png`, `team_comparison.png`, `player_progression.png`, `team_progression.png` | Dashboard screenshots, referenced below |

---

## Project Summary

The project builds a fully custom award framework on top of six seasons of Basketball Reference
data: every player and team is scored on a set of composite metrics (Offensive Impact, Defensive
Impact, Scoring Efficiency, Playmaking, Player Value Score at the player level; Team Offense,
Team Defense, Team Success Score at the team level), all built from percentile-ranked raw stats
combined through weighted addition. Those composite scores feed directly into award formulas for
MVP, DPOY, OPOY, and a set of fictional awards (Best Player, Best Scorer, Best Playmaker, and
four team-level fictional awards).

Several awards that were part of the original plan — MIP, 6MOY, ROY, COY, All-Rookie Teams,
and All-Defense Teams — were evaluated during development and ultimately cut, because they
either couldn't produce statistically defensible results or were mathematically redundant with
an award that was kept. Full reasoning for every cut is in
[`Final_Award_Set.pdf`](./Final_Award_Set.pdf).

Every formula — foundation scores, composite scores, award formulas, and the percentile engine
itself — is documented in full in
[`Formula_Reference_Final.pdf`](./Formula_Reference_Final.pdf).

For the full reasoning behind the methodology, the data-quality issues found and fixed along the
way, and the validation checks that confirm the calculated outputs match real basketball
reality, see [`NBA_Data_Project_Methodology-Report.pdf`](./NBA_Data_Project_Methodology-Report.pdf).

---

## Tech Stack

- **Database:** PostgreSQL 18, pgAdmin
- **Data source:** Basketball Reference, cleaned in Excel and staged via CSV import
- **Calculation engine:** SQL only — every composite score and award formula lives in a
  PostgreSQL view, not in Power BI's DAX layer or in a separate scripting language
- **Visualization:** Power BI Desktop (Import mode)
- **Methodology report:** R / R Markdown (`rmarkdown`, `knitr`)

---

## Methodology, In Brief

- **Percentile-based scoring** — every raw stat is converted to a percentile rank *within its
  own season* before it's used in any composite score, so players are measured against their
  actual contemporaries rather than a fixed historical baseline.
- **Weighted addition, not multiplication** — composite scores sum proportional contributions
  from each input. An early multiplicative version of Player Value Score collapsed almost every
  player's score toward zero; switching to weighted addition fixed it.
- **Minutes and games floors** — a 1,000-minute threshold (600 in shortened seasons) keeps
  small-sample rate stats from producing winners that aren't defensible.
- **65-game eligibility rule** — mirrors the real NBA's own award-eligibility policy, in effect
  from the 2023-24 season onward.

Full reasoning, worked examples, and the real bugs found along the way are in the methodology
report.

---

## Dashboards

Seven Power BI dashboards, covering player and team grain across season, comparison, and
progression views.

| | |
|---|---|
| ![Player Season](./Dashboard%20Screenshots/player_season.png) **Player Season** | ![Team Season](./Dashboard%20Screenshots/team_season.png) **Team Season** |
| ![Season Awards](./Dashboard%20Screenshots/season_awards.png) **Season Awards** | ![Player Comparison](./Dashboard%20Screenshots/player_comparison.png) **Player Comparison** |
| ![Team Comparison](./Dashboard%20Screenshots/team_comparison.png) **Team Comparison** | ![Player Progression](./Dashboard%20Screenshots/player_progression.png) **Player Progression** |
| ![Team Progression](./Dashboard%20Screenshots/team_progression.png) **Team Progression** | |

---

## Setup / Reproducing This Project

This repo includes the **schema only** — no data is redistributed here (see *Data Source &
License Note* below). To reproduce the project end to end:

1. **Database** — install PostgreSQL 18 and run `NBA_Data_Project.sql` against a new database to
   recreate every table and view (empty).
2. **Data** — source your own copy of Basketball Reference player and team stats for the
   2019-20 through 2024-25 seasons, clean and stage it to match the schema, and import it.
3. **Power BI** — open `NBA_DATA_PROJECT_DASHBOARDS.pbix` in Power BI Desktop and point
   the data source at your own PostgreSQL instance.
4. **Methodology report** — open `NBA_Data_Project_Methodology-Report.Rmd` in RStudio. It expects a `data/` folder of
   CSV exports from the award and composite-score views (see the R Markdown file's own code
   chunks for exact filenames), then knits to PDF with `rmarkdown`/`knitr` (requires `tinytex`
   for the LaTeX toolchain).

### Exporting `schema.sql` yourself (pgAdmin)

Right-click the `nba_project` database → **Backup...** → set Format to **Plain**, and look for
a schema-only option (labeled **Only schema**, or under a **Dump Options** tab, depending on
your pgAdmin version) → run the backup → the output file is your `NBA_Data_Project.sql`.

---

## Data Source & License Note

Player and team statistics originate from [Basketball Reference](https://www.basketball-reference.com/).
The raw dataset is not redistributed in this repository — only the database schema (table and
view structure) is included. This keeps the repo small and avoids redistributing bulk scraped
data beyond what the source site's terms permit. Anyone reproducing this project should source
their own copy of the underlying stats.

All original analysis, SQL, scoring formulas, dashboards, and written content in this repository
are released under the MIT License unless noted otherwise.

---

## Author

Built by Vaughn Edwards Jr as a data analytics portfolio project — database design, SQL engineering,
statistical methodology, and BI dashboard development, end to end.
