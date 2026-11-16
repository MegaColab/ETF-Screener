# ASX ETF Screener

Live site: **https://megacolab.github.io/ETF-Screener/**

A static screener for ASX-listed ETFs/ETPs with AUM of A$100M or more. Filter by search (ticker or name), minimum AUM, maximum MER, minimum yield, category and product type. Each ticker links to its TradingView symbol page.

Data source: the ASX Investment Products monthly Excel report (sheet "Spotlight ETP List"). Fields: ticker, type, name, ASX category, AUM (A$m), MER, 1Y total return, distribution yield.

## Files

- `index.html`: the whole app (HTML, CSS, JS, no dependencies).
- `data.csv`: the data. Line 1 is a comment with the as-of date and source.
- `docs/gemini-monthly-update-prompt.md`: prompt for a monthly automated refresh.
- Branch `picks`, file `chosen.json`: your shortlist (the Chosen tab). It lives on its own branch so saving a pick never triggers a site rebuild, and a monthly data update never touches it.

## The Chosen shortlist

- Click the ☆ at the left of any ETF to add it to your shortlist; the **★ Chosen** tab lists your picks with a note and the date added.
- The list is stored in this repo (branch `picks`), so it is the same on every device. It is public: anyone with the site link can view it, and **Copy link** gives a direct link to the Chosen tab.
- Chosen ignores the filters. If a pick later drops out of the data set (under A$100M or delisted), it stays in the list with its last-known numbers and a "No longer in the data set" tag.

## Editing the Chosen list

Everyone can view the list; only you can change it, using a GitHub token saved in that browser. Set up once per device:

1. Go to https://github.com/settings/personal-access-tokens/new (Settings, Developer settings, Personal access tokens, Fine-grained tokens, Generate new token).
2. Token name: `ETF screener picks`. Expiration: your choice (you will need a new token when it expires).
3. Resource owner: **MegaColab**. Repository access: **Only select repositories**, then choose **ETF-Screener**.
4. Repository permissions: **Contents: Read and write** (Metadata read-only is added automatically). Generate the token and copy it (it starts with `github_pat_`).
5. Open the site, click **Unlock editing**, paste the token, click **Unlock**.

Notes: the token is stored only in that browser's local storage and is sent only to api.github.com. It can change this repo's files, so treat it like a password, use it only on your own devices, and revoke it any time from the same GitHub settings page. Click **Editing on** to lock a device again.

## Open it

Go to https://megacolab.github.io/ETF-Screener/ . Opening `index.html` by double-clicking will not load the data (browsers block local file reads); use the site, or run `python3 -m http.server` in this folder and open http://localhost:8000.

## Update the data (monthly)

Any one of these:

1. Ask Claude: "update". It pulls the latest ASX report and commits the new `data.csv`.
2. Use the Gemini prompt in `docs/`.
3. Manually: download the latest Excel from the [ASX monthly reports page](https://www.asx.com.au/issuers/investment-products/asx-investment-products-monthly-report), open the site, expand "Update the data", choose the .xlsx, click "Copy data.csv", then on GitHub open `data.csv`, Edit, paste over everything, Commit.

The ASX posts each report around the 10th-15th of the following month.

For information only, not financial advice.
