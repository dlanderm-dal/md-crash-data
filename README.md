# Maryland crash data

R analysis of Maryland police crash reports (2015–2026) and national FARS traffic deaths, for a Capital News Service story. Everything runs from one R Markdown file, `crash_data_processing_lean.Rmd`, which reads the raw data from the folders below and writes every result into `output/`.

## What's in this repo

| Path | What it is |
|---|---|
| `crash_data_processing_lean.Rmd` | The analysis. Run it top to bottom. |
| `2015-2018/` | 2015–2018 crash data, sent directly by the Maryland Department of State Police (MDSP). Included in the repo. |
| `FARS/FARS national.csv` | State traffic deaths, 2023 and 2024, from NHTSA's Fatality Analysis Reporting System. Included in the repo. |
| `output/` | Every CSV the Rmd writes, including the `flourish_N_…csv` files uploaded to Flourish. |

The 2019–2026 crash data is **not** in the repo (the files are too large for GitHub). Download it with the steps below and put it in the folders shown, with the exact file names shown, so the Rmd finds it.

## Folder layout the Rmd expects

```
md crash data/
├── crash_data_processing_lean.Rmd
├── 2015-2018/                         (in the repo)
│   ├── Crash_2015date and time.xlsx
│   ├── Crash_2016date and time.xlsx
│   ├── Crash_2017date and time.xlsx
│   └── Crash_2018date and time.xlsx
├── 2019-2023 dashboard/               (download, step 1)
│   └── 2019 CrashMap_REPORT_data.csv      ← one file holding 2019 through 2023
│       (plus the other tabs: CIRCUMSTANCES, DRIVER, NONMOTORIST, PASSENGER, VEHICLE)
├── 2024-2026_dashboard/               (download, step 2)
│   ├── 2024 Crash Map_Reports_data.csv
│   ├── 2024 Crash Map_Contributing Circumstances_data.csv
│   ├── 2024 Crash Map_Occupants_data.csv
│   ├── 2024 Crash Map_NonMotorists_data.csv
│   ├── 2024 Crash Map_Vehicles_data.csv
│   ├── 2025 Crash Map_… (same five tabs)
│   └── 2026 latest download/
│       └── 2026 Crash Map_… (same five tabs)
├── FARS/                              (in the repo)
│   └── FARS national.csv
└── output/                            (created by the Rmd)
```

### Naming convention

Each download keeps the name the dashboard gives it, with the **year added to the front and a space**:

- 2019–2023 dashboard: `2019 CrashMap_<TAB>_data.csv`. The prefix is `2019` even though the file covers 2019–2023.
- 2024–2026 dashboard: `<YEAR> Crash Map_<Tab>_data.csv`, one set of files per year.

The 2026 files go in a subfolder called `2026 latest download/`, because 2026 is still being reported and gets re-downloaded. To refresh it, rename the old folder (e.g. `2026 download Oct 6`) to keep it, make a new `2026 latest download/` folder with the new files, and note the new download date in Part 1 of the Rmd. The Rmd always reads `2026 latest download/`, so its import lines don't change.

> The downloaded `.csv` files are actually **tab-separated, UTF-16** text (that's how the dashboard exports them). The Rmd reads them with `read_tsv(..., locale = locale(encoding = "UTF-16LE"))`; don't re-save them in Excel.

## Step 1: 2019–2023 crash data

Source: MDSP public crash data dashboard,
https://analytics.mdsp.org/t/Public/views/PublicCrashData/DownloadCrashData?%3Aembed=y&%3AisGuestRedirectFromVizportal=y

1. Open the page and enter the date fields **1/1/2019** to **12/31/2023**.
2. Wait for the page to finish rendering.
3. Click once on the map. This activates the download button in the lower right corner.
4. Click the download button and choose the format **Data**.
5. A popup window opens with tabs of data (Summary, Circumstances, Driver, …). For each tab:
   1. Click **Show Fields**.
   2. Click **(All)** to select every field.
   3. Click **Download**.
6. Rename each file with the `2019 ` prefix (see the naming convention above) and put it in `2019-2023 dashboard/`.

## Step 2: 2024–2026 crash data

Source: MDSP public crash dashboard (ACRS 2.0),
https://analytics.mdsp.org/t/Public/views/PublicCrash2/PublicCrash?%3Aembed=y&%3AisGuestRedirectFromVizportal=y

Same steps as step 1, but **one year at a time**:

1. Enter the date fields for a single year (1/1/2024 to 12/31/2024, then 2025, then 2026 to date).
2. Wait for rendering, click once on the map, click the download button in the lower right, choose **Data**.
3. For each tab, click **Show Fields** → **(All)** → **Download**.
4. Rename each file with that year's prefix (e.g. `2024 Crash Map_Reports_data.csv`) and put it in `2024-2026_dashboard/`. The 2026 files go in the `2026 latest download/` subfolder (see the naming convention above).

The Rmd uses five tabs per year: **Reports, Contributing Circumstances, Occupants, NonMotorists, Vehicles**. The other tabs can be downloaded too, but aren't read.

Downloaded: 2024–2026 on Oct. 6, 2026.

## FARS national data (included)

`FARS/FARS national.csv` is in the repo, so there's nothing to download. It has traffic deaths by state for 2023 and 2024 from NHTSA's Fatality Analysis Reporting System. Columns: `State, 2024, 2023, Percent Change`, one row per state plus D.C. and a `USA` row.

Source: NHTSA's FARS Encyclopedia, table "2024 Traffic Fatalities by STATE and Percent Change from 2023,"
https://www-fars.nhtsa.dot.gov/States/StatesCrashesAndAllVictims.aspx

To re-create it:

1. Start at https://www.nhtsa.gov/research-data/fatality-analysis-reporting-system-fars and click **Run a Query Using the FARS Web-Based Encyclopedia**.
2. In the top menu, click **States**. It opens on **Crashes and All Victims**.
3. The first table on that page is "2024 Traffic Fatalities by STATE and Percent Change from 2023" (STATE: USA, YEAR: 2024). Use the **OUTPUT OPTIONS** export button just above it.
4. Save it as a CSV named `FARS national.csv` in `FARS/`. Keep the columns `State, 2024, 2023, Percent Change`, and keep the `USA` row at the bottom.

## Census API key (for Part 24)

The FARS rates use state populations from the Census Bureau's American Community Survey, pulled with the `tidycensus` R package. That needs a free Census API key:

1. Request one at https://api.census.gov/data/key_signup.html.
2. In R, run `tidycensus::census_api_key("YOUR KEY", install = TRUE)` once. It saves the key in your `~/.Renviron` file, outside this project. Never commit the key.

## Running it

1. Install the packages listed in the Rmd's first chunk (`tidyverse`, `rio`, `lubridate`, `janitor`, `openxlsx`, `tidycensus`).
2. Open `crash_data_processing_lean.Rmd` in RStudio and run all chunks (or Knit).
3. Results land in `output/`. The files to upload to Flourish are the `output/flourish_N_…csv` ones.

## Establish a year-to-date benchmark

2026 is a partial year, so comparing it with full years of 2024 and 2025 would make 2026 look artificially low. Instead, the year-to-date (YTD) tables compare the **same stretch of the calendar in every year**: Jan. 1 through a cutoff date.

**Where it's set.** Part 3 of the Rmd sets the cutoff with two numbers:

```r
ytd_month <- 9
ytd_day   <- 30
```

Change those two lines and every year-to-date comparison in the file moves with them. Part 3 also builds a readable label (`ytd_label`, e.g. "Sep 30") for chart titles. It prints a warning if the cutoff falls after the last day in the data.

**How the cutoff was chosen.** For this reporting, the cutoff is the **end of the last full month before the download**. The data was downloaded Oct. 6, 2026, so the cutoff is Sept. 30. The most recent days are always incomplete, because police reports are approved and submitted to ACRS days to weeks after a crash. In this data, daily counts dropped off sharply in the final days before the download. 2026's year-to-date fatal crash count also rose between two downloads taken a few weeks apart. Stopping at the end of the previous month leaves time for most of those reports to arrive. After a new download, move the cutoff to the end of the month before it.

**What uses it.** Every table filtered with `crash_date <= make_date(year, ytd_month, ytd_day)`:

- **Part 4:** fatal and total crashes, Jan. 1 to the cutoff, every year since 2015.
- **Part 11:** crashes by severity, year to date (`severity_ytd`).
- **Part 14:** the 2025 vs. 2026 summary of people killed, fatal crashes and total crashes.
- **Part 16:** fatal crashes by county and cause, year to date (`causes_by_county_ytd`). This feeds the statewide cause table (Flourish graphic 5), each big county's leading cause (graphic 4) and the county tables.

The running-total charts (Flourish graphics 1 and 2) aren't cut off this way. They show every day of each year, and the 2026 line simply stops at the last day in the data.
