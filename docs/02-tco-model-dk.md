# TCO model — Denmark (v0.2, to be validated)

> All rates are **TODO-VERIFY** until the Architect adds a source + validFrom in `src/backend/config/dk/`. The structure is the contract. The numbers are not.

## Changelog
| Version | Date | Change |
|---|---|---|
| v0.2 | 2026-11-23 | Product owner decisions (part of D-09): no break-even (the comparison itself is the goal); cash purchase only (loan inputs, interest and financing removed); comparison period `Y` is 1..10 years, default 4; new output TCO per year; new input odometer at purchase `O0`; mileage limit `Kmax` by powertrain; residual value is the lower of the age curve and a straight km line. **Still needs D-09 approval.** |
| v0.1 | — | First draft. |

## Inputs
| Symbol | Meaning | Default source |
|---|---|---|
| `condition` | `new` or `used` | User (default `new`) |
| `A0` | Car age in years at purchase (0 for new) | User / derived from first-registration date |
| `O0` | Odometer at purchase in km (the car's mileage when picked). 0 for a new car; a small value is allowed (e.g. delivery km) | User (default 0) |
| `P` | Purchase price paid by the user, incl. VAT and any registration tax already embedded in it (DKK). For a used car this is the used-market price. Paid in cash | User input / list price |
| `Y` | Comparison period = years of ownership (integer, 1..10) | User (default 4) |
| `K` | km per year | User (default 15,000) |
| `c` | Consumption: kWh/km (EV) or l/km (ICE), WLTP adjusted by a real-world factor | Car data × config factor |
| `c_el`, `c_fuel` | PHEV only: electric consumption (kWh/km) and fuel consumption (l/km) in their respective modes | Car data × config factor |
| `u_el` | PHEV only: share of km driven on electricity (0..1) | User (default from config, TODO-VERIFY) |
| `e_home`, `e_public` | Electricity price DKK/kWh at home / public charging | Energi Data Service + tariffs (config) |
| `s_home` | Share of charging done at home (0..1) | User (default 80%) |
| `f` | Fuel price DKK/l | Config |
| `T_owner(car, y)` | Grøn ejerafgift (+ udligningsafgift for diesel) for ownership year `y`. May vary by year (rule changes, EV phase-in) | Config rules by CO₂/fuel/weight with validFrom |
| `I` | Insurance per year | Default by segment/fuel (config), editable |
| `M` | Service + tyres per year | Default by fuel/segment (config), editable |
| `V(a)` | Residual value fraction at car age `a` years, relative to the new-car price (age curve) | Depreciation curve by fuel/segment (config), editable. Values TODO-VERIFY |
| `Kmax` | Mileage limit in km at which the car's value is 0 (km line). Defaults by powertrain: petrol 400,000; diesel 400,000; EV 250,000; PHEV 250,000 | Config, editable. Source: "product owner decision, 2026-11-23" (a modelling assumption, not external data), validFrom 2026-11-23 |

## Odometer per year
For every ownership year `y = 0..Y`, the odometer at the end of year `y` is:

- `O(y) = O0 + K × y`

## Residual value per year
The value fraction `W(y)` is the lower of the age curve and a straight km line from 0 km (fraction 1) to `Kmax` (fraction 0):

- `W(y) = min( V(A0 + y), max(0, 1 − O(y) / Kmax) )`   for `y = 0..Y`

The model anchors at purchase so that the residual value right after buying equals the price paid, `R(0) = P`:

- `P_new = P / W(0)`   (reference new price, used only to scale `W`)
- `R(y) = P_new × W(y)`

Properties:
- New car with `O0 = 0`: `W(0) = min(V(0), 1) = 1`, so `P_new = P`.
- New car with a small `O0 > 0`: `W(0)` is slightly below 1, so `P_new` is slightly above `P`. `R(0) = P` still holds.
- Used car: `W(0)` is whichever of `V(A0)` and the km line is lower at purchase. `R(0) = P` by construction.
- The value is 0 once `O(y) ≥ Kmax`: `R(y) = 0` from that year on.
- `V(0) = 1`, `V` is non-increasing and `V(a) > 0` at every config point (strictly positive, so `W(0) > 0` whenever `O0 < Kmax`). With `K ≥ 0` the km line is non-increasing too, so `W` and `R` are non-increasing over `y`.

The curve `V` is defined in config as yearly points (age 0..10). Between points: linear interpolation. Beyond the last point: hold the last value. The km line is evaluated only at whole ownership years (`y = 0..Y`); there is no interpolation inside a year.

## Formulas (per car, per ownership year y = 1..Y)
- **Energy(y)**
  - EV: `K × c × (s_home × e_home + (1 − s_home) × e_public)`
  - ICE: `K × c × f`
  - PHEV: `K × u_el × c_el × (s_home × e_home + (1 − s_home) × e_public) + K × (1 − u_el) × c_fuel × f`
- **Running(y)** = Energy(y) + `T_owner(car, y)` + `I` + `M`
- **Depreciation(Y)** = `P − R(Y)`
- **TCO(Y)** = Depreciation(Y) + Σ(y=1..Y) Running(y)
- **TCO per year** = `TCO(Y) / Y`
- **Cost per km** = `TCO(Y) / (K × Y)`. Not defined when `K = 0`. The UI shows "—".

The purchase is cash only. There are no loan, interest or financing terms in v1 (see `docs/parking-lot.md`).

## Cumulative cost
Cumulative net cost at the end of year `y` (`y = 0..Y`), as if the car were sold at that point:

- `C(0) = 0`  (buying and selling right away at `R(0) = P` costs nothing)
- `C(y) = (P − R(y)) + Σ(j=1..y) Running(j)`

This means `C(Y) = TCO(Y)`. The chart plots `C(y)` for both cars, year by year. The results show, for each car, `TCO(Y)`, TCO per year and cost per km over the chosen period, plus the breakdown. The model does not compute a break-even year: the comparison itself is the output.

## Simplifications in v1 (show them in the UI)
- No inflation or discounting (optionally a v2 toggle)
- Constant energy prices over the period (user-adjustable)
- Insurance `I` and service `M` are constant per year (no age-based rise, no one-off repairs or battery replacement)
- Constant km per year and constant PHEV electric share
- Registration tax is assumed included in `P`; shown separately for information (new cars only, as computed from config)
- Cash purchase only: no loans, interest, leasing or fees
- Residual value: the lower of a generic age curve `V` (by fuel/segment) and a straight-line km depreciation to 0 at `Kmax`. No adjustment for condition, equipment or market data; the km line is linear and `Kmax` is a product-owner assumption per powertrain, not a measured value

## Input validation
- `Y` integer 1..10 (default 4).
- `K` 0..100,000.
- `s_home`, `u_el` in 0..1.
- `A0` integer 0..20. `condition = new` ⇒ `A0 = 0`.
- `O0` integer km, `0 ≤ O0 < Kmax` (otherwise `W(0) = 0` and `P_new` is undefined; the error message says the car is at or above the mileage limit for its powertrain).
- `condition = new` ⇒ `O0 ≤ 1,000` km (delivery/transfer km; a car with more is entered as used).
- `Kmax` integer km, 50,000..1,000,000 if the user edits it.
- Config check (at load time, not user input): every `V(a)` point is in `(0, 1]`, `V(0) = 1`, and the points are non-increasing.

## Oracle
The Tester maintains at least 12 hand-worked cases in `tests/oracle/`. Each case states its assumed rates, `V` points and `Kmax` explicitly (the numbers test the formulas, not today's rates):

1. EV vs. petrol (defaults: `Y = 4`, `K = 15,000`)
2. EV vs. diesel
3. Low km (`K = 8,000`)
4. High km (`K = 30,000`)
5. Short period (`Y = 1`, the minimum)
6. Long period (`Y = 10`, the maximum)
7. PHEV vs. petrol
8. Used EV (`A0 = 3`) vs. used diesel (`A0 = 3`), both with a realistic `O0` where the age curve `V` binds
9. Km line binds from high km/year: new cars, `K` high enough that `1 − O(y)/Kmax < V(y)` for some years inside the period
10. Used car with high `O0`: the km line binds already at purchase (`W(0)` comes from the km line, so `P_new = P / (1 − O0/Kmax)`)
11. A car reaches `Kmax` inside the period: `R(y) = 0` from that year on (include one year where `O(y) = Kmax` exactly, or an extra edge case for it)
12. PHEV using the 250,000 km limit vs. a petrol car using 400,000 km at the same `K`, so the km line binds for the PHEV but not for the petrol car

Each case includes `O(y)`, `W(y)`, `R(y)`, `Running(y)` and `C(y)` for every year `y = 0..Y`, plus `TCO(Y)`, TCO per year and cost per km.

Extra edge cases (in one of the cases above or as separate cases): `K = 0` (cost per km shown as "—"; the km line stays flat at `1 − O0/Kmax`); a new car with a small `O0 > 0` (`P_new` slightly above `P`); `A0 + y` between two `V` points (linear interpolation) and beyond the last point (hold last value); rejected inputs (`O0 ≥ Kmax`, `condition = new` with `O0 > 1,000` or `A0 ≠ 0`, `Y` outside 1..10).
