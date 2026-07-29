# LFTP — Command Node Demo

Simulated run of the LFTP command screen. No hardware, no internet, no setup beyond Python.

You get a synthetic fireteam moving around El Paso County, Colorado, on a real offline
topographic map.

## Requirements

- Python 3.11+
- ~150 MB free disk

## Run it

```bash
tar xzf lftp-command-pi-20260729.tar.gz
cd lftp-command-pi/backend

python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

pip install -r requirements.txt
LFT_SIM=1 python -m app.main
```

Open **http://127.0.0.1:8765/?live**

> The `?live` matters. Without it you get static demo data instead of the running simulation.

Stop it with `Ctrl+C`.

## Things to try

**⚙ Admin** (bottom bar) — Build the org. Each node announces a 4-character ID derived from
its public key, so it can't be spoofed. Assign people into Squads → Teams, give them
callsigns, then push the table out.

**Tactical Net** (left panel) — Type an order and send it. It stays **red and pinned at the
top** until it's acknowledged, then turns green and scrolls away with the rest. Only the
responsible leader's ACK completes it — anyone else acknowledging is recorded but doesn't
close it out.

**Route + elevation** (bottom bar) — Click the map to drop waypoints. You get distance,
bearing, cumulative climb/descent, and an elevation profile — all sampled offline from DEM
data. **⌖ From person** anchors the route to a teammate's live position instead of a map click.

**Pin rail** (left edge of map) — Drop OBJ / ENEMY / IED / CASEVAC / LZ / DZ and more. Objectives
auto-number OBJ 1, 2, 3… and never recycle a designator. Click one to mark it COMPLETE and it
announces on the net.

**↔ A/B link** (bottom bar) — Pick any two contacts and ask the question that matters
on a radio net: *can these two actually talk?* It samples the terrain between them, builds
the ray between both antennas, corrects for earth curvature, and checks the 60% Fresnel
criterion at 915 MHz. You get **CLEAR / MARGINAL / BLOCKED** — and when it's blocked, where
the obstruction is, how far above the line it sits, and how high a relay would have to be to
fix it. Try **SLAYER ↔ WARLORD** (Pikes Peak to the Air Force Academy): blocked by 166 m of
front range, needing a 189 m relay — which is above the legal drone ceiling, so that link
can't be fixed from the air.

**The drone** — A quadcopter (SCYTHE) orbits the team. Click it: altitude MSL *and* above
ground (subtracted from the offline DEM — the aircraft has no barometer), battery, endurance,
and the countdown on its **time-boxed relay window**. That window is the point: a drone at
120 m has roughly 344× the direction-finding footprint of a ground node, so the relay is
armed for a bounded period and then shuts itself off. Watch it go quiet when the timer
expires — the silence is the feature. `◈ Relay LOS` draws its coverage as two rings: solid
for the range you can plan on, dashed for the optimistic horizon.

**⊞ Addons → Drone Request** — Ask for airborne relay between two elements. It works out the
altitude the link needs before you ask, and tells you when a drone physically can't solve
your problem.

**Selected-contact box** (bottom-right of the map) — bearing, range, back-bearing, grid and
elevation delta for whatever you've clicked. Stays put no matter how far you zoom in.

**Also worth a click:** `MGRS grid`, `Range rings`, `Center on me`, `⋯ Trails` (movement
history), and the `DAY / NGT / NVG / GRN` display modes top-right.

## Notes

- **Fully offline.** Pull your network cable and it behaves identically. Maps, fonts and
  libraries are all local files — nothing phones home.
- **Map coverage is El Paso County, CO.** Pan outside it and the map goes dark. That's
  expected: it's a pre-baked area of operations, not a map of the world.
- **The fine 2 m contours** (from 1 m lidar) cover a ~20×10 km box over Colorado Springs and
  the western foothills, and only appear at zoom 13+. Elsewhere you get 40 m county contours.
- **Everything is per-browser.** The org, pins and channels live in your browser's local
  storage, so you start with a clean slate and can't break anything.

## Troubleshooting

| Problem | Fix |
|---|---|
| No contacts appear | You forgot `?live` on the URL |
| `ModuleNotFoundError` | Virtualenv isn't activated, or `pip install` didn't finish |
| Port already in use | Something else is on 8765 — stop it, or edit `ws_port` in `config.yaml` |
| Map is black | You've panned outside El Paso County |
