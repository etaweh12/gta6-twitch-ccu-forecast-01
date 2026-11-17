# GTA VI launch-day Twitch viewers — hour-by-hour quantile forecast

Entry for the make.fun bounty **gta6-twitch-ccu-forecast-01**: given one
case (the target launch's hour-1 Twitch print plus 38 comparable launches),
write five quantiles (5%, 25%, 50%, 75%, 95%) of that hour's Twitch
concurrent viewers for each of hours 2–24 of the release day (UTC).

## Program

`main.py` (Python 3.13, standard library only, no network, deterministic).

- Reads every `*.in` in `/input` (or `$MAKEFUN_INPUT_DIR`) and writes
  `/output/<id>.out` (or `$MAKEFUN_OUTPUT_DIR`) as
  `{"hours":[{"hour":h,"q":[q05,q25,q50,q75,q95]}...]}` — 23 rows.
- Run: `python3 main.py` (about 0.1 s per case; well under the 8 s limit).

## Method — local ratio curves conditioned on hour-1 size

For every comparable launch, take its release day (UTC) and form the ratio
curve `r_h = viewers(h) / viewers(1)` for h = 2..24. A launch's hour-1
print is mostly set by *when* it unlocked relative to 00:00 UTC: games
already live at midnight have a big hour 1 and a flat day, while games
unlocking later in the day start near zero and explode 10–1000x by the
evening. Across the comparables in the case files, `log(viewers(1))`
predicts `log(r_h)` with correlation about −0.9 at late hours, so pooling
every launch together would give a forecast far too wide for a huge
launch and far too narrow for a small one.

For each forecast hour, the program takes the **K = 10 comparables
nearest in log(hour-1 viewers)** to the target, and uses the **median**
of their log-ratios as the centre and **1.4826 × MAD** as the log-scale
spread:

    q_tau(h) = p1 * exp(centre_h + z_tau * spread_h)

with `z` the standard normal quantiles. The spread is floored at 0.12
(no over-confident hour) and capped at 2.0, and the median ratio is
clamped to [0.05, 50]. If a case has no usable comparable, it falls back
to persistence (every hour at hour 1's print) with the house baseline's
widening spread, so a valid file is still written.

## Validation

Leave-one-out cross-validation on the 37 usable comparables in the kit
(each launch treated as the target, the other 36 as the pool; mean
pinball loss on the log scale, the contest's metric):

| model | all 37 launches | hour-1 ≥ 20k (27) | 5 biggest hour-1 |
|---|---|---|---|
| house baseline (flat) | 0.93 | 0.32 | 0.22 |
| this entry (K = 10) | 0.45 | 0.26 | 0.13 |

The big-launch regime is what matters here: GTA VI launches 19 Nov 2026
and is the biggest launch ever, so its hour-1 print should sit at or
above the largest comparable (ELDEN RING, 875,155 viewers), where the
ratio curve stays near-flat (about 0.5–1.3× through the day) — the
model's local window is exactly those launches.

## Why GTA VI's day should look like the big console launches

GTA VI unlocks at rolling midnight local time (New Zealand 11:00 UTC
18 Nov, UK 00:00 UTC 19 Nov, US East 05:00 UTC, US West 08:00 UTC), the
same pattern as ELDEN RING, Cyberpunk 2077 and the other big
midnight-local launches — so hour 1 (00:00–01:00 UTC) is already
post-unlock for Europe/UK/Oceania, and the day's shape is a broad
afternoon-evening peak rather than a late-unlock explosion. The
comparables' median ratio curve peaks around hour 19–20 (about 2.4×
hour 1) and stays near 0.7–1.0× in the small hours.

## Licence

MIT — see `LICENSE`.
