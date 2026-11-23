# Product brief — TCO Cars (v1, Denmark)

## 1. Problem
People choosing between an electric car and a diesel or petrol car mostly compare sticker prices. The real difference is in taxes, energy, maintenance and resale value over the years they own the car. TCO Cars makes that difference visible.

## 2. Users
Private car buyers in Denmark comparing two specific cars (new or used), typically EV vs. ICE.

## 3. Core user flow (v1)
1. Pick **Car A** and **Car B** (search by make/model/variant; choose fuel type: EV, petrol, diesel, plug-in hybrid).
2. Enter or accept defaults: new or used (car age), odometer at purchase (km; 0 for a new car), purchase price, km per year, home-charging share, PHEV electric-driving share, and the comparison period (years of ownership): 1 to 10 years, default 4. The purchase is always treated as cash; loans and other financing are left out of the calculation.
3. See a side-by-side comparison:
   - Total cost of ownership over the chosen period, total cost of ownership per year of that period, and cost per km over the period.
   - Breakdown: purchase/registration, owner tax, energy, insurance, service/tyres, depreciation
   - Cumulative cost year by year over the period (chart) for both cars. There is no break-even year: the comparison itself is the result.
4. Adjust assumptions (energy prices, km/year) and see the results update instantly.
5. Share the comparison by URL (parameters in the URL, nothing stored).

## 4. Scope
**In v1:** Denmark only, DKK, Danish/English UI, two-car comparison, transparent assumptions with sources, responsive web.
**Out of v1:** user accounts, more than two cars, live dealer prices, countries other than Denmark, company-car taxation (beskatning af firmabil), financing/loans (cash purchase only). 

## 5. Data sources (candidates — Architect must validate in Sprint 0)
| Need | Candidate free sources | Notes |
|---|---|---|
| Car specs (consumption, CO2, weight, fuel) | EEA CO2 monitoring data for new cars (EU open data); Danish Motor Register (DMR/Motorregistret) open data extract | Check licence, how to access, model/variant matching |
| EV specs (kWh/100 km, battery) | EEA data; manufacturers' published WLTP figures | Avoid sources whose terms forbid reuse |
| Electricity prices | Energi Data Service (Energinet) — spot prices, DK1/DK2 | Plus tariffs/taxes from config |
| Fuel prices | Danish fuel companies' published list prices, or a public price index | Possibly a manual config update monthly |
| Taxes | Skattestyrelsen / Motorstyrelsen published rates: registreringsafgift, grøn ejerafgift, udligningsafgift (diesel) | Stored in config with validFrom; EV registration-tax phase-in by year |
| Insurance, service, depreciation | No reliable free API → transparent default assumptions per segment and fuel type, editable by the user | Documented in the TCO model. Depreciation uses the lower of an age curve and a km line to a mileage limit per powertrain (product owner assumption) |

## 6. Success criteria
- A user can complete a comparison in under 60 seconds.
- Every number shown can be traced to its formula and source (an "i" tooltip).
- The oracle test cases match within ±1 DKK.
- Monthly AWS cost stays under ~20 USD at low traffic.

## 7. Future
More countries (country plug-in: tax rules, energy, currency), company-car tax, more than two cars, used-car valuation data (condition, market prices), financing/loans.
