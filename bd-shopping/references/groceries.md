# Groceries, fresh food & household consumables

**Coverage:** online grocers mostly serve Dhaka and a few other cities. Check delivery coverage for the user's area (`shopping/prefs.md`) before comparing.

**Cache age: 2 hours.** Grocery prices change daily.

## Retailers
- **Chaldal** — online grocer, fast delivery in Dhaka and several other cities.
- **Shwapno** — supershop chain with online store (also through pandamart).
- **Meena Click** — Meena Bazar's online store.
- **Daraz Mart** — packaged and non-perishable items.
- **Khaas Food** — food-safety-focused grocer.

## Evidence
Mostly price. **Compare unit price** (৳/kg, ৳/L, ৳/unit), never pack price.

## Check
- Pack size and unit price.
- Delivery fee and minimum order (free-delivery thresholds change often; check live).
- Expiry vs. how fast the household will use a bulk pack.
- BSTI mark on packaged food. It can be faked too, so it's a screen, not proof.

## Fresh fish, meat, produce
- Quality varies by order. The user may prefer to inspect in person.
- Chemical adulteration (formalin and similar preservatives) has been a recurring concern in BD fresh markets. Prefer sellers that state their sourcing and testing, and say plainly when no seller makes a credible claim.
- Don't present any seller as "chemical-free" on its own say-so.

## Output (overrides the core verdict)
Return a short table sorted by unit price instead of a BUY/WAIT verdict:
```
item | shop | pack | price ৳ | ৳/unit | in stock | checked
```
Note bulk-buy savings vs. shelf life underneath.
