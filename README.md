# Google Maps Data Extractor: Can You Beat the 120-Result Cap, Are Reviews Included, and What Does 1,000 Leads Really Cost? (Plan Comparison + Self-Hosted Alternative)

ScraperAPI runs a dedicated Google Maps endpoint at `api.scraperapi.com/structured/google/mapssearch`. It takes a search query plus a latitude and longitude and returns business names, addresses, review counts, star ratings, website URLs and opening times as JSON or CSV. There is an async version that accepts a batch of queries with a webhook callback, and a no-code DataPipeline for scheduled projects.

Pricing is credit-based, and one Google request costs 25 credits, so a 100,000-credit plan buys roughly 4,000 Google queries, not 100,000. That single number decides most plan choices, and it is the first thing the pages ranking for this keyword leave out.

> **Most readers should start here:** Hobby (100,000 credits, 20 concurrent threads) if you are testing a lead list or running a handful of city scans; Business or above if you need country-level geotargeting, unlimited analytics history, or a volume that survives the 25-credit multiplier. The Google Maps page advertises 5,000 API credits on signup with a 7-day trial, which is enough to price a real query before you commit.
> 👉 [Compare the current Hobby and Business plans](https://www.scraperapi.com/pricing?fp_ref=coupons)

## All ScraperAPI plans at a glance

Every tier is billed monthly with a 10% discount for annual billing; Enterprise gets custom terms. Credits do not roll over on any plan, which matters more than it sounds when each Google request eats 25 of them.

| Plan | API credits | Concurrent threads | Geotargeting | Billing notes | Price |
| --- | --- | --- | --- | --- | --- |
| Hobby | 100,000 | 20 | US & EU | No pay-as-you-go; auto-upgrade opt-in | [See Hobby pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | US & EU | No pay-as-you-go; auto-upgrade opt-in | [See Startup pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 100 | Country-level | No pay-as-you-go; auto-upgrade opt-in | [See Business pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | Country-level | Pay-as-you-go available; auto-upgrade opt-in | [See Scaling pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | Country-level | Pay-as-you-go available; auto-upgrade opt-in | [See Professional pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | Country-level | Pay-as-you-go available; no auto-upgrade | [See Advanced pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Country-level | Pay-as-you-go; custom annual terms | [Request Enterprise pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Two differences between tiers are easy to miss. Analytics history is capped at the last 30 days on Hobby and Startup and unlimited from Business up. Pay-as-you-go overage exists only from Scaling upward, and automatic plan upgrades are opt-in on Hobby through Professional but unavailable on Advanced and Enterprise. On Hobby, running out of credits mid-month pauses scraping until renewal unless you turned auto-upgrade on.

## What a Google Maps scrape actually costs in credits

ScraperAPI bills per request, and the per-request rate depends on the target. A standard page is 1 credit. Amazon, Walmart and eBay are 5. Google and Bing are 25, including subdomains. LinkedIn is 30. Bypassing bot protection, forcing JavaScript rendering, and using premium proxies each add 10 credits, and premium proxies combined with rendering come to 25. Ultra premium is 30, or 75 with rendering. Geotargeting, sessions, device type, output format and auto parsing are free add-ons.

Budget against the 25-credit Google rate until the estimator tells you otherwise:

- 100,000 credits (Hobby) ≈ 4,000 Google requests
- 1,000,000 credits (Startup) ≈ 40,000 Google requests
- 3,000,000 credits (Business) ≈ 120,000 Google requests

If a scrape needs rendering stacked on top, the per-request cost rises and those counts fall accordingly. Every response carries an `sa-credit-cost` header showing what that call consumed, and the Domain Cost Estimator in the dashboard prices any URL before you run it. You can also set `max_cost` per scrape so a single request cannot exceed a ceiling you choose.

This is why "how many leads per month" is the wrong way to size a plan. A city scan that fans out to 40 coordinate points costs 40 requests. Twenty cities with ten query variants each costs 8,000 requests, or 200,000 credits, which already exceeds Hobby before you have pulled a single review.

👉 [Price one Maps query with the trial credits before picking a tier](https://www.scraperapi.com/signup?fp_ref=coupons)

## Four things that break Google Maps extraction, whichever tool you use

### 1. The 120-result ceiling comes from how you search, not from the scraper

Google's own map view shows no more than about 120 places per map screen with finite scroll. Any tool that submits a plain search term or a Google Maps URL and stops there inherits that ceiling. Apify's documentation for the compass-built Google Maps Extractor states it outright: skipping the location input limits results to a maximum of 120 and is only worth doing when you need low latency or Google's original ordering.

ScraperAPI's Maps endpoint takes a coordinate pair per request, so the lever you control is how many points you feed it. Denser coordinates across a metro area mean more businesses found; one query for "restaurants in Chicago" means one result set. The same logic drives the grid-based actors on the market, which split a viewport into cells to avoid the blind spots a single search leaves behind.

### 2. Category filters silently delete valid businesses

Google carries thousands of categories and many are near-duplicates. `Divorce lawyer`, `Divorce service` and `Divorce attorney` are three distinct categories, and a firm may sit in only one of them. Filter by a single term and the rest vanish. Apify's own guidance suggests listing every synonym, and for some use cases selecting as many as 100 categories.

With a keyword-driven endpoint like ScraperAPI's, the equivalent trap is assuming one query covers a vertical. "Coffee shop" and "cafe" and "espresso bar" return overlapping but different sets.

### 3. Reviews, photos and emails are not standard features

Field lists differ sharply between products that all call themselves Google Maps extractors. ScraperAPI's page advertises reviews, photos and opening times in the response. Apify's compass actor explicitly excludes images and reviews and points users to a different, larger scraper for those. The open-source gosom tool returns 36 fields including images and reviews, but extended reviews (up to roughly 300) need the `-extra-reviews` flag and emails need `-email`.

Decide your must-have fields first, then check the tool. Comparing total field counts tells you nothing about whether your three required columns exist.

### 4. Some fields are missing because Google hides them from logged-out visitors

Image categories, "people also search for", popular times (histogram, live text, live percentage), web results, customer updates and most of the additional info block are shown mostly to signed-in Google users. Scrapers that do not log in will return those fields empty or absent, and no amount of retrying fixes it. If your local SEO report depends on popular times, plan around the gap rather than discovering it in the CSV.

👉 [Check the exact fields the Google Maps endpoint returns](https://www.scraperapi.com/solutions/structured-data/google-maps-scraper-api?fp_ref=coupons)

## How the ScraperAPI Maps endpoint works

The synchronous call is a GET to `https://api.scraperapi.com/structured/google/mapssearch` with `api_key`, `query`, `latitude` and `longitude`. The async version at `async.scraperapi.com/structured/google/mapssearch` accepts either a single job or a `jobs` array of query and coordinate pairs, each with an optional webhook callback, and returns a status URL per job. Both return JSON or CSV.

For teams that would rather not maintain a scheduling script, DataPipeline offers a visual builder for complete Maps projects, with dedicated endpoints and monitoring for up to 10,000 queries per project. That covers the gap between a one-off CSV and a production ETL job.

👉 [See how DataPipeline handles scheduled Maps runs](https://www.scraperapi.com/solutions/data-pipeline?fp_ref=coupons)

## How the alternatives compare

Four options show up repeatedly in this category. Prices and limits below come from published documentation; where a vendor does not publish a figure, none is listed.

| Tool | Model | Published pricing | Fields and limits |
| --- | --- | --- | --- |
| Google Maps Extractor (Apify, published by compass) | Hosted actor, pay-per-event | $5.00 per 1,000 places on Free, $4.00 Starter, $3.00 Scale, $2.10 Business; category filter adds $1.00 / $1.00 / $0.75 / $0.55 per 1,000; detail page $0.002 per place; $5 free credit covers about 1,000 places | 30+ fields including place ID, hours, coordinates and contact enrichment; explicitly no images or reviews; skipping the location field caps results at 120 |
| Google Maps Extractor (Apify, published by scrapapi) | Hosted actor | Not published in the material reviewed | Viewport grids, proxy escalation from direct to datacenter to residential, dedupe by `place_id`, optional review capture, `maxResults` defaults to 20 across all searches |
| gosom/google-maps-scraper | MIT-licensed, self-hosted CLI, web UI and REST API | Software free; you pay for proxies and compute | ~120 places per minute, 36 fields, `-email` for emails, `-extra-reviews` for extended reviews, Fast Mode capped at 21 results per query, SOCKS5/HTTP/HTTPS proxy rotation |
| praveenMadanayake/Google-Maps-Scraper | Python and Playwright script | Free (MIT) | Name, address, phone and reviews written into a Google Sheet; requires a Google Cloud project with Sheets API and OAuth2 setup |

The open-source route deserves a caveat. MIT licensing removes the software fee, not the operating cost. Google Maps scraping at any volume needs rotating residential proxies, and that bandwidth bill is usually what decides whether self-hosting is cheaper than a credit plan. You also own the failure handling, the browser pool and the deduplication logic.

## Which option fits your situation

**Validating a list before committing budget.** Use the trial credits and run a handful of city and vertical combinations. You will learn the real credit cost per useful row within an hour.

**Recurring lead lists with reviews and photos.** ScraperAPI's endpoint returns those fields directly; check the trial output against your CRM schema before choosing a tier, then size the plan on requests per month times 25.

**More than 10,000 queries on a schedule, no code.** DataPipeline is the relevant product here, not the raw endpoint.

**Full control and no per-row fee.** The gosom scraper is the strongest open option, with 36 fields and roughly 120 places per minute. Budget for proxies and for the engineer who maintains it.

**A single small market, run once.** A Playwright script writing into Google Sheets is enough, and the setup cost is one OAuth consent screen.

## Compliance

All of these tools read publicly visible Google Maps data. That does not settle the legal question. ScraperAPI's and Apify's documentation both point users at Google's terms of service, and any business data you store about sole traders or individuals can fall under GDPR or CCPA. Keep to public listings, avoid account-gated data, and set a retention period for the CSVs.

## FAQ

**How many businesses can I get from one query?**
One request returns one result set for one coordinate pair. To exceed the ~120-place map ceiling, fan out across multiple coordinates within the area you care about.

**Can I scrape reviews and photos?**
ScraperAPI advertises reviews, photos and opening times in the Maps endpoint response. Apify's compass actor does not return either. The gosom scraper returns images by default and extended reviews with a flag.

**Do I need to write code?**
A single request is a GET with four parameters, so basic scripting is enough for most pipelines. DataPipeline removes code entirely for scheduled projects.

**How much are the free credits worth?**
The Google Maps page advertises 5,000 API credits with a 7-day trial at signup. Free-tier terms have changed before, so confirm the figure on the signup screen before designing a test around it.

**What happens when I get blocked?**
ScraperAPI handles proxy selection and bot-protection bypass on its side, billed at 10 credits per bypass, or 30 for ultra premium. Self-hosted scrapers need their own proxy pool and retry logic.

**Do unused credits carry over?**
No. Credit rollover is unavailable on every plan, so size monthly rather than annual if your volume is uneven. Annual billing takes 10% off but locks the allowance in.

## Before you order

Match the plan to requests, not rows: multiply your monthly query and coordinate count by 25 credits, add 10 for any request that needs rendering, and compare that to the credit totals above. Then confirm three things on the pricing page: the monthly price for the tier you picked, whether pay-as-you-go overage is available on it (only from Scaling up), and that the fields you need appear in a real response from the trial.

👉 [Start with the plan that matches your request volume](https://www.scraperapi.com/pricing?fp_ref=coupons)
