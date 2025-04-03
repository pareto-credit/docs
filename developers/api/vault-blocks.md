# Vault blocks

Vault blocks are detailed blockchain-based snapshots of a vault’s state at a specific block height. They are used for calculating APR/APY, tracking liquidity, and generating analytics.

## Structure

Each vault block object includes:

* `_id` _(string)_ — Unique identifier
* `vaultId` _(string)_ — Vault being tracked
* `vaultAddress` _(string)_ — Smart contract address of the vault
* `block` _(object)_:
  * `number` _(number)_ — Block number
  * `timestamp` _(number)_ — Unix timestamp of the block
* `APRs` _(object)_ — Non-compounded APRs:
  * `BASE`, `HARVEST`, `REWARDS`, `GROSS`, `NET`, `FEE` _(number)_
* `APYs` _(object)_ — Compounded yield rates:
  * `BASE`, `HARVEST`, `REWARDS`, `GROSS`, `NET`, `FEE` _(number)_
* `totalSupply` _(string)_ — Total shares issued by the vault
* `price` _(string)_ — Price per share
* `TVL` _(object)_ —
  * `token` _(string)_ — TVL in vault token
  * `USD` _(string)_ — TVL in USD
* `pools` _(array of objects)_ — Breakdown of pool-level performance:
  * `address` _(string)_
  * `protocol` _(string)_
  * `rates`: `{ supply: number, borrow: number }`
  * `utilization`: `{ supplied: string, borrowed: string, rate: number }`
  * `available`: `{ toBorrow: string, toWithDraw: string }`
* `allocations` _(array)_ — Optional pool allocation breakdown
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/VaultBlocks (1).yaml" path="/vault-blocks" method="get" %}
[VaultBlocks (1).yaml](<../../.gitbook/assets/VaultBlocks (1).yaml>)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/VaultBlocks (1).yaml" path="/vault-blocks/{vaultBlockId}" method="get" %}
[VaultBlocks (1).yaml](<../../.gitbook/assets/VaultBlocks (1).yaml>)
{% endopenapi %}

## Vault latest blocks

Vault latest blocks are real-time snapshots of the most recent state for each vault. Unlike vault blocks (historical), these represent the **current** or **latest available** data.

## Endpoints

{% openapi src="../../.gitbook/assets/VaultLatestBlocks (1).yaml" path="/vault-latest-blocks" method="get" %}
[VaultLatestBlocks (1).yaml](<../../.gitbook/assets/VaultLatestBlocks (1).yaml>)
{% endopenapi %}
