# Fighting Island Arena — Design Log

A cartoonish, kid-friendly **capture-the-flag** shooter set on the island of Unguja
(Zanzibar). Two teams (Red vs Blue), slow dodgeable bullets, and "tags" instead of
kills (no blood/gore — a hit is a tag, not a death). The whole game is a single
`index.html` plus one vendored library. It auto-deploys to **Vercel** on every push
to GitHub `kiwi-pete/ctf-shooter`.

## Engine
- **Three.js r128**, the classic global-script (**UMD**) build, vendored at
  `vendor/three.min.js` and loaded with a plain `<script>` tag (`window.THREE`).
- **True 3D, third-person** follow camera, with a **first-person bullet-cam** when
  you shoot (see Controls).
- Everything in the world — terrain, water, soldiers, trees — is built **procedurally
  from core `THREE` primitives** (no external textures or models). The map is a
  **procedural heightfield**, not a flat plane.

### TODO: DEPENDENCY
- **`vendor/three.min.js` — Three.js r128 (UMD / global build, ~600 KB), committed.**
  This is the project's one external dependency.
- **Why this build:** the r128 global script attaches `window.THREE` and works on
  both `file://` (double-click `index.html`) and HTTP/HTTPS (Vercel). ES-module
  Three.js fails to load from `file://` in Chrome, so it is deliberately not used.
- The game may stream assets in future (the offline requirement has been relaxed),
  but as of Increment 2.2 everything is still **procedural / asset-free**, so it
  continues to run fully offline by double-clicking.

## Controls
- **Mouse** — look (yaw + clamped pitch). Click the canvas to grab pointer-lock; **Esc** releases.
- **WASD / arrows** — move relative to facing.
- **Space or Left-click** — shoot. Either one **snaps the camera to a first-person
  bullet-cam** down the front of the gun and holds there until the bullet hits
  something or reaches the end of its range, then reverts to the third-person view.
- **G** — fire a rocket (5 per match).

## Flag mechanic — A (LOCKED)
- **Blue flag + Blue base at Nungwi (north).** **Red flag + Red base at the southern
  end** (between Makunduchi and Kizimkazi).
- Touch the enemy flag to pick it up (it rides above the carrier); carry it home and
  touch your own base/flag to score (+1) — **but your own flag must be home to count**.
- A tagged carrier drops the flag; it returns home after ~5 s (or instantly if the
  owning team touches it).
- HUD shows the Red vs Blue capture count. **First to 3 captures wins** → overlay + Restart.
- The central-hold variant was not built. No round timer this increment.

## The map — Unguja (Zanzibar)
- A **3D heightfield** whose coastline approximates Unguja: elongated north–south,
  wider in the north, tapering south, with the **Chwaka Bay / Michamvi peninsula** on
  the central-east coast and the **Fumba + Kizimkazi finger peninsulas** in the SW.
- **Hills & high ground** rise inland (a central hill, NW highland, southern hill,
  northern rise — up to ~110 units), with **sandy beaches** at the waterline and a
  **rocky tint on steep faces / peaks**. Players, bots, bullets, flags and the camera
  all follow the terrain height. The coastline is still a wall (movement clamped to
  the island polygon).
- The surrounding **sea is animated** — rolling swell with whitecaps, plus
  **breaking-surf rings** at several beaches.
- **Scale & pace:** north–south length ≈ **6000 world units**; walk speed ≈ **83.5
  units/sec** → ~72 s to sprint tip-to-tip. Bullets are fast (≈400 u/s) but still
  clearly visible and dodgeable; they're also blocked by rising terrain.
- **13 villages** as landmarks, each a cluster of low-poly huts with a billboarded
  name label that faces the camera: Nungwi, Kendwa, Matemwe, Kiwengwa, Stone Town,
  Michamvi, Bwejuu, Paje, **Jozani**, Jambiani, Fumba, Makunduchi, Kizimkazi.
- **Jozani is a forest you walk *through*** — dozens of individual trees, each a
  trunk you collide with and weave between (plus a second wood on the NW highland).
  Other villages and scattered rocks act as cover (block movement and line-of-sight).

## Characters & combat
- You play a **low-poly soldier**: olive uniform, tactical vest, helmet with a
  **team-coloured band**, backpack, boots, and a rifle held forward (the rifle is the
  viewmodel you see in the first-person bullet-cam).
- Two teams, **3 AI bots each**. Bots pick the nearest enemy, keep rough distance,
  and shoot on a cooldown (and fight each other). One "runner" per team also goes for
  the enemy flag.
- **Rocket launcher (G)** — 5 per player per match: a slower projectile that flies
  ~320 units then **detonates with an area blast** that tags every enemy nearby.
  Bots use rockets occasionally. Friendly fire is off (bullets and rockets).
- A hit is a **tag**: brief flash, freeze, then **respawn at your own base after ~1 s**.
  No health bar.

## Build increments
- **Increment 1**: Top-down rectangular arena (2D canvas), WASD + mouse-aim movement,
  slow-bullet shooting, two teams with AI bots, tag/respawn (no scoring or flags).
- **Increment 2**: Full rewrite to **true 3D (Three.js r128)** — third-person camera,
  pointer-lock mouse-look, **Zanzibar (Unguja) island map** with 13 villages, and
  **capture-the-flag mechanic A** (first to 3 wins).
- **Increment 2.1** (tuning + weapon): island scaled to 3× (~6000 units), faster
  movement, bullet speed & range ×2, and the **rocket launcher (G)**.
- **Increment 2.2** (terrain + feel): replaced the flat island with a **procedural
  heightfield — hills & high ground, sandy beaches, animated sea with whitecaps and
  breaking-surf rings**; **Jozani became a walk-through forest** (individual tree
  collision); walk speed doubled again (≈83.5 u/s); shooting (Space or click) now
  switches to a **first-person bullet-cam** that follows the shot and reverts when it
  lands; and the player became a **detailed low-poly soldier**.
