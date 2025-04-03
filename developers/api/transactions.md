# Transactions

Transactions represent user and system interactions with Pareto vaults such as deposits, redemptions, and claims.

## Structure

Each transaction object includes:

* `_id` _(string)_ — Unique identifier
* `type` _(string)_ — One of: `DEPOSIT`, `REDEEM`, `CLAIM`
* `status` _(string)_ — One of: `PENDING`, `SUCCESS`, `FAILED`
* `hash` _(string)_ — Transaction hash on-chain
* `chainId` _(string)_ — Blockchain ID where the transaction occurred
* `walletAddress` _(string)_ — Wallet initiating the transaction
* `vaultId` _(string)_ — Associated vault
* `epochId` _(string)_ — Related epoch (if applicable)
* `tokenId` _(string)_ — Token involved
* `amount` _(string)_ — Amount transacted
* `amountUSD` _(string)_ — USD equivalent of the transaction
* `gasFeeUSD` _(string)_ — Gas fee in USD
* `block`: _(object)_
  * `number` _(number)_ — Block number
  * `timestamp` _(number)_ — Unix timestamp of the block
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps
* `createdBy`, `updatedBy` _(string)_ — Actor identifiers

## Endpoints

{% openapi src="../../.gitbook/assets/Transactions.yaml" path="/transactions" method="get" %}
[Transactions.yaml](../../.gitbook/assets/Transactions.yaml)
{% endopenapi %}
