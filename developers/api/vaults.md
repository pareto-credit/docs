# Vaults

Vaults are the core contracts of the Pareto protocol. They represent on-chain vehicles that accept user deposits, deploy strategies, and accrue yield over time.

Each vault is tied to a specific strategy, chain, and token, and is managed by one or more operators.

## Structure

Each vault object includes:

* `_id` _(string)_ — Unique identifier
* `tokenId` _(string)_ — Accepted token reference
* `chainId` _(string)_ — Blockchain network identifier
* `typeId` _(string)_ — Reference to vault type
* `categoryId` _(string)_ — Vault category
* `operatorIds` _(array\[string])_ — Managing entities
* `name` _(string)_ — Name of the vault
* `address` _(string)_ — On-chain vault contract address
* `symbol` _(string)_ — Symbol of the share token
* `protocol` _(string)_ — Strategy protocol (e.g., AaveV2, Clearpool, Morpho, etc.)
* `contractType` _(string)_ — Contract logic (e.g., BestYield, CDO, CDO\_EPOCH)
* `abi` _(array)_ — ABI definition
* `description`, `shortDescription`, `caption` _(object)_ — Multilingual metadata
* `keyInfo` _(array)_ — Key-value UI information
* `visibility` _(string)_ — One of: `PUBLIC`, `RESTRICTED`, `HIDDEN`
* `status` _(string)_ — Deployment status (e.g., `READY`, `PAUSED`, `DISABLED`)
* `feePercentage` _(number)_ — Vault fee as percentage
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps
* `createdBy`, `updatedBy` _(string)_ — System actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/Vaults.yaml" path="/vaults" method="get" %}
[Vaults.yaml](../../.gitbook/assets/Vaults.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/Vaults.yaml" path="/vaults/{vaultId}" method="get" %}
[Vaults.yaml](../../.gitbook/assets/Vaults.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/Vaults.yaml" path="/vaults/performances" method="get" %}
[Vaults.yaml](../../.gitbook/assets/Vaults.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/Vaults.yaml" path="/vaults/{vaultId}/integrations" method="get" %}
[Vaults.yaml](../../.gitbook/assets/Vaults.yaml)
{% endopenapi %}
