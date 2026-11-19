# IchthyoGenesis · De-novo Procedural Fish Lab

Based on [youssef22karam/fishes25](https://github.com/youssef22karam/fishes25). The 106-species measured atlas, 17 environments, migration, offspring and cross-breeding are retained. **The generator is new**: it no longer mutates and recolours atlas species.

## Run

- `npm install`
- `npm run dev` · `npm run build`

## De-novo generation (`src/gen/genesis.ts`)

Every click on **Generate specimen** grows a fish from first principles — no atlas parent:

1. **Ecology** – the habitat picks a diet and one of 13 body constructions (spindle, disc, arrow, eel, ribbon, bottom-hugger, globe/box, great-jaw, keel-belly, tadpole, shark-form, winged ray, side-lying flatfish). Each habitat has its own weighting, which is its identity: knifefish ribbons and sail-finned discs in blackwater, rays and flatfishes on sand, lure-jawed and whip-tailed forms in the abyss, keeled silver cruisers offshore.
2. **Outline synthesis** – the dorsal and ventral silhouette are drawn from ~16 continuous shape genes (apex positions, forehead law, nuchal hump, forceps snout, belly keel, taper law, peduncle), then measured back like an atlas outline.
3. **Organs by rule** – jaws from diet; eye size from light × body-size allometry; 10 dorsal-fin architectures × anal layouts × 12 tail types chosen from cruising speed; paired fins from gait; skin from lifestyle.
4. **Habitat signatures** – finlets, keels and bills offshore; lures in the dark; barbels in mud; periscope eyes on mudflats; fronds in kelp; adipose fins in rivers; veils and feeler pelvics in still water.
5. **Livery** – the existing pigment engine paints the result with the habitat's colour ecology.

Every decision is written to the Field Guide as a note. The **Novelty** slider: `0` = real atlas species, above that everything is de-novo (higher = rarer constructions and more extreme display organs). **Body construction** forces any of the 13 plans in any habitat. Consecutive clicks avoid repeating the construction just shown; a seed alone still reproduces a specimen (`?hab=reef&seed=…&nov=…`).

## Anatomy fixes

- **Whale shark** – the eye sits behind and above the corner of the mouth. The body model now also keeps any orbit off the oral cleft (atlas and generated heads alike).
- **Longhorn cowfish** – horns are lofted out of the carapace above each eye with a flared root and gentle curve (`Spike.horn`), steeper face profile, eye under the horn base.
- **Mudskipper** – close-set periscope eyes on raised pedestals on the skull roof (`EyeSpec.close`, `EyeSpec.stalk`); generated mudflat fishes can inherit the same construction.

## Verification

- `npx tsc --noEmit`
- `tools/genstats.ts` – generator sweep (failures, retries, diversity per habitat).
- `tools/view.ts` – software-rasterises the real engine meshes from any angle (`atlas`, `gen`, `genhead`, `plans`, `audit` modes); see the header of each file for the esbuild command.

`src/engine/model.ts`, `shader.ts`, `skin.ts` and `src/gen/pigment.ts` are also stored as parts (upstream upload limit). After editing them run `node tools/split.mjs`; `node tools/reassemble.mjs` rebuilds them from the parts.

## v21 · Discovery engine, full fin/tail catalogue, render fixes

**Every click is a discovery** (`src/gen/discover.ts`). One roll decides what the net brings up: a **living species** from the 106-species atlas (natives first; one in four is a wanderer from anywhere on Earth: seahorses, sunfish, hammerheads, coelacanths…), a **natural hybrid** of the real lineages of that water, or a **de-novo species**. Each find gets a rarity tier (Common → Mythic, or Living species) that drives the materialisation colour, the chime and the badge. The **Codex** counts the living species you have found (stored locally). Shared URLs (`&d=1`) reproduce the find exactly.

**Magic** – the specimen is woven out of light: a swarm of tier-coloured motes converges onto the real mesh vertices while a noisy, glowing dissolve front materialises the skin from the centre outward (`FRAG_REVEAL` in `shader.ts`, wall-clock timed). Then a radial flash and rings, a shimmering rarity badge, the title revealed glyph by glyph, and a synthesized WebAudio arpeggio whose brightness follows the rarity (♪ toggle).

**Morphology catalogue** (`src/gen/flourish.ts`, engine support in `fins.ts`):
- 11 new caudal types – swallowtail, sword tail, hypocercal (flying fishes), spade, double tail, crowntail, whip filament, clavus (ocean-sunfish pseudo-tail with tall opposed dorsal/anal blades), veiltail, double-emarginate, trilobed (coelacanth) – 23 in total.
- 8 new fin outlines – wing, crest, scythe, notch, saddle, plume, streamer, comb of separate spines – 27 in total.
- Gliding wing pectorals, walking "hands", lionfish fans, gourami feeler pelvics, nuchal humps, hammerhead cephalofoils, sawshark rostra, needlefish bills.

**Render fixes**
- *Head shards and cornea bubbles*: oversized generated eyes made the two orbital collars cross the forehead midline. `fitEye` (`build.ts`) limits generated, hybrid and offspring globes to the local skull half-width and depth and keeps them behind the snout tip; the measured atlas proportions are left untouched.
- *Plank-like jaws*: `jawAngle` keeps the lower-jaw tip's drop within the head depth, so great-jawed hunters open a mouth instead of hanging a slab.
- *Stretched patterns*: the albedo sheet now takes the proportions of the fish (square texels on the body: up to 4096 px along eels and billfishes, square sheets on globe and disc fishes). The shader's procedural skin detail (mottling, melanophores, warp) uses the local girth at every station (`sGirthA`) instead of one mean value, so detail stays isotropic on bulbous heads, thin peduncles and long snouts.
