# Fighting Island Arena — Design Log

A cartoonish, kid-friendly **capture-the-flag** shooter set on the island of Unguja
(Zanzibar). Two teams (Red vs Blue), dodgeable bullets, and "tags" instead of kills
(no blood/gore — a hit is a tag, not a death). The whole game is a single
`index.html` plus one vendored library. Plays on **desktop (mouse/keyboard)** and
**iPad / touch** (on-screen controls). Auto-deploys to **Vercel** on every push to
GitHub `kiwi-pete/ctf-shooter`.

## Engine
- **Three.js r128**, classic global-script (**UMD**) build, vendored at
  `vendor/three.min.js` (plain `<script>`, `window.THREE`).
- **True 3D, third-person** follow camera, with a **first-person bullet-cam** when
  you shoot the pistol.
- Terrain, water, soldiers, weapons and trees are all **procedural** (no external
  textures/models). The map is a **procedural heightfield**. Still runs offline by
  double-clicking `index.html`.

### TODO: DEPENDENCY
- **`vendor/three.min.js` — Three.js r128 (UMD, ~600 KB), committed.** The one
  external dependency. The r128 global build works on both `file://` and HTTP/HTTPS;
  ES-module Three.js fails from `file://`, so it is not used.

## Controls
**Desktop:** Mouse look (click canvas for pointer-lock, Esc releases) · **WASD/arrows**
move · **Space** or **Left-click** shoot (snaps to first-person bullet-cam) ·
**G** rocket · **M** switch pistol↔machine-gun · **Shift** jump · **C** crouch.

**iPad / touch:** tap **Play** to start (no pointer-lock needed) · **left thumb** =
floating move stick · **right side** = swipe to look · on-screen buttons **FIRE**,
**🚀 rocket**, **JUMP**, **DUCK**, **WPN** (weapon switch) and **⏸ pause**.

## Flag mechanic — A (LOCKED)
- **Blue flag + Blue base at Nungwi (north).** **Red flag + Red base at the southern
  end.** Grab the enemy flag, carry it home, touch your base to score — **your own
  flag must be home**. A tagged carrier drops it (returns after ~5 s). **First to 3 wins.**

## The map — Unguja (Zanzibar)
- A **3D heightfield** matching Unguja's coastline (Chwaka Bay / Michamvi peninsula,
  Fumba + Kizimkazi SW fingers), with **hills & high ground**, **sandy beaches**, and
  rocky steeps. Everyone + the camera follow the terrain. The coastline is a wall.
- The **sea is animated** (swell + whitecaps) with **breaking-surf rings** at beaches.
- **Scale & pace:** ~6000 units N–S; walk speed **83.5 u/s** (~72 s tip-to-tip) — half
  that while carrying the machine gun.
- **13 villages** with billboarded labels. **Jozani is a forest you walk through**
  (individual tree collision); villages and rocks are cover.

## Weapons & combat
- **Soldier** model: olive uniform, vest, helmet with team-coloured band, backpack,
  boots; carries a rifle (the pistol) and a **shoulder-mounted rocket launcher**.
- **Pistol bullets** — fast (≈1200 u/s, dodgeable at range), shooting snaps to the
  first-person bullet-cam.
- **Rocket launcher (G / 🚀)** — **unlimited ammo, one shot every 10 s**. Fired from
  the shoulder; the rocket **arcs under gravity** (a lob) and **detonates with an area
  blast** that tags every enemy nearby. HUD shows the reload countdown.
- **Machine gun** — **pick one up** in the field (belt-fed model, ammo belt out the
  side). Press **M / WPN** to switch between it and the pistol. While carrying it your
  **move and turn speed are halved**. It fires a **regenerating 20-round burst**: empty
  it and it takes ~10 s to refill (regen ~2 rounds/s), so short controlled bursts
  recover faster than a full dump.
- **Enemies escalate near their flag:** the closer you get to the **Red flag**, the
  **more skilful** the red soldiers become (faster aim, faster fire, more rockets) and
  the **more of them spawn** to defend (up to 7).
- **Enemy rocket launchers** are area weapons like yours: a bot **telegraphs with an
  obvious muzzle flash**, then lobs an **arcing rocket toward you** — fast reflexes can
  dodge it.
- 3 AI bots per team (plus red reinforcements); a "runner" per team also goes for the
  flag. A hit is a **tag**: flash, freeze, respawn at base after ~1 s. Friendly fire off.
- **Jump** and **crouch** (crouch lowers your stance, slows you, and shrinks your profile).

## Build increments
- **Increment 1**: 2D top-down arena, movement, slow bullets, AI bots, tag/respawn.
- **Increment 2**: true 3D (Three.js r128), third-person, Zanzibar island + 13 villages,
  CTF mechanic A (first to 3).
- **Increment 2.1**: island ×3, faster movement, ×2 bullets, rocket launcher (G).
- **Increment 2.2**: procedural heightfield (hills, beaches, animated sea, Jozani forest),
  ×2 walk speed, first-person bullet-cam on shoot, detailed soldier.
- **Increment 2.4**: vertical aim (look + shoot up/down in first-person), **50%
  see-through player** so you can see ahead, a north-up **Zanzibar minimap** (you,
  bases, flags, hill rings), and subtle ground detail (**contour lines on hills +
  scattered grass specks**) for a sense of motion.
- **Increment 2.3**: ×3 bullets; **shoulder rocket launcher that arcs**, now **unlimited
  with a 10 s cooldown**; **proximity-scaled enemy difficulty + reinforcements** near the
  Red flag; **telegraphed, arcing, area-effect enemy rockets**; a pickup-able **belt-fed
  machine gun** (M to switch, half speed/turn, regenerating 20-round burst); **jump &
  crouch**; and full **iPad touch controls** (twin-stick + buttons, tap-to-start).
