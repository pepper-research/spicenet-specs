# SpiceNet Module System

SpiceNet is a sovereign rollup built on Solana leveraging Celestia for high-throughput DA. It uses the Sovereign SDK for the base components of the rollup, so that we can focus on the application itself.

Like smart contracts on other chains, Sovereign SDK exposes a "module system" for the purpose of writing application logic. For example, an NFT standard can be implemented as a module on the Sovereign SDK.

The semantics of the module system are quite different than smart contracts. Writing modules is quite barebone, considering there is barely any tooling available, and to build a sufficiently advanced application like a derivatives protocol, one would need to build a lot of components themselves, which on other chains would otherwise be abstracted. Secondly, writing modules does require some understanding of the building blocks of the rollup itself, like what is a State Transition Function, what is a DA Layer, which DA Layer to choose, etc.

The Sovereign SDK fortunately does not require different application logic to be run in native mode(native execution) and in the zkVM, and hence is a good abstraction. Meaning that one instance of an application can be taken and packaged to run in both native and zk mode. For an Optimistic rollup like SpiceNet, that is a massive advantage.

## What is a Module?

But, what is a module? A module, like a smart contract, runs a state transition logic, with the ability to modify the state it _controls_. This might be a good time to explain how state and state interoperability works in the Sovereign SDK. The Sovereign SDK has a global state, represented by the Sovereign SDK(optimized implementation of a Jellyfish Merkle Tree). This means that a module does not _own_ state per se, but has access to the state that it has the ability to modify. In other words, it means that a module doesn't necessarily hold the state itself, but contains logic to access and modify state. This also means that it is required to explicitly define what state that a module has access to.

## The Module System

The Sovereign SDK's "module system" is an opinionated framework for building rollup components, or better said, the rollup itself using the Sovereign SDK. It implements base tooling for anyone to get started with building modules easily. It also does not mean that the module system is restrictive in terms of customization. It does allow rollups to customize certain aspects of the rollup, such as the hash function to be used within the zkVM, the DA layer of choice, signature scheme, etc. It does impose certain default parameters, such as the serialization logic(borsh), address formats(bech32) and so on.

## Inner Workings of the Module System

Every module by default is written in pure rust, over smart contract languages(solidity) and abstractions(anchor). This also means that the logic of each module is very simple and easy to understand, unlike the jargon introduced by smart contract languages, which themselves are wrappers over the inner workings of the VM at-play.

Every module is inherently interoperable with other modules, enabling composability. This means that one module can call other modules and use their functionality. For example, a module A can use the accounts module to create accounts for users.

A module has two core components, the module definition, and the call messages. The module definition provides clarity about details of the module itself, such as it's ID, the state it has access to, and it's gas configuration. The call messages explicitly define the state transitions that the module supports and also contains implementations for each specific state transition. The module definition glues together the call message implementations with the module properties, thus enabling full-fledged functionality of a module.

## Properties of the Module System

Modules are not meant to offer general purpose programmability. Hence, they are "trusted" by nature. Modules on SpiceNet cannot be dynamically deployed by users, they are most likely built up-front by the team.

Smart contracts can be thought of as a layer above the Sovereign SDK stack, and hence to allow general-purpose programmability, the smart contract environment and the VM itself would need to be implemented as a module on SpiceNet.

The module system, as discussed before, is an abstraction of the inner-chain workings, although it does require deep understanding of the chain, unlike smart contracts. For example, to charge gas, a module would need to provide gas configurations in the form of a `json` file upfront, and provide a way for the module to access that configuration. The complexity of acessing and interpreting gas configurations is performed by the module system.

Modules also allow for extended functionality, that typical smart contracts may not. They allow developers to customize parts of the rollup functionality without touching the application functionality. For example, DA layers can be hot-swappable without modifying the application logic. For another example, if you have read the [overview](https://github.com/pepper-research/spicenet/tree/specs/specs/1_overview.md) spec, you probably do know that SpiceNet enforces preferential STFs offering higher priority for specific kinds of transactions, increasing efficiency. Such additions can also be made to the rollup, without modifying the application logic itself.
