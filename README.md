# FraudCoins open dataset

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22494421.svg)](https://doi.org/10.5281/zenodo.22494421) [![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

On-chain **holder-concentration** measurements and **contract-permission** data
for cryptocurrency tokens, published as plain JSON under CC BY 4.0.

<!-- STATS:START -->
Snapshot `2026-09-15` — 1,583 assets scored · 303 with an
on-chain holder measurement (0 taken on the snapshot date) · 246 with
wallet-level correction evidence · 240 with at least one contract permission reported
(304 contracts queried).
<!-- STATS:END -->

## What this adds

A raw "top 10 holders hold X%" figure counts whatever the ten largest addresses
happen to be. In practice those routinely include an exchange's omnibus wallet,
a liquidity pool, a bridge contract, a locked treasury or a burn address — which
are very different things. An exchange wallet is custodied for thousands of
people and a burned balance is gone for good, while a treasury or vesting
contract is very much someone's to release later.

This dataset classifies each of the ten largest holders first, publishes the
share held by **private wallets only**, and — the part that makes it checkable —
**names each wallet set aside, with its classification and stake**, so the
correction can be verified on a block explorer rather than taken on trust.

```
v1/concentration.json         raw vs corrected top-10 share per token
v1/excluded-holders.json      the wallets removed from each figure, and why
v1/contract-permissions.json  owner privileges + structural contract properties
v1/scores.json                composite risk score, level and flags
v1/history.json               concentration series (see convention 2)
v1/coins-index.json           id / name / symbol for every asset covered
v1/alerts.json                concentration events by day (see note below)
v1/index.json                 discovery document: endpoints, licence, cadence
```

**Reading `alerts.json` correctly:** most entries carry `kind: "first-measured"`
— the asset was measured for the first time and was already above the threshold.
Only `kind: "crossed"` means it moved across the threshold between two
measurements. In the current snapshot that is 95 vs 6. Reading a
`first-measured` entry as "this project newly deteriorated on this date" would
be wrong, and it names real projects, so the distinction matters.

`index.json`, `excluded-holders.json` and `contract-permissions.json` are also
served live at `https://fraudcoins.com/dataset/v1/`; the other five sit at the
site root (`https://fraudcoins.com/concentration.json`, etc.). `index.json` is
the authority for every endpoint URL. CORS is enabled — no key, no registration,
no rate limit.

## Four conventions that matter more than the field list

1. **`null` means no measurement — never a clean result.** An unreported
   contract permission is *unknown*, not *absent*. 45 of the 188 queried
   contracts returned no permission fields at all.
2. **A flat interval in `history.json` means no change was observed, not that no
   measurement occurred.** Consecutive identical readings are not re-emitted.
   Each point is a reading whose value differed from the one before it.
3. **The date field differs per file**: `sampledAt` in `concentration.json`,
   `asOf` in `scores.json`, `measuredAt` in `contract-permissions.json`. Read it
   rather than assuming a figure is current.
4. **Flag thresholds are per tier, defined in `concentration.json`'s `tiers`
   array** — 50% for assets above 50 million USD market cap, 70% for the rotated
   10–50 million USD tier where high concentration is close to ordinary. A flag also
   requires at least 50 holder records.

## Coverage is uneven, on purpose

Assets above 50 million USD are re-measured daily. The 10–50 million USD tier is rotated, so those
readings are older — 170 of the 226 rows were measured on the snapshot date; the
rest carry an earlier date in `sampledAt`. Correction evidence exists for the
125 assets whose most recent measurement recorded it.

Rows with fewer than 10 usable holder records are not published at all: a "top
ten" computed from four addresses is not a meaningful quantity.

## Limitations worth stating plainly

- Holder classification relies on **public address labels**. An unlabelled
  custodial wallet is counted as private, so every correction here is a
  **floor**, not a complete accounting.
- **Excluded supply is not automatically harmless.** Burned tokens are gone, but
  treasury and vesting holdings are excluded too and can reach the market when
  they unlock. A low private-wallet figure is not a low future sell-side risk.
- Contract data comes from **GoPlus Labs' static analysis** of the deployed
  contract. We do not decompile bytecode ourselves, and the field is reported as
  they returned it. Trade-*simulation* verdicts (honeypot, cannot-sell-all,
  taxes) are deliberately **excluded**: they produce false positives on
  legitimate, freely-traded assets, and republishing one as fact would be
  defamatory.
- `isProxy` and `isOpenSource` are **not** owner privileges. Upgradeable proxies
  are the standard deployment pattern for many established protocols.
- Risk **scores and flags are automated, opinion-based assessments** derived from
  public market data — not statements of fact, financial advice, or allegations
  of illegal conduct against any project or person.

## Licence & citation

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — free to use,
including commercially, with attribution.

```
FraudCoins.com (2026). On-chain holder-concentration dataset.
https://doi.org/10.5281/zenodo.22494421
```

The DOI above is the **concept DOI** — it always resolves to the latest archived
version, so a citation using it stays correct as the dataset is re-released.
Each release also receives its own version DOI if you need to pin an exact
snapshot. Archived at CERN's Zenodo.

Methodology: <https://fraudcoins.com/methodology/> ·
Corrections: <https://fraudcoins.com/corrections/>
