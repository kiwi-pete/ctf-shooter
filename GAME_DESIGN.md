# Fighting Island Arena — Design Log

A cartoonish, kid-friendly **capture-the-flag** shooter. Two teams (Red vs Blue),
slow dodgeable bullets, and "tags" instead of kills (no blood/gore — a hit is a
tag, not a death). The whole game is a single `index.html` plus one vendored
library, so it is playable by **double-clicking `index.html` locally (offline)**
and is also served over HTTPS via **Vercel** (auto-deploys on every push to the
GitHub repo `kiwi-pete/ctf-shooter`).

## Engine
- **Three.js r128**, the classic global-script (**UMD**) build, vendored at
  `vendor/three.min.js` and loaded with a plain `<script>` tag (exposes
  `window.THREE`).
- **True 3D, third-person** — you see your own character from behind via a
  follow camera positioned behind and slightly above the player.
- Custom **WASD + pointer-lock mouse-look** controls — no OrbitControls or any
  add-on files. Everything in the world is built from core `THREE` primitive
  geometries (boxes, spheres, cones, cylinders, an extruded island polygon).

### TODO: DEPENDENCY
- **`vendor/three.min.js` — Three.js r128 (UMD / global build, ~600 KB), committed to the repo.**
  This is the project's **one allowed external dependency**.
- **Why this exact build:** the r128 global script attaches `window.THREE` and
  works on **both** the `file://` protocol (double-click `index.html`) **and**
  over HTTP/HTTPS (Vercel). ES-module Three.js (`three.module.js` + import maps)
  fails to load from `file://` in Chrome, which would break double-click-to-run —
  so it is deliberately **NOT** used.
- No build step, no npm, no server, no other libraries, no other external
  assets. (The Google Fonts `@import` is cosmetic only and degrades gracefully to
  a system font when offline.)

## Flag mechanic — A (LOCKED)
Two flags, carry-the-enemy's-home-to-score.
- **Blue flag + Blue base at Nungwi (north).** **Red flag + Red base at the
  southern end** (between Makunduchi and Kizimkazi). Bases at opposite ends make
  the ~2-minute run the core of the game.
- Touch the enemy flag to pick it up; it rides visibly above the carrier.
- Carry it home and touch your own base/flag to score (+1) — **but your own flag
  must be home for the capture to count**.
- If a carrier is tagged, the flag drops where they fell and returns to its home
  base after ~5 s (or instantly if the owning team touches it).
- HUD shows the Red vs Blue capture count. **First to 3 captures wins** → overlay
  with the winning team and a Restart button.
- The central-hold variant was **not** built. No round timer this increment.

## The map — Unguja (Zanzibar)
- A flat **island mesh whose outline approximates Unguja's coastline**: elongated
  north–south, wider in the north and tapering toward the south, with a
  pronounced **central-east peninsula enclosing a bay (Chwaka Bay / Michamvi)**
  and **two finger peninsulas in the south-west (Fumba and the Kizimkazi tip)**.
  A blue **sea** plane surrounds it; distance fog hides the horizon.
- Players cannot leave the island — the **coastline is a wall** (movement is
  clamped to the island polygon; bullets simply fly out over the water and
  despawn).
- **Scale & pace:** north–south length ≈ **2000 world units**; run speed ≈ **16.7
  units/sec**, so a straight sprint from the southern tip to the northern tip
  takes **~120 seconds**. The long traversal between the opposite-end bases is the
  core of the game (bullet speed is ~12× run speed — slow enough to watch and
  dodge).
- **13 villages** as landmarks (not objectives), each a cluster of simple
  low-poly huts plus a **floating name label that always faces the camera**
  (billboarded canvas-texture sprite): **Nungwi, Kendwa, Matemwe, Kiwengwa,
  Stone Town, Michamvi, Bwejuu, Paje, Jozani (inland forest), Jambiani, Fumba,
  Makunduchi, Kizimkazi.** Villages and a few scattered rocks act as **cover** —
  they block movement and break bots' line of sight.

## Preserved rules (from Increment 1)
- Two teams, **3 AI bots each**. Bots pick the nearest enemy, move to keep rough
  distance, and shoot on a cooldown — and they fight each other, not just the
  human. (Per-team one "runner" bot will also opportunistically go for the enemy
  flag, but combat is the bots' main job.)
- **Slow, clearly visible glowing bullets** (~12× run speed) you can watch and
  dodge.
- A hit is a **tag**: brief flash, freeze, then **respawn at your own base after
  ~1 s**. No health bar. **Friendly fire off.**

## Build increments
- **Increment 1**: Top-down rectangular arena (2D canvas), player movement
  (WASD + mouse aim), slow-bullet shooting, two teams (Red vs Blue) with AI bots,
  and tag/respawn mechanic (no scoring or flags yet).
- **Increment 2**: Full rewrite of the render/engine layer to **true 3D with
  Three.js r128** (third-person follow camera, click-to-pointer-lock mouse-look,
  WASD relative to facing). Replaced the abstract arena with a **Zanzibar
  (Unguja) island map** — sculpted coastline + surrounding sea, 13 named villages
  with billboarded labels, and rocks/huts as cover — and added **capture-the-flag
  mechanic A** (two flags, carry the enemy's home to score, own flag must be home,
  first to 3 wins). All Increment-1 rules (two teams, fighting bots, dodgeable
  slow bullets, tag = respawn at base) are preserved.
