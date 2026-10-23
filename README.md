# Ryanair Flight Fares Scraper [Just 💰$0.18]

Extract structured data from [ryanair.com](https://ryanair.com) — Get Ryanair fares per day for a route like BER-MAD or from one airport: price and currency, departure and arrival times, sold-out flags and the booking link. Structured rows that load straight into fare tracking or a spreadsheet.

**[Ryanair Flight Fares Scraper [Just 💰$0.18] on Apify →](https://apify.com/blackfalcondata/ryanair-com-scraper?fpr=1h3gvi)**

---

## 🚀 How to use this actor

> ### 💚 $5 free Apify credits — every month
> No credit card required. No commitment. Cancel anytime.

### 👉 [Sign up free on Apify →](https://console.apify.com/sign-up?fpr=1h3gvi)

1. **Click sign up** — pick GitHub, Google, or email; takes ~30 seconds
2. **Open this actor** — input is pre-filled with a working example
3. **Click Start** — export results as JSON, CSV, or Excel

Your **$5 monthly platform credit** is enough to run this actor right away — and again every month — scraping typically several hundred to several thousand results per run, depending on your input.

## Key features

**Search with filters** — Search by keyword and location. Filter by 🔁 trip type, ↕️ sort by, 📝 description format, and more.

**Incremental mode** — Only get new or changed fares since your last run. Content hash per fare — no duplicates, no re-processing.

**Change classification** — Track unchanged, cross-run duplicate detection across runs. Build audit trails of how fares evolve over time.

**Compact output** — Emit core fields only (AI-agent / MCP-friendly). Keeps response size small for LLM workflows.

**Result cap** — Stop after N fares (up to 1.000). Set to 0 for the full catalog.

**Export anywhere** — Download as JSON, CSV, or Excel. Stream via Apify API, webhooks, or integrations with Make, Zapier, Airbyte, Keboola.

**Structured data** — Every fare returns the same schema with consistent field naming. All fields always present — `null` when unavailable, never omitted.

---

## Use cases

**Data pipeline automation**
Integrate with your ETL pipeline to collect structured fares from ryanair.com on a schedule. Export to CSV, JSON, or directly to your database. Use compact mode to control output size.

**Market research**
Monitor fares, track trends, and analyze market dynamics with structured, deduplicated data from ryanair.com.

**Change monitoring**
Run daily or hourly in incremental mode to capture only new, updated, or expired ${NOUNS}. Perfect for price-tracking, churn analysis, and alerting pipelines.

**AI / LLM training data**
Structured JSON per fare is ready for RAG pipelines, embeddings, and agent workflows. Compact mode trims tokens for LLM context windows.

---

## Quick start

```json
{
  "query": "BER-MAD",
  "maxResults": 100,
  "compact": false,
  "excludeEmptyFields": false,
  "incrementalMode": false,
  "skipReposts": false,
  "notifyOnlyChanges": false,
  "notificationLimit": 5,
  "descriptionFormat": "all"
}
```

---

## Input parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | string | — | What to look up, as Ryanair airport codes (IATA, 3 letters). `BER-MAD` returns a day-by-day price calendar for that route (one month per page). A single code such as `BER` returns the cheapest fare to every destination from that airport. For several searches use a JSON array, e.g. `["BER-MAD","DUB-STN"]`. |
| `location` | string | — | Route calendar (`BER-MAD`): the first month as `YYYY-MM`, e.g. `2026-11`; later pages continue month by month, up to 12 months ahead. Origin only (`BER`): a departure window `YYYY-MM-DD..YYYY-MM-DD`, e.g. `2026-11-01..2026-11-30`. Leave empty for the current month, or the next 30 days. The Depart From / Depart To fields below take precedence when set. |
| `market` | string | `"en-gb"` | Language and country of the Ryanair site, as `language-country`, e.g. `en-gb`, `de-de`, `es-es`. Default `en-gb`. It sets the booking links and the market the cheapest-fare list is requested for. |
| `maxResults` | integer | `100` | Maximum total results (0 = unlimited). |
| `startUrls` | array | `[]` | Paste raw search URLs from the target site. Each URL becomes its own search task; results are merged and deduped by listing ID across all URLs. A pasted flight-selection link keeps its exact outbound day, its return day (round trip), passengers and the site language/country; a fare-finder link keeps its outbound and return windows. These override the matching fields above for that link only. When provided, startUrls REPLACE the query/location fields (each supported URL becomes one task; unsupported URLs are skipped, and if none is supported the run stops with an input error). |
| `tripType` | enum | `"oneWay"` | One way (default) or round trip. A round trip on a route (`BER-MAD`) returns one row per outbound day, paired with the cheapest return flight inside the return window that leaves after the outbound lands; the price is the total of both legs per person. A round trip on an origin only (`BER`) returns Ryanair's cheapest return deals with both legs. |
| `departDateFrom` | string | — | First outbound day, `YYYY-MM-DD`. Set it equal to Depart To for one exact day. Replaces the Date Range field when set. |
| `departDateTo` | string | — | Last outbound day, `YYYY-MM-DD`. Leave empty to continue to the end of the booking window (a route calendar) or 30 days (an origin only). |
| `returnDateFrom` | string | — | Round trips only: first return day, `YYYY-MM-DD`. Default: the first outbound day. |
| `returnDateTo` | string | — | Round trips only: last return day, `YYYY-MM-DD`. Default: the last outbound day plus 7 days. |
| `allFlights` | boolean | `false` | Route search, one way only: return a row for every scheduled flight of each day instead of only the day's cheapest. Ryanair prices the day, not each flight, so only the cheapest flight carries `price` (the other flights have `price: null`); every row shows `dayCheapestPrice` and `isDayCheapest`. Ignored (with a log warning) for round trips and for an origin-only search. |
| `currency` | string | `"EUR"` | Currency code the fares are quoted in, e.g. `EUR`, `GBP`, `USD`, `PLN`. Default `EUR`. Ryanair decides which currencies exist per route. |
| `adults` | integer | `1` | Passengers aged 16+. Default 1. Fares are always per person; this only sets the passenger counts on the result and in the booking link. |
| `teens` | integer | `0` | Passengers aged 12-15. Default 0. |
| `children` | integer | `0` | Passengers aged 2-11. Default 0. |
| `infants` | integer | `0` | Passengers under 2. Default 0. |
| `maxPrice` | number | — | Only return fares up to this price per person, in the chosen currency. For a round trip the total of both legs counts. Empty = no price limit. Days without a price are left out when this is set. |
| `includeUnavailable` | boolean | `true` | Keep days with no bookable fare (sold out or not flown), marked `available: false`, so a calendar has no gaps. Turn off to return bookable fares only. |
| `sortBy` | enum | `"sourceOrder"` | Order of the delivered results: Source order (as Ryanair serves them, streamed while scraping), Cheapest, Earliest departure or Shortest duration. A sorted run delivers its results at the end of the run instead of streaming them. |
| `compact` | boolean | `false` | Core fare fields only (for AI-agent/MCP workflows). |
| `excludeEmptyFields` | boolean | `false` | Drop null, empty-string, and empty-array fields from each record before push. Smaller payloads for AI agents and dashboards. |
| `descriptionFormat` | enum | `"all"` | Choose which representation of the listing description to include. `all` keeps every variant; the others keep only the selected one. |
| `incrementalMode` | boolean | `false` | Compare against previous run state. stateKey is optional — defaults to a value derived from the search inputs (route or origin, dates, trip type, currency, market, passengers, price limit, startUrls) so different searches never share state. Sort By does not change it. |
| `stateKey` | string | — | Optional. Stable identifier for the tracked search universe. Leave empty to auto-generate from search inputs. |
| `emitUnchanged` | boolean | `false` | When Incremental Mode is on, also return fares whose price and availability have not changed since the previous run. Off by default: only new and changed fares are returned. |
| `skipReposts` | boolean | `false` | When incremental, skip listings whose content matches an expired listing from a prior run (cross-run duplicate detection). |
| `notifyOnlyChanges` | boolean | `false` | When Incremental Mode is on, only send notifications for NEW and UPDATED listings. Has no effect outside incremental mode. |
| `notificationLimit` | integer | `5` | Maximum number of listings included in each notification message (1–20). |
| `telegramToken` | string | — | Telegram bot token (from @BotFather). Required for Telegram notifications. |
| `telegramChatId` | string | — | Telegram chat or channel ID (e.g. "-100123456789"). Required when telegramToken is set. |
| `discordWebhookUrl` | string | — | Discord incoming webhook URL. Server Settings → Integrations → Webhooks → New Webhook. |
| `slackWebhookUrl` | string | — | Slack incoming webhook URL. api.slack.com/messaging/webhooks. |
| `whatsappPhoneNumberId` | string | — | Your WhatsApp Business phone-number ID (numeric, from Meta dashboard). Required when whatsappAccessToken is set. |
| `whatsappAccessToken` | string | — | WhatsApp Cloud API permanent access token (System User token from Meta Business). Recipient must have messaged the business number within the last 24h (service-conversation window — free since Nov 2024). |
| `whatsappTo` | string | — | Recipient phone in E.164 format without + (e.g. "436641234567"). Recipient must have messaged your business number within last 24h. |
| `webhookUrl` | string | — | Receives a JSON POST with {metadata, items} after each run. Universal escape hatch for n8n / Make / Zapier / custom backends. |
| `webhookHeaders` | object | — | Optional JSON object of custom headers (e.g. {"Authorization":"Bearer ..."}). |
| `appConnector` | string | — | Optional. Pick a connected app under Settings → API & Integrations to receive your results (including any contact details). Best-effort across MCP connectors as Apify expands its catalog. |
| `mcpIssueTeam` | string | — | Only when the connected app is an issue tracker: the team (name or ID) the summary issue is created under, if that app requires one. |
| `maxPages` | integer | `0` | Maximum result pages per search (0 = all, until Ryanair has no more). For a route calendar one page is one month, so 3 returns the next three months. Origin-only searches page through the destinations. |

---

## Output fields

Every fare returns the same 40-field schema. Missing values are `null` — never omitted.

- `listingId`
- `listingKey`
- `title`
- `scrapeMode`
- `tripType`
- `type`
- `direction`
- `route`
- `origin`
- `originName`
- `destination`
- `destinationName`
- `location`
- `date`
- `outboundDate`
- `returnDate`
- `departureDateTime`
- `arrivalDateTime`
- `departAt`
- `arriveAt`
- `flightNumber`
- `price`
- `currency`
- `currencySymbol`
- `available`
- `soldOut`
- `unavailable`
- `previousPrice`
- `priceChangePct`
- `outbound`
- `inbound`
- `bookingUrl`
- `portalUrl`
- `searchQuery`
- `contentQuality`
- `detailFetched`
- `scrapedAt`
- `source`
- `contentHash`
- `changeType`

---

## Sample output

One object per fare. Here is a real example from a production run:

```json
{
  "listingId": "328c22fd6ee8671111d322325f7c1b4c178ca458756edacf6283c68478b5225a",
  "listingKey": "BER-MAD-2026-10-01",
  "title": "Berlin Brandenburg → Madrid 2026-10-01",
  "scrapeMode": "routeCalendar",
  "tripType": "oneWay",
  "type": "calendar_day",
  "direction": "outbound",
  "route": "BER-MAD",
  "origin": "BER",
  "originName": "Berlin Brandenburg",
  "destination": "MAD",
  "destinationName": "Madrid"
}
```

*Truncated — full records contain 40 fields. See Output fields for the complete schema.*

**[Try Ryanair Flight Fares Scraper [Just 💰$0.18] now — $5 free credit, no credit card →](https://apify.com/blackfalcondata/ryanair-com-scraper?fpr=1h3gvi)**

---

## Pricing

Pay only for what you extract. No subscription required — Apify's free $5 credit covers thousands of results.

| Event | Price (USD) |
| --- | --- |
| Actor Start | $0.01 |
| Result | $0.00018 |

See the [actor on Apify](https://apify.com/blackfalcondata/ryanair-com-scraper?fpr=1h3gvi) for current pricing.

---

## FAQ

**How do I scrape ryanair.com?**
Use this actor on Apify to extract structured data from ryanair.com. Configure your search query and filters in the input, then click Start — no coding required.

**How do I get ryanair.com data as JSON, CSV, or Excel?**
The actor writes each fare to Apify's dataset. Download as JSON, CSV, or Excel from the Console, stream via the API, or push to Make, Zapier, Airbyte, or Keboola.

**Is it legal to scrape ryanair.com?**
Web scraping of publicly available data is generally legal. This actor only accesses publicly visible information. Always check ryanair.com's terms of service for your specific use case.

**How much does it cost?**
Pay-per-event pricing — you only pay for ${NOUNS} extracted. Apify's free $5 credit is enough to run thousands of results before you pay anything.

**How does incremental mode work?**
Each fare gets a content hash. On subsequent runs, only new or changed fares are emitted — saving time, compute, and storage. Expired fares can be tracked separately.

**Do I need an API key or credentials?**
No. Just sign up for Apify, paste your input, and click Start. No credit card required.

---

## Related products by Black Falcon Data

[Browse all Black Falcon Data actors →](https://apify.com/blackfalcondata?fpr=1h3gvi)

---

## Getting started with Apify

New to Apify? [Create a free account with $5 credit](https://console.apify.com/sign-up?fpr=1h3gvi) — no credit card required.

1. Sign up — $5 platform credit included
2. Open [Ryanair Flight Fares Scraper [Just 💰$0.18]](https://apify.com/blackfalcondata/ryanair-com-scraper?fpr=1h3gvi) and configure your input
3. Click **Start** — export results as JSON, CSV, or Excel

Need more later? [See Apify pricing](https://apify.com/pricing?fpr=1h3gvi).

---

## About Black Falcon Data

Black Falcon Data builds production-grade web scrapers for job boards and marketplace data. Browse our full actor catalog at [www.blackfalcondata.com](https://www.blackfalcondata.com).

---

*Last updated: 2026 10*
