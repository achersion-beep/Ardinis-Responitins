# Fishing Skill Tree: Economy Balance Spreadsheet

## Purpose

This document provides a structured balance spreadsheet format for the 12-skill fishing tree system. All values are [PLACEHOLDER] hypotheses to be validated through playtesting.

## Core Fish Pool Ratios (Database — DO NOT MODIFY)

| Source | 1-Star | 2-Star | 3-Star |
|--------|--------|--------|--------|
| Native pool odds | ~90.09% | ~9.01% | ~0.9% |
| Ratio | 100:10:1 | — | — |

## Skill Balance Sheet

```
Variable              | Base Value | Min  | Max  | Level Step | Max Level | Tuning Notes
----------------------|------------|------|------|------------|-----------|-------------------
S1 Double Rate        | 15%        | 10%  | 35%  | +3%        | 7         | If too high → inflation risk
S2 Rescue Rate        | 20%        | 15%  | 50%  | +5%        | 6         | >60% destroys randomness feel
S3 Time Reduction     | 15%        | 10%  | 35%  | +3%        | 7         | >50% makes waiting meaningless
S4 1→2 Upgrade        | 10%        | 8%   | 25%  | +3%        | 5         | Too high devalues 2-star fish
S5 Combo Rate         | 12%        | 8%   | 25%  | +2.5%      | 6         | Watch for resource inflation
S6 Luck Bonus         | +3%        | +2%  | +9%  | +1.5%      | 4         | Absolute % addition, not multiplier
S7 Replace Rate       | 8%         | 5%   | 20%  | +2.5%      | 5         | Max replaces also scales
S7 Max Replaces       | 2          | 1    | 4    | +1 per 3lv | —         | Hard cap per cast
S8 2→3 Upgrade        | 5%         | 3%   | 12%  | +1.5%      | 5         | Guardian of 3-star scarcity
S9_A Trigger Bonus    | +5%        | +3%  | +9%  | +1%        | 4         | Must not exceed S6 value
S9_B Time Multiplier  | 1.5x       | 1.3x | 2.0x | +0.15      | 4         | Extreme: 35%×2.0=70% reduction
S9_C Combo Threshold  | combo≥3    | —    | —    | —          | —         | Only activates at ≥3 combo
S10 Event Rate        | 2%         | 1%   | 5%   | +0.8%      | 4         | Must be rare for ritual feel
S10 Treasure Sub-rate | 3%         | 1%   | 5%   | —          | —         | Within 100% event pool
S12 Divine Rate       | 0.5%       | 0.3% | 1.5% | +0.25%     | 4         | Daily cap: 1 recommended
```

## Event Reward Pool Breakdown (S10)

| Reward Tier | Probability | Contents |
|-------------|-------------|----------|
| Standard | 60% | 1× 3-star fish |
| Good | 25% | 2× 2-star + 3× 1-star |
| Great | 12% | 1× 3-star + 2× 2-star |
| Abyssal Treasure | 3% | 1× 3-star + 3× 2-star + 5× 1-star + special item |

## Inflation Detection Metrics

Monitor these per-active-player-per-day:

| Metric | Danger Threshold | Action |
|--------|-----------------|--------|
| 3-star fish / player / day | > 5.0 | Nerf S8 and/or S10 rates |
| Total fish / player / day | > 200 | Check S5 combo chains |
| Empty cast rate (with S2) | < 5% | S2 rescue rate too high |
| S12 global triggers / day | > 0.1% of DAU | Reduce S12 base rate |

## Monte Carlo Simulation Outline

To validate before code, run paper simulation:

1. Simulate 1000 casts with NO skills → baseline distribution
2. Add S1 only → expected fish count increase
3. Add S1+S4 → expected 2-star increase
4. Add full quality path → expected 3-star per session
5. Add S5 chain → check for infinite loop potential
6. Full tree → check for economy solvency

Flag any scenario where 3-star fish / session exceeds 10% of total yield.
