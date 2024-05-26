# SpiceNet Native Liquidity Standard

We propose a rollup-native module called the "native liquidity standard" to let market creators support markets by "seeding" temporary liquidity with the help of an associated vault that either the creator, or his/her co-creators, or supporters can fund.

This is a natural use-case of enshrined vaults in spicenet. Vaults allow for permissionless deployment of concentrated pools focused towards a specific purpose, or a strategy.

## The Problem

Liquidity in DeFi has always been hard to obtain. With very less market makers willing to quote at reasonable widths, meaningful volume has been hard to garner.

Market Makers demand for obnoxious amounts of payment, in the form of incentives, rebates and token allocations. This often puts a project/community in jeopardy.

Secondly, liquidity provided by third parties is often inefficient. Market Makers tend to size down their depth without any prior warnings, and this causes a drawdown in liquidity that is already meager, compared to CEXs and TradFI.

Market Makers also tend to prefer a faster execution environment, meaning freedom from the slow and sluggish EVM Rollups. In a faster environment, depth rebalancing can be done extremely easily, thus preventing market makers from toxic flow.

## The Solution

The ideal kind of solution that we would expect for is a community-owned-and-operated liquidity provisioning solution, that quotes at fair spreads, acts as a public good for the community and the ecosystem as a whole, is efficient, predictable, and understandable, and lastly, is based on a fast execution environment.

Using SpiceNet, we offer users a blazing fast, yet decentralized execution environment, designed for sleek trading experience, and boosting operational efficiency. This means that users can obtain E2E latencies of upto 50ms, and with minimal interruptions, as common edge-cases known to disrupt exchange operation, such as stale mark prices, rollover issues, etc, have been solved with the help of a custom state transition function(see [state transition function](https://hackmd.io/@karthikpariti/spicenet))

With the goal of providing communities exactly this, we have designed the native liquidity standard. Niche communities, or the trading community itself, can decide to list permissionless markets, and choose to deploy permanent, yet withdrawable liquidity to the market to support trading.

### Liquidity Bootstrapping Phase

Market creators can choose to deploy this liquidity standard to the market by creating an "associated" vault, which can be thought of as a vault bonded to the specific market, and whose job solely is to provide liquidity to the market. The market creator can allow co-creators and the community as a whole to deposit in the vault for a week after market creation/strategy deployment. This phase is known as the "liquidity bootstrapping phase". This would provide the strategy initial capital to operate. After the first week passes, the vault becomes "permanent" for a time period of _atleast_ 3 months. This is called the permanence phase, about which we will discuss below.

### Strategy Flexibility and Community Engagement

Market creators can choose a "type" of strategy, depending on the price action of the base asset for which they are attempting to create a futures instrument for. Secondly, the standard provides flexibility for the market creator and the community to participate in liquidity bootstrapping by sactioning a week to be used for bootstrapping liquidity, using which deposits to the vault can be made.

### Permanence Phase

Then, to abide by the "consistency" objective, liquidity is locked for a time period of _atleast_ 3 months, allowing for constant liquidity in the market at the initial stages of operation. This period is known as the "permanence phase", the duration of which can be configured during market creation, with a minimum of 3 months required to be set.

### Deposit restrictions

There are no limits on individual deposits, but there is a minimum threshold required to be achieved by the vault during the liquidity bootstrapping phase, with the threshold currently set to $50,000. Meaning that a newly created vault cannot pass the liquidity bootstrapping phase successfully without gaining a minimum of $50,000 contributed liquidity in the associated liquidity vault.

What happens when a vault is unable to gain the sufficient levels of deposits required to pass the liquidity bootstrapping phase? The chain currently sets a protocol-level parameter that allows an extension of maximum of another week allowing the creator/community to cover the deficit. This would also mean that the onset of the permanence phase, which is right after the liquidity bootstrapping phase is delayed by a week. However, if the vault is unable to cover the deficit even afer the one week extension, the vault is immediately dissolved with an admin instruction. Dissolution involves refunding all of the deposits that have been made into the vault in question, and after the vault is fully emptied, the vault is set to "de-activated" mode(see [vaults](https://github.com/pepper-research/spicenet/tree/specs/specs/application/3_vaults.md).

### The market making strategy.

The actual strategy underpinning the native liquidity vaults, is a market making strategy enshrined into the rollup as a "module". Meaning that, the strategy "natively" allows for liquidity consistency, as it is built into the state transition function of the rollup. This allows for both upholding of speed desired by a market making strategy, and the consistency required by users. The market making module is built in such a way that it requires a minimum liquidity threshold, meaning that the market maker cannot actually leave the books abruptly. Every slot, a depth rebalancing is done by the market maker, with the above threshold in mind. The result, fast and ever evolving liquidity that is always consistent!

You can learn more about the market making strategy and module [here](https://github.com/pepper-research/spicenet/tree/specs/specs/application/2_native_market_making.md)
