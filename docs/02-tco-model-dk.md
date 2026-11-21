# TCO model — Denmark (v0.1, to be validated)

> All rates are **TODO-VERIFY** until the Architect adds a source + validFrom in `src/backend/config/dk/`. The structure is the contract. The numbers are not.

## Inputs
| Symbol | Meaning | Default source |
|---|---|---|
| `condition` | `new` or `used` | User (default `new`) |
| `A0` | Car age in years at purchase (0 for new) | User / derived from first-registration date |
| `P` | Purchase price paid by the user, incl. VAT and any registration tax already embedded in it (DKK). For a used car this is the used-market price | User input / list price |
| `Y` | Years of ownership (integer, 1..15) | User (default 5) |
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
| `V(a)` | Residual value fraction at car age `a` years, relative to the new-car price | Depreciation curve by fuel/segment (config), editable |
| `P_new` | Reference new price used to anchor `V` | = `P` for a new car; for a used car `P_new = P / V(A0)` |
| `L` | Loan principal (0 = no loan). Must be ≤ `P` | User (default 0) |
| `r` | Annual loan interest rate (nominal) | User |
| `N` | Loan term in years (integer, ≥ 1) | User (default = `Y`) |

## Residual value per year
For every ownership year `y = 0..Y`:

- `R(y) = P_new × V(A0 + y)`
- `V(0) = 1`, `V` is non-increasing, and `V(a) ≥ 0`.
- New car: `R(0) = P`. Used car: `R(0) = P` by construction of `P_new`.

The curve `V` is defined in config as yearly points (age 0..20). Between points: linear interpolation. Beyond the last point: hold the last value.

## Formulas (per car, per ownership year y = 1..Y)
- **Energy(y)**
  - EV: `K × c × (s_home × e_home + (1 − s_home) × e_public)`
  - ICE: `K × c × f`
  - PHEV: `K × u_el × c_el × (s_home × e_home + (1 − s_home) × e_public) + K × (1 − u_el) × c_fuel × f`
- **Running(y)** = Energy(y) + `T_owner(car, y)` + `I` + `M`
- **Interest(y)**: annuity loan, monthly payments over `N × 12` months at monthly rate `r/12`. Interest(y) = the sum of the interest part of the 12 payments in year `y`. Interest(y) = 0 for `y > N` or `L = 0`. If `r = 0`, interest is 0.
- **Outstanding loan at year Y** `B(Y)`: the remaining principal after `Y × 12` payments (0 if `N ≤ Y`). It is a debt, not a cost. It is shown as information only and doesn't change TCO, because paying it off comes out of the residual value the owner already holds.
- **Depreciation(Y)** = `P − R(Y)`
- **Financing(Y)** = Σ(y=1..Y) Interest(y). Only interest paid during ownership counts. Principal repayments aren't costs (they're already covered by `P`).
- **TCO(Y)** = Depreciation(Y) + Σ(y=1..Y) Running(y) + Financing(Y)
- **Cost per km** = `TCO(Y) / (K × Y)`. Not defined when `K = 0`. The UI shows "—".

## Cumulative cost and break-even
Cumulative net cost at the end of year `y` (`y = 0..Y`), as if the car were sold at that point:

- `C(0) = 0`  (buying and selling right away at `R(0) = P` costs nothing)
- `C(y) = (P − R(y)) + Σ(j=1..y) Running(j) + Σ(j=1..y) Interest(j)`

This means `C(Y) = TCO(Y)`. The chart plots `C(y)` for both cars.

**Break-even year** = the first integer `y` in `1..Y` where the car that costs more up front stops being more expensive. Let `X` = the car with the higher `P` (if the prices are equal, there is no break-even and the UI shows "same purchase price"):
- break-even = the smallest `y` with `C_X(y) ≤ C_other(y)`
- If none in `1..Y`: "no break-even within Y years".
- Only integer years are reported in v1. No interpolation inside a year.

## Simplifications in v1 (show them in the UI)
- No inflation or discounting (optionally a v2 toggle)
- Constant energy prices over the period (user-adjustable)
- Insurance `I` and service `M` are constant per year (no age-based rise, no one-off repairs or battery replacement)
- Constant km per year and constant PHEV electric share
- Registration tax is assumed included in `P`; shown separately for information (new cars only, as computed from config)
- Used cars: depreciation comes from the same new-car curve `V`, shifted by age `A0`. No mileage or condition adjustment
- Financing: fixed-rate monthly annuity only; no fees, no balloon/leasing

## Input validation
- `Y` integer 1..15; `K` 0..100,000; `s_home`, `u_el` in 0..1; `0 ≤ L ≤ P`; `r` 0..25%; `N` integer 1..10; `A0` integer 0..20.
- `condition = new` ⇒ `A0 = 0`.

## Oracle
The Tester maintains at least 9 hand-worked cases in `tests/oracle/`:
EV vs. petrol, EV vs. diesel, low km (8,000), high km (30,000), short (3 years) and long (10 years) ownership,
PHEV vs. petrol, used EV (A0 = 3) vs. used diesel, financed purchase (`L > 0`, `N ≠ Y`).
Each case includes `C(y)` for every year and the expected break-even year.
