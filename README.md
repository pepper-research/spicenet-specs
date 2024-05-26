# SpiceNet Official Specification
SpiceNet is a Sovereign rollup based on the Sovereign SDK. It uses the Rome protocol as a shared sequencing service, Celestia as a DA service, and Risc0 for generating ZK proofs to prove fault.

SpiceNet is currently designed as an optimistic rollup using ZK proofs to prove faulty state transition. This design allows us to reduce traditional challenge periods of ~7 days to ~8 hours, a reduction of about 21x. It also reduces verification costs of the fraud proof by roughly 50x, considering a ZK proof can be verified in 1 transaction, and is relatively cheap, compared to the 50 or so transactions induced by traditional, Interactive-Verification-Game(IVG)-based fraud proofs.

This directory aims to provide a detailed description and explanation of all the design choices and tradeoffs we have made till now, and will be continuously updated to reflect the design choices and tradeoffs we are going to make in the future. We believe in utmost transparency and engaging the community in healthy dialogues focused towards improving the protocol. If the specification was too technical, feel free to read the [overview](https://github.com/pepper-research/spicenet/tree/specs/1_overview.md) which aims to provide a condensed description of the same.


# Guidelines for Readers
The specification is broken down into `application` and `protocol`.

Application refers to our design choices made while designing Dexterity, the highly modular and extensible trading protocol that SpiceNet is designed to host. Examples of content in `application` directory can be - trading-specific design decisions, such as design of a new trading instrument, risk engine parameter fine-tuning, account structure modifications, etc. It also includes new [modules](https://github.com/github.com/pepper-research/spicenet/tree/specs/protocol/1_module_system.md) meant to introduce new, additive functionality to Dexterity, such as [Enshrined market making](https://github.com/github.com/pepper-research/spicenet/tree/specs/application/2_native_marmet_making.md) and [vaults](https://github.com/github.com/pepper-research/spicenet/tree/specs/application/3_vaults.md).

Protocol refers to pure SpiceNet, the rollup, specific talk. It contains information about how the chain is actually designed and built, the various components that make up the rollup, the different tradeoffs we have made and why, and so on. For example, it contains information about the module system, which is an equivalent of smart contracts on SpiceNet, the [STF](https://github.com/pepper-research/spicenet/tree/specs/protocol/2_stf_and_execution.md), Execution mechanism(https://github.com/pepper-research/spicenet/tree/specs/protocol/2_stf_and_execution.md), etc.

Soon, once the community is informed about SpiceNet and it's specifics, we will create a new directory called `proposals`, referring to modifications of the protocol, as well as the application for which we want to engage the community before moving forward. It allows us to gain feedback on the proposal, make changes to it and execute it the way the community desires it to be.
