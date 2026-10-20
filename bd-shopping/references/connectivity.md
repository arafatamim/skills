# Home internet & mobile plans

Services, not products. Same research shape: verify the offer, check real-world quality, compute real cost.

## Home broadband
### Who
- BTRC-licensed ISPs, from nationwide providers to local area ISPs. **Availability is per building**: the first question is which ISPs actually have fibre in the user's building or lane. Ask neighbours/building management, or the ISP directly.

### Check
- **Real speed, not the package name.** Ask for or find owner reports on evening-peak speeds. Distinguish BDIX/local speed (often advertised as much higher) from international bandwidth, which is what most of the internet uses.
- **Uptime during load-shedding:** whether the ISP's local switches/OLT have backup power. Also the user's own ONU/router on backup (see `power-backup.md`).
- Installation fee, router/ONU cost or rental, and contract or advance terms.
- Support response time in the area (owner reports).
- **For self-hosting:** public IP vs. CGNAT, real (static) IP availability and cost, IPv6 support, whether inbound ports are blocked. Ask the ISP explicitly; sales staff often don't know, so ask for technical support.

### Output
```
ISP | package | international / BDIX speed | monthly ৳ | setup ৳ | public/static IP | source
```

## Mobile plans
- Operators: Grameenphone, Robi, Airtel (Robi), Banglalink, Teletalk.
- **Coverage where the user actually is** (home, work) matters more than the plan. Use owner reports or a coverage map, and say coverage claims are the operator's.
- Compare **৳ per GB** and **৳ per day of validity** for packs that fit the user's real monthly use. Note validity and whether unused data carries over.
- Packs change constantly. Fetch live from the operator; cache max 2 hours; never quote a pack from memory.
