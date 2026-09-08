# Quandoo Exit List

Every Italian restaurant that takes bookings through Quandoo, tiered by what it
loses when the platform shuts down.

**Live:** https://xerocool36.github.io/quandoo-exit-list/ — `noindex` +
`robots.txt Disallow` while the campaign is running.

## Why

Recruit Holdings put Quandoo into liquidation across every country it operated
in. No acquirer, no automatic data migration.

| Date | What happens |
|---|---|
| 30 Sep 2026 | Last day Quandoo accepts reservations |
| 1 Oct 2026 | Services stop — booking widgets and the Google "Prenota" button go dead |
| 31 Dec 2026 | Site, app and infrastructure switched off |

Quandoo published **no export procedure for merchants** — only
`dataprotection@quandoo.com`. The free guide handed to owners lives in the
agency repo at `offers/quandoo-esci-in-sicurezza.md`.

## Tiers

Sales order, computed rather than guessed:

| Tier | Meaning |
|---|---|
| A1 | Google's "Sito web" points at Quandoo — it *is* their web presence |
| A2 | No website link at all on Maps |
| B | Own live site embedding the Quandoo widget — dead button on a page they control |
| C | Own site with no booking system — Quandoo *was* their online booking |
| D | Site broken or unreachable |
| E | Own site plus TheFork/OpenTable — those stay; only the Quandoo half is replaced |
| F | Not yet verified. An honest label, not "no website". |

## How it is built

Generated, never hand-edited. Tooling lives in the `x3roautomations` repo under
`quandoo/` and `crm/enrichment/harvest_quandoo.py`:

1. `harvest_quandoo.py --sitemap` — Quandoo's public directory (name, address,
   phone, cuisine, price, coordinates) from each place page's JSON-LD.
2. `osm_websites.py` — one Overpass query for every Italian eatery carrying a
   `website` tag, matched to venues on coordinates.
3. `probe_web.py --guess` — domain guessed from the name, accepted only when the
   page proves it (the venue's phone number, or street + CAP).
4. `probe_web.py --platforms` — fetch each known site, detect which booking
   system it embeds.
5. `maps_probe.py --shard N --of M` — Google Maps for whatever is left. The only
   source that can prove a negative ("no website").
6. `build_page.py --site` — merge, tier, write `docs/index.html`.

Raw data stays in `.tmp/quandoo/` in the agency repo and is not committed.
