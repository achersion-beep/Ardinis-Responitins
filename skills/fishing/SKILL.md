---
name: fishing-skill-tree
description: Design talent/skill tree systems for casual fishing games or random-collection idle games with rarity tiers, post-processing skill pipelines, and branch-based player progression. Provides a complete 12-skill GDD template including dependency tree, execution priority, behavioral economics design, economy balance spreadsheet, and Monte Carlo simulation outline.
agent_created: true
---

# Fishing Skill Tree Design System

## Purpose

Provide a complete, reusable design template for a 12-skill talent tree built for casual fishing mobile games. The system models a 4-tier, 3-branch tree where all skills operate as post-processing layers that wrap around existing fish pool logic — never modifying the underlying database.

## When to Use

- Designing a new skill/talent tree for a fishing game or similar random-collection idle game
- Evaluating whether a proposed skill system can work without database changes
- Balancing an existing economy that includes rarity tiers (common/rare/epic)
- Adding behavioral economics hooks (variable rewards, social proof, commitment devices) to progression systems
- Creating a GDD or tuning spreadsheet for a post-processing skill pipeline

## How to Use

### Quick Start

Load the full GDD reference for the complete 12-skill specification:

```
Read references/fishing-skill-tree-gdd.md
```

For balance and tuning, load the economy spreadsheet:

```
Read references/economy-balance.md
```

### Design Workflow

1. **Understand the base system**: Confirm the underlying rarity ratios (e.g., 1-star:2-star:3-star = 100:10:1) and the core catch loop (cast → wait → catch/empty → collect)

2. **Choose the number of branches**: The template uses 3 (Quality / Efficiency / Luck). Adjust to match the player personas in your game

3. **Map the dependency tree**: Use the text-based tree diagram in the GDD as a starting point. The key principle: each branch should have a clear "entry skill" (T1) → "amplifier" (T2) → "climax" (T3) → and converge at T4

4. **Define the execution pipeline**: Order matters. Skills that determine "whether fish exist" (rescue/anti-empty) must run BEFORE skills that modify quality or quantity

5. **Set [PLACEHOLDER] values**: All numeric values start as hypotheses. Use the economy balance reference to define min/max ranges and inflation detection thresholds

6. **Run paper simulation**: Before writing code, simulate 1000+ casts to verify the economy doesn't break at any skill combination

### Key Design Principles Embedded in This Template

- **Post-processing only**: Skills wrap around results; they do not modify the database/pool logic
- **Player personas drive branches**: Each branch appeals to a different motivation (collection, efficiency, luck)
- **Scarcity guards**: The rarest tier (3-star / S12 Divine) uses extremely low probabilities + daily caps
- **Behavioral economics**: Variable reward schedules, social proof (server broadcasts), commitment devices (S11→S12 being "one step away")
- **System interaction matrix**: Every skill pair should be classified as "intended", "acceptable", or "bug" before implementation

### Files

| File | Purpose | Load When |
|------|---------|-----------|
| `references/fishing-skill-tree-gdd.md` | Complete 12-skill GDD with dependency tree, individual specs, execution pipeline, player paths, and system interaction matrix | Main reference — load for all design work |
| `references/economy-balance.md` | Balance spreadsheet with value ranges, event pool breakdowns, inflation detection metrics, and Monte Carlo simulation outline | Load for tuning, balancing, or adding new skills |
