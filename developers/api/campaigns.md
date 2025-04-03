# Campaigns

Campaigns are point-based programs used to incentivize user activities on Pareto. Users can earn points by interacting with vaults or participating in social activities.

## Structure

Each campaign object includes:

* `_id` _(string)_ — Unique identifier
* `code` _(string)_ — Campaign code
* `name` _(object)_ — Multilingual name, e.g. `{ en: "My Campaign" }`
* `description` _(object)_ — Multilingual description
* `rules` _(array)_ — Conditions for earning points:
  * `name`, `description` _(object)_ — i18n fields
  * `trigger` _(string)_ — `DEPOSIT` or `DEPOSIT_REQUEST`
  * `deposit` _(object)_ —
    * `type`: `BALANCE` or `AGE`
    * `value`: number
  * `reward` _(object)_ —
    * `type`: `AMOUNT` or `MULTIPLIER`
    * `value`: number
  * `frequency` _(object)_ — repetition rules (`value`, `unit`)
* `referrals` _(array)_ — Invite codes with activation flag
* `startDate` / `endDate` _(string)_ — ISO datetime
* `link` _(string)_ — URL
* `galxeId` _(number)_ — External reference
* `createdAt`, `updatedAt` _(string)_ — ISO timestamp
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/Campaigns.yaml" path="/campaigns" method="get" %}
[Campaigns.yaml](../../.gitbook/assets/Campaigns.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/Campaigns.yaml" path="/campaigns/{campaignId}" method="get" %}
[Campaigns.yaml](../../.gitbook/assets/Campaigns.yaml)
{% endopenapi %}
