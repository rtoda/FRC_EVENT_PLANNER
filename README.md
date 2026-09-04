# Statbotics — FRC Pre-Scouting Notebooks

Personal Jupyter notebooks for pre-scouting FRC (*FIRST* Robotics Competition) events:
who's attending, how strong the field is, and (for district teams) which events are
worth preferencing. Everything is built on top of two data sources — [Statbotics](https://www.statbotics.io/)
EPA ratings and [The Blue Alliance](https://www.thebluealliance.com/) (TBA) event/team
data — pulled live via their public APIs.

There's no application, package, build step, or test suite here. Each notebook is a
standalone analysis: open it, edit a few constants at the top (team number, year,
event/district), and re-run.

## What's here

- **`FRC_TBA_stats.ipynb`** — Compares one team against every other team at a single
  event. Looks up the event's teams from TBA, pulls each team's Statbotics EPA
  breakdown (auto / teleop / endgame), and renders a two-panel view: a ranked
  EPA leaderboard on the left and a Teleop-vs-(Auto+Endgame) scatter plot on the
  right, with the team of interest highlighted in red. The event's real name is
  looked up from TBA (e.g. `2026dal` → "2026 World Championship Daly Division")
  rather than showing the raw event code, and chart axes auto-scale to that
  event's actual EPA spread instead of a fixed range.

- **`FRC_District_Event_Planner.ipynb`** — For district teams choosing which
  regular-season events to preference. Given a team, a season, and a TBA district
  abbreviation (e.g. `"ca"`, `"fim"`, `"ne"`), it pulls every event in that
  district, groups them by week, estimates each event's field strength (mean and
  top-8 EPA, cross-checked against TBA's OPR), and recommends the pair of events
  at least `MIN_WEEK_GAP` weeks apart with the weakest combined field — i.e. the
  best shot at banking district points without back-to-back weekends or an
  unnecessarily tough field. Bar charts break out every event by week (some weeks
  have multiple events) and highlight the recommended pair in red. An optional
  `PREFERRED_EVENT_CODES` allowlist lets you restrict to a sub-region of a
  district (e.g. Southern California only, to skip Northern California travel).
  If the season you're planning for hasn't been published yet, point `DATA_YEAR`
  at the most recent season with real data as a stand-in.

- **`frcdatapy/`** — A vendored clone of a third-party wrapper for the *official*
  FRC Events API. It's read-only reference and isn't imported by either notebook
  above, which talk to TBA and Statbotics directly instead.

## Setup

```
pip install numpy pandas matplotlib requests statbotics jupyter
```

Then open either notebook in Jupyter or VS Code's notebook UI and run the cells
top to bottom. A `.venv/` exists in this repo but its `pyvenv.cfg` points at a
Python install that's no longer present — recreate it (`python -m venv .venv`)
and install the packages above, or just use whatever interpreter is on `PATH`.

## Credentials

Both notebooks talk to The Blue Alliance, which requires an API key
(`X-TBA-Auth-Key`). Nothing is hardcoded — each notebook looks it up in order:

1. the `TBA_AUTH_KEY` environment variable,
2. a `.tba_auth_key` file in the repo root (gitignored, never committed),
3. an interactive `getpass` prompt as a last resort.

Get a key from <https://www.thebluealliance.com/account>. If you add a new
notebook that needs TBA or another external API, follow this same lookup pattern
rather than hardcoding a key.

## Editing these notebooks

Each notebook is meant to be edited in place for the event/team/season/district
you care about right now — there's no shared config file. The knobs to change are
called out at the top of each notebook's first code cell (`event`/`my_team`/`year`
in `FRC_TBA_stats.ipynb`; `TEAM`/`TARGET_YEAR`/`DATA_YEAR`/`DISTRICT_ABBREVIATION`/
`PREFERRED_EVENT_CODES` in `FRC_District_Event_Planner.ipynb`).
