# DA Layers in a Sovereign Rollup
SpiceNet is a Sovereign rollup. SRUs operate over a generic DA layer, allowing various parties to download and verify the data posted by the (preferred + based) sequencer. Rollup full nodes, for example, are one of the interested parties. Every rollup slot, they download transaction data from the DA layer, and verify the transaction data.

Verification here refers to re-executing the transactions and obtaining the new state root. If the state root matches the state root produced by the sequencer, then the state transition is considered to be valid, upon which the rollup full nodes update their own internal states. If not, it is said that the sequencer initiated an invalid state transition. Upon identifying, rollup full nodes can then "challenge" the state using fraud proofs.

In other words, the DA layer for an SRU is a key enabler of rollup security, as it enables honest full nodes to download, verify and challenge state if required. It can also be said that a DA layer replaces the role of consensus in a fully sovereign blockchain. Conensus allows participants like validators to re-execute the block proposed by the leader, and express their decision with regards to the proposed state transition with the help of a voting process.

Therefore, it is also crucial to select a highly capable and efficient DA layer that SpiceNet can operate over. After careful consideration and deliberation, we have decided to move ahead with Celestia as the proposed DA layer for SpiceNet.

## Why Celestia?
We have only two expectations from a "specialized" DA layer: how capable and advanced is the data layer, and how much time in market does the DA layer have. Only having sufficient advanced technology isn't enough, the DA layer must also be battle-tested such that newer adopters know that it can handle traffic at scale. Analyzing the current set of available DA layers, Celestia is the *only* DA layer that fits both of the criteria. It has a simple, yet effective architecture, with NMTs for storage, erasure coding for predictable re-structuring of data, and a VM-less architecture to remove centralized points of bottleneck. The chain itself has been constructed from scratch to serve as a "true" Data Availability layer.

Other DA layers, although having impressive tech, haven't proven themselves in the market yet, and it's unknown yet in the current time how(and if) they can handle traffic from hundreds and thousands of chains at scale.

Celestia also has other benefits. Celestia, as an L1 is a blockchain based on the CosmosSDK offers interoperability with the rest of the cosmos ecosystem, via IBC. This unlocks huge liquidity benefits as users can seamlessly bridge from other cosmos chains(like dYdX) to SpiceNet in a trust-minimized fashion.

## Celestia vs Solana: Tradeoffs
Then why not use Solana as a DA layer? Solana has amazing tech, and has been live for about ~4 years. Solana has it's own set of problems. First, Solana is not a "DA" layer per se, and is obviously harder to integrate as a sole DA layer than Celestia, for suppose. Second, Celestia being a specialized DA layer offers way higher DA throughput than Solana. Third, Celestia uses a VM-less architecture, and does not face congestion the same way Solana does. This is also the reason why Celestia is significantly cheaper to post DA to, unlike Solana which has been recently prone to fee spikes.

## Scope for Improvement
We do expect Solana to become a much more feasible DA layer in the future, with respect to the amount of integration effort required to enable DA capabilities for Solana. We also do think that Solana will solve it's congestion liabilities, resulting in DA fees feasible enough for rollups, at which point we *may* consider porting over SpiceNet's DA adapters to use Solana instead. Using Solana as a DA layer allows for access to Solana's liquidity, which at the point of writing this, is ample. Using Solana as a DA layer also allows for SpiceNet to turn into a based SRU, a one of it's kind Sovereign rollup whose state transitions are proposed by the L1 block leader.

Also note that DA is the only cost we will incur with Solana provided we use it for DA down the line, considering SpiceNet operates as an SRU and it's state is not necessarily not operated by an isolated settlement contract on L1.
