# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A personal collection of Python scripts and Jupyter notebooks for pre-scouting FRC (FIRST Robotics
Competition) events for team **2429**. There is no application, package, build step, test suite, or
linter — each file is a standalone analysis run directly with Python or Jupyter. Changes are typically
made by editing constants (event code, year, team list) and re-running.

## Running the code

```
FRC_Statbotics_stats.py      # plot EPA histogram for a hardcoded list of teams
FRC_Statbitucs_stats2.py     # pull an event's teams from TBA, plot Teleop vs Auto+Endgame EPA
FRC_TBA_stats.ipynb          # larger, evolving scratchpad notebook (no markdown docs; read the code)
FRC_EPA_stats.ipynb          # EPA-focused scratchpad notebook
```

`.venv/` exists but its `pyvenv.cfg` points at a Python 3.9 install that is no longer present on this
machine — it will not run as-is. Recreate it (`python -m venv .venv`) and install `numpy`, `matplotlib`,
`statbotics`, and `jupyter` before relying on it, or use whatever interpreter is currently on PATH.

## Architecture / recurring pattern

Every script and notebook follows the same shape:

1. Get a list of team numbers for an event — either hardcoded (`FRC_Statbotics_stats.py`) or fetched
   live from The Blue Alliance v3 API (`GET https://www.thebluealliance.com/api/v3/event/{event}/teams`,
   requires an `X-TBA-Auth-Key` header).
2. For each team, call `statbotics.Statbotics().get_team_year(team, year)` to get that team's EPA
   ("Expected Points Added") breakdown for the season: `epa_end`, `auto_epa_end`, `teleop_epa_end`,
   `endgame_epa_end`.
3. Sort/aggregate with `numpy`, then plot with `matplotlib` (histogram or scatter), annotating team
   2429's own EPA in red so it stands out against the rest of the field.

When editing one of these files, the parts that actually vary between runs are: the `event` code (TBA
event key, e.g. `'2025caav'`), the `year`, the hardcoded `teams` list (when not fetched from TBA), and
2429's own EPA constants used for the annotation — everything else is boilerplate fetch/sort/plot logic
duplicated across files rather than shared.

## frcdatapy/

This is a vendored clone of a third-party repo (`github.com/isiah-lloyd/frcdatapy`, its own nested git
repo) — an unofficial wrapper for the *official* FRC Events API. **It is not used by any script or
notebook in this repo** (they call the `statbotics` package and the TBA API directly instead). Treat it
as read-only reference code, not part of this project's own logic, unless asked to wire it in.

## Secrets

Two personal API credentials are used across these files and are **not** hardcoded — each script/
notebook looks them up in order: an env var, then a local gitignored file, then an interactive
`getpass` prompt as a last resort:

- **TBA `X-TBA-Auth-Key`** (`FRC_Statbitucs_stats2.py`, `FRC_TBA_stats.ipynb`) — env var
  `TBA_AUTH_KEY`, else `.tba_auth_key` in the repo root.
- **FIRST Events API `username:api_key`** (`FRC_EPA_stats.ipynb`) — env var `FRC_API_CREDS`, else
  `.frc_api_key` in the repo root.

Both key files are listed in `.gitignore`. If you add a new script/notebook that needs one of these
APIs, follow the same lookup pattern rather than hardcoding the value.
