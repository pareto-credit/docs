# API

Pareto APIs provide access to campaign data, vault analytics, token information, and third-party integrated services.

{% code title="Base URL" %}
```
https://api.pareto.credit/
```
{% endcode %}

This is the active **major version v1** of the API. All endpoints listed are part of the `v1` namespace.

## Quick Start

1. **Request an API key** via [this form](https://tally.so/r/mY9y6B). Once approved, you will receive a key for authentication
2. Use your preferred HTTP client (e.g., Postman, Axios, cURL)
3. Add your API key as a **Bearer Token** in the `Authorization` header
4. Start by fetching vaults or campaign data:

<pre class="language-bash"><code class="lang-bash"><strong>curl -H "Authorization: Bearer YOUR_API_KEY" https://api.pareto.credit/v1/vaults
</strong></code></pre>

## Endpoints

### Campaigns

A campaign is a points-based engagement program that allows users to earn rewards by interacting with Pareto.

* `GET /v1/campaigns` — List all campaigns
* `GET /v1/campaigns/:campaignId` — Get campaign by ID
* `GET /v1/campaigns/:campaignId/points` — Get campaign points

### Chains

* `GET /v1/chains` — List supported chains
* `GET /v1/chains/:chainId` — Get chain by ID

### Operators

An entity managing or integrating with Pareto products.

* `GET /v1/operators` — List all operators
* `GET /v1/operators/:operatorId` — Get operator by ID

### Token blocks

* `GET /v1/token-blocks` — List all token blocks
* `GET /v1/token-blocks/:tokenBlockId` — Get token block by ID

### Tokens

* `GET /v1/tokens` — List all tokens
* `GET /v1/tokens/:tokenId` — Get token by ID

### Transactions

* `GET /v1/transactions` — List all protocol transactions

### Vaults

A yield-generating smart contract product.

* `GET /v1/vaults` — List all vaults
* `GET /v1/vaults/:vaultId` — Get vault by ID
* `GET /v1/vaults/performances` — Vault performance overview
* `GET /v1/vaults/:vaultId/integrations` — Integrations by vault ID

### Vault blocks

* `GET /v1/vault-blocks` — List all vault blocks
* `GET /v1/vault-blocks/:vaultBlockId` — Get vault block by ID
* `GET /v1/vault-latest-blocks` — Latest block data for vaults

### Vault categories

* `GET /v1/vault-categories` — List all vault categories
* `GET /v1/vault-categories/:typeId` — Get category by ID

### Vault epochs

A predefined period for vault accounting.

* `GET /v1/vault-epochs` — List all vault epochs

### Vault performances

* `GET /v1/vault-performances` — List all performance metrics
* `GET /v1/vault-performances/:vaultPerformanceId` — Get performance by ID

### Vault types

* `GET /v1/vault-types` — List all vault types
* `GET /v1/vault-types/:typeId` — Get vault type by ID

## Listing

All listing endpoints across the API (e.g., `/vaults`, `/transactions`, `/tokens`, etc.) follow a consistent response format in consuming paginated data throughout the API:

* `data`: an array of returned resources.
* `totalCount`: the total number of resources matching the query, useful for pagination.

```
{
  "data": [ /* items */ ],
  "totalCount": 123
}
```

## **Pagination, sorting, fields**

To control the number of items returned per request, you can use:

* `limit` — Number of items to return (default: 50, max: 200)
* `offset` — Number of items to skip before starting to collect the result set

```
GET /v1/vaults?limit=10&offset=20
```

The `sort` query parameter allows you to sort results by one or more fields:

* Use `field` for ascending order
* Use `-field` for descending order

```
GET /v1/vaults?sort=-createdAt
```

Some endpoints support the `fields` parameter, which lets you request only specific fields per object:

This can be useful for optimizing network performance and response size.

```
GET /v1/vaults?fields=_id,name,createdAt
```
