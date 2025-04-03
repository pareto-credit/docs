# Operators

Operators represent key entities that interact with the Pareto protocol. These can be protocol partners, borrowers, or curators responsible for deploying or managing vaults.

## Structure

Each operator object includes:

* `_id` _(string)_ — Unique identifier
* `name` _(string)_ — Name of the operator
* `code` _(string)_ — Unique internal code
* `type` _(string)_ — One of: `PROTOCOL`, `BORROWER`, `CURATOR`
* `description` _(object)_ — Multilingual description (e.g. `{ en: "This is a protocol operator" }`)
* `shortDescription` _(object)_ — Short version of the description
* `caption` _(object)_ — Promotional caption
* `rating` _(string)_ — Internal rating indicator
* `location` _(string)_ — Geographic reference
* `links` _(object)_ —
  * `website` _(string)_
  * `twitter` _(string)_
  * `linkedIn` _(string)_
  * `crunchbase` _(string)_
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps (UTC Unix time)
* `createdBy`, `updatedBy` _(string)_ — Actor IDs for auditing

## Endpoints

{% openapi src="../../.gitbook/assets/Operators.yaml" path="/operators" method="get" %}
[Operators.yaml](../../.gitbook/assets/Operators.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/Operators.yaml" path="/operators/{operatorId}" method="get" %}
[Operators.yaml](../../.gitbook/assets/Operators.yaml)
{% endopenapi %}
