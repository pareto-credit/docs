# Vault categories

Vault categories group vaults by their investment profile, strategy type, or risk classification. They provide high-level metadata for sorting and UI filtering.

## Structure

Each vault category object includes:

* `_id` _(string)_ — Unique identifier
* `code` _(string)_ — Unique category code (e.g. "CAT\_1", "STRATEGY")
* `name` _(object)_ — Multilingual name (e.g., `{ en: "Stable Yield" }`)
* `description` _(object)_ — Multilingual description
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps (UTC Unix time)
* `createdBy`, `updatedBy` _(string)_ — Actor IDs

## Endpoints

{% openapi src="../../.gitbook/assets/VaultCategories.yaml" path="/vault-categories" method="get" %}
[VaultCategories.yaml](../../.gitbook/assets/VaultCategories.yaml)
{% endopenapi %}

{% openapi src="../../.gitbook/assets/VaultCategories.yaml" path="/vault-categories/{typeId}" method="get" %}
[VaultCategories.yaml](../../.gitbook/assets/VaultCategories.yaml)
{% endopenapi %}
