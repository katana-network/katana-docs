# Vote Boost

Pre-stakers receive a temporary vote boost that amplifies their voting power
and reward weight during the first 8 weeks after TGE. The boost decays in
fixed steps, ensuring early supporters are rewarded while the system
transitions to a level playing field.

## How the Boost Works

The vote boost multiplies both your **voting power** and your
**reward weight** (for incentives and trading fees distributed via Merkl). It
applies only to your founding-eligible amount: Pre-Staked amount before TGE only.

This means a small pre-stake does not unlock an outsized boost on a large
post-TGE position. The boost is earned on the amount you actually committed before TGE.

## Decay Schedule

The boost decays in 0.5x steps every two weeks (one epoch = 14 days):

| Epoch | Period | Effective Power |
|-------|--------|-----------------|
| 1 | Day 0-14 | 3.0x |
| 2 | Day 15-28 | 2.5x |
| 3 | Day 29-42 | 2.0x |
| 4 | Day 43-56 | 1.5x |
| 5+ | Day 57+ | 1.0x (normal) |

After Day 56, all stakers operate at 1.0x — the boost fully expires and the
system enters steady state.

## Eligibility

The vote boost is available to **pre-stakers only** (those who committed KAT
before TGE via the relayer). Manual stakers who stake within 72 hours of TGE
qualify for Founding Staker status but do not receive the vote boost.

## What the Boost Applies To

- **Gauge voting weight** — Your votes on emission gauges carry more influence
- **Incentive and trading fee rewards** — Your share of Merkl-distributed rewards
  is proportionally larger
- **Founding distribution** — Your weight in the Founding Staker exit fee
  distribution

The boost applies to the founding-eligible amount only, not your entire
staked balance.

## Pre-Staker vs. Post TGE Founding Staker

The **vote boost** is the key differentiator between pre-stakers and post-TGE founding stakers. Both qualify for founding status, but pre-stakers receive significantly more:

| Benefit | Pre-Staker | Manual Founding Staker |
|---------|-----------|----------------------|
| **Founding Staker status** | Yes | Yes |
| **Share of accumulated exit fees** | Yes (boosted weight) | Yes (1x weight) |
| **3x vote boost (decaying)** | Yes | No |
| **Boosted founding distribution weight** | Yes - boost multiplier applies | No - 1x weight only |
| **35% guaranteed yield (first 350M KAT)** | Yes (capped) | No |

The 35% guaranteed yield applies to the first 350M KAT pre-staked. If organic
yield (exit fees + trading fees + vote incentives) over the first 60 days falls
below 35%, the KAT treasury tops up the difference in vKAT. Pre-stakers after
the 350M cap still receive the vote boost, founding status, and organic yield.
 Only the floor guarantee is capped.

## Suspension and Revocation

| Action | Effect on Boost | Recoverable? |
|--------|----------------|--------------|
| Hold avKAT or vKAT normally | Boost active | N/A |
| Switch between avKAT and vKAT via app | Boost active | N/A |
| Enter the exit queue | Suspended to 1.0x | Yes - cancel exit to restore |
| Cancel exit and resume staking | Boost restored | N/A |
| Complete a withdrawal | Permanently revoked | No |
| Sell avKAT on DEX below balance floor | Permanently revoked | No |
| Transfer tokens to another wallet | Permanently revoked | No |

The key principle: as long as your staked balance stays at or above your
founding-eligible amount and you remain in the same wallet, your boost is
safe.

!!! info "Balance floor"
    Your balance floor is your founding-eligible amount: ie Pre-Staked amount or 
    staked balance within 72hours of TGE. If your avKAT or vKAT balance drops below this at
    any point during Day 0-60, you lose both your vote boost and Founding Staker
    eligibility. See [Founding Stakers](kat-founding-stakers.md) for details.

## Why Decay Instead of a Flat Boost

A flat 3x boost for 90 days would make every post-TGE staker second-class for
three months, deterring new entrants. The 56-day decay:

- Creates a clean transition to steady state
- Gives pre-stakers meaningful early advantage without permanent structural
  privilege

## What's Next

- [Pre-Staking KAT](kat-pre-staking.md) — How to commit KAT before TGE
- [Founding Stakers](kat-founding-stakers.md) — Eligibility and rewards for
  long-term stakers
