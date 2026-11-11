# ASX ETF Screener

Live site: **https://megacolab.github.io/ETF-Screener/**

A static screener for ASX-listed ETFs/ETPs with AUM of A$100M or more. Filter by search (ticker or name), minimum AUM, maximum MER, minimum yield, category and product type. Each ticker links to its TradingView symbol page.

Data source: the ASX Investment Products monthly Excel report (sheet "Spotlight ETP List"). Fields: ticker, type, name, ASX category, AUM (A$m), MER, 1Y total return, distribution yield.

## Files

- `index.html`: the whole app (HTML, CSS, JS, no dependencies).
- `data.csv`: the data. Line 1 is a comment with the as-of date and source.
- `docs/gemini-monthly-update-prompt.md`: prompt for a monthly automated refresh.

## Open it

Go to https://megacolab.github.io/ETF-Screener/ . Opening `index.html` by double-clicking will not load the data (browsers block local file reads); use the site, or run `python3 -m http.server` in this folder and open http://localhost:8000.

## Update the data (monthly)

Any one of these:

1. Ask Claude: "update". It pulls the latest ASX report and commits the new `data.csv`.
2. Use the Gemini prompt in `docs/`.
3. Manually: download the latest Excel from the [ASX monthly reports page](https://www.asx.com.au/issuers/investment-products/asx-investment-products-monthly-report), open the site, expand "Update the data", choose the .xlsx, click "Copy data.csv", then on GitHub open `data.csv`, Edit, paste over everything, Commit.

The ASX posts each report around the 10th-15th of the following month.

For information only, not financial advice.
