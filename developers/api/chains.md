# Chains

Chains represent the supported blockchain networks that Pareto interacts with. These include the networks where vaults are deployed, where user interactions occur, and where transactions are tracked.

### 🧬 Model Structure

Each chain object includes:

* `_id` _(string)_ — Unique identifier for the chain
* `name` _(string)_ — Human-readable name of the blockchain (e.g., "Ethereum")
* `chainId` _(string)_ — Network ID used internally by Pareto
* `nativeToken` _(string)_ — Symbol of the native currency (e.g., ETH, MATIC)
* `explorerUrl` _(string)_ — Block explorer base URL for linking transactions or accounts
* `icon` _(string)_ — Optional icon URL
* `createdAt`, `updatedAt` _(string)_ — ISO timestamps (UTC Unix time)

### 📡 Endpoints
