# Vault types

Vault types define internal classifications for vault contracts, distinguishing between different logic structures, yield mechanisms, or operational modes. These types are useful for rendering vault metadata, sorting/filtering in UI, or determining strategy compatibility.

## Structure

Each vault type object includes:

* `_id` _(string)_ — Unique identifier
* `code` _(string)_ — Internal reference code for the vault type
* `name` _(object)_ — Multilingual object (e.g., `{ en: "Fixed Rate" }`)
* `description` _(object)_ — Multilingual description object
* `createdAt`, `updatedAt` _(string)_ — ISO UTC timestamps
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/VaultTypes.yaml" path="/vault-types" method="get" %}
[VaultTypes.yaml](../../.gitbook/assets/VaultTypes.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/VaultTypes.yaml" path="/vault-types/{typeId}" method="get" %}
[VaultTypes.yaml](../../.gitbook/assets/VaultTypes.yaml)
{% endopenapi %}
