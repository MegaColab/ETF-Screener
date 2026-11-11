# Gemini prompt: monthly refresh of data.csv

Paste everything below the line into a Gemini scheduled action. Schedule: daily at 9:00 am Sydney time (the prompt exits immediately unless work is due), or if Gemini allows it, every day from the 16th to the 22nd of each month.

---

You are updating the data file for my ASX ETF screener. Work only on the steps below. Do not guess, estimate or invent any number. Answer in numbered points.

PROJECT
- Site: https://megacolab.github.io/ETF-Screener/
- Repo: https://github.com/MegaColab/ETF-Screener , file `data.csv` in the repo root (branch main).
- Source: ASX Investment Products monthly Excel report, listed at https://www.asx.com.au/issuers/investment-products/asx-investment-products-monthly-report

1. WHEN TO RUN (use Sydney time)
   - Work out the report month = the previous calendar month (a run on 16 Nov 2026 needs "October 2026").
   - Open the current `data.csv` in the repo and read its first line: `# as_of=YYYY-MM-DD; source=...`.
   - If that as_of date is already in the report month, stop and reply: "Already up to date (as of <date>). Nothing to do."
   - If today is before the 16th, stop and reply: "Too early. The ASX usually posts between the 10th and 15th, so the next check is the 16th."
   Why the 16th: over the last 14 months the ASX posted each report between the 10th and 15th of the following month (Sydney time); two reports appeared later (3 Feb 2026 and 20 Oct 2026).

2. FIND THE FILE, WITH RETRIES
   a. Open the report page above and find the link for the report month under the Excel downloads. Links look like `https://www.asx.com.au/content/dam/asx/issuers/asx-investment-products-reports/<YEAR>/excel/asx-investment-products-<mon>-<YEAR>-abs.xlsx`. The month part is not consistent: it has been `jan`, `feb`, `mar`, `apr`, `may`, `jun`, `july`, `aug`, `sep`, `oct`, `nov`, `dec`. Always take the link from the page; if you must build one, try the 3-letter month, then the full month name, then `sept`.
   b. If the report month is missing from the page, or the link gives 404 or an error: wait 10 minutes and try again, up to 3 attempts in this run. Also try the direct URL variants above.
   c. If it is still not there: do not change anything. Reply: "ASX file for <month> not available yet (checked <time>, <n> attempts). I will check again tomorrow." The daily schedule is the retry mechanism. If it is still missing after the 22nd, reply "ALERT: ASX file for <month> still missing on the 22nd. Please check manually." and list what you tried.
   d. If the Excel opens but cannot be read, use the PDF report for the same month (same folder, `/pdf/asx-investment-products-<mon>-<YEAR>.pdf`, table "Spotlight ETPs") and mark the result "lower confidence (PDF)". If neither works, stop and report.

3. READ THE DATA (use code execution, for example Python with openpyxl or pandas; never retype from memory)
   - Sheet: "Spotlight ETP List". Find the header row where column B reads "ASX Code".
   - Columns needed (match by header text, not letter): ASX Code, Type, Fund Name, MER (% p.a), FUM ($m) (the plain one, NOT "FUM ($m) Change" and NOT "CHESS FUM"), 1 Year Total Return, Historical Distribution Yield.
   - Category: rows where only column B has text (for example "Equity - Australia", "Fixed Income - Global") are category headings. They apply to every product row below until the next heading.
   - As-of date: the sheet title area contains "Period ending: <weekday>, <d Month YYYY>". Convert it to YYYY-MM-DD.
   - Product rows have a code matching ^[A-Z0-9]{2,6}$ and a Type value.

4. BUILD THE CSV
   - Line 1: `# as_of=<YYYY-MM-DD>; source=ASX Investment Products Report - <Month YYYY>`
   - Line 2: `code,type,name,category,fum_m,mer,ret1y,yield`
   - Keep only products with FUM ($m) of 100 or more. Sort by fum_m, largest first.
   - fum_m: whole number. mer: as given (a percent, for example 0.07), 2 decimals. ret1y and yield: the ASX decimal x 100, rounded to 2 decimals (0.0393 becomes 3.93). Write "n/a" or empty values as an empty field.
   - Collapse repeated spaces in names. Wrap a field in double quotes if it contains a comma.
   - Example row: `VAS,ETF,Vanguard Australian Shares Index ETF,Equity - Australia,26993,0.07,3.93,3.13`

5. VALIDATE (all must pass; otherwise do NOT update the repo, and report which check failed)
   - Between 230 and 330 data rows (the October 2026 file had 278).
   - No duplicate codes; every row has all 8 fields.
   - Sorted by fum_m descending.
   - VAS, VGS, IVV, A200 and IOZ are present, each with fum_m above 5,000.
   - The new as_of date is later than the current file's as_of date.
   - Compared with the current `data.csv`: the row count changed by no more than 15%. List (do not fail on) any product whose fum_m moved by more than 40%, and any ret1y outside -90 to +300.

6. PUBLISH
   - Preferred: commit the new `data.csv` to MegaColab/ETF-Screener on main with the message "Update ASX ETF data to <Month YYYY>". Change no other file.
   - If you cannot write to GitHub: reply with the complete CSV in a single code block and nothing else inside it, so I can paste it into the file.

7. REPORT (always end with this)
   1) Result: updated / already up to date / not available / failed validation.
   2) As-of date, number of products, source file used (Excel or PDF) and the number of attempts.
   3) New tickers and removed tickers versus the previous file.
   4) The 5 largest AUM changes.
   5) Validation results and anything flagged.
