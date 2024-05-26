# Vaults on SpiceNet

Vaults are designed to be an in-built feature on the SpiceNet L2 enabled via native modules. Vaults can be thought of as community-contributed pool of funds, controlled by a vault operator, targeted towards specific trading strategies on the SpiceNet L2s.

Vaults allow traders, novice and pro alike to park their capital in high-yielding opportunities and passively earn yield from actively managed strategies. For vault operators, they can become fund managers of decently big corpuses, all the while being unknown to the depositors. That is the inherent power of vaults, it allows anyone to become a fairly big operator purely on the basis of the yield they offer, even if they are completely anon.

## The Problem

Idle capital is one of the biggest issues in derivatives, with many traders wishing to diversify their capital base into different strategies across the risk spectrum. However, crypto hasn't hit it's "hedge fund" moment yet, with a scarce number of funds and investment firms. Moreover, centralized teams and funds go against the ethos of crypto, as they require some level of trust, high levels of supervision by middlemen, and illiquidity.

## The Solution

Vaults are analogous to decentralized and trustless fund management, as the community can decide to fund vaults purely based on the merit of the underlying strategy and yield offered by the vault.

Anyone can create a vault with minimal fee(due to sovereign blockspace) and become a vault operator. A vault is designed to support any strategy and hence is generic. Meaning that, a vault operator can decide to run any arbitrary strategy with the vault they operate. This allows for flexibility and increases the diversity of the vaults marketplace.

### Vaults Marketplace

A diverse vaults marketplace is a net positive. It allows depositors to diversify between vaults, in turn fueling competition between vaults. It also fuels organic growth in the volume on SpiceNet. We believe that vaults are the best way to fuel incentive-driven, organic growth in volume. Higher volume attracts external capital to the L2, further boosting capital deployed into vaults.

### Vaults Design

Vaults work a bit more intricately under the hood. A vault in the most basic sense represents an address on SpiceNet, to which users send their funds to when they deposit, and request funds from when they withdraw. The vault(or more specifically, the vault address) creates a trader address(TRG) on Dexterity during it's initialization. This allows for seamless interoperability between the vault(liquidity to serve withdrawal claims) and the trader account.

The vault operator is assigned a "delegate" of the trader account, allowing him to undertake actions on the exchange on behalf of the trader account. The vault operator also would need to transfer initial liquidity from the vault to the TRG to begin trading, and hence would request a transfer to the vault. The vault, after performing the required checks and accounting, would transfer the funds to the account in context(only the owner can transfer funds to a TRG and hence only the vault can transfer funds to the TRG, and only upon the request of the vault operator after verification). However, while withdrawing from the TRG to the vault, both the vault operator(the delegate) AND the vault can withdraw.

### Configuration and Initialization Parameters

When a vault operator is creating their vault, they can configure a few parameters, while the protocol imposes a few other parameters upon the vault operator himself.

The vault operator can set minimum deposit limits for users, effectively discarding low deposit sizes. Secondly, they can set the amount of the performance fee they want to charge, with a cap of 10%. Typically, in a competitive marketplace, lower the performance fee, the better, considering the potentially leftover performance fee can be given away as additional yield to users, hence making the vault a better choice for depositors. A vault can also be assigned a name and description, allowing the user to understand the strategy that the vault aims to run. Lastly, the vault operator can set "tags" which allows potential depositors to filter vaults based on the type of strategy a vault runs, and the risk spectrum it operates on.

The protocol itself sets a few constraints against the vault operator. The protocol mandates that the vault operator be a depositor in the vault he operates, in order to guarantee "skin in the game" for the vault operator. The protocol also sets a minimum floor over the deposits the vault operator holds in his vault as a percentage of the total deposits(AUM). The protocol currently mandates that the vault operator's share in the total AUM of the vault cannot be below 10%. Say the vault operator deposited 300$ in his vault, and the vault's total AUM ia 900$. This would evaluate to the vault operator holding ~33% of the vault he manages. Say, a depositor wants to deposit $3000. This would mean that the new total AUM would be $3900, and the new percentage would evaluate to ~7%, which is below the threshold the protocol sets. This would mean that the new deposit would be voided with a protocol-level error. Here, the maximum deposit one can make without breaching the threshold would ~2100.

A vault operator can also choose to modify the configuration he has set initially for the vault. For example, the vault operator can modify the name, description, choice of tag(s), performance fee, minimum deposit amounts, etc even after the vault has been created. The vault operator can also choose to "dissolve" the vault. Dissolving a vault would first need a few requirements to be complete, such as no outstanding OI in the TRG controlled by the vault, and no cash remaining in the TRG, meaning that it should be fully empty. In other words, it can be said that the vault should contain all of the AUM it manages, after accounting for any (unrealized + realized) PnL, and nothing should be left in the TRG. Then, and only then, can the vault operator successfully close down the vault, which would result in all of the deposits being returned to the depositors of the vault at the point of dissolution, after accounting for the yield(can be negative) the vault produced during it's operation. The vault operator's deposit would be returned to him/her accordingly too. The vault itself would be set into "de-activated" mode, meaning that it no longer would be active, would not accept any deposits, and would not take part in any other activities, while the TRG would be closed _before_ the vault was set into de-activated mode(as after the vault is de-activated, it cannot perform any action).

# Disclaimer

The yield offered by any vaults is not endorsed by SpiceNet, PepperDEX, or any other legal entities related to SpiceNet or PepperDEX. This specification is purely meant for the educational purposes of the reader, wishing to provide him/her merely with the details of how vaults work, and not provide any financial advice.

Vaults are used to park idle capital. However, they may not always offer positive yield, considering the strategies targeted are actively-managed strategies. Please exercise caution and conduct proper due diligence before depositing into a vault. Vaults are truly permissionless, and not controlled by the protocol.
