# STF and Execution

The Sovereign SDK and SpiceNet do not use a VM for the purpose of transitioning state via execution. Instead, we make use of the module system to define application logic. To understand how the module system works, please read this [specification](https://github.com/pepper-research/spicenet/tree/specs/specs/protocol/1_module_system.md).

### What is an STF?

Modules are analogous to smart contracts of the Sovereign SDK. Modules contain application-specific logic responsible for modifying state of the rollup. Modules can only update the state that they explicitly define in the module definitions.

The modules together along with the module system dictate how the rollup operates, and hence constitute the "state transition function" of the rollup. The Sovereign SDK defines the module system upon which modules can be built, and hence defines the default "state transition function" for a Sovereign SDK-based rollup.

This means that modules using the default module system rather than a custom state transition function will have to abide by the provisions and limitations of it. Because of how modular the Sovereign SDK is, it is well within the realm of possibility to write a custom state transition function from scratch, that may or may not follow the module system altogether.

SpiceNet initially will use the default state transition function to build modules upon, because of how stupidly easy it is to do so. The default module system does a great job at abstracting a lot of intracacies, such as the different forms of inputs to be fed during native execution and execution in the zkVM. Moreover, it has a rich library of utilities such as the sequencer registry, runtime capabilities, sequence reward mechanisms, and so on, that are of direct use within our rollup.

However, down the line, we may explore writing our own state transition, making use of the [rollup interface](https://github.com/pepper-research/spicenet/tree/specs/specs/protocol/3_rollup_interface.md), which serves as the base for a sufficiently-advanced state transition function.

## Execution

Execution phase allows a rollup to update it's state in the presence of inbound transactions. The Sovereign SDK makes use of the Runtime to execute transactions. The Runtime collects transactions and passes them onto the respecitve modules. As said before, modules contain logic to advance state. In the module, a transaction's instruction triggers a state transition by calling functions which modify state.

Let's take a very simplistic example to understand this. Let's say we have a module to mint and freeze NFT collections, and mint and burn NFTs within them. A user wants to mint an NFT belonging to a specific collection. This user transaction would be dealt by the NFT module in the following way -- the module first provides access to the mint nft interface, a "working set". A working set is the an uncomitted state instance of a rollup, allowing a module to access and modify state. Then, using the parameters provided, the state transition logic would fetch the collection id and state. It would then mint a new NFT and modify the NFT state with the new NFT with a "set" method, which allows for safe manipulation of arbitrary state. Then, the collection state is modified, first by increasing the supply and modifying it's inner state, again using the "set" method.

### Modules and Libraries

From the start, we aim to make the rollup's design as modular and flexible as possible. Implementation modularity may introduce complexity at first, but vastly increases the extensibility of each component in the future. For example, Dexterity on the Solana L1 is not a single program, but a set of programs talking to each other via CPI.

One of the immediate benefits of application logic modularity is expansion of use-case. For example, SpiceNet's Orderbook powering derivatives trading is asset-agnostic, meaning that it can be easily deployed to trade another asset, like spot, for suppose. If the orderbook were to be clamped with other derivatives specific logic into a single module, for suppose, this use-case expansion would not have been possible.

Hence, we are building SpiceNet as a collection of interconnected libaries and modules, that interact with each other with the means of the Sovereign runtime, or otherwise. This means that library-specific logic is shared between modules from day 1, making it easy to develop new, module-specific logic using the libraries without much hassle. This also means that critical components in modules can be hot-swappable with the help of utility libraries, *without* impacting other core functionality.

The current set of libraries we are building towards are
- A custom library hosting various implementations of trees, such as the critbit tree, binary tree, etc that can be used in various places, such as in the orderbook.
- A numerical library hosting various custom, memory and resource-efficient implementations
- A constants library hosting various enums and constants that will be used in various modules.

The current set of modules we are building on top of the libraries are
- the AAOB[Asset Agnostic Orderbook]
- the Instruments module
- the primary risk engine[named SPANDEX]
- the alpha risk engine
- the actual exchange[wrapper over the above facilitiating trader interaction]
- the vaults module[built on top of the exchange]
- native market making module[custom vaults module]
- native liquidity[runs the native market making module scoped to a specific market, with additional conditions]
