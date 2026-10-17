# Dig to the Core: "Backpack & Shovel" Master Prompt

> **How to use this:** give this whole file to the AI developer (Claude Code) working on Dig to the Core. Work through it phase by phase (§13). The phrases in quotes are the owner's own words: keep their meaning exactly.

---

## 1. Role, mission and how to work

You're the lead developer on **Dig to the Core** (published as "DIG A HOLE | FREE ROBOT PET", placeId 109466393081367). The game today has four parts:
- **Digging:** you dig for worms.
- **Fishing:** worms are bait, and fishing at Lucky Docks catches creature eggs.
- **The pond:** eggs hatch into fish in your personal pond, and the fish earn money.
- **The island:** a small island with a hub, 6 plots and the docks.

**This redesign:**
- **Not a mining game any more:** no mining theme at all.
- **Real items:** eggs and dug-up valuables become actual items in a **backpack with a 1–0 hotbar**.
- **Lighter screen:** fewer on-screen buttons. Interacting happens through the default **E** prompt.
- **A digging economy:** dig up valuables (scrap metal up to crowns), sell them at a hub seller, and spend the money on upgrades.
- **New things to buy and earn:**
  - a Shovel NPC
  - four upgrade signs on the pond fence
  - a rare Message in a Bottle
  - a free Dig Robot.

**How to work:**
- **Autonomy:** work on your own. Only stop for things that are truly the owner's call (see §12, Decisions).
- **Code is the source of truth:** `src/` is synced into Studio with the local file server plus `execute_luau`. Test every phase in Studio: play mode, remotes, screenshots, zero script errors.
- **Keep what works:**
  - fishing, eggs, hatching, pond income
  - the island terrain and scenery (Map Phase 1)
  - saving and data
  - anti-cheat
  - the balance simulator.
- **Change, don't rebuild:** change systems rather than starting over.
- **Kid-friendly:** no pressure or guilt mechanics, no slurs, clear and friendly words, readable on phones.
- **Before the final publish:** the NEWFOUNDLAND test code ($250M + 100K gems) **must be removed** (`docs/Before_Final_Publish.md`). Remind the owner at the end.

## 2. What exists today (build on it)

| System | Where | What happens to it |
|---|---|---|
| Dig down a private shaft, depth layers (Topsoil → The Core), worms by layer | `DigService`, `LayerConfig`, `WormDatabase`, `DigController` | **Keep the dig-down mechanic and depth layers** (depth drives rarity). **Re-skin it as shovel digging, not mining** (§3). Finds become backpack items (§5). |
| Drills, Old Gus's drill shop, drill parts, Workbench | `DrillConfig`, `UpgradeService`, `ShopController` | **Replaced** by the Shovel NPC and shovel levels (§7). Remove drill parts and the Workbench. |
| Bait Bucket (`p.worms` stacks) and its bottom bar ("33/30") | `FishingController`, HUD | **Removed.** Worms become backpack items (§5, §12-A). |
| Egg Basket (`p.storage`) | `TreasureService`, `VaultService` | **Merged into the backpack.** Eggs go "directly into the backpack". |
| DIG and PLACE screen buttons | HUD / `DigController` / `FishingController` | **Removed.** Use the default E prompt at the dig spot and the pond (§6). |
| Pond nests and pond income | `VaultService`, `PlotService` (pond walls `Wall*`) | Keep. Add four upgrade signs on the pond fence (§8). |
| Gems, shop, passes, Miner's Club subscription | `MonetizationService`, `SubscriptionService`, `ShopConfig` | Keep. **Money = upgrades; gems = "for things later on"** (and cheap robot upgrades, §10). The subscription gains the infinite robot backpack. |
| Digby the Mole (paid companion pass) | `ShopController` | Superseded by the **free Dig Robot** (§10). Decide what happens to Digby owners (§12-D). |
| Celebration sound (`Sound.play("fanfare")`, about 10 callers) | `SoundController` + callers | Fix the spam (§11). |
| Hub (plaza, shops, Wall of Fame, many signs, stalls, Rift, Ruby, VIP Lounge, Home of the Week) | `PlotService.buildTown/decorateTown`, `LeaderboardService`, `RiftService`, `MonetizationService`, `HousingService`, `SceneryService` | **Strip it back** (§4). |

## 3. "This is not a mining game anymore"

Remove every mining element:
- **Words:** "miner", "mine", "Miner's…", "WELCOME, MINER!", "⛏️" icons, "drill", "pickaxe", "ore".
- **Hub props:** mine carts with gold, glowing crystals, the "DIG TO THE CORE ⛏️" monument sign.
- **Drill shop:** Old Gus's drill shop.
- **Plot sheds:** the elevator shed and wheel on each plot.
- **Mine extras:** the mine-layer cave moods (dust and embers), and pickaxe tool skins.

What replaces them:
- **The theme is a seaside island of digging up treasure with a shovel and fishing it out of the sea.**
- **Tools:** the character holds a **shovel**, not a pickaxe. Swing animations and sounds become dirt scoops.
- **The dig spot:** each plot gets a **dig spot**, a patch of dirt with a little sign, where the elevator shed was. You dig down into your own hole.
- **Depth still matters:** depth layers stay as **soil and earth strata**, renamed and recoloured to suit treasure digging, for example Garden Soil → Clay → Old Stones → Buried Ruins → … Rarer valuables come deeper down.
- **Club name:** the subscription (currently "The Miner's Club") gets a non-mining name, e.g. **"Treasure Club"**. The Roblox product stays the same.
- **Check new text and emoji:** run all new text and emoji through the Roblox emoji check (no newer emoji like 🪨🪶).

## 4. The hub: "remove most of it, keep a couple of important aspects"

**Keep:**
1. **The spawn and a centrepiece**, the fountain. Replace the monument's mining sign with something neutral, e.g. a statue of a fish or a treasure chest.
2. **The General Seller** (new, §7): sells valuables.
3. **The Shovel NPC** (new, §7): buys and upgrades your shovel.
4. **Way-finding:** the gate and path to Lucky Docks and the signposts to the plots.
5. **The Wall of Fame** (one compact leaderboard group). Optional, see §12-B.

**Remove:**
- Old Gus's drill shop
- mine carts
- crystals
- the "WELCOME, MINER!" arch
- the leftover stalls and cart (unless they become the Seller or Shovel stalls)
- "Today's Luckiest" clutter signs
- the Home of the Week board.

**Decide (§12-B):** the Trade Plaza, The Rift, Madame Ruby and the VIP Lounge each power a system. For each one, either remove its building and the system that goes with it, or move it out of the hub.

**Look:** a calm, open plaza with two clear shops facing the spawn, plenty of open space, and greenery from the existing scenery.

**Acceptance:** from the spawn you see the fountain, the Seller and the Shovel shop, and nothing else competing for attention.

## 5. Real items: the backpack and the hotbar

- **What the backpack holds:**
  - **creature eggs** (from fishing, "directly into the backpack" when you reel in)
  - **valuables** (from digging, "straight into the backpack")
  - **worms** (bait, see §12-A)
  - **special items** (the Message in a Bottle).
- **Hotbar:**
  - **Replaces the bucket UI:** the bucket bar ("33/30") is removed and replaced by a **hotbar of 10 slots, keys 1 2 3 4 5 6 7 8 9 0**, along the bottom of the screen.
  - **Tapping:** tap on mobile.
  - **Gamepad:** shoulder buttons to cycle.
  - **Selecting a slot:** the character holds that item in their hand (a Tool, so it shows in the 3D world, e.g. holding an egg).
- **The full backpack:**
  - **Opening it:** a backpack button or the **B** key opens the full backpack (a grid) to see everything and rearrange the hotbar.
  - **Size:** it has a **capacity** (number of slots), upgradable.
  - **When it's full:** a friendly "Backpack full! Sell or place something 🎒" message, and new finds wait at the spot instead of being lost.
- **Stacking:**
  - **Eggs:** unique items (each egg has its own species, rarity and hatch time).
  - **Valuables:** can stack by type (e.g. "Scrap Metal ×12") so the backpack doesn't fill instantly.
  - **Worms:** stack by type.
- **Server rules:**
  - **Server-authoritative:** the server owns the inventory. The client only shows it and asks.
  - **Anti-cheat:** keep all checks (rate limits, argument checks, honeypots).
- **Data migration:** add a DataService version.
  - Old `p.worms` stacks become worm items.
  - Old `p.storage` eggs and fish become backpack items. Overflow goes to a one-time "Lost & Found" chest at the plot.
  - Old drill progress becomes an equivalent shovel level.
  - **Nobody loses anything.**

## 6. Interaction: E prompts instead of screen buttons

- **Remove the screen buttons:** the big **DIG** and **PLACE** buttons go.
- **Use the default E prompt:**
  - **At your dig spot:** "Dig" (E).
  - **At your pond:** "Place egg" (E), with the egg in hand or the first egg in the backpack, plus "Pick up" on nests.
  - **At NPCs and signs:** "Shop", "Sell", "Upgrade".
- **Visibility:** prompts appear only when you're close (default Roblox ProximityPrompt look), and only for your own plot.
- **Fishing:** stays as it is (cast, bite, reel), but the catch goes into the backpack, not a basket.
- **Acceptance:** away from the dig spot and the pond there are **no dig or place buttons on screen at all**.

## 7. Digging valuables, the General Seller and the Shovel NPC

**Valuables from digging.** "Find random items of different rarities, starting from scrap metal all the way up to crowns or expensive jewellery".
- **Rarity tiers:** Common → Uncommon → Rare → Epic → Legendary → Mythic → Secret (the existing `RarityConfig`).
- **Deeper is better:** going **deeper raises the chance of rarer items and their sell value** ("increase in rarity as you go down so that they sell more"). Each depth layer has its own odds table (like `LayerConfig.odds`), shown in game.
- **Starter list** (rename and extend freely):
  - **Common:** Scrap Metal, Rusty Can, Old Boot, Bent Spoon, Bottle Cap
  - **Uncommon:** Copper Coin, Old Key, Brass Compass, Silver Spoon
  - **Rare:** Silver Bar, Pocket Watch, Gold Coin, Pearl
  - **Epic:** Gold Bar, Jewelled Ring, Ruby, Golden Chalice
  - **Legendary:** Diamond Necklace, Emerald Brooch, Sapphire Tiara
  - **Mythic:** Royal Crown
  - **Secret:** the Crown Jewels (or an Ancient Sceptre)
- **Worms keep dropping too:** digging still finds worms (bait), see §12-A.
- **The find moment:** a pop-up reveal like today, scaled by rarity, then **the item flies into the hotbar or backpack**.

**The General Seller (hub).**
- **Selling:** sells valuables for money. Use E: "Sell all valuables", plus a sell screen to pick items.
- **Lock:** never sells eggs or worms by accident. A "lock" toggle protects favourites.
- **Prices:** depend on the item and its tier. Show the total before confirming.

**The Shovel NPC (hub).** "Buy and upgrade your shovel".
- **Levels:** shovel levels give a multiplier: **1.0x, 1.5x, 2.0x, 2.5x, 3.0x, 3.5x, 4.0x, 4.5x, … and it keeps going** (+0.5x per level, no hard cap).
- **What the multiplier does:** it multiplies **digging speed** (how fast you dig down). See §12-C for how this sits with the pond-sign upgrade.
- **Price:** grows with each level. Tune it with the balance simulator so the next shovel always feels reachable with digging money.
- **Look:** each level shows a nicer-looking shovel in hand (colour or material steps).

## 8. Four upgrade signs on the pond fence

Four signs **attached directly to the pond's fence** (the pond walls). Walk up and press E to open each upgrade. They're paid with **money**, levelled, with costs that rise per level:

| Sign | Effect |
|---|---|
| 🐟 **Pet Income** | +X% income from your fish |
| 💨 **Digging Speed** | +X% dig speed (stacks with the shovel; §12-C) |
| 🍀 **Luck** | Better chance of rarer egg spawns when fishing |
| 🎣 **Reel Speed** | Faster reeling / easier reel mini-game on your rod |

- **Visibility:** only the plot owner sees the prompts (the server checks ownership).
- **What each sign shows:** its current level, the next level's effect and its price.

## 9. Message in a Bottle (rare fishing find)

- **How rare:** very rare. Roughly 1 in 150–300 catches, tuned in the sim, and never guaranteed. When fishing, it comes up instead of an egg.
- **Where it goes:** into the backpack as a special item.
- **Using it:** select it in the hotbar, then click or tap "Open". It grants a **buff** and is **removed from the backpack**.
- **The buff:** a random one, for a set time (e.g. 10–15 minutes, shown with a timer icon on the HUD):
  - **Dig Speed ×2**
  - **Hatch Speed ×2** ("so your eggs can hatch faster")
  - optionally Reel Speed or Luck.
- **Rules:** the buff stacks by extending the time, not by multiplying. It's saved, so it survives rejoining.

## 10. The Dig Robot (free for everyone)

- **Who gets it:** **everyone gets it for free**. This matches the game's name, "FREE ROBOT PET".
- **What it does:** it **digs for you**. It goes down your hole and finds valuables (and worms) with its own odds.
- **Its own backpack:**
  - **When it fills up:** the robot comes back up and puts everything in a **Robot Chest** at your plot.
  - **Emptying the chest:** you take the items from the chest with E (into your backpack) and go sell them.
  - **Then:** the robot goes back down by itself.
- **Monthly subscription:** **infinite robot backpack**. It never has to come up and keeps digging, and finds go straight into the chest.
- **Robot upgrades:** speed, backpack size, depth reach and luck.
  - **Two prices for every upgrade:** each can be paid with **money (very, very expensive)** or **gems (relatively cheap)**.
  - **Hard rules:** never pay-to-win pressure, never a timer that nags. It's a nice robot, not a chore.
- **Look:** a cute robot model (AI-generate it or use a CC0 pack). It shows on your plot with a little status bubble ("Digging… 23/40", "Full! Unloading").

## 11. Celebration sound spam fix

- **The problem:** "the celebration sound spams itself if you get multiple achievements".
- **The fix:** in `SoundController`, before playing a celebration sound (fanfare and similar), **check whether one is already playing**. If it is, **don't play the new one** ("mute itself and let the original one play"). Apply the same rule to confetti bursts.
- **Scope:** one central check, not a patch at every caller.

## 12. Decisions (defaults to use unless the owner says otherwise)

| | Question | Default |
|---|---|---|
| A | **Worms:** still bait? Where do they live? | Keep worms as bait (they drive egg rarity). They're **backpack items stacked by type**. Holding the rod uses the worm you have selected (or your best one). |
| B | **Hub systems:** Trade Plaza, The Rift, Madame Ruby, VIP Lounge, Wall of Fame | Keep the **Wall of Fame** (compact). Move the **VIP Lounge** door to a small spot off the plaza edge. **Hide** the Trade Plaza, Rift and Ruby buildings and turn their systems off for now. Their code stays, behind flags, like the parked Raids/Lock. |
| C | **Two "dig speed" upgrades:** the shovel and the pond sign | Both apply and multiply together. **Shovel** = big tier jumps (1.0x, 1.5x, 2x…) from the NPC. **Pond sign** = small cheap steps (+5% per level). |
| D | **Digby the Mole owners** (paid pass), now that a free robot exists | Keep Digby as a cosmetic companion for owners, and give owners a bonus robot skin plus gems as thanks. Never take something paid away. |
| E | **Robot speed vs the player** | The robot digs at about 40% of the player's speed at level 1, so playing yourself is always worth it. |
| F | **Backpack size** | 20 slots to start, upgradable (money). The hotbar shows the first 10. |

## 13. Phases and deliverables (test each one in Studio before moving on)

1. **R-Inv: backpack and hotbar.**
   - **Build:** the inventory model, the hotbar (1–0) and the backpack screen.
   - **Wire it up:** eggs from fishing go into the backpack, placing an egg works from the backpack, the bucket bar is removed, and the data migration runs.
2. **R-Prompt:** remove the DIG and PLACE buttons, and add E prompts at the dig spot and the pond.
3. **R-Unmine:** remove all mining elements and re-skin to shovel digging (§3).
4. **R-Hub:** strip the hub back (§4), and add the General Seller and Shovel NPC buildings.
5. **R-Treasure:**
   - **Valuables:** the database, art (§14), dig drops by depth, selling, and shovel levels.
   - **Tuning:** retune the balance sim (`tools/BalanceSim.luau`) for the new money flow (selling valuables plus pond income).
6. **R-Signs:** the four pond-fence upgrade signs (§8).
7. **R-Bottle:** Message in a Bottle and the buffs (§9).
8. **R-Robot:** the Dig Robot, its chest, the subscription infinite backpack, and money/gem upgrades (§10).
9. **R-Polish:**
   - **Fixes:** the sound-spam fix (§11).
   - **Checks:** a full play-through, phone layout checks and Low Detail checks.
   - **Docs:** the report and screenshots.
   - **Reminder:** remind the owner about saving the place, publishing and **removing NEWFOUNDLAND**.

**Each phase ends with:**
- **Tests:** compile 100%, sync, play-test with 0 errors, and screenshots.
- **Report:** a short report in `docs/` (what changed, what was tested, what's left).
- **Memory:** an updated memory note.

## 14. Art: free asset packs for the valuables ("from scrap metal to junk right up to expensive jewellery")

Use CC0 packs first, check each licence on the creator's own page, record them in `docs/World_Asset_Sheet.md`, and bundle them with Blender into one Import 3D file, as with the island props.

| Pack | What's in it | Licence | Link |
|---|---|---|---|
| **Low Poly Treasure** (Elegant Crow) | 80+ models. Bronze, silver and gold **bars**, **chalices**, **coins** (stacks), **3 crowns** in 3 metals, **7 gems**, a chest. All share one texture. | CC0 (author confirms on the page; check the download's licence file) | elegantcrow.itch.io/low-poly-treasure |
| **3D Treasure Pack** (mehrasaur) | Chests, keys, coins, 18 gemstones, 13 gems, medals, scrolls, a crown, a gold brick. **No materials:** colour them in Blender. | CC0 | mehrasaur.itch.io/3d-treasure-pack |
| **KayKit Resource Bits** (free tier) | 75+ models. Wood, stone, iron, copper, silver, gold **ingots**, crates, barrels (coins and gems are "Extra" only) | CC0 | kaylousberg.itch.io/resource-bits |

- **Junk tier:** scrap metal, rusty can, old boot, bent spoon and bottle cap aren't in a confirmed free pack. **Generate them with Roblox's AI model generator** (as done for the eggs, boat, palm, gull and fish), or check Kenney's Survival Kit.
- **Never:** ripped or re-uploaded models from other games, and no Creator Store models with scripts.

## 15. Quality bars
- **Phones:**
  - The hotbar fits a phone screen (10 slots at 44 px minimum, or two rows).
  - Prompts are tappable.
  - 60 fps on mid-range phones with Low Detail available.
- **Security:** every purchase, sale and inventory move is checked on the server. No client-trusted values.
- **Data:** no data loss. The migration is tested with an old-format profile.
- **Kid-friendly:** friendly wording, no nagging, no fake timers or countdown pressure, and gems never required to progress.
