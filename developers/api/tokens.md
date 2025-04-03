# Tokens

Tokens are ERC-20 compatible assets used in vaults and tracked by the Pareto protocol across multiple chains.

## Structure

Each token object includes:

* `_id` _(string)_ — Unique identifier
* `name` _(string)_ — Name of the token
* `symbol` _(string)_ — Token symbol (e.g. USDC, WETH)
* `chainId` _(string)_ — Chain this token belongs to
* `address` _(string)_ — On-chain contract address (0x...)
* `decimals` _(number)_ — Token decimal precision
* `color` _(string)_ — UI color reference for this token
* `oracle` _(object)_ — Price oracle metadata:
  * `address` _(string)_ — Oracle contract address
  * `abi` _(array)_ — Oracle ABI definition
  * `protocol` _(string)_ — Data source (e.g., Idle, AaveV2, Morpho, Clearpool)
  * `fee` _(number, optional)_ — Optional oracle fee
  * Additional optional config fields (e.g. `fromBlock`, `USDCAddress`, ...)
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps
* `createdBy`, `updatedBy` _(string)_ — System actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/Tokens.yaml" path="/tokens" method="get" %}
[Tokens.yaml](../../.gitbook/assets/Tokens.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/Tokens.yaml" path="/tokens/{tokenId}" method="get" %}
[Tokens.yaml](../../.gitbook/assets/Tokens.yaml)
{% endopenapi %}
