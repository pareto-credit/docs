# Token blocks

Token blocks are periodic snapshots of token-related data captured at specific blockchain blocks. These are used in vault logic and on-chain analytics.

## Structure

Each token block object includes:

* `_id` _(string)_ — Unique identifier
* `tokenId` _(string)_ — ID of the associated token
* `tokenAddress` _(string)_ — Ethereum address of the token (0x... format)
* `price` _(string)_ — Price of the token at this block
* `block` _(object)_ —
  * `number` _(number)_ — Block number
  * `timestamp` _(number)_ — Unix timestamp (UTC)
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

{% openapi src="../../.gitbook/assets/TokenBlocks.yaml" path="/token-blocks" method="get" %}
[TokenBlocks.yaml](../../.gitbook/assets/TokenBlocks.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/TokenBlocks.yaml" path="/token-blocks/{tokenBlockId}" method="get" %}
[TokenBlocks.yaml](../../.gitbook/assets/TokenBlocks.yaml)
{% endopenapi %}
