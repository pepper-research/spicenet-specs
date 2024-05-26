# Sovereign Rollups

SpiceNet is a Sovereign rollup, and hence is not enshrined to any specific base layer. Rollups in the traditional sense have always been bound to a base layer, like the Ethereum L1, for the purpose of verification and settlement. Enshrining apps to the base layer can have it's own sets of disadvantages, like the inability to hardfork or update the chain without convincing the base layer, expensive and redundant proof verification procedures on the L1 for the purpose of offering interoperability between L2s.

Here, we will discuss why sovereign rollups aren't that bad of a tradeoff.

## Why do we Enshrine apps?

Rollups were created to separate execution from the base layer, which initially was the Ethereum L1. The ethereum foundation had a clear distinction early on, as to what should be part of the base protocol, and what should be offloaded. While rollups tackled execution, the L1 focussed on providing the economic security required to smart contracts(and by definition, rollups).

While economic security might seem like a meme, it does have a deep meaning behind it. Economic security refers to how resistant is a chain to social hardforking and block re-org. Social hardforking can have terrible consequences to a chain, even potentially destroying it. A chain's economic security, i.e it's stake(in POS-based chains) determines it's resistance from potential hardforks. It means that although the biggest validators may form a cartel and fork the chain, the full nodes will not follow suit, thus making any attempt worthless. This guarantee against hardforking resistance is very much valuable to rollups because if a chain hardforks, it allows the rollup contracts to be upgradable, opening possibilities for theft of user funds, and can be as devastating for the rollup as it is for the L1 itself.

Economic security is still a major reason why rollups desire to enshrine themselves to a base layer. It is also a reason why any forms of verification and settlement are preferred to be done on the L1, because of it's strong guarantees against hardforking. For example, rollups desire to verify execution proofs on the L1 for the purposes of validity(validity proofs) and interoperability. This is because rollups know that once a proof is verified, it is pretty much immutable and finalized because of the strong security guarantees of the L1.

## Multisigs: Are apps really enshrined?

So far, we have understood that hardfork resistance is of utmost importance for L2s and hence is a potential risk for L2s. Ethereum has billions of stake, and hence provides ample of economic security for rollups. This minimizes the upgradability of rollup contracts because of hardforks, hence securing user funds. However, that is not the case in today's rollups. Rollup contracts today are governed by multisigs, which _do_ have the ability to upgrade the contracts, and steal the funds. Now there are measures in place to protect this, but vulnerabilities stemming from the multisig is definitely still in the realm of possibilities.

This has led us to believe that the actual benefits of enshrining, i.e immutability and economic security are outpowered by the risks imposed by a centralized multisig of operators which very well can steal the funds of the rollup by enforcing an invalid state transition.

This also means that rollups face dual risks, among others -- risk of hardforking, and risk of multisig malpractice. Billions of L1 stake can minimize the former risk by making hardforks really hard to execute, but using a centralized multisig significantly increases the chance of rollup upgradability and possibility of user fund theft, making the former benefit basically pointless.

## Is sovereignty a better solution?

Considering that billions in economic security is meaningless when rollups can still be upgraded and funds can still be stolen, we have started to believe that sovereignty, on the contrary, can be a better solution.

Don't get us wrong, the demand of rollups STILL is resistance from hardforks and re-orgs, multisigs were a poor design choice that ended up hurting their biggest advantage.

Sovereign rollups are a contrarian bet, but can be a solution to the above problem: by eliminating concentrated risks(multisigs) and allowing rollups' full nodes, instead of the L1's, to control upgradability. This allows us to make another point: economic security is a way of controlling upgradability of L1 contracts and preventing theft of funds, but it is not the only solution. In the context of rollups, rollup full nodes (potentially) are more than capable of controlling upgradability of a rollup and securing funds.

Now, we can form one definition of sovereign rollups, and the different set of options they offer: "Sovereign rollups are rollups which allow the rollup full nodes themselves to control the upgradability of the rollup, thereby protecting user funds. This allows the rollup to be independent of the economic security of the base layer, and reduce the centralization risks posed previously, fka multisigs".

In a practical world of fragmentated knowledge, sovereign rollups also take responsibility of settlement and verification, along with execution, leaving ordering and DA to another layer. While this may not make sense, it can be understood this way: A sovereign rollup operates more like a sovereign chain, in the way it deals with conflicts. If the sequencer posts an invalid state transition, it is the rollup full nodes that have to deal with it, and not the base layer because Sovereign rollups are _not_ enshrined. Now _dealing_ here can mean a lot of things, but typically it means to challenge the state and create a fraud proof.

## Interoperability, a gift of Sovereignty(?)

In the traditional enshrined world, you would verify the proofs on the L1 because of it's said economic security guarantees. But considering that security of funds and upgradability is now in the hands of the full nodes, proofs need not be verified elsewhere. This is pretty sweet considering the limitations of ethereum for the purposes of proof verificaion.

One of the major roadblocks of interoperability was the inclination of rollups to verify executin proofs on the L1, which was time consuming and costly. With that restriction bypasses, interoperability can now be carried out at extremely low-latency(ethereum block times are a major limitation for low-latency interop) and with similar security guarantees.

In the Sovereign SDK, a transaction from an origination chain is sent to a destination chain containing the execution proof. The destination chain would then verify the proof, thereby securely knowing the state of a rollup. This enables trust-minimized bridging between two sovereign rollups without the intervention of an often expensive base layer.

There are certain limitations, though. Trust-minimized bridging between two sovereign rollups can only be possible _if and only if_ they share the same DA layer. This means that although the base layer is not verifying proofs, it still needs to be common between two sovereign rollups to enable _trust-minimized_ interop between them. This is because a common DA layer allows a common record of occurence of a bridging event between two sovereign rollups. The same property could not have been possible if there were two distinct DA layers being used. The same property is present in enshrined rollups too. It is not widely visible because all enshrined rollups typically use Ethereum and de-facto have a "common record of occurence", i.e the Ethereum L1.

What other alternatives can be considered if two rollups do not share the same DA layer? There are a few approaches. Firstly, both the rollups can integrate IBC provided both the DA layers are integrated with IBC, and use IBC as a middleman for trust-minimized interoperability and messaging. However, if one of the DA layers or both of the DA layers do not have IBC compatibility, then some level of trust would need to be introduced, via providers like Axelar.
