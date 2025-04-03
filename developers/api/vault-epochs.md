# Vault epochs

Vault epochs define fixed-duration accounting periods used in vault logic to compute returns, manage liquidity flows, and determine claim availability.

## Structure

Each vault epoch object includes:

* `_id` _(string)_ — Unique identifier
* `vaultId` _(string)_ — Vault the epoch belongs to
* `vaultAddress` _(string)_ — Address of the vault
* `block` _(object)_ —
  * `number` _(number)_ — Block number
  * `timestamp` _(number)_ — Unix timestamp
* `count` _(number)_ — Epoch sequence index
* `status` _(string)_ — One of: `WAITING`, `RUNNING`, `DEFAULTED`, `CURE`
* `startDate` _(string)_ — ISO 8601 start date
* `endDate` _(string)_ — ISO 8601 end date
* `startCureDate` _(string)_ — When cure phase begins (if applicable)
* `apr` _(number)_ — Estimated APR
* `lastApr` _(number)_ — APR from previous epoch
* `expectedInterest` _(string)_ — Interest forecasted during this epoch
* `unclaimedFees` _(string)_ — Fees not yet claimed
* `deposits` _(string)_ — Total deposits in epoch
* `duration` _(number)_ — Duration in seconds
* `bufferDuration` _(number)_ — Buffer window after epoch ends
* `withdrawType` _(string)_ — Either `INSTANT` or `STANDARD`
* `withdraws` _(object)_ —
  * `amount` _(string)_
  * `fees` _(string)_
* `depositQueue` _(object)_ —
  * `amount` _(string)_
  * `lastAmount` _(string)_
  * `isInstant` _(boolean)_
* `withdrawQueue` _(object)_ —
  * `amount` _(string)_
  * `lastAmount` _(string)_
  * `isInstant` _(boolean)_
* `instantWithdraws` _(object)_ —
  * `allowed` _(boolean)_
  * `delay` _(number)_
  * `amount` _(string)_
  * `aprDelta` _(number)_
  * `deadline` _(string)_ — ISO 8601 date
  * `disabled` _(boolean)_
* `createdAt`, `updatedAt` _(string)_ — ISO 8601 timestamps
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/VaultEpochs (1).yaml" path="/vault-epochs" method="get" %}
[VaultEpochs (1).yaml](<../../.gitbook/assets/VaultEpochs (1).yaml>)
{% endopenapi %}
