# Ezigo eSIM — plan data feed

Machine-readable price and allowance data for Ezigo travel eSIM plans, published for price-comparison and directory partners.

## Files

| File | Contents |
|---|---|
| `ezigo-esim-plans-single-country.json` | 2,651 single-country plans across 200 destinations |

## Endpoints

| | URL |
|---|---|
| Primary (GitHub Pages) | `https://ampaimam.github.io/ezigo-esim-data/ezigo-esim-plans-single-country.json` |
| Fallback (raw) | `https://raw.githubusercontent.com/ampaimam/ezigo-esim-data/main/ezigo-esim-plans-single-country.json` |

Neither endpoint is behind bot protection. **No special User-Agent or request header is required** — a plain `requests.get()` or `http.Get()` works.

## Schema

Top level:

```json
{
  "provider": "Ezigo eSIM",
  "generated": "2026-10-22",
  "currency": "USD",
  "note": "Single-country plans only. throttle_speed_kbps=0 means the plan stops when the allowance is used.",
  "plans": [ ... ]
}
```

Each item in `plans` (all values are strings):

| Field | Meaning |
|---|---|
| `destination_name` | Country / destination label |
| `plan_id` | Ezigo plan id |
| `plan_name` | Display name |
| `plan_type` | `daily` (1,271) · `fixed_total` (1,285) · `unlimited` (95) |
| `data_amount_mb` | Total allowance in MB. Only populated for `fixed_total`. |
| `data_per_day_mb` | Daily allowance in MB. Only populated for `daily`. |
| `data_unlimited` | `"true"` when the plan keeps working at reduced speed after the high-speed allowance |
| `throttle_after_mb` | High-speed allowance before any speed change |
| `throttle_speed_kbps` | Reduced speed after the allowance. **`0` means the plan stops** rather than dropping to a slower tier. |
| `validity_days` | Plan duration in days |
| `price_usd` | Price in USD |
| `network_operator` | Upstream network |
| `network_type` | Radio technology (e.g. `4G, LTE, 5G`) |
| `hotspot_allowed` | Tethering permitted |
| `activation_type` | Delivery method |
| `product_url` | Purchase page |
| `price_flag` | Internal flag, generally empty |

### Reading the three plan types

| `plan_type` | Where the allowance lives | After the allowance |
|---|---|---|
| `daily` | `data_per_day_mb`, resets each local midnight | continues at `throttle_speed_kbps` |
| `fixed_total` | `data_amount_mb`, one pool for the whole validity | stops (`throttle_speed_kbps = 0`); recharge available on some plans |
| `unlimited` | `throttle_after_mb` is the daily high-speed amount | continues at `throttle_speed_kbps` |

`unlimited` does **not** mean full speed for the whole plan. The daily high-speed amount is in `throttle_after_mb`, and the reduced speed afterwards is in `throttle_speed_kbps`.

## Refresh

The file is regenerated when plan prices or allowances change. Always fetch the Pages endpoint for the current copy — it is served with `Cache-Control: max-age=600`, so a cached response is at most ten minutes old. The top-level `generated` date makes a stale mirror easy to spot.

## Contact

service@ejesim.com
