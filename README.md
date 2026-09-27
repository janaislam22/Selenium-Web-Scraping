# Naukrigulf Job Scraper — Data Engineer Listings

A Selenium-based web scraper that collects "Data Engineer" job listings from [Naukrigulf](https://www.naukrigulf.com/data-engineer-jobs) and exports them to a structured CSV file.

## 📌 Objective

Scrape real job listings from a live job portal using Selenium with Python, and store the extracted data in a structured CSV file.

## 🎯 Task

The script collects all "Data Engineer" job listings from the **first three pages** of Naukrigulf search results, navigating dynamically between pages and extracting detailed information for every listing found.

## 📊 Data Extracted

For each job listing:

| Field | Description |
|---|---|
| Job Title | Title of the position |
| Company Name | Hiring company |
| Job Location | City/country of the role |
| Required Experience | Years of experience required |
| Job Description | Short description snippet |

## ⚙️ How It Works

1. **Launch & navigate** — Opens Chrome via Selenium (with anti-detection options) and loads the Naukrigulf Data Engineer search page.
2. **Locate job cards** — Each listing on the page is a `div.ng-box.srp-tuple` element; the script grabs all of them per page (not just the first one).
3. **Extract fields per card** — For every card, it reads:
   - `a.info-position` → job title (and company name, when the site combines both in the same element)
   - `a.info-org` → company name (when listed separately)
   - `li.info-loc` → location
   - `li.info-exp` → required experience
   - `p.description` → job description
   - Each extraction is wrapped in `try/except` so a missing field on one card doesn't crash the whole run.
4. **Paginate** — Clicks the "Next" button to move to the following page, up to page 3.
5. **Export** — Saves all collected rows into `data_engineer_jobs.csv` using pandas.

## 🛠️ Tech Stack

- **Python**
- **Selenium** — browser automation & scraping
- **pandas** — data structuring & CSV export

## 📂 Files

```
├── app.ipynb                  # scraping script (notebook)
├── data_engineer_jobs.csv     # scraped output
└── README.md
```

## ▶️ How to Run

1. Install dependencies
   ```bash
   pip install selenium pandas
   ```
2. Make sure ChromeDriver matches your installed Chrome version
3. Run the notebook / script — it will open a Chrome window, scrape pages 1–3, and save the results to `data_engineer_jobs.csv`

## 🧠 Notes / Lessons Learned

- Early versions used a fixed-index XPath (e.g. `div[1]`) which only ever captured the **first** job card on the page. Switching to a class-based selector (`div.ng-box.srp-tuple`) and looping over every match fixed this.
- Storing `find_elements(...)` results directly (without `.text`) saved raw WebElement objects instead of readable text — fixed by explicitly reading `.text` from each field.
- On some cards, the company name isn't in its own element — it's on a second line inside the job title element. The script falls back to splitting that text when `a.info-org` isn't found.
- The page increment (`page += 1`) must sit **inside** the `while` loop — placing it outside caused an infinite loop that kept re-scraping the same page.
