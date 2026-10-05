# buy rotating proxies: how per-GB pricing, rotation type, and targeting fees decide your real cost

Most people searching this phrase already know they need IPs that change on their own. The hard part starts a click later, when every provider shows a different unit — some quote per GB, some per IP per month, some per 1,000 requests — and the cheapest number on the page is rarely the cheapest invoice.

Here's the thing worth internalizing before you spend anything: you are not buying bandwidth or IPs. You are buying *successful requests*. Everything else — rotation logic, pool freshness, targeting fees, whether your unused traffic survives next month — is just a lever that moves that final number up or down.

DataImpulse is a useful case study for this because their whole pitch is built on the boring part of the problem: flat per-GB pricing, pay-as-you-go, and traffic that doesn't expire. It's also a provider with real limits, which we'll get to.

## Why the headline $/GB number rarely matches what you pay

A per-GB rate is only comparable if the units underneath are comparable. Three things break that assumption in practice.

**Expiring traffic.** Buy 100 GB on a monthly plan you can't fully consume and your effective rate isn't the advertised one, it's the advertised one divided by the fraction you actually used. A 50%-used plan doubles the real cost. Pay-as-you-go with non-expiring credits removes that specific failure mode, which matters most if your scraping volume swings month to month.

**Minimum commitments.** A low listed rate attached to a big monthly floor means your entry price is the floor, not the rate. The number that decides whether a test is cheap is the smallest amount you're allowed to pay.

**Targeting surcharges.** Country-level targeting is usually free. City, ZIP, and ASN lookups often aren't. Estimate a big multi-region job at the country rate and the quote can land 50% off once you need city-level accuracy.

None of this is exotic. It's just rarely written on the same line as the price.

## Rotating, sticky, and the ports that decide which one you get

"Rotating proxy" gets used loosely, so it helps to be precise about the two connection modes you'll actually configure.

**Rotating** means the exit IP changes with every new request. You point everything at one endpoint and let the pool do the work. On DataImpulse that's port **823** for HTTP/HTTPS and **824** for SOCKS5, with the gateway host being `gw.dataimpulse.com`. No session bookkeeping, no state to manage — the right default for crawling, SERP checks, price monitoring, and anything where you want one request to look unrelated to the last.

**Sticky** means the IP stays pinned to a port for a set window. DataImpulse's sticky connections run on ports **10000 through 20000**, with rotation intervals configurable from 1 to 120 minutes and a default of 30 minutes when you don't specify one. Reach for this when the target site behaves better with a little session continuity — multi-step flows, paginated results, a login you need to keep alive briefly — but you still want a different IP tomorrow.

There's also a middle option: the `sessid` parameter, which pins you to the same IP for roughly 30 minutes even through a rotating port. DataImpulse's docs are upfront that this isn't a replacement for sticky ports, since the underlying device can drop offline and get swapped out mid-session. Treat it as convenience, not a guarantee.

If you want to see how these map onto the dashboard and current plan structure, 👉 [check the rotating and sticky setup options on DataImpulse](https://bit.ly/dataimPulse) before you commit to a traffic size.

## Cost per successful request: the only comparison that holds up

$/GB and $/1,000 requests measure different things and can't be plotted on one axis. But you can convert both into the same currency for *your* workload, and then the comparison becomes trivial.

Assume you're pulling 1,000,000 pages a month at an average 500 KB of HTML each. That's about 500 GB of traffic. At $1/GB, you're at roughly $500. At the 1 TB volume rate DataImpulse publishes ($800 per TB, or $0.80/GB), the same 500 GB costs about $400.

Now adjust for reliability, because every blocked request is paid-for waste. At a 95% success rate you need to divide by 0.95, pushing the $500 figure to roughly $526. That also explains why a $0.50/GB pool that gets blocked half the time is more expensive per usable page than a $1/GB pool that mostly works. The advertised rate is not the metric; cost per usable record is.

Managed scraping APIs invert the tradeoff. At $2 per 1,000 requests, that same million-page job runs about $2,000 — more per record, but zero engineering for rotation, browser rendering, and anti-bot handling. If your volume is low or you have no one to maintain a scraper, the "expensive" option is often the cheaper one.

DataImpulse publishes a 99.51% success rate and a 90M+ IP pool across 195 countries. Those are vendor-stated figures, not independently audited ones, so treat them as a starting hypothesis. Third-party coverage is mixed in the usual way: review sites like HostAdvice describe the flat $1/GB rate, first-party IP pool, and non-expiring traffic as holding up under scrutiny, while comparison directories like ProxyLook score the provider closer to 3.9 out of 5. Both data points are worth knowing; neither replaces running your own target list through a small test purchase.

## The published tiers, side by side

DataImpulse sells four proxy products on the same pay-as-you-go model. Prices below are the tiers published on the official pricing pages, in USD, with no subscription required.

| Product | Published tier | Traffic | Price | Effective rate | Buy |
| --- | --- | --- | --- | --- | --- |
| Residential | Intro | 5 GB | $5 | $1/GB | [Get the residential intro plan](https://bit.ly/dataimPulse) |
| Residential | Volume | 1 TB | $800 | $0.80/GB | [Top up at the 1 TB residential rate](https://bit.ly/dataimPulse) |
| Datacenter | Intro | 10 GB | $5 | $0.50/GB | [Start with 10 GB of datacenter traffic](https://bit.ly/dataimPulse) |
| Datacenter | Mid | 100 GB | $50 | $0.50/GB | [Buy the 100 GB datacenter package](https://bit.ly/dataimPulse) |
| Datacenter | Volume | 1 TB | $450 | $0.45/GB | [Buy 1 TB of datacenter traffic](https://bit.ly/dataimPulse) |
| Datacenter | Custom | 5 TB+ | From $2,250 | Custom | [Request custom datacenter pricing](https://bit.ly/dataimPulse) |
| Mobile | Intro | 2.5 GB | $5 | $2/GB | [Try the mobile intro plan](https://bit.ly/dataimPulse) |
| Mobile | Mid | 25 GB | $50 | $2/GB | [Buy 25 GB of mobile traffic](https://bit.ly/dataimPulse) |
| Mobile | Volume | 1 TB | $1,600 | $1.60/GB | [Buy 1 TB of mobile traffic](https://bit.ly/dataimPulse) |
| Mobile | Custom | 5 TB+ | From $8,000 | Custom | [Ask about custom mobile volume](https://bit.ly/dataimPulse) |
| Premium Residential | Intro | 1 GB | $5 | $5/GB | [Test the premium residential pool](https://bit.ly/dataimPulse) |
| Premium Residential | Basic | 10 GB+ | $50 | $5/GB | [Buy 10 GB of premium residential](https://bit.ly/dataimPulse) |
| Premium Residential | Custom | 1,000 GB+ | $4,000 | $4/GB | [Request premium enterprise pricing](https://bit.ly/dataimPulse) |

A few things the table makes obvious:

- **The entry point is $5 across all four products.** That's the minimum payment, not a free tier. What $5 buys varies a lot: 5 GB of residential, 10 GB of datacenter, 2.5 GB of mobile, or just 1 GB of premium.
- **Volume discounts land late on mobile and premium.** Residential and datacenter get meaningfully cheaper per GB at the 1 TB mark. Mobile doesn't drop until you're already at 1 TB ($1.60/GB), and premium's first real discount starts at 1,000 GB.
- **Traffic doesn't expire on any of these.** Unused GBs stay in your balance, which is the main structural difference from subscription-based providers.

Before buying a tier, 👉 [compare the current DataImpulse plan lineup](https://bit.ly/dataimPulse) yourself — pricing pages change, and the effective rate is only half the decision.

## Country targeting is included; city, ZIP, and ASN usually are not

This is the fee that surprises people mid-project, so it's worth stating plainly.

On standard residential, DataImpulse includes country selection (and country exclusion) plus ASN exclusion in the base price. State, city, ZIP, and specific ASN selection are advanced filters billed at **2× the standard per-GB rate**. A job that reads as $1/GB on paper can behave like $2/GB once you're targeting a ZIP code, and there's no way to notice that from the headline price.

Premium residential works differently: all targeting levels — country, state, city, ZIP, and ASN — are included at no surcharge, which is part of what the $5/GB covers. So the honest comparison between standard and premium residential isn't $1 versus $5. It's $1 versus $5 *if you only need countries*, and $2 versus $5 if you need city precision on every request. The gap narrows faster than the sticker prices suggest.

Datacenter listings include state, city, ZIP, and ASN targeting as features according to DataImpulse's own product page, which is a different billing treatment than residential gets. If precise geo-targeting drives your budget, confirm the current treatment with support before you commit — this is exactly the kind of line item that gets revised.

## Turning credentials into an actual request

Setup is genuinely short. The rotating residential endpoint, HTTP and HTTPS:

bash
curl -x "http://login:password@gw.dataimpulse.com:823" https://api.ipify.org/


SOCKS5 rotating uses port 824. Country targeting rides in the username with a `__cr.` suffix — `login__cr.us` for US exits, for example — and sticky sessions combine a `sessid` value with the same suffix pattern, which the docs demonstrate with `login__cr.au;sessid.123`.

That's it. No per-IP provisioning, no IP list to refresh, no zone configuration. The tradeoff is that you can't pin a specific address permanently, because these are residential devices that come and go by nature. If your workflow needs a fixed IP that never changes, rotating proxies in general are the wrong product category — look at static ISP or dedicated datacenter instead, which DataImpulse doesn't sell.

## The small print that decides whether a cheap plan stays cheap

Four practical limits, all documented by the vendor or reviewers:

**No free trial.** Every product starts at the $5 minimum. Intro plans carry a 7-day money-back guarantee for card payments, conditional on having consumed less than 80% of the traffic. Cryptocurrency purchases on Intro plans are not refundable, per HostAdvice's review.

**Advanced targeting costs double on residential.** Already covered, but it belongs on the list because it's the most common reason a budget estimate misses.

**Some jobs are out of scope.** DataImpulse describes itself as a rotating residential, mobile, and datacenter provider for public data collection — not a static ISP reseller, not a fully managed scraping API, and not a tool for bank or government site access. If those are your requirements, price the categories you actually need before comparing anything.

**The pool is first-party.** The company states it sources IPs directly from consenting users rather than reselling another network. That's a meaningful distinction for compliance reviews, but it's still a vendor claim. Ask for whatever documentation your procurement process needs.

## Which plan fits which job

Fairly direct guidance based on the tiers above:

- **Testing a scraper or verifying a target site works at all:** the $5 residential intro. 5 GB is enough requests to measure your own success rate, which is the number that actually decides your budget.
- **High-volume crawling of unprotected or lightly protected targets:** datacenter at $0.50/GB, dropping to $0.45/GB at 1 TB. Roughly half the residential rate, and usually fast enough for static HTML.
- **Protected targets — e-commerce, SERPs, ad verification, social:** standard residential. Country targeting is free, and 90M+ IPs across 195 countries gives enough rotation depth to avoid reusing exits on a large crawl.
- **City, ZIP, and ASN precision on every request:** the math only favors standard residential if your precision needs are occasional. Frequent advanced targeting pushes you toward premium, where the whole targeting stack is included and you get a dedicated account manager on top.
- **Mobile app data, 4G/5G-only inventory, or the hardest targets:** mobile at $2/GB. Budget for it — the first volume discount doesn't arrive until 1 TB.
- **Spiky or unpredictable volume:** the pay-as-you-go model is the whole argument here. With non-expiring credits, a slow month costs you nothing extra, which subscription plans can't say.

Two more decisions worth making before you pay: measure your real average page size, because headless browser rendering pulls far more data than fetching raw HTML, and calculate your own cost per successful request using the success rate you observe on your own targets rather than any published figure. Start with the smallest tier that covers a day of production-like traffic, run it, and scale the tier you actually need — 👉 [buy a small batch of rotating proxy traffic and measure it yourself](https://bit.ly/dataimPulse).

## Quick answers

**Is $1/GB cheap for rotating residential proxies?** It sits at the low end of the published range. Provider rates typically run from about $1/GB up to $5–8/GB for enterprise pools, with $3–4/GB representing the middle of the market. What matters more is whether the pool delivers on your targets, and whether unused traffic survives the month.

**Rotating or sticky — which do I need?** Rotating for anything request-based: scraping, monitoring, verification. Sticky for short multi-step flows where a site behaves better with session continuity. Most projects use both and just change the port.

**Do I need a subscription?** Not on this model. You buy traffic, it sits in your balance, and you spend it whenever. The upside is no wasted spend on partial months; the tradeoff is that you pay up front for capacity you haven't used yet.

**What's the fastest way to evaluate a provider?** Buy the smallest tier, run it against the exact sites you'll be scraping, and log success rate, latency, and bytes per request. Those three numbers tell you more than any comparison table, including this one.
