---
name: bd-shopping
description: Research any purchase for a buyer in Bangladesh — electronics, appliances, clothing, beauty, groceries, furniture, books, medicines, home goods. Pins requirements, shortlists from independent evidence, verifies BDT prices and stock at local retailers, checks warranty/authenticity/returns, computes landed cost for imports, and returns a buy/wait/skip verdict. Use for any "should I buy", "find me a", "best X under ৳", price check, compare, or import-vs-local question.
version: 1.3.0
metadata:
  hermes:
    tags: [shopping, bangladesh, research, price-check]
---

# BD Shopping Research

End-to-end procedure for buying anything in Bangladesh. The core phases below apply to every purchase. Each category has a playbook in `references/` with its retailers, evidence sources, and failure modes. **Load only the playbook for the category in hand.**

For a trivial lookup ("price of X at Chaldal"), jump straight to Phase 3 and return one line.

**Self-maintenance:** retailers, URL patterns, and response formats change. When a step or a retailer entry is wrong, fix the file in place (SKILL.md or the playbook) and note it in the Changelog. Do not work around a broken step silently.

## Category playbooks

| Category | Load |
|---|---|
| Phones, laptops, PC parts, peripherals, networking, audio, cameras | `references/electronics.md` |
| AC, fridge, washer, fan, cooler, oven, geyser, purifier | `references/appliances.md` |
| Clothing, footwear | `references/clothing.md` |
| Skincare, makeup, haircare, personal care | `references/beauty.md` |
| Groceries, household consumables | `references/groceries.md` |
| Furniture, home furnishing | `references/furniture.md` |
| Books, media | `references/books.md` |
| Medicines, health products | `references/medicines.md` |

**No matching playbook?** Apply the core phases and work out for the category: (1) who sells it legitimately, (2) what independent evidence exists, (3) what the common fake or failure mode is, and (4) what makes its warranty or returns policy meaningful. If the category comes up a second time, write a new playbook in `references/` and add it to this table.

---

## Phase 1 — Pin the requirement

Identify the **category** first and load its playbook. Extract from the request, or ask in **one** message (max 3 questions):

| Field | Why it matters |
|---|---|
| Use case | Who it's for, how often, how long it must last |
| Budget | Hard ceiling or soft target? Ask if unclear |
| Deal-breakers | Fail one → candidate is dropped, no debate |
| Timeline | Need now vs. can wait for a sale, season, or successor |
| Fit constraints | Size, space, ecosystem, skin type, dietary needs — whatever the playbook says matters |

Check USER.md for stored preferences first (sizes, brands avoided, warranty stance, skin type, dietary restrictions). Don't re-ask what's already there.

If the request is clear enough, state assumptions in one line and proceed.

**Push back** when constraints conflict: "Every option you named is ৳15k over your ceiling — which constraint is real?"

---

## Phase 2 — Shortlist from evidence, not retailers

1. Find candidates from **Tier A/B** sources for the category (see Source Tiers and the playbook). Use Tier D only to discover names.
2. Shortlist 3–5. Drop deal-breaker failures immediately.
3. **Timing check:** the playbook says what applies (model successors, seasonal pricing, new editions).
4. **Complaint search** for each finalist. Adapt the terms to the category:
   - Durable goods: `"<product>" (broke OR died OR "stopped working" OR warranty claim)`
   - Clothing/footwear: `"<brand>" (shrink OR fade OR sizing OR "poor quality")`
   - Beauty/food/consumables: `"<product>" (fake OR duplicate OR "batch code" OR reaction OR expired) Bangladesh`
   - Look for recurring patterns. One-off complaints are noise.

---

## Phase 3 — Verify locally

### Retailers

Use the retailers in the playbook. Check **at least two** per candidate when the item is sold in more than one place.

Search URL patterns go stale. If one fails, use a site-scoped web search (`site:<domain> <product>`) instead of guessing URLs, then update the playbook.

### Record for every listing

```
shop | exact product/variant | price ৳ | stock | warranty or return policy | URL | checked <YYYY-MM-DD HH:MM>
```

Rules:
- **"Call for price", "price inbox", or no price → UNKNOWN.** Never estimate it.
- **Exact variant match.** Size, weight, volume, capacity, colour, storage, model revision. A 200ml price is not a 500ml price.
- **Unit price for consumables:** normalise to ৳/kg, ৳/L, ৳/100ml, or ৳/unit before comparing.
- If a site blocks the request or returns nothing, say so in the output. Don't fill gaps from memory.
- Search-snippet and cached prices are leads, not facts. Confirm on the live page.

### Daraz

Daraz covers every category. Fetch search results as JSON with plain HTTP, no browser:

```
GET https://www.daraz.com.bd/catalog/?ajax=true&q=<url-encoded query>&page=<n>
```

- Results are under `mods.listItems[]`. Useful fields: `name`, `price`, `originalPrice`, `discount`, `ratingScore`, `review`, `sellerName`, `brandName`, `itemId`, `itemUrl`, `inStock`, `location`. Field names can change; if one is missing, inspect the response and update this list.
- `itemUrl` is protocol-relative (`//www.daraz.com.bd/...`), so prefix it with `https:`.
- Optional params: `&sort=priceasc` / `pricedesc`, `&price=<min>-<max>`.
- Filter the results yourself: exact variant match in `name`, drop refurb/used/"copy"/"master copy"/"first copy" listings, and for branded goods prefer the brand's official store or DarazMall sellers.
- **Rate limits:** one request every few seconds, at most 3 pages per query, and cache results for a few hours. Only query when a price is actually needed.
- **If you get HTML instead of JSON,** or a slider/"punish" captcha page, you've been flagged. Stop. Don't retry in a loop or switch to a browser to get past it. Report Daraz as "blocked, couldn't verify" and back off for at least an hour.
- To get product detail (variants, warranty text, seller info), fetch the product page and parse the embedded JSON state blob, not the DOM. Do this only for finalists.
- Seller rating alone is not evidence. Read 1-star reviews for "fake", "used", "different product", "expired".

### Facebook shops

Facebook is a **discovery source, not a price source**. It matters most for clothing, beauty, home décor, and food. Many pages post "price inbox" with no number.

1. Find candidate pages through web search.
2. Check whether the shop also sells on Daraz or has its own website. If it does, verify there.
3. Otherwise, report it in the Brief as `FB-only — price unverified, ask the seller`. Note whether it offers cash on delivery and whether returns are possible. A page that demands full advance bKash payment from a stranger is a risk flag.
4. **Absolute last resort:** use Apify's Facebook Pages scraper, and only if the user explicitly asks for FB data and steps 1–3 found nothing. It costs money per run. Say so and get a yes before running it. Never scrape Facebook with the user's logged-in account.

### Used goods (Bikroy, FB Marketplace)

Bikroy is the main classifieds site for used goods. Use it to gauge used-market prices and to find listings. Every used purchase gets flagged with:
- Inspect in person before paying. Never pay in advance to a stranger.
- Phones: IMEI/NEIR check (see the electronics playbook). Laptops and phones: battery health.
- Brand claims ("original Hatil", "official unit") are unverified until checked.

### Authenticity & after-sales

The playbook names the category's specific risk. Universally:

| Seller type | Treat as |
|---|---|
| Brand's own store / authorized distributor | Genuine, full warranty/returns |
| Established retailer with stated policy | Policy is only as good as the shop. Check its reputation |
| Unknown seller, Daraz non-mall, FB page, classifieds | Possible fake, grey, used, or expired until proven otherwise |

---

## Phase 4 — Real cost

### Local
```
price + delivery + COD/card/EMI surcharge = total
```
Note whether EMI is genuinely 0% or carries a processing fee. For consumables, compare unit price and how long the pack lasts, not pack price.

### Import (AliExpress, Amazon via forwarder, regional sellers)
Only consider when local is unavailable, clearly more expensive after adjusting for warranty and returns, or the user asks.

```
item price (USD/other)
+ international shipping
+ forwarder / courier fee
+ customs duty, VAT, and other taxes on the declared value
× FX at the buyer's actual card rate (not the interbank rate)
= landed cost ৳
```

- **Look up current duty/VAT for the product's HS code** from NBR (National Board of Revenue). Rates change every fiscal-year budget. Never hardcode them in an answer.
- Cards: international online payments draw on the dual-currency card's USD endorsement/quota. Flag if the item is large enough to hit typical limits.
- Restricted or slow-clearing categories: wireless/radio devices (BTRC approval), drones, batteries, cosmetics and food (may need permits or get held), medicines. The playbook flags these too.
- Imported = no local warranty and effectively no returns.

### Ownership
Flag running costs that change the ranking: consumables, refills, electricity (AC, fridge, geyser), filters, maintenance, replacement parts and their local availability.

---

## Phase 5 — Verdict

Pick **one**. A verdict that hedges across five options is a failure.

- **BUY** — good fit, fair price, verified stock, acceptable warranty/returns.
- **WAIT** — successor or new season is close, or a known sale is coming. Must give a date or a target price.
- **SKIP** — nothing in budget is worth buying. Name the minimum budget that gets something good.
- **DEPENDS** — only when one unanswered fact flips the decision. Name that fact.

### Sale timing (BD)
Major discount windows: Daraz 11.11 and 12.12, pre-Eid sales (both Eids), Pohela Boishakh, Black Friday/year-end campaigns, end-of-season clothing sales. Category-specific timing is in the playbooks.

Retailer "sale" prices are often inflated-then-discounted. Compare against the price recorded before the campaign, not the struck-through MSRP.

---

## Source Tiers

| Tier | Examples | Use for |
|---|---|---|
| **A — measured/tested** | RTINGS, Notebookcheck, Wirecutter, Consumer Reports, Which?, GSMArena tests, Project Farm, ingredient databases, energy ratings | Performance, durability, efficiency, ingredient claims |
| **B — owner reports** | Reddit category subs, r/bangladesh, r/dhaka, BD Facebook groups, long-term owner reviews, Daraz reviews with photos | Reliability, quality in practice, sizing, local service, fake prevalence |
| **C — retailer/brand** | Retailer listings, brand sites | Price, stock, warranty/return terms. **Not** quality |
| **D — discovery only** | SEO "best X 2026" listicles, affiliate roundups, sponsored videos, influencer posts | Finding names. Never cite as evidence |

When Tier A and B disagree (great test scores, many failure reports), report the conflict. Don't pick the convenient one.

---

## Output

### Brief (default; always use when called over A2A)
```
VERDICT: <BUY|WAIT|SKIP|DEPENDS> — <product> at <shop>, ৳<price> (checked <date>)
WHY: <1–2 lines>
RUNNER-UP: <product> — ৳<price> — pick if <condition>
SKIP: <product> — <reason>
RISKS: <authenticity / warranty or returns / stock / timing / sizing>
COULDN'T VERIFY: <anything blocked or unknown>
SOURCES: <links>
```

### Comparison (on request)
Table with only the rows that differ **and** matter for the stated use case. For consumables, include a unit-price column. No padding rows.

### Watch (for WAIT verdicts)
```
WATCH: <product> — target ৳<price> or <event/date>
CURRENT: ৳<price> at <shop> (<date>)
```
Offer to create a scheduled job that re-runs Phase 3 for this item and notifies through the gateway when the target is hit or the item goes out of stock.

---

## After the decision

- If the user states a durable preference (sizes per brand, skin type, "always official warranty", brands to avoid, dietary restrictions), save it to USER.md.
- If the user buys, append to a purchase log: item, variant, shop, price, date, and warranty/return expiry. Use it later for ecosystem/size checks, reorder reminders for consumables, and warranty-expiry reminders.

## Hard rules

1. No source + date → no price.
2. Never invent stock, coupons, review quotes, or specs.
3. Never log in, add to cart, or check out. Research only.
4. No medical advice: no medicine, dose, or substitute recommendations.
5. "Don't buy anything" is a valid answer.

## Changelog
- 1.3.0 — playbooks moved to `references/` (load on demand); retailer lists checked against current sources; Unimart dropped; Gree distributor corrected to Electro Mart; added Jamuna, Haier, Hisense, Midea, Navana, Wafilife, PBS, Prothoma, ePharma, MedEasy, Daraz Mart, Khaas Food; Bikroy used-goods section.
- 1.2.0 — generalized beyond tech: category-agnostic core plus playbooks.
- 1.1.0 — Daraz via catalog AJAX JSON endpoint; Facebook discovery-only, Apify as last resort.
- 1.0.0 — initial version.
