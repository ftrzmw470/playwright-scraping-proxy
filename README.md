# Playwright Bot Detection Got You Blocked Again? Here's Why Stealth Plugins Aren't Enough — Plus a Proxy API Comparison and Pricing Breakdown for Reliable Scraping

If you've spent the last few hours watching your Playwright script return 403 errors, blank CAPTCHA pages, or silently empty HTML, you're not alone. Anti-bot systems have gotten aggressive, and Playwright — for all its power as a browser automation tool — was never built to be invisible. It was built to be reliable and deterministic, which, ironically, is exactly the kind of behavior that modern detection systems are trained to spot.

This article walks through why Playwright gets flagged in the first place, what stealth plugins can and can't fix, and where a managed scraping API like ScraperAPI fits into the picture when you've hit the ceiling of what client-side patches can do.

## Why Playwright Gets Detected in the First Place

Out of the box, Playwright leaves a trail. The most well-known signal is `navigator.webdriver`, a property the W3C WebDriver spec requires browsers to expose when under automation control — and it's set to `true` by default. Anti-bot scripts check it first because it's the cheapest, fastest detection method available.

But that's just the start. Detection systems typically look at:

- **Browser fingerprinting** — navigator properties, missing plugins, canvas/WebGL rendering quirks, font lists, and the infamous "HeadlessChrome" string in the user-agent
- **TLS/JA3 fingerprinting** — the cipher suite ordering and handshake signature of Playwright's bundled Chromium doesn't match what a real Chrome install produces, which can flag a request before a single line of JavaScript even runs
- **CDP-level signals** — some anti-bot scripts detect the Chrome DevTools Protocol connection Playwright uses to talk to the browser
- **Behavioral analysis** — mouse trajectories, scroll patterns, click timing, and navigation sequences. A script that loads a page, waits a fixed interval, scrapes, and leaves produces a pattern that's statistically nothing like a human

That last category matters a lot more than most people expect. Stealth patches deal with fingerprint leaks. They do nothing for the fact that your bot navigated directly to a deep product page with zero scroll events and a perfectly even click interval.

## What playwright-stealth Actually Fixes (and What It Doesn't)

The most common fix people reach for is the `playwright-stealth` package, a Python port of the older `puppeteer-extra-plugin-stealth`. It deletes `navigator.webdriver`, strips "HeadlessChrome" from the user-agent, and patches a batch of other commonly checked properties so that an automated session passes basic fingerprint tests.

> Stealth plugins patch the fingerprint leaks attackers — or in this case, detectors — check for. They don't fix IP reputation, TLS fingerprinting, or behavioral analysis. As one recent scraping guide puts it bluntly: stealth alone is not enough once you're up against a dedicated anti-bot vendor.

So here's the honest breakdown of what stealth handles versus what it leaves on the table:

| Detection Vector | Fixed by Stealth Plugins? |
|---|---|
| `navigator.webdriver` flag | ✅ Yes |
| Missing plugins / language mismatches | ✅ Yes (partially) |
| HeadlessChrome user-agent string | ✅ Yes |
| TLS/JA3 fingerprint mismatch | ❌ No |
| CDP protocol detection | ❌ No |
| Behavioral pattern analysis (mouse, timing) | ❌ No |
| Cloudflare/DataDome JS challenges | ❌ No |
| IP reputation / datacenter IP blocks | ❌ No |

If your target is a basic site with no dedicated anti-bot vendor, stealth plus a clean browser context will probably get you through. If you're up against Cloudflare, DataDome, or PerimeterX, you're going to need something more — and that "something more" usually comes down to either building a much heavier in-house stack (residential proxy rotation, behavioral simulation, CAPTCHA-solving integration, constant fingerprint maintenance) or offloading that complexity to a managed scraping API.

## The Real Cost of DIY Stealth Maintenance

This is the part that doesn't get talked about enough. Even when you do get stealth working, anti-bot vendors update their detection logic regularly. What passes today can get flagged next month. Maintaining a homemade stealth stack means:

1. Continuously testing against fingerprinting test sites
2. Rotating and managing proxy pools yourself (and paying for residential IPs, which run several times the cost of datacenter IPs)
3. Handling CAPTCHA challenges as they appear
4. Rewriting evasion logic every time a target site updates its WAF rules

For a one-off scrape, that's manageable. For anything running in production at scale, it turns into a part-time job. This is exactly the gap that proxy/scraping APIs like ScraperAPI are built to fill — they sit between your Playwright script (or any HTTP client) and the target site, handling proxy rotation, header management, JavaScript rendering, and CAPTCHA bypass on their infrastructure instead of yours.

## Where ScraperAPI Fits Into a Playwright Workflow

ScraperAPI works as a single API endpoint: you send a request, it handles the IP rotation, browser/header fingerprinting, retries, and CAPTCHA bypass on the backend, and returns clean HTML (or rendered JS output) back to you. You can keep using Playwright for your own logic and parsing — or skip launching a local browser altogether and just call the API directly — and let ScraperAPI absorb the part of bot detection that's hardest to maintain yourself: IP reputation and anti-bot bypass at the network layer.

Some of the core mechanics worth knowing:

- A standard page request costs 1 credit
- Heavier targets cost more — Amazon is 5 credits, Google/Bing (including subdomains) are 25 credits, LinkedIn is 30 credits
- Sites sitting behind dedicated bot protection like **Cloudflare, DataDome, or PerimeterX add 10 credits per request** when ScraperAPI bypasses them on your behalf
- You can check the exact credit cost for any URL ahead of time using the Domain Cost Estimator in the dashboard, and cap spend with a `max_cost` parameter per scrape
- Unsuccessful requests caused by their own system errors (5xx/6xx responses) aren't billed — only 2xx/4xx outcomes count against your credits

For teams running Playwright scrapers against sites with active anti-bot defenses, that 10-credit Cloudflare/DataDome/PerimeterX bypass line item is effectively the price of not having to build and maintain your own bypass stack.

## Full Plan Comparison: Every ScraperAPI Tier

Here's the complete breakdown of every plan currently listed, including monthly and annual pricing:

| Plan | Monthly Price | Annual Price (per mo) | API Credits / mo | Concurrent Threads | Notes | Get Started |
|---|---|---|---|---|---|---|
| Free | $0 | $0 | 1,000 credits | 5 | 7-day trial includes 5,000 requests |  [Start free with ScraperAPI](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | $49/mo | $44/mo | 100,000 credits | 20 | US & EU regions only |  [Check Hobby plan pricing](https://www.scraperapi.com/?fp_ref=coupons) |
| Startup | $149/mo | $134/mo | 1,000,000 credits | 50 | US & EU regions only |  [Check Startup plan pricing](https://www.scraperapi.com/?fp_ref=coupons) |
| Business | $299/mo | $269/mo | 3,000,000 credits | 100 | Adds country-level geotargeting |  [Check Business plan pricing](https://www.scraperapi.com/?fp_ref=coupons) |
| Professional | $499/mo | $427/mo | 5,000,000 credits | 200 | Includes Pay-As-You-Go overflow |  [Check Professional plan pricing](https://www.scraperapi.com/?fp_ref=coupons) |
| Enterprise | Custom | Custom | 3,000,000+ credits | Custom | Dedicated account manager, custom contract |  [Talk to sales about Enterprise](https://www.scraperapi.com/?fp_ref=coupons) |

A few notes that matter when picking a tier:

- Plans below Professional (Hobby, Startup, Business) don't include automatic Pay-As-You-Go overflow — if you hit 100% of your credits before renewal, you can either auto-upgrade to the next tier or request a custom plan
- Professional, Advanced, and Enterprise plans include Pay-As-You-Go, letting you keep scraping past your limit at a fixed predictable rate, with an optional monthly spending cap so you're never surprised by a bill
- You can cancel anytime from the dashboard with no cancellation fee, and there's a 7-day no-questions-asked refund window if it's not working out

## So Should You Use Stealth Plugins, ScraperAPI, or Both?

In practice, most teams that scrape regularly end up running a hybrid setup rather than picking one or the other:

1. **Use playwright-stealth for low-risk targets** — sites without dedicated anti-bot vendors, internal QA environments, or anything you control. Free, lightweight, no extra infrastructure.
2. **Layer in behavioral realism** — randomized timing, scroll events, and mouse movement help when a site does light behavioral checks but isn't running Cloudflare-tier defenses.
3. **Switch to a managed API like ScraperAPI when you hit Cloudflare, DataDome, or PerimeterX walls** — at that point the anti-bot bypass is happening at the network and proxy layer rather than inside your browser, which is fundamentally harder for detection systems to flag than anything client-side stealth can offer.
4. **Use the free tier to validate before committing** — 1,000 credits a month (5,000 in your first week) is enough to test whether a target that's been blocking your Playwright script actually goes through cleanly via the API.

One more practical point worth flagging if you're testing legitimate QA automation rather than scraping third-party sites: CAPTCHA and bot-challenge pages in your own staging environment usually indicate a configuration issue, not something to "defeat." For QA workflows, the right move is disabling CAPTCHA in test environments or using test keys — not building evasion logic against your own infrastructure. The stealth-and-proxy techniques above are aimed at scraping public data you have a legitimate reason to access, not bypassing access controls on systems you don't own.

## Final Thoughts

Playwright bot detection isn't really about Playwright being a bad tool — it's a genuinely excellent automation library that happens to ship with default fingerprints and behavior patterns that look nothing like a real browsing session. Stealth plugins close the easy gaps. Everything past that — TLS fingerprinting, IP reputation, CAPTCHA-tier challenges — gets expensive to maintain yourself fast.

If you're at the point where stealth patches aren't cutting it anymore and you'd rather not build a residential proxy pool and CAPTCHA-solving pipeline from scratch, it's worth running a few real requests through ScraperAPI's free tier against the exact targets that have been blocking you. 👉 [Try the 1,000 free credits here](https://www.scraperapi.com/?fp_ref=coupons) and see whether the requests that were timing out in your Playwright script come back clean through the API instead.
