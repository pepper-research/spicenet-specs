# Background

:::info
:information_source: This paper aims to put forward PepperDEX's app-rollup, SpiceNet, powered by the Sovereign SDK and Rome Protocol, which unlocks increased throughput and performance required by a derivatives protocol(in our case, dexterity). The rollup is designed to be a sovereign rollup posting DA to Celestia. Using a state transition function written natively in Rust, we are able to bolster performance and offer a CEX-like experience. Using rollup-native customizations, we are able to increase operational efficiency and offer overall better UX.

Lastly, our rollup will be integrated with the Rome Protocol. By sharing a unified bridge on L1 and using the Remus API of Rome, we are able to offer atomic features such as cross-rollup bridging, arbitrage and MEV, which are always desirable for a DEX app-rollup.
:::

## Speed is always the problem!

Speed is of essence in trading. Avenues which are inherently faster have been valued over ones that weren't as fast. Traders have a few basic demands from the exchange that they trade on, and choose an ideal exchange accordingly. Firstly, they demand good onboarding, sleek UI/UX and lucrative features(including incentives). However, none of these matter if the exchange itself is clunky, unpredictable, and slower than others in the market. Onboarding can be solved, UI/UX can be improved, lucrative features can be copied, but speed/performance is one facet that underpins the mindset of a trader.

We are seeing this trend play out in current DeFi, and especially derivatives. Solana, for the past few months has become the home of finance. It's spot exchanges, like Orca, Lifinity and the Jupiter Aggregator consistently compete with the likes of Uniswap or even outmatch them occasionally. However, the trend doesn't seem to be playing out in derivatives, where the majority of volume still lives on dYdX, Hyperliquid, Vertex, Aevo, etc. Solana derivatives DEXs do good, no doubt, but the same momentum of spot does not seem to replicate in derivatives. We think the reason is this -- Spot is analogous to a lot of memecoin activity, and it doesn't matter if you were a few seconds late from apeing into a new memecoin. Secondly, there haven't been many high-performance execution environments built for spot trading. The dYdX chain, Hyperliquid L1, Aevo L2 have been designed for derivatives trading(hyperliquid however is also expanding into native spot assets). Moreover, spot trading has gained more traction in traditional swap interfaces like those of Jupiter and Uniswap, unlike complex interfaces like Vertex(which may also explain why Vertex does significantly less spot volume than Jupiter for example).

However, in perpetuals, execution and speed matters a lot. This tweet [here](https://twitter.com/beast_ico/status/1778864658834669613) and a series of other tweets represent fellow traders venting their frustration against Jupiter versus Hyperliquid, with the main complaint being Jupiter is not as fast, while Hyperliquid is. Although not evident, "good" UI/UX still weighs a lot on the platform itself being performant. We think the comparison with Jupiter is representative, and that it is reflective of every other derivatives platform on Solana, including us. Jupiter being the biggest, is just a better comparison to EVM giants.

Unlike consensus, we don't believe the way to improve UI/UX is to necessarily only improve the L1. No mistake, the L1 is a crucial factor for any app, but there is only so much improvement one can do on the L1. Even if speed were to be solved hypothetically, there are a lot more issues that we'll discuss below.

## The lesser known problem -- Operational Efficiency

Performance, UI/UX and lucrative features were known to be the holy grail for a DEX. But, they have come at a sacrifice, operational efficiency. This problem is lesser seen in spot exchanges, and more in derivatives exchanges as they have a lot more moving parts than their counterparts.

Derivatives exchanges need to maintain a lot of infra, which at times can be inefficient, leading to bad UX for users. For example, to keep mark prices up-to-date with the oracle, many exchanges like PepperDEX run "cranks" which can be thought of as bots that are responsible for calling specific on-chain instructions that are responsible for "updating" mark prices. Cranks during many instances can be slow and do not update the state at the desired interval, either because of an edge case or because the bot was unable to land the transaction. Most recently, the Solana blockchain fell prey to one of the most insane traffic seen in crypto, ever. At that time, exchanges really felt the heat to make sure the unreliable part of their infra functioned smoothly. The downsides of stale mark prices are obvious, risk engines do not like stale mark prices and hence do not allow trades at mark prices which are older than a specific interval.

Second, liquidations. Liquidations in DEXs are outsourced to a group of permissionless liquidators, whose responsibility is to takeover risky accounts, liquidate them and receive a reward in return. When liquidations are delayed, or do not happen at all, the protocol is left to pay the bad debt. Liquidations can be delayed because liquidators themselves can be slow, the number of liquidators are relatively low compared to the amount of accounts to be liquidated, liquidators sometime may not have the sufficient margin to take over an account, or there may not be any liquidators at a specific instance at all.

Third, recurring activities. Mark price updates do come under this category but there are a broad range of activites which need to be undertaken regularly but fall prey to inefficiency. For example, futures need to rollover in order to allow market participation. This is performed in a decentralized environment with the help of a transaction, which settles all positions of the previous future and initiates a new future at a new expiry, representing a "rollover". This process can be inefficient too, especially with congestions and bot failure. The repercussions of rollover failures are obvious, and is not a delightful UX for users.

# What can we do? What are the options?

The problems are pretty clear. What can we do to solve them? The first and obvious option is to believe in Solana, believe that the fee market issues will be solved, believe that spam will solved, and that Firedancer somewhere down the road will make Solana a better place. However, that may ease the problem a tad bit, but it's not completely solved. Currently it takes about 2-3s to confirm a transaction on Solana, as per official [reports](https://www.validators.app/ping-thing). However, for an end user, the delay can be way more, as much as 10s, because of intermediary processes.

The major effect of high latency is arguably on liquidity. Even if the platform has all of the world's volume, if the market maker needs to wait 10s to know whether his order was placed or not, the end result will eventually be a thinning of market depth.

## SPEs?

SPE, or Solana Permissioned Environments refers to a fork of the Solana L1, deployed by another organization as it's own sovereign chain. This has it's own benefits.

For one, an SPE inherits the same performance of the Solana L1 with much lesser activity, considering traffic is limited to users of an application. This _should_ solve the problems for a high-traffic application for a long time as it gets the same blockspace, just with no other applications to compete with. This also will lead to better UX for users, as not only do they experience better reliability and performance, but they can also use their native Solana wallets on the SPE.

However, an SPE-related approach does have it's own set of drawbacks. SPE maintainers need to bootstrap their own validator set and design fee and incentive systems to reward validators for their service. Secondly, SPEs are not backward compatible with the Solana L1 (yet). This means that an appchain SPE L1 is missing out on all of the innovations happening on Solana. Third, an SPE is a _temporary_ performance boost, considering confirmation is still blocked by consensus, and at scale, the same problems will emerge again.

We believe validator bootstrapping is a tedious process(since it's quite similar to bootstrapping liquidity). And, backward compatibility and symbiotic relationships with the Solana L1 are crucial. Lastly, we want to achieve true scale whose design space is ever expanding, showing us a clear path of optimization in the future. Hence, we believe an SPE is not a feasible approach.

## Rollups?

Rollups were chosen as the de-facto scaling path for Ethereum, considering the scaling limitations of the L1 itself. However, rollups on Solana will look much different than rollups on Ethereum. The main difference we think will be the use-cases of rollups on Solana. We believe that applications demanding low-latency and high performance will eventually move to their own app-rollups, leaving other, less demanding applications behind on the L1. We also believe that interoperability will be one of the main focuses of rollups on Solana, rather than something that will be solved later down the roadmap, like what Ethereum rollups have chosen to do. Lastly, we will see interoperability infrastructure solved first, or at parallel with intra-rollup issues. We are already seeing shared sequencers like [Rome Protocol](https://romeprotocol.com) that offer atomic interoperability and shared bridges for rollups.

Secondly, we believe that rollups have an ever-expanding design space over L1s. For example, L1 is constrained to one specific execution environment, aka VM, while a rollup can have a completely different execution environment than the L1 itself. For example, if the L1 runs an EVM environment, a rollup does not _have_ to run EVM too, and can choose between other(and more performant) options.

Thirdly, rollups are not bound by consensus overhead of the L1, and can post transaction data asynchronously to the L1. This unlocks much higher theoretical throughput for the rollup, considering the overhead is just execution(more broadly, transaction execution overhead and state access overhead, both of which can be solved), and optionally, ZK(more specifically, proving time, proof size, proof verification overhead, proof recursion semantics, if any, circuit implementation details, if any, zkVM efficieny optimizations, if any, etc.) if the rollup is a ZK rollup.

Further, unlike contrarian beliefs that rollups are not sovereign and bound to the L1 they post state to, rollups _can_ be sovereign with the help of [sovereign rollups](https://celestia.org/learn/sovereign-rollups/an-introduction/).

Lastly, app-rollups offer much more customizability over a general-purpose L1. This take is pretty much obvious, considering an application can customize parts of the rollup to increase functional efficiency .

With the above arguments in mind, we believe that an app-rollup on Solana is the best path for scaling a derivatives exchange, by keeping in mind the higher throughput unlocked by a rollup, and the customization opportunities offered by an app-rollup.

# SpiceNet

PepperDEX SpiceNet is our latest iteration of an app-rollup hosting an uber-efficient, modular and extensible derivatives protocol, Dexterity. Dexterity was initially developed by the Hxro Network team and still runs on the Solana L1. PepperDEX V1 is a frontend interface for the Dexterity protocol. PepperDEX V2 is a fork of Dexterity, (modified and) re-deployed on SpiceNet. Let's look at the details of SpiceNet itself and how Dexterity fits into the full picture.

SpiceNet runs on the Sovereign SDK stack, mainly because of how we've been continuously impressed by the various innovations the Sovereign Labs have incorporated into the SDK, which we would have to build on our own otherwise. We'll see many examples of the same in various parts of this paper.

At it's core, SpiceNet is an optimistic rollup utilizing ZK Fraud Proofs(powered by the Sovereign SDK) to optimize the fraud proving process and reduce the typical challenge period from a week to a few hours(we'll discuss more about this in the fraud proof section). We'll split components of the rollup into the Dexterity protocol, the State transition function, Sequencer and based sequencing, DA layers, ZK fraud proofs, interoperability between rollups, and compatibility with the Solana L1.

## Dexterity

Dexterity is a modular, feature-complete and extensible implementation of a derivatives trading protocol. It supports trading of perpetuals, expiry futures, zero-day futures, spreads and because of how modular the protocol is, any new instrument can be added easily with incredibly less integration efforts.

Dexterity contains of mainly 4 components, which are implemented as programs on Solana -- An asset-agnostic orderbook, a DEX, instruments, and the risk engine.

### AAOB

The asset-agnostic orderbook provides an orderbook instance facilitating trading of various assets, like derivatives. Projects like dexterity can incorporate the AAOB to facilitate market creation and trading on an orderbook infrastructure.

### DEX

The DEX component is a wrapper over the AAOB facilitating creation of MPGs, markets within the MPGs and TRGs linked to the MPG. An MPG refers to a "market product group", which can be thought of as a category of like-structured products, bound by a single risk engine instance(we'll discuss the risk engine below). The idea behind the innovation of an MPG is to govern products with similar risk profiles with unified risk engine parameters. Meaning that, we would like to separate the risk engine parameters we set for Perpetuals and ZDFs, and hence we would like to put them in different MPGs.

A TRG stands for a "trader risk group", which can be thought of as a trader account. One TRG is bound to only one MPG that it was initialized with. This does have fragmentation concerns, but offers risk isolation between TRGs(indirectly, isolated margin between TRGs)

### Instruments

The instruments component defines the metadata and parameters for the types of products that the protocol intends to support. This is separated totally from the DEX and the risk engine, meaning that each individual component fit in together in a symbiotic manner to create a derivatives protocol. To introduce a new instrument, one would only need to modify the instruments module with minimal configuration in other modules. Currently, the instruments component supports Perpetuals, Expiry Futures and ZDFs.

### Risk Engine

The risk engine is arguably the biggest innovation of Dexterity. It uses SPAN-type margining to determine the initial margin required to open a position, and the maintenance margin required in an account to keep it safe from liquidation. It determines initial margin using the standard deviation of the underlying and multiplying it with a constant multiplier. It uses portfolio covariance to take into account opposite positions on correlated assets to provide relief on the maintenance margin.

## State Transition Function

A state transition function is at the core of a rollup(and a blockchain), as it dictates _how_ the state of a rollup is modified, given a set of inputs(transactions). Most blockchains use VMs that are representations of a state transition function, allowing for transaction execution, preparation of a block and state commitment, while the consensus protocol allows for block propagation and voting. A state transition in rollups has a few similarities and a few differences. Sequencers(analagous) to leaders in L1s execute a set of transactions, prepare a block, and post it to the L1, upon which full nodes download the block, verify it's contents and update their state(or generate a fraud proof, if the rollup is an OR).

Unlike L1s, SpiceNet takes an appchain approach, by writing our state transition function in native rust, over a VM(i.e, we take a VM-less approach). The main reasoning behind this is that SpiceNet is not a general-purpose rollup(for now), and hence does not support smart contracts. Hence, a VM is unnecessary and in-turn adds execution overhead in comparison to native rust execution. State in SpiceNet is implemented as a _module_, which is a concept native to the Sovereign SDK. Modules are analogous to smart contracts, instead implemented in native rust, and hence code of a module is way more understandable, as it does not contain the jargon of a smart contract language(which itself is an abstraction of the details of the VM).

The de-facto state transition function used by SpiceNet is "preferential", as it enforces/allows for specific transactions to be always/conditionally present in a block. For example, SpiceNet enforces an oracle update(and hence a mark price update) every L2 block, solving oracle staleness and inefficiencies of mark price update instructions that were discussed previously. The same applies for liquidations. Given a (set of) liquidatable accoumt(s), the L2 conducts a priority auction comprising of a group of liquidators who are incentivized to takeover the account from the liquidatee in the shortest time possible. The L2 enforces that this auction is complete within N L2 blocks and the account is transferred to the liquidator. We apply the same towards expiry of contracts. If the L2 has any active instruments that are expiry futures, the L2 prioritizes a transaction which expires the contract _within_ the slot which it was supposed to expire at. The possibilities for preferential transactions that improve efficiency that can be incorporated into the state transition function are endless.

But, who are going to actually "call" these transactions that execute the transactions, like mark price updates? Initially, the PepperDEX team will be running the required infrastructure, but we eventually plan to decentralize the process to a set of keeper bots that are incentivized to timely update the state of the chain. What happens when the preferred transactions are not in block that they are supposed to be in? The chain of the rollup does not advance. This is quite a brutal consequence and we expect a lot of "chain halts" due to state not allowed to advance, but as time progresses, we anticipate the process to get more efficient. Hence, to minimize these occurences during the initial stages of the rollup, we aim to fund a vault(built into the chain state) whose job is to initiate these preferential transactions in time. Because it is a vault, others can participate in it, too and get rewarded.

**State access optimization is important!**
Merklization of state is probably the worst curses bestowed upon blockchains, as one of the most heaviest overheads during execution is calculation of the merkle state root. Modern blockchain implementations that still use merkle trees(in specific the Merkle Patricia Trie) like Monad make low-level improvements including a custom high-performance database called as the MonadDB to make state access faster(by saturing io threads, aka parallel state access).

Other implementations like Sei avoid using a merkle tree to commit state at all. Sei, specifically uses a memIAVL tree which is an IAVL tree stored in memory. This storage mechanism is the main interaction point during execution. Sei also employes a raw key-value store which is populated by the memIAVL tree using a WAL. The main premise of the KV store is for full nodes and archive nodes to serve historical queries.

Sovereign SDK employes a third method, which is known as "optimal state merklization". By making the trie binary, improving disk layout, compressing and halving, and other techniques such as using Blake3(allows for _very_ large leaves) and aggressive caching, state merklization is _almost_ solved. For further understanding, you can read their blog [here](https://sovereign.mirror.xyz/jfx_cJ_15saejG9ZuQWjnGnG-NfahbazQH98i1J3NN8).

## Sequencing and Based Sequencing on SpiceNet

A sequencer is the main actor responsible for transitioning the rollup's state. A sequencer picks transactions from the mempool, executes them and prepares a block to be posted to the L1 for verification. A sequencer upon preparation of a block offers "soft-confirmations" to users. In a sovereign rollup context, the block is then posted to Celestia(the DA layer of choice). Upon successful inclusion of rollup blobs on Celestia, rollup full nodes can download the transaction data, verify them, and either update their version of the state or challenge the state transition(using fraud proofs, which we will talk about below).

Because rollups do not face consensus overhead, with our optimized state transition function written in native rust, combined with Sovereign's state-of-art state merklization and access improvements, SpiceNet's preferred sequencer can offer soft-confirmations at latencies as less as 5ms!!

**What is a preferred sequencer?**
A preferred sequencer is a term native to the Sovereign SDK with the ability to give stateful pre-confirmations to the user. Currently, the Sovereign SDK has only one "preferred" sequencer option. This is because giving soft confirmations would create a lock on the state that the sequencer is attemtping to change, and unless the rollup has "differentiated" state spaces, having multiple preferred sequencers is not possible.

However, if the user is not getting inclusion in the current preferred sequencer block, they _may_ avail our based sequencing feature, which allows superior inclusion by trading off confirmation(i.e with based sequencing, users will not get the same latency they get on the preferred sequencer)

### Based Sequencing(?)

Based rollups as a concept are known to decentralize a rollup massively. They are based on the idea that a sequencer is just a "not so useful" middleware for creating a state transition of an L2 on an L1. They emerge from the thought that if the current L1 block proposer is responsible for including a rollup's state transition in an L1 block, why do we even need a sequencer? Why can't we as a rollup submit rollup state transitions directly to the block proposer via a relay?

The Sovereign SDK utilizes this concept to build around "based sequencing". In this mechanism, any user, beyond a specific transaction size limit, can become their own sequencer and send blobs to the DA layer like a normal sequencer would. This allows for multiple, parallel sequencers, ultimately leading in better "landing" metrics. However, there is a caveat, as a Sovereign SDK based rollup can only have one preferred sequencer(because of state-lock issues), other sequencers cannot be "preferred" and hence cannot offer pre-confirmations. This would mean that they can only offer confirmation when the blobs on the DA layer get included, which can be upto 4 DA layer blocks. Celestia, for example, as a block time of around 12s. This would mean that although based sequencers offer decentralization and better inclusion, they do tradeoff speed.

This tradeoff is something that we haven't solved yet. Because of incredibly low block times, the preferred sequencer in practicality will offer marginally comparable inclusion guarantee, at a _much_ better speed(because of pre confirmations).

## DA layer

Enshrined L2s use the L1 they post data to for the purpose of DA too. There are alternative models such as Validiums and Optimiums that use off-chain DA and use validity proving/fraud proving to verify/challenge state posted to the DA. Optimiums and Validiums aren't the best solutions from the perspective of security, and lose out on all of the benefits that Sovereign Rollups provide.

A sovereign rollup needs a base layer for the sole purpose of DA and not for settlement, considering the rollup itself handles settlement. There are multiple blockchains built for the sole purpose of offering DA services to rollups, such as Celestia, Avail and EigenDA(operating as an AVS on Eigenlayer).

We have decided to move forward with Celestia as the default DA layer for SpiceNet initially. Celestia mainnet has been live for over 6 months, while EigenDA has recently launched with the mainnet launch of Eigenlayer mainnet, and Avail mainnet hasn't even launched yet. This also means that Celestia is currently the most stable and reliable DA layer offering sufficient DA throughput.

We have considered using Solana L1 for DA too. However, Solana has not been used purely for DA before, and doing so comes with a lot of intracies and technicailities. While using Solana for DA opens us up to Solana DeFi liquidity, using Celestia opens us up to the broader IBC liquidity. Solana as a base chain is often congested, which is not the case with Celestia, as it has a much simpler architecture with no programmability. Moreover, although Celestia has block times of about 12s, it is being worked upon to reduce this even further.

## ZK Fraud Proofs(ZKFP)
This section is an explanation of Sovereign SDK's ZK fraud proving system, and references this tweet by [@cemozer\_](https://x.com/cemozer_) [here](https://twitter.com/cemozer_/status/1777363406149861820). Hence, you can decide to read Cem's much more detailed explanation and skip this section instead.

SpiceNet is an optimistic sovereign rollup that utilizes hybrid proving to challenge invalid state transitions. Most optimistic rollups require the attester and the challenger to play an "interactive verification game" in order to determine which (set of) transaction(s) they disagree on. Afterward, the thus found incorrect execution gets re-executed on L1 light clients. The reason of this design is to reduce the overhead imposed upon the light clients as much as possible. If the light clients were to verify a full L2 block, the throughput of the L2 is technically limited by L1 throughput.

Modfying fraud proving to a single transaction(i.e fraud proofs are no longer made for the whole block, but for each transaction) would mean that L2s would need to commit to state every transaction, destroying throughput.

The Sovereign Labs team have come up with a simple, yet subtle workaround for the same problem, that also happens to address the (awfully long) 7 day withdrawal period. Instead, of playing the IVG, the challenger just proves the L2 block. And the ZK proof of the challenged block becomes the fraud proof. This way, the verification of a challenge is now reduced to just 1 on-chain transaction, as compared to >50 for the IVG.

This also means that the withdrawal periods have been reduced (by a lot!). The 7 day withdrawal period have been set to provide sufficient margin for the IVG(or more specifically, the transactions for the IVG) has been included on-chain. But since there is only one transaction, we can safely reduce the 7 day threshold to about 8 hours(extremely conservative as we also take into assumption the warm-up period apart from proof generation which itself takes about ~15m). We can modify this with a hardfork(benefits of being a Sovereign chain) and increase/decrease this depending on the level of security we want.

**Why not be a full-fledged ZK Sovereign Rollup?**
Well, we have considered this and the set of tradeoffs that being a ZK rollup brings. Currently, ZK proofs are time consuming and costly to generate(comparing to the level of cheapness that we desire), and will take a lot of work to increase the throughput, as well as reduce the cost.

There are well-capable teams working on this fundamental issue, and we believe in a future powered by ZK, but just not now.

## Inter-rollup Interoperability

Interoperability between rollups has been one of the most pressing issues for rollups since they existed, and has not been completely solved yet. There have been interesting designs, such as the Agglayer, Shared sequencing(notably, espresso), ZKSync's Hyperchain vision, and so on.

In the traditional world, a rollup needs to prove it's state to the Ethereum L1 in order to be securely connected to other rollups. This way, one rollup makes it possible for other rollups to verify its state by proving it first on the Ethereum L1 which other rollups can then verify. Since proving state on the L1 is quite an expensive task, rollups try to make trust compromises by proving the same on an L2(and effectively becoming an L3), or any other way.

However, being built as a Sovereign SDK chain and a Sovereign rollup, this process is completely redundant. Every Sovereign rollup built using the Sovereign SDK is a function of the DA layer it operates on, meaning that the base layer does need to verify the rollup's state and any rules if they exist are just described over the data layer of the base layer(which itself is a "DA" layer).

WTF does this mean? This means a sovereign rollup A does not need to prove it's state to an expensive, or for that matter, any L1 to enable interoperability. Other sovereign rollups(or more specifically, their light clients) can simply download and verify the proof of rollup A without having to communicate with an external layer. Pretty elegant!

**About shared sequencing and enforced atomicity**
[Rome Protocol](https://romeprotocol.com) is a shared sequencing service on Solana that allows interoperable and composable rollups on Solana, via their Rhea and Romus services. Rhea API allows rollups to post blocks to the Solana L1 for verification, while the Romus service allows for atomicity between rollups. This service can support use-cases such as atomic inter-rollup bridging, giving a seamless bridging experiences, atomic MEV and arbitrage for traders to exploit disparities between rollups.

We hope to integrate SpiceNet into Rome someday, as we think it is a massive unlock in terms of unification and interoperability. Faster and better bridging experiences, and more accessible arbitrage opportunities tends to improve deposits and liquidity on the L2.
:::

## Compatibility with Solana L1

Now onto the last part of the puzzle, compatibility with the Solana L1. We want to be completely transparent in this matter. SpiceNet is an appchain/app-rollup designed to host a single application, Dexterity, and is not meant for general programmability. This means that Solana L1 compatibility is not our immediate goal.

It is of importance as we want users to be able to use their regular Solana wallets on SpiceNet too. Hence, we are brainstorming and discussing internally on altering our transaction formats that are compatible with current Solana wallets, although we could accomplish general wallet interaction with Metamask snaps.

However, there are problems with transaction simulation and network switching, as current Solana wallets will likely not be able to simulate transactions on SpiceNet, and with their current setup also not be able to prompt users to switch to SpiceNet when they collect their wallet to Dexterity.

# Further communication

We're working hard on SpiceNet, which is targeted to be V2 of PepperDEX, all the while we keep cooking on PepperDEX V1. V1 will be maintained alongside V2 and will still be open to users, and still be marketed alongside V2. We will share more updates via blogs, papers, spaces, and community calls about progress on V2. We are expecting an internal testnet in the next 2-3 months, and a public testnet after that.

Thanks for reading!

# Links

- [PepperDEX Landing](https://pepperdex.xyz)
- [PepperDEX V1](https://app.pepperdex.xyz)
- [Twitter](https://x.com/thepepperdex)
- [Discord](discord.gg/2uBmpUdSGM)

See you on the other side, anon!
