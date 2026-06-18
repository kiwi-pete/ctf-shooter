# Fighting Island Arena — Design Log

A cartoonish, kid-friendly **capture-the-flag** shooter set on the island of Unguja
(Zanzibar). Two teams (Blue north vs Green south — you pick a side on the start
screen), dodgeable bullets, and "tags" instead of kills
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
- **Blue flag + Blue base at Nungwi (north). Green flag + Green base at the southern
  end.** On the **start screen you choose your side — Blue (start at the top) or Green
  (start at the bottom)** — and the AI takes the other team. Grab the enemy flag, carry
  it home, touch your base to score — **your own flag must be home**. A tagged carrier
  drops it (returns after ~5 s). **First to 3 wins.** (Difficulty/reinforcements escalate
  near whichever flag is the enemy's, so it works from either side.)

## The map — Unguja (Zanzibar)
- A **3D heightfield** matching Unguja's coastline (Chwaka Bay / Michamvi peninsula,
  Fumba + Kizimkazi SW fingers), with **hills & high ground**, **sandy beaches**, and
  rocky steeps. Everyone + the camera follow the terrain. The coastline is a wall.
- The **sea is animated** (swell + whitecaps) with **breaking-surf rings** at beaches.
- **Scale & pace:** ~**18000 units N–S** (a big, explorable island); walk speed **83.5 u/s**
  — half that while carrying the machine gun; the army car (2×) is the fast way to cross it.
- **Trees and buildings are 4× scale** (towering trees, multi-storey houses). **Trees are
  solid** — you walk around them like buildings.
- **Just 3 big hills** (one ~1/3 from the top, one ~1/3 from the bottom, one east of Stone
  Town) with otherwise gently-undulating ground, so you can see across the island from a
  summit. A **sun** and a few **cumulus clouds** sit in the sky as reference points.
- **13 villages** are real **walkable towns**: each is a grid of **12+ houses** (Stone Town
  20+) with **streets you walk/drive down** and per-building collision. Billboarded labels.
  **Jozani is a forest you walk through** (individual tree collision); rocks are cover too.

## Weapons & combat
- **Soldier** model: olive uniform, vest, helmet with team-coloured band, backpack,
  boots; carries a rifle (the pistol) and a **shoulder-mounted rocket launcher**.
- **Pistol bullets** — very fast (≈4800 u/s, dodgeable at range), shooting snaps to the
  first-person bullet-cam.
- **5× scope zoom** — **double-click** (desktop) or **double-tap FIRE** (touch) to look down
  a gun scope: the view magnifies **5×** with a scope reticle, and you keep aiming as normal.
  Double-click / double-tap again to zoom back out.
- **Rocket launcher (G / 🚀)** — **unlimited ammo, one shot every 10 s**. Fired from
  the shoulder; the rocket now **flies dead straight** (no arc) and reaches **3× as far**,
  **detonating with an area blast** that tags every enemy nearby. HUD shows the reload
  countdown. (Only the **tank cannon** still lobs an arcing shell.)
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
- **Squad AI:** fighter-bots are **paired up** and regroup with their buddy, **spread out**
  so they don't bunch, **approach off-axis (flank) and sneak**, and **take a knee (crouch)**
  to fire when an enemy is in sight — crouching slows them and shrinks their profile.
- **Jump** and **crouch** (crouch lowers your stance, slows you, and shrinks your profile).
- **Tank (E to get in/out):** an open-top 4×4 with a **mounted cannon** parked by the
  Blue base. It **drives at ×2 walk speed**, the camera pulls back, and the cannon aims
  where you look. The cannon **lobs a rocket-equivalent every 3 s at 2.5× range**; the
  camera **follows that shell from further back and slower** than your own rocket, then
  glides back to the tank view ~0.5 s after impact. Engine **overheats after 20 s** (~18 s
  to cool). It **rides over the hills** and **launches off steep crests**.
- **Hill rocket nests (E to man/dismount):** each of the 3 hill summits has a **tripod-mounted automatic rocket launcher** (labelled 🚀 ROCKET NEST). Climb to a summit, press **E** to take it over — you're pinned to the launcher, aim with the mouse, and **FIRE pumps out one dead-straight rocket every second at 5× normal range**. Press **E** to get off.
- **Enemy look:** the enemy team is unmistakable — **black kit with red armbands** (helmet band, chest patch and arm bands all red), versus your camo. Works whichever side you pick.
- **Enemy force ×10:** the defending team now fields **30 soldiers** from the start and reinforces up to **70** as you near their flag — a real horde to fight through.
- **Matched teams:** your own side is kept the **same size as the enemy** at all times (30 to start, scaling up to 70 as the enemy reinforces near their flag), so you're never outnumbered — allies rally from your base.
- **Drone (collect 3 in the field; switch to it in Inventory I, key 5):** a single-use
  quadcopter you pilot — **WASD flies level** (forward/back/strafe relative to heading,
  unaffected by where you look); **climb/dive with `]` (up) / `\` (down)** on keyboard, or the
  **right thumb** (slide up/down) on touch. Camera behind & above. **20 s battery**, then it
  dies, falls and explodes. **FIRE detonates it** (kamikaze); flying into the ground, a
  tree/building or an enemy also detonates it.

## Economy — gems & the base shop (per-match)
- **Gems** are a single, simple currency the **human player only** earns and spends (bots
  stay out of the economy). The current total is always on screen (**💎 top-right**).
- **Earning:** **collectible gems** (16 emerald octahedra) float around the island —
  walk over one to bank **+1**, and it instantly **respawns elsewhere** so there's always
  one to chase. **Tagging an enemy** (bullet or rocket) pays **+1**; **scoring a capture**
  pays **+10**. So both fighting and objectives feed the wallet.
- **Spending — village armouries:** every village has an **🛡 ARMOURY** (a sandbagged
  bunker with a glowing emblem). Walk within ~90 units and a compact **pit-stop shop**
  panel opens; walk away and it closes — no blocking menu, the match keeps running.
  Affordable items glow green, anything too expensive is greyed-out and **locked**, and a
  purchase **takes effect instantly**. Buy with **7 / 8 / 9** (desktop) or **tap** (touch).
- **The three items** (buy once each, last the rest of the match):
  1. **Combat Helmet — 💎5 (cheap, cosmetic).** A tactical combat helmet with a radio
     antenna — makes the soldier look the part, no stat change.
  2. **Speed Boots — 💎20 (mid).** On-foot move speed ×1.5 — noticeably faster.
  3. **Rapid Fire — 💎40 (top).** Pistol cooldown 0.4 s → 0.2 s (MG fire-rate also ~1.8×) —
     noticeably faster shooting.
- **Reset:** gems and purchases are **per-match only** — they clear on restart (speed,
  helmet and fire-rate revert, gems zero out, collectibles re-scatter). No saving between
  sessions yet.

## Graphics
- **Real-time shadows** (PCFSoft) from a single sun whose shadow camera **follows the
  player/car** in a tight ±640 box, so a 6000-unit map still gets crisp contact shadows.
- **sRGB + ACES tone mapping**, rebalanced hemisphere/sun lighting + a warm fill.
- **Procedural detail textures** (canvas-drawn, no asset files): sand grain, hut plaster,
  thatch roofs, foliage, **camo** uniforms & car, rock striations (bump), brushed metal
  (Phong specular) on guns/car. Each pattern averages near-white so it adds grain/relief
  without re-tinting the hand-painted palette.
- **3× geometry detail** (tripled segment counts across trunks, canopies, helmets, barrels,
  rockets, wheels, etc.), a **gradient sky dome + sun disc**, a **Phong specular sea**, and
  **soft radial contact-shadows** under each soldier. Rolling car wheels.

## Build increments
- **Increment 1**: 2D top-down arena, movement, slow bullets, AI bots, tag/respawn.
- **Increment 2**: true 3D (Three.js r128), third-person, Zanzibar island + 13 villages,
  CTF mechanic A (first to 3).
- **Increment 2.1**: island ×3, faster movement, ×2 bullets, rocket launcher (G).
- **Increment 2.2**: procedural heightfield (hills, beaches, animated sea, Jozani forest),
  ×2 walk speed, first-person bullet-cam on shoot, detailed soldier.
- **Increment 2.5**: solid player with a behind/slightly-high view and **bullets that
  follow the crosshair**; **health bars + armour** (a hit = −34 HP, armour halves it);
  **taller building cover**; rotating **armour & machine-gun pickups**; **inventory
  (I)** that switches weapons while slowing the game ×4; and **3 airstrikes** (a plane
  flies in and bombs the crosshair point).
- **Increment 2.4**: vertical aim (look + shoot up/down in first-person), **50%
  see-through player** so you can see ahead, a north-up **Zanzibar minimap** (you,
  bases, flags, hill rings), and subtle ground detail (**contour lines on hills +
  scattered grass specks**) for a sense of motion.
- **Increment 2.6**: **squad AI** (paired bots that spread, flank and crouch to fire);
  a drivable **army car** with a **mounted 40-round regenerating turret**, **×2 walk
  speed**, a **20 s overheat / ~18 s cool-down**, and **hill jumps**; synthesized engine,
  turret, whistle and explosion audio.
- **Increment 2.3**: ×3 bullets; **shoulder rocket launcher that arcs**, now **unlimited
  with a 10 s cooldown**; **proximity-scaled enemy difficulty + reinforcements** near the
  Red flag; **telegraphed, arcing, area-effect enemy rockets**; a pickup-able **belt-fed
  machine gun** (M to switch, half speed/turn, regenerating 20-round burst); **jump &
  crouch**; and full **iPad touch controls** (twin-stick + buttons, tap-to-start).
- **Increment 3**: a **per-match gem economy** (human only) — **collectible gems** that
  respawn, plus **+3 per tag** and **+10 per capture**, with a live **💎 counter**; **village
  armouries** opening a non-blocking **pit-stop shop** with three buy-once upgrades —
  **Combat Helmet** (💎5, cosmetic), **Speed Boots** (💎20, ×1.5 on foot) and **Rapid Fire**
  (💎40, faster shooting); everything **resets each match**.
