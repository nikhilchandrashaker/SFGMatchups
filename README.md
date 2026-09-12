# Giants Franchise Analysis

Real season-by-season stats for the San Francisco Giants, 1958–2023 — 493 pitchers, 1,052 batters. This replaces an earlier version of this project that generated all its "pitch speed" and "strike zone" numbers randomly; nothing in this version is simulated.

## Files

| File | What it is |
|---|---|
| `SFG_batting.csv` | Real season-level batting stats, 1958–2023 |
| `SFG_pitching.csv` | Real season-level pitching stats, 1958–2023 |
| `giants_franchise_analysis.R` | R script: leaderboards, career trajectories, advanced metrics, decade trends, aging curves |
| `giants_dashboard.jsx` | Interactive React dashboard covering the same data, built with `recharts` |

## Why this isn't a pitch-speed / strike-zone tool

The original version of this project analyzed pitch velocity and strike-zone location — but that data was entirely fabricated with `runif()`/`rnorm()`, and it isn't something `SFG_batting.csv` or `SFG_pitching.csv` actually contain. Those files hold real **season totals** (batting average, ERA, OPS+, WHIP, etc.), not pitch-by-pitch Statcast data. So this project now does what the real data supports well: franchise history, career arcs, and advanced-metric analysis, all grounded in real numbers.

## R Script (`giants_franchise_analysis.R`)

Requires the `tidyverse` package. Run from the same folder as the two CSVs:

```r
source("giants_franchise_analysis.R")
```

This loads the data, prints a demo (top OPS+ seasons, best ERAs, a Tim Lincecum report, decade ERA trends), and makes the following functions available:

- `season_leaders(dataset, stat, n, min_pa, min_ip, direction)` — single-season leaderboard for any column
- `career_leaders_batting(stat, n, min_career_pa)` / `career_leaders_pitching(stat, n, min_career_ip)` — career totals, properly weighted (e.g. career average = total hits ÷ total at-bats, not an average of season averages)
- `career_trajectory(player_name, dataset, stat)` — line chart of a player's stat over their Giants seasons, saved as a PNG
- `compare_players(player_names, dataset, stat)` — overlay chart for several players
- `batter_advanced_report(player_name)` / `pitcher_advanced_report(player_name)` — ISO, BB%/K%, FIP-vs-ERA gap by season, K/BB, WHIP
- `decade_summary(dataset)` — team OPS/ERA by decade, with a bar chart
- `aging_curve(dataset, stat)` — performance by age (Giants tenure only — see caveat in the function's own output)
- `durability_leaders(dataset, n)` — longest tenures with the franchise
- `player_summary(player_name)` — full two-way lookup (batting + pitching) for one name

Example:

```r
career_leaders_batting("OPS", n = 10)
pitcher_advanced_report("Tim Lincecum")
career_trajectory("Buster Posey", "batting", "Batting_Average")
```

## Dashboard (`giants_dashboard.jsx`)

A four-tab interactive explorer of the same data:

- **Leaderboards** — pick a dataset, stat, and minimum PA/IP threshold; see the top 10 as a bar chart and ranked list
- **Player Arc** — type any player name to see their stat trend across their Giants seasons
- **Eras** — team-wide batting/pitching performance by decade
- **Compare** — overlay up to 3 players on the same stat over time

All data is embedded in the file, so it runs standalone with no external requests.

## Known limitations

- **Not full-career stats.** Every number reflects only a player's seasons *with the Giants* — Barry Bonds' MLB career average differs from his Giants-only average, for example.
- **Decade-level "Team OPS" is an approximation.** On-base percentage is calculated from hits and walks only (`(H+BB)/(AB+BB)`), because hit-by-pitch and sacrifice-fly counts weren't included in the trimmed dataset used by the dashboard. It's close to real OBP but not exact. The R script's `decade_summary()` uses the full CSV columns and is exact.
- **Aging curves reflect Giants tenure, not true career age curves** — a player who joined at 22 and left at 28 only contributes to those six ages, which can skew the shape for ages where few Giants played.
- **Career FIP** in `career_leaders_pitching()` is an average of each season's FIP, not recalculated from raw components — the CSVs don't include the league/park constants needed to rebuild FIP from scratch.
