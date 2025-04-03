# API

## Pareto API Documentation

Welcome to the official API documentation for **Pareto**. These APIs provide access to campaign data, vault analytics, token information, and on-chain integrations that power the Pareto protocol.

***

### 🌐 Base URL

```
https://api.pareto.credit/
```

***

### 🚀 Quick Start

1. **Request an API key** via [this form](https://tally.so/r/mY9y6B)
2. Use your preferred HTTP client (e.g., Postman, Axios, cURL)
3. Add your API key as a **Bearer Token** in the `Authorization` header
4. Start by fetching vaults or campaigns data:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" https://api.pareto.credit/v1/vaults
```

***

### 🔐 Authentication

Pareto API requires authentication via **Bearer Token**.

#### How to get your API key

* Fill out [this request form](https://tally.so/r/mY9y6B)
* Once approved, you will receive an API key to use in your requests

#### Example Header

```
Authorization: Bearer YOUR_API_KEY
```

***

### 🧭 Endpoints Reference

#### 📣 Campaigns

* `GET /v1/campaigns` — List all campaigns
* `GET /v1/campaigns/:campaignId` — Get campaign by ID
* `GET /v1/campaigns/:campaignId/points` — Get campaign points

#### 🔗 Chains

* `GET /v1/chains` — List supported chains
* `GET /v1/chains/:chainId` — Get chain by ID

#### ⚙️ Operators

* `GET /v1/operators` — List all operators
* `GET /v1/operators/:operatorId` — Get operator by ID

#### 🧱 Token Blocks

* `GET /v1/token-blocks` — List all token blocks
* `GET /v1/token-blocks/:tokenBlockId` — Get token block by ID

#### 🪙 Tokens

* `GET /v1/tokens` — List all tokens
* `GET /v1/tokens/:tokenId` — Get token by ID

#### 🔁 Transactions

* `GET /v1/transactions` — List all protocol transactions

#### 📦 Vault Blocks

* `GET /v1/vault-blocks` — List all vault blocks
* `GET /v1/vault-blocks/:vaultBlockId` — Get vault block by ID

#### 🗂 Vault Categories

* `GET /v1/vault-categories` — List all vault categories
* `GET /v1/vault-categories/:typeId` — Get category by ID

#### 📆 Vault Epochs

* `GET /v1/vault-epochs` — List all vault epochs

#### ⛓ Vault Latest Blocks

* `GET /v1/vault-latest-blocks` — Latest block data for vaults

#### 📊 Vault Performances

* `GET /v1/vault-performances` — List all performance metrics
* `GET /v1/vault-performances/:vaultPerformanceId` — Get performance by ID

#### 🧬 Vault Types

* `GET /v1/vault-types` — List all vault types
* `GET /v1/vault-types/:typeId` — Get vault type by ID

#### 🔐 Vaults

* `GET /v1/vaults` — List all vaults
* `GET /v1/vaults/:vaultId` — Get vault by ID
* `GET /v1/vaults/performances` — Vault performance overview
* `GET /v1/vaults/:vaultId/integrations` — Integrations by vault ID

***

### :scroll:  Listing behavior

Many `GET` endpoints in the Pareto API return lists of resources. These endpoints support common features that make the API flexible and developer-friendly. \
This is the common pagination result: \


**🔢  Pagination**

To control the number of items returned per request, you can use:

* `limit` — Number of items to return (default: 50, max: 200)
* `offset` — Number of items to skip before starting to collect the result set

Example:

```
GET /v1/vaults?limit=10&offset=20
```

**🔃 Sorting**

The `sort` query parameter allows you to sort results by one or more fields:

* Use `field` for ascending order
* Use `-field` for descending order

Example:

```
GET /v1/vaults?sort=-createdAt
```

**🎯 Projection (Field Selection)**

Some endpoints support the `fields` parameter, which lets you request only specific fields per object:

Example:

```
GET /v1/vaults?fields=_id,name,createdAt
```

This can be useful for optimizing network performance and response size.

***

### 📚 Glossary

* **Campaign**: A campaign is a points-based engagement program that allows users to earn rewards by interacting with Pareto.
* **Vault**: A yield-generating smart contract product.
* **Epoch**: A predefined period for vault accounting.
* **Operator**: An entity managing or integrating with Pareto products.
* **Integration**: A system or protocol connected to Pareto’s infrastructure.

***

### 📌 Changelog

Pareto API is currently at **version 1.18.0**.

This is the active **major version v1** of the API. All endpoints documented here are part of the `v1` namespace.

A dedicated changelog page with detailed version history and updates will be available soon.

***

### 🛠 SDKs & Tools

A dedicated SDK is currently in **beta-testing** and will soon be available as an NPM package.

The SDK will allow seamless integration with Pareto APIs in JavaScript/TypeScript projects.

A dedicated section with **practical examples** and SDK usage guides will be published soon.

***

### ❓ Support

For help, join our [Discord server](https://discord.com/invite/mpySAJp).
