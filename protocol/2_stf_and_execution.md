# STF and Execution

The Sovereign SDK and SpiceNet do not use a VM for the purpose of transitioning state via execution. Instead, we make use of the module system to define application logic. To understand how the module system works, please read this [specification](https://github.com/pepper-research/spicenet/tree/specs/specs/protocol/1_module_system.md).

### What is an STF?

Modules are analogous to smart contracts of the Sovereign SDK. Modules contain application-specific logic responsible for modifying state of the rollup. Modules can only update the state that they explicitly define in the module definitions.

The modules together along with the module system dictate how the rollup operates, and hence constitute the "state transition function" of the rollup. The Sovereign SDK defines the module system upon which modules can be built, and hence defines the default "state transition function" for a Sovereign SDK-based rollup.

This means that modules using the default module system rather than a custom state transition function will have to abide by the provisions and limitations of it. Because of how modular the Sovereign SDK is, it is well within the realm of possibility to write a custom state transition function from scratch, that may or may not follow the module system altogether.

SpiceNet initially will use the default state transition function to build modules upon, because of how stupidly easy it is to do so. The default module system does a great job at abstracting a lot of intracacies, such as the different inputs to be fed to both native execution(Runtime) and the zkVM, the runtime and sequencer definitions, etc.

However, down the line, we may explore writing our own state transition, making use of the [rollup interface](https://github.com/pepper-research/spicenet/tree/specs/specs/protocol/3_rollup_interface.md), which serves as the base for a sufficiently-advanced state transition function.

## Execution

Execution phase allows a rollup to update it's state in the presence of inbound transactions. The Sovereign SDK makes use of the Runtime to execute transactions. The Runtime collects transactions and passes them onto the respecitve modules. As said before, modules contain logic to advance state. In the module, a transaction's instruction triggers a state transition by calling functions which modify state.

Let's take a very simplistic example to understand this. Let's say we have a module to mint and freeze NFT collections, and mint and burn NFTs within them. A user wants to mint an NFT belonging to a specific collection. This user transaction would be dealt by the NFT module in the following way -- the module first provides access to the mint nft interface, a "working set". A working set is the an uncomitted state instance of a rollup, allowing a module to access and modify state. Then, using the parameters provided, the state transition logic would fetch the collection id and state. It would then mint a new NFT and modify the NFT state with the new NFT with a "set" method, which allows for safe manipulation of arbitrary state. Then, the collection state is modified, first by increasing the supply and modifying it's inner state, again using the "set" method.

### Modularizing Execution

From the start, we aim to make the rollup's design as modular and flexible as possible. Implementation modularity may introduce complexity at first, but vastly increases the extensibility of each component in the future. For example, Dexterity on the Solana L1 is not a single program, but a set of programs talking to each other via CPI.

One of the immediate benefits of application logic modularity is expansion of use-case. For example, SpiceNet's Orderbook powering derivatives trading is asset-agnostic, meaning that it can be easily deployed to trade another asset, like spot, for suppose. If the orderbook were to be clamped with other derivatives specific logic into a single module, for suppose, this use-case expansion would not have been possible.

Hence, we are building SpiceNet as a collection of interconnected modules glued together to function as a unified derivatives trading protocol. However, modularity from day 1 means that each module can be used as an independent unit for a totally new use-case. We believe that this design choice will pay dividends in the future, as SpiceNet becomes a maximally extensible rollup feasible enough to build novel trading use-cases.

The current set of modules we have in mind are:

- AAOB
- Instruments
- Risk Engine
- [Vaults](https://github.com/pepper-research/spicenet/tree/specs/specs/application/3_vaults.md)
- [Native Liquidity](https://github.com/pepper-research/spicenet/tree/specs/specs/application/1_native_liquidity.md)
- [On-chain Market Making](https://github.com/pepper-research/spicenet/tree/specs/specs/application/2_native_market_making.md)
- Native Oracle System

PS: This list will be updated with new modules and links to specifications of each module as and when they are written
