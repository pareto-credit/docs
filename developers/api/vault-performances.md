# Vault performances

Vault performances track yield metrics, user activity, and reward distributions for vaults. These metrics are used in analytics, reporting, and performance visualization on Pareto.

## Structure

Each vault performance object includes:

* `_id` _(string)_ — Unique identifier
* `vaultId` _(string)_ — Vault being tracked
* `vaultBlockId` _(string)_ — Related vault block reference
* `block` _(object)_ — Block information:
  * `number` _(number)_ — Block number
  * `timestamp` _(number)_ — Unix timestamp
* `age` _(number)_ — Block age in seconds
* `holders` _(number)_ — Number of unique users in the vault
* `realizedAPY` _(number)_ — Realized annual percentage yield
* `accruedRewards` _(array of objects)_ —
  * `tokenId` _(string)_ — Reward token ID
  * `amount` _(string)_ — Raw amount of rewards
  * `amountUSD` _(string)_ — Value in USD
  * `APR` _(number)_ — APR contribution
  * `percentage` _(number)_ — Share of total reward
* `earnings` _(object)_ —
  * `USD` _(string)_ — USD-denominated earnings
  * `token` _(string)_ — Token earnings amount
  * `percentage` _(number)_ — Share of vault value
* `createdAt`, `updatedAt` _(string)_ — ISO 8601 timestamps
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/VaultPerformances (1).yaml" path="/vault-performances" method="get" %}
[VaultPerformances (1).yaml](<../../.gitbook/assets/VaultPerformances (1).yaml>)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/VaultPerformances (1).yaml" path="/vault-performances/{vaultPerformanceId}" method="get" %}
[VaultPerformances (1).yaml](<../../.gitbook/assets/VaultPerformances (1).yaml>)
{% endopenapi %}
