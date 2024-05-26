# Native Market Making

Native market making is a native module constructed by the team at SpiceNet which runs a fully-equipped market making strategy that synchronizes at the speed of the chain itself, considering it is part of the chain's state transition function. This means that the strategy is fully enshrined within the chain, and it's actions are bound and verified by the state transition function. Hence, the market maker, or more specifically, the market making module can only perform actions that it's state transition function allows for, and not be arbitrary. This also means that it's actions are fully verifiable and transparent, thanks to our other efforts with the chain, such as full ZK provability, light clients, etc. We also plan to make the status of the market maker fully RPC queryable.

## The Problem

Bootstrapping liquidity is one of the most pressing issues for a new DEX. Exchanges need to design specific incentive programs, offer rebates, provide token allocations for market makers to even consider their exchange. On top of that, market makers demand consistent levels of volume for them to sustain their liquidity on the book.

Often, this behaviour may hurt the exchange initially leaving it in a vicious cycle of attracting volume to suffice liquidity, and liquidity to suffice volume. A chicken and egg problem, basically. We propose an equally satisfying alternative, native market making.

## The Solution

### Reliabiity and Uptime

Having control of one's chain has a lot of advantages. The biggest one arguably is letting the chain serve you, the application, and not the other way around. Meaning, the application can "tune" the chain such that it can let the chain run errands for the application. One such example is native market making.

Conducting market making as a verifiable, open and predictable module reduces a lot of abruptness and arbitrariness linked with modern market making. The module bounds the market making strategy to specific required depth, width and uptime limits, not allowing the market maker to completely leave the books, guaranteeing some level of minimum liquidity. This avoids a lot of liquidity mishaps that may happen when market makers suddenly pull their liquidity for a variety of reasons.

Width and Depth limits are configured based on the volatility of the base asset, and current congestion of the chain. Volatility of the base asset is derived from our in-built oracle network, while the current congestion of the chain is measured as traffic to the preferred sequencer vis a vis the traffic that the preferred sequencer is actually able to handle. This allows for dynamic evaluation of depth limits. For example, in a wildly volatile market, the depth limit can be reduced. While in a relatively less congested chain, the width limit can be reduced, to make liquidity tighter.

Uptime is a constant limit representing the percentage of times in a day that the market maker had orders on the book. This allows for a minimum consistency and upkeep guarantee for users.

The market maker's internal state updates synchronize with the state transition internal, i.e the block time of the chain, considering it is built into the chain itself. And considering that the chain itself operates at a very low block time, it ensures that the market maker can update it's state at a sufficiently fast speed and not fall prey to toxic flow.

### Verifiability

SpiceNet runs as a ZK-powered Fraud Proof-based optimistic sovereign rollup. This means that all initiated transactions are ZK-verifiable by default, broadcasted by the light client network. Users can natively run a light client and choose to actively verify the genuinety of the market maker's activities. In an off-chain orderbook environmet, this would not have been possible.

### Keeper network to actually transition state

Modules are stationary pieces of logic and are not meant to be run as async vehicles, unlike a market maker. This means that although the market maker module can place orders on the book using a certain strategy, and control the liquidity provided, it cannot asynchronously take in inputs, run the strategy on top of the inputs, and dynamically update it's liquidity.

Hence, the market maker module would need a "nudge" every chain slot that would force it to update it's state, i.e provide the market maker with a new set of inputs upon which a generic strategy would be run upno and a new set of outputs would be created(which in this case are modifications to liquidity). In principle, the market maker would accept these "inputs" in the form of transaction, and more specifically the metadata of the transaction. The strategy is a core component of the market maker module to which the inputs are applied. As inputs are generic, the strategy defines "rules" depending on the magnitudes of the inputs, directly influencing the outputs themselves. For example, if the input were to be a volatility estimate of some sort, and the strategy were to systematically downsizr the liquidity, the outputs would contain a lot of order cancels.

The market maker, although accepting generic inputs, only accepts specific "types" of inputs, defines by the "call messages" of the module. These can be thought of as the state transitions that the market making module defines explicitly. This also means that the different inputs are stored in the storage layer(Sovereign DB). There is another reason for adopting this design -- allowing anyone to query inputs received by the market maker improves accountability levels.

But, who will provide the inputs, i.e create the transactions to the market maker module? We aim to leverage our keeper network for this purpose(spec not yet finalized), with a fastest-finger-first mechanism of selecting the keeper responsible for initiating the transaction in context for the specific slot.

This way, we aim to build a fully on-chain, ZK verifiable and accountable native market making module to provide liquditiy to the books with community-provided and aggregated sets of inputs ingested by a market making strategy(the module itself).

## Use-cases

Native market making can be used for a variety of use-cases. One of the main use-cases is a market making vault, for example. Vaults are also an in-built module of SpiceNet and hence the market making module can be inherently used by the vaults module to deploy a vault strategy that provides liquidity on the books.

There are obviously many other use-cases that we are currently exploring, and encourage the community to take part in the same too.
