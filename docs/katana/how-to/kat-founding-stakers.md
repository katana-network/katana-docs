# Founding Stakers

Founding Stakers are early participants who commit to the network during the
launch period and maintain their position through the first 60 days. In return,
they receive a share of exit fees accumulated during the stabilization window.

## Eligibility

!!! warning
    Unclaimed vKAT is **not** eligible for Founding Staker status. You must
    actively stake to qualify. Any unclaimed vKAT that is not claimed within
    90 days of TGE will be clawed back.

Founding Staker status requires three on-chain-verifiable conditions:

### 1. Entry — Stake within 72 hours of TGE

You must stake KAT within the first 72 hours after TGE through one of two
paths:

| Path | Group | How |
|------|-------|-----|
| Pre-staking | Pre-stakers | Auto-executed by relayer into avKAT |
| Manual stake | Early stakers | Stake KAT to vKAT or deposit to avKAT within 72h |

### 2. Balance floor — Maintain your position for 60 days

Your vKAT or avKAT balance must never drop below your **founding-eligible
amount** at any point during Day 0 through Day 60.

The balance floor is a single rule that covers all exit scenarios:

- Entering the exit queue and completing a withdrawal = balance decrease
- Selling avKAT on the DEX = balance decrease
- Moving tokens from founding wallet to another = balance decrease
- If either takes you below the floor, you are disqualified

If you stake additional KAT after TGE and later partially unstake, that is
fine — as long as your balance stays at or above the founding-eligible amount.

### 3. Anti-gaming — No sell-and-rebuy

This prevents selling KAT after TGE and rebuying to inflate a position. The
snapshot for pre-stakers and early stakers is the on-chain balance at TGE.

## Important Rules

| Rule | Detail |
|------|--------|
| **Same wallet** | Founding status requires holding staked balance in the same wallet used for pre-commitment or initial stake. Sending tokens to another wallet is treated as a sell and results in disqualification. |
| **avKAT/vKAT switching** | Moving between avKAT and vKAT via the app does **not** reset Founding status. Only unstaking to liquid KAT or selling on the open market disqualifies. |
| **Minimum commitment** | 100 KAT minimum, consistent with the pre-staking threshold. |

## Founding Distribution

Exit fees collected during the stabilization window (Day 0-60) are
**accumulated, not distributed in real-time**. This prevents short-term stakers
from harvesting peak yield.

After Day 60, the accumulated exit fees are distributed to Founding Stakers
over several epochs, proportional to each staker's founding-eligible amount.

From Day 61 onward, exit fees revert to normal real-time distribution to all
active vKAT holders.

!!! note
    During the accumulation period (Day 0-60), displayed yield reflects bribes
    and protocol revenue only. When the founding distribution begins after
    Day 60, yield increases for those who stayed.

## Founding Staker vs. Vote Boost

Founding Staker status and the vote boost are related but distinct:

| Feature | Founding Staker | Vote Boost |
|---------|----------------|------------|
| **Who qualifies** | Pre-stakers, early stakers 72h | Pre-stakers only |
| **Duration** | Determined at Day 60 | Decays over 56 days |
| **Reward** | Share of accumulated exit fees | Amplified voting power and reward weight |
| **Lost by** | Breaking the balance floor or transferring to another wallet | Same conditions |

## What's Next

- [Pre-Staking KAT](kat-pre-staking.md) — How to commit before TGE
- [Vote Boost](kat-vote-boost.md) — The 3x decaying boost for pre-stakers
- [Unstake and Exit](kat-unstake-and-exit.md) — Exit fees and the
  stabilization window schedule
