# Product brief — TCO Cars (v1, Denmark)

## 1. Problem
People choosing between an electric car and a diesel or petrol car mostly compare sticker prices. The real difference is in taxes, energy, maintenance and resale value over the years they own the car. TCO Cars makes that difference visible.

## 2. Users
Private car buyers in Denmark comparing two specific cars (new or used), typically EV vs. ICE.

## 3. Core user flow (v1)
1. Pick **Car A** and **Car B** (search by make/model/variant; choose fuel type: EV, petrol, diesel, plug-in hybrid).
2. Enter or accept defaults: new or used (car age), purchase price, km per year, years of ownership, home-charging share, PHEV electric-driving share, financing (optional: loan amount, interest rate, loan term).
3. See a side-by-side comparison:
   - Total cost of ownership and cost per km
   - Breakdown: purchase/registration, owner tax, energy, insurance, service/tyres, depreciation, financing
   - Cumulative cost over time (chart) and break-even year
4. Adjust assumptions (energy prices, km/year) and see the results update instantly.
5. Share the comparison by URL (parameters in the URL, nothing stored).

## 4. Scope
**In v1:** Denmark only, DKK, Danish/English UI, two-car comparison, transparent assumptions with sources, responsive web.
**Out of v1:** user accounts, more than two cars, live dealer prices, countries other than Denmark, company-car taxation (beskatning af firmabil). The last one is a strong v2 candidate.

## 5. Data sources (candidates — Architect must validate in Sprint 0)
| Need | Candidate free sources | Notes |
|---|---|---|
| Car specs (consumption, CO₂, weight, fuel) | EEA CO₂ monitoring data for new cars (EU open data); Danish Motor Register (DMR/Motorregistret) open data extract | Check licence, how to access, model/variant matching |
| EV specs (kWh/100 km, battery) | EEA data; manufacturers' published WLTP figures | Avoid sources whose terms forbid reuse |
| Electricity prices | Energi Data Service (Energinet) — spot prices, DK1/DK2 | Plus tariffs/taxes from config |
| Fuel prices | Danish fuel companies' published list prices, or a public price index | Possibly a manual config update monthly |
| Taxes | Skattestyrelsen / Motorstyrelsen published rates: registreringsafgift, grøn ejerafgift, udligningsafgift (diesel) | Stored in config with validFrom; EV registration-tax phase-in by year |
| Insurance, service, depreciation | No reliable free API → transparent default assumptions per segment and fuel type, editable by the user | Documented in the TCO model |

## 6. Success criteria
- A user can complete a comparison in under 60 seconds.
- Every number shown can be traced to its formula and source (an "i" tooltip).
- The oracle test cases match within ±1 DKK.
- Monthly AWS cost stays under ~20 USD at low traffic.

## 7. Future
More countries (country plug-in: tax rules, energy, currency), company-car tax, more than two cars, used-car valuation data.
