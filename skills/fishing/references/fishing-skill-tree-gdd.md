# Fishing Game 12-Skill Cards GDD

> A complete Game Design Document for a **12 parallel skill card** system designed for casual fishing mobile games with random catch mechanics. All skills operate as post-processing layers and do NOT modify the underlying fish pool database logic.
> **Structure**: Flat / Parallel — No tiers, no prerequisites. All 12 skills are equal-weight cards that players can freely combine.

## Design Context

- **Game type**: Fixed-water random fishing
- **Fish rarity**: 1-Star (common) → 2-Star (1:10 ratio) → 3-Star (1:10 ratio of 2-Star)
- **Core loop**: Cast → Wait → Catch/Empty → Collect → Repeat
- **Constraint**: Skills must NOT change underlying fish pool database logic
- **Skill model**: 12 independent cards, equip-and-use, free combination

## Skill Architecture

### Overview

| Property | Value |
|----------|-------|
| **Total skills** | 12 |
| **Structure** | Flat parallel cards (no tiers, no unlock gates) |
| **Categories** | 8 functional categories |
| **Execution model** | Post-processing pipeline |
| **Player freedom** | Equip any subset; mix & match freely |

### 8 Functional Categories

| # | Category | Color | Role | Skills |
|---|----------|-------|------|--------|
| 0 | Yield output | Teal #0F6E56 | Direct quantity gain | S1 Double Catch, S5 Golden Bait |
| 1 | Accuracy / Event | Blue #185FA5 | Hit rate + special events | S2 Fish Sense, S11 Deep Sea Call |
| 2 | Speed / Efficiency | Amber #854F0B | Time reduction + global buff | S3 Lightning Reel, S10 Master Intuition |
| 3 | Luck / Safety | Purple #534AB7 | Anti-empty net | S4 Lucky Charm |
| 4 | Quality upgrade | Green #3B6D11 | Star-level promotion | S7 Quality Refine, S9 Star Upgrade |
| 5 | Re-roll / Filter | Pink #993556 | Replace low-quality results | S8 Catch Filter |
| 6 | Chain reaction | Coral #993C1D | Cascade effects | S6 Combo Cast |
| 7 | Miracle event | Red #A32D2D | Ultra-rare server events | S12 Divine Fishing |

### Execution Priority Pipeline

When a fishing action completes, skills execute in this order:

```
1. S4 Lucky Charm      -- Rescue empty → 1-star
2. S3 Lightning Reel   -- Reduce wait time (parallel)
2. S2 Fish Sense       -- Boost base catch rate
3. [Fish Pool DB]      -- Original logic (untouched)
4. S5 Golden Bait      -- Weight shift on rare fish
5. S1 Double Catch     -- Quantity x2
6. S8 Catch Filter     -- Re-roll 1-star fish
7. S7 Quality Refine   -- 1-star → 2-star
8. S9 Star Upgrade     -- 2-star → 3-star
9. S6 Combo Cast       -- Trigger free re-cast
10. S11 Deep Sea Call  -- Rare event check
11. S12 Divine Fishing -- Miracle event check
12. S10 Master Intuition-- Global buff (applied to all prob checks above)
```

---

## Individual Skill Specifications

### S1. Double Catch — 双倍垂钓

**Category**: Yield output (Teal)

| Field | Detail |
|-------|--------|
| Purpose | Entry-level yield skill; immediate value perception |
| Player fantasy | "Sometimes I just catch twice as much!" |
| Input | After each fishing result is determined |
| Output | Probability P: result quantity x 2 |
| Base trigger rate | 15% ([PLACEHOLDER]) |
| Max trigger rate | 35% |
| Upgrade step | +3% per level |
| Visual feedback | "DOUBLE!" text animation + golden flash |
| Edge case | Applies to ALL star levels; executes before quality upgrades |

### S2. Fish Sense — 鱼群感应

**Category**: Accuracy / Event (Blue)

| Field | Detail |
|-------|--------|
| Purpose | Reduce empty-catch frustration; smooth the experience curve |
| Player fantasy | "I can feel where the fish are" |
| Input | Before catch result determination |
| Output | Base catch probability +X% |
| Base bonus | +10% ([PLACEHOLDER]) |
| Max bonus | +25% |
| Upgrade step | +3% per level |
| Visual feedback | Subtle water ripple effect on cast |
| Edge case | Stacks multiplicatively with other accuracy effects |

### S3. Lightning Reel — 闪电收杆

**Category**: Speed / Efficiency (Amber)

| Field | Detail |
|-------|--------|
| Purpose | Core speed skill for efficiency-minded players |
| Player fantasy | "My reel is faster than light" |
| Input | Each fishing wait timer starts |
| Output | Wait time reduced by X% |
| Base reduction | 15% ([PLACEHOLDER]) |
| Max reduction | 35% |
| Upgrade step | +3% per level |
| Visual feedback | Progress bar accelerates; "Fast!" text |
| Edge case | Multiplicative with other time-reduction effects |

### S4. Lucky Charm — 幸运护符

**Category**: Luck / Safety (Purple)

| Field | Detail |
|-------|--------|
| Purpose | Safety net against empty catches; casual-friendly |
| Player fortune | "Fortune smiles on me" |
| Input | When raw result = empty (no fish) |
| Output | Probability P: convert to 1x 1-star fish |
| Base rescue rate | 20% ([PLACEHOLDER]) |
| Max rescue rate | 50% |
| Upgrade step | +5% per level |
| Visual feedback | Green glow + "Lucky!" text |
| Edge case | Executes FIRST in pipeline; rescued fish enters normal flow |
| Note | Rescued fish is always 1-star (keeps skill balanced) |

### S5. Golden Bait — 黄金鱼饵

**Category**: Yield output (Teal)

| Field | Detail |
|-------|--------|
| Purpose | Indirectly increase rare fish appearance without touching DB |
| Player fantasy | "The big ones can't resist my bait" |
| Input | During fish pool selection (post-processing weight layer) |
| Output | 2-star and 3-star selection weight +X% |
| Base boost | 8% ([PLACEHOLDER]) |
| Max boost | 20% |
| Upgrade step | +2.5% per level |
| Visual feedback | Bait sparkle particle when casting |
| Edge case | Does NOT change actual DB probabilities; adds a weighting overlay |

### S6. Combo Cast — 连击抛竿

**Category**: Chain reaction (Coral)

| Field | Detail |
|-------|--------|
| Purpose | Create dopamine spike moments through cascading actions |
| Player fantasy | "Once I start, I can't stop" |
| Input | After each successful catch |
| Output | Probability P: trigger 1 free auto-cast (no cost) |
| Base combo rate | 12% ([PLACEHOLDER]) |
| Max combo rate | 25% |
| Upgrade step | +2.5% per level |
| Visual feedback | Combo counter "x2! x3!" + screen shake + particles |
| Edge case | Free casts CAN trigger combo again (theoretically infinite); soft cap at display x10 |
| Note | Free casts count in stats but consume no stamina/ticket |

### S7. Quality Refine — 品质提炼

**Category**: Quality upgrade (Green)

| Field | Detail |
|-------|--------|
| Purpose | Solve the "too many 1-star fish" inventory problem |
| Player fantasy | "I turn common fish into prizes" |
| Input | For each 1-star fish obtained |
| Output | Per-fish probability P: upgrade to 2-star |
| Base upgrade rate | 10% ([PLACEHOLDER]) |
| Max upgrade rate | 25% |
| Upgrade step | +3% per level |
| Visual feedback | Card white flash; star count 1→2 animation |
| Edge case | Each fish judged independently; same fish cannot upgrade twice in one round |
| Note | Only affects originally-1-star results; does not touch native 2-star catches |

### S8. Catch Filter — 渔获筛选

**Category**: Re-roll / Filter (Pink)

| Field | Detail |
|-------|--------|
| Purpose | Give players a "second chance" on low-quality results |
| Player fantasy | "I only keep the best" |
| Input | After all catches collected; scan for 1-star fish |
| Output | Up to N 1-star fish removed and re-pulled from original pool |
| Base re-roll rate | 8% ([PLACEHOLDER]) per fish slot |
| Max re-roll rate | 20% |
| Base max replace count | 2 fish |
| Max replace count (fully upgraded) | 4 fish |
| Visual feedback | Card flip animation revealing new result |
| Edge case | Re-pull goes through original DB logic; could yield ANY star level (including 3-star!) |
| Note | Distinct from S7: S7 UPGRADES existing fish; S8 REPLACES them via re-roll |

### S9. Star Upgrade — 星光升级

**Category**: Quality upgrade (Green)

| Field | Detail |
|-------|--------|
| Purpose | The holy grail of quality — 2-star → 3-star promotion |
| Player fantasy | "3-star fish? Just a matter of time for me" |
| Input | For each 2-star fish obtained (native or upgraded via S7) |
| Output | Per-fish probability P: upgrade to 3-star |
| Base upgrade rate | 5% ([PLACEHOLDER]) |
| Max upgrade rate | 12% |
| Upgrade step | +1.5% per level |
| Visual feedback | Golden pillar + gold border + "LEGENDARY!" burst |
| Edge rate | Can chain: S7(1→2) then S8(re-roll→2) then S9(2→3) in one round |
| Note | Low probability by design; 3-star scarcity must be preserved |

### S10. Master Intuition — 大师直觉

**Category**: Speed / Efficiency (Amber)

| Field | Detail |
|-------|--------|
| Purpose | Universal synergy buff; pairs well with any skill set |
| Player fantasy | "I've mastered every technique" |
| Input | Passive aura (always active when equipped) |
| Output | All equipped probabilistic skills get +X% absolute trigger bonus |
| Base buff value | +3% ([PLACEHOLDER]) |
| Max buff value | +9% |
| Upgrade step | +1.5% per level |
| Visual feedback | Subtle halo/particle around character |
| Edge case | Does NOT apply to passive skills (S3, self); only prob-triggered ones |
| Note | This is the "glue" skill that rewards equipping many active skills |

### S11. Deep Sea Call — 深渊呼唤

**Category**: Accuracy / Event (Blue)

| Field | Detail |
|-------|--------|
| Purpose | Create ritualistic "big moment" surprise events |
| Player fantasy | "I summon the secrets from the deep" |
| Input | At the start of each fishing action |
| Output | Ultra-low P: trigger "Abyss Event" with guaranteed premium reward |
| Base event rate | 2% ([PLACEHOLDER]) |
| Max event rate | 5% |
| Upgrade step | +0.8% per level |
| Abyss Reward Pool: | |
| - 60% chance | 1x 3-star fish |
| - 25% chance | 2x 2-star + 3x 1-star |
| - 12% chance | 1x 3-star + 2x 2-star |
| - 3% chance | Abyss Treasure (1x3* + 3x2* + 5x1* + rare item) |
| Visual feedback | Screen darkens to deep blue; light beam from water; special BGM |
| Note | Abyss rewards enter normal skill pipeline after being awarded (can be doubled, etc.) |

### S12. Divine Fishing — 神之垂钓

**Category**: Miracle event (Red)

| Field | Detail |
|-------|--------|
| Purpose | Server-wide炫耀点; long-term retention hook; social proof driver |
| Player fantasy | "In that moment, godhood descended" |
| Input | After ALL other skill processing completes |
| Output | Ultra-ultra-low P: trigger "Divine Miracle Event" |
| Base miracle rate | 0.5% ([PLACEHOLDER]) |
| Max miracle rate | 1.5% |
| Daily limit | 1 time per day (recommended) |
| Divine Reward (guaranteed): | 3x 3-star + 5x 2-star + 10x 1-star + 1 rare item |
| Extra effects: | Full-server broadcast "[Name] triggered Divine!" + cinematic animation + Hall of Fame record |
| Visual feedback | 5-8 second unskippable ceremony animation |
| Note | Rewards do NOT re-enter skill pipeline (final settlement) |

---

## Economy Balance Summary Table

| # | Skill | Key Param | Base | Min | Max | Step |
|---|-------|-----------|------|-----|-----|------|
| S1 | Double Catch | Trigger % | 15 | 10 | 35 | +3 |
| S2 | Fish Sense | Catch Rate+ | 10 | 5 | 25 | +3 |
| S3 | Lightning Reel | Time Save % | 15 | 10 | 35 | +3 |
| S4 | Lucky Charm | Rescue % | 20 | 15 | 50 | +5 |
| S5 | Golden Bait | Rare Boost % | 8 | 5 | 20 | +2.5 |
| S6 | Combo Cast | Combo % | 12 | 8 | 25 | +2.5 |
| S7 | Quality Refine | 1→2★ % | 10 | 8 | 25 | +3 |
| S8 | Catch Filter | Re-roll % | 8 | 5 | 20 | +2.5 |
| S9 | Star Upgrade | 2→3★ % | 5 | 3 | 12 | +1.5 |
| S10 | Master Intuition | Global Buff+ | 3 | 2 | 9 | +1.5 |
| S11 | Deep Sea Call | Event % | 2 | 1 | 5 | +0.8 |
| S12 | Divine Fishing | Miracle % | 0.5 | 0.3 | 1.5 | +0.25 |

All values marked `[PLACEHOLDER]` require playtest validation.

---

## Recommended Loadout Combinations

Players typically equip 3-6 skills simultaneously. Here are archetypal builds:

### Build A: Volume Hunter (刷子流)
S1 + S3 + S6 + S10 + S2
> Maximize catches per minute; use speed + combo + double

### Build B: Quality Collector (品质党)
S5 + S7 + S9 + S8 + S1 + S10
> Focus on high-star yields; stack all quality paths

### Build C: Safe Steady (稳健休闲)
S2 + S4 + S10 + S3 + S5
> Never go empty; consistent moderate gains with zero stress

### Build D: Miracle Chaser (赌狗流)
S11 + S12 + S1 + S10 + S6
> Go big or home; chase events and let the RNG gods decide

### Build E: Balanced General (均衡通用)
S1 + S2 + S3 + S7 + S10
> One from each category; good at everything, great at nothing

---

## Behavioral Economics Notes

1. **Variable Reward Schedule**: S11 (2%) and S12 (0.5%) create strongest dopamine loops
2. **Endowment Effect**: Letting players name/customize their skill loadouts increases attachment
3. **Social Proof**: S12's server broadcast makes all players aware miracles exist
4. **Commitment Device**: S10 (Master Intuition) rewards owning many skills → encourages collection
5. **Loss Aversion**: S4 (Lucky Charm) feels like "saving" a loss — more satisfying than equivalent gains
