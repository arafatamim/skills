# Power backup: IPS, UPS, solar, batteries, stabilizers

Load-shedding makes this a high-value category in BD. It is also a category full of recycled and rebranded batteries.

## Retailers
- Established IPS/battery brands and their dealers. Common names in the market include Rahimafrooz, Hamko, Luminous, Microtek, and Walton. Confirm the current authorized dealer list on each brand's own site.
- Star Tech / Ryans for computer UPS units.
- Solar: installers registered with the relevant authority. Look up current rooftop-solar and net-metering rules before advising; they change.
- Listed prices are negotiable at dealers.

## Sizing (show the working)
1. **Load:** list each device and its watts (fans, lights, router, laptop, fridge if wanted). Sum the running watts, and note startup surges (fridges, motors, pumps).
2. **Inverter/IPS capacity:** VA rating comfortably above the running load (typically 25–30% headroom), and able to handle the largest startup surge.
3. **Battery capacity:** `Wh needed = load W × backup hours`. Then `Ah ≈ Wh ÷ (battery V × usable depth of discharge × inverter efficiency)`. State the DoD and efficiency you assumed (lead-acid: use ~50% DoD; LiFePO4: ~80–90%).
4. If the user hasn't given loads and hours, ask. Don't size from a guess.

## Battery types
- **Tubular lead-acid:** the long-life standard for IPS; needs water top-ups and ventilation.
- **Flat-plate lead-acid:** cheaper, shorter life.
- **Sealed/maintenance-free:** no top-ups; usually shorter cycle life than tubular.
- **Lithium (LiFePO4):** higher upfront cost, much longer life and usable capacity, lighter. Needs a compatible inverter/charger and a proper BMS.
Compare **cost per year of service**, not sticker price.

## Fakes and recycled stock
- Recycled or reconditioned batteries resold as new are common. Check manufacturing date code, weight against spec (fakes are often lighter), and buy from authorized dealers.
- Warranty on batteries is usually pro-rata. Report the free-replacement period and the pro-rata period separately.

## Check
- Pure sine wave vs. modified/square wave. Sine wave is needed for fridges, motors, sensitive electronics, and most laptops' long-term health.
- Charging time and charge current (load-shedding cycles can be frequent).
- Installation, wiring, and changeover switch cost.
- **Safety:** batteries need ventilation (lead-acid gives off hydrogen while charging) and proper cable sizing and fusing. Recommend professional installation for anything beyond a plug-in UPS.

## Stabilizers
Match capacity to the appliance (AC, fridge), check input voltage range against local fluctuation, and check whether the appliance already has built-in protection.
