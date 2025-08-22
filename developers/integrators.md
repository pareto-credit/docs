# Integrators

This guide describes how to integrate Credit Vaults into Web3 applications using both the Pareto REST API and smart contracts. It is intended for technical audiences familiar with Web3 and frontend frameworks.

## Smart contract instantiation

To interact with on-chain vaults, tokens, and queue contracts, a Web3 contract instance must be created using the ABI and contract address:

```ts
const contract = new web3.eth.Contract(abi, address);
```

This pattern is required for calling methods such as `depositAA`, `requestWithdraw`, `approve`. Refer to the [Web3.js documentation](https://web3js.readthedocs.io/en/v1.10.0/web3-eth-contract.html) for full syntax details.

## API access

All API routes below belong to the public Pareto API at `https://api.pareto.credit`. Refer to the [API chapter](api/) for a complete overview.

### 1. Required vault data

These steps outline the minimum data required to render a vault and enable interactions.

{% stepper %}
{% step %}
#### Load [Vault Entity](https://docs.pareto.credit/developers/api/vaults)

Use either:

* `GET /v1/vaults/{_id}` — by ID
* `GET /v1/vaults?address=0x...` — by address

Only vaults with `visibility: PUBLIC` should be displayed to users.
{% endstep %}

{% step %}
#### Retrieve Signatures

Each vault contains a `signatures` array of the form:

```ts
interface VaultSignature {
  _id: string;
  entity: "ALL" | "LENDER" | "MANAGER";
}
```

Filter all entries where `entity === 'LENDER'`and use the `_id` values to load them. This returns the full signature definitions required to enable future user interactions.

```ts
GET /v1/signatures?_id=signatureId1,signatureId2
```
{% endstep %}

{% step %}
#### Retrieve underlying [Token](https://docs.pareto.credit/developers/api/tokens)

Retrieve token information with:

`GET /v1/tokens/{vault.tokenId}`
{% endstep %}

{% step %}
#### Fetch latest [Vault Block](https://docs.pareto.credit/developers/api/vault-blocks)

Fetch up-to-date vault metrics. This includes share price, APRs, TVL, and queue configuration.

`GET /v1/vault-latest-block?vaultId={vault._id}`
{% endstep %}
{% endstepper %}

### 2. Required wallet access data

Defines the required checks and data fetches needed immediately after wallet connection to verify access and authorization for vault operations.

{% stepper %}
{% step %}
#### Check wallet access

To verify if a wallet is allowed to interact with the vault, instantiate the contract using the `cdoEpoch` ABI and address retrieved from the vault JSON:

```ts
const cdoEpochContract = new web3.eth.Contract(
  vault.cdoEpoch.abi,
  vault.cdoEpoch.address
);
const isAllowed = await cdoEpochContract.methods
  .isWalletAllowed(walletAddress)
  .call();
```

If `isAllowed` is false, initiate a KYC flow using [Keyring Connect SDK](https://www.npmjs.com/package/@keyringnetwork/keyring-connect-sdk). Only after KYC is completed, it's possible to proceed with next steps.
{% endstep %}

{% step %}
#### Signature Validation

Check if the required signatures have been signed:

`GET /v1/signatures/{signatureId}/check?walletAddress=0x...123`

otherwise

```ts
const hash = await web3.eth.personal.sign(
  atob(walletMessage),
  walletAddress,
  ""
);
```

Submit the signed hash:

`POST /v1/signatures/{signatureId}/sign`
{% endstep %}

{% step %}
#### Retrieve Wallet Position

Retrieve the wallet position to track users' deposits, claims, and balances.

```ts
GET /v1/vaults/{vaultId}/position?walletAddress={walletAddress}
```
{% endstep %}
{% endstepper %}

### 3. Deposit flow

Outlines the required contract state and transactions to successfully execute a deposit into a vault.

{% stepper %}
{% step %}
#### Verify allowance and balance

Instantiate the token contract using the vault token ABI and address:

```ts
const tokenContract = new web3.eth.Contract(token.abi, token.address);
const allowance = await tokenContract.methods
  .allowance(walletAddress, spenderAddress)
  .call({ from: walletAddress });
const balance = await tokenContract.methods
  .balanceOf(walletAddress)
  .call({ from: walletAddress });
```

Spender can be found in:

* `vault.cdoEpoch.address` if `NETTING`
* `vault.depositQueue.address` if `RUNNING`
{% endstep %}

{% step %}
#### Approve token spending

Make sure the amount respects the token's decimals (6 for USDC, USDT, 18 for the majority of ERC-20 tokens).

```ts
await tokenContract.methods
  .approve(spenderAddress, amount)
  .send({ from: walletAddress });
```
{% endstep %}

{% step %}
#### Submit Deposit Transaction

```ts
// during NETTING period
await cdoEpochContract.methods.depositAA(amount).send({ from: walletAddress });
// during cycle RUNNING
await depositQueueContract.methods
  .requestDeposit(amount)
  .send({ from: walletAddress });
```
{% endstep %}
{% endstepper %}

### 4. Withdraw flow

Describes how to prepare, calculate, and submit a withdrawal request from a vault, depending on its current status and configuration.

{% stepper %}
{% step %}
#### Fetch user balance and max withdrawable amount

```ts
const vaultTokenContract = new web3.eth.Contract(
  vaultToken.abi,
  vaultToken.address
);
const balance = await vaultTokenContract.methods
  .balanceOf(walletAddress)
  .call({ from: walletAddress });
const maxWithdrawable = await cdoEpochContract.methods
  .maxWithdrawable(walletAddress, vault.address)
  .call({ from: walletAddress });
```
{% endstep %}

{% step %}
#### Check allowance

The allowance must be verified only when the vault is in RUNNING state and uses the withdrawQueue. In this case, the `withdrawQueue` contract address should be used as `spender`.

```ts
const spender = vault.cdoEpoch.withdrawQueue.address;
const allowance = await tokenContract.methods
  .allowance(walletAddress, spender)
  .call({ from: walletAddress });
```
{% endstep %}

{% step %}
#### Compute the LP tokens required

```ts
const percentage = 1000000 / maxWithdrawable;
const lpAmount = LPDeposited * percentage;
```
{% endstep %}

{% step %}
#### Submit withdrawal request

Track requests in the `block.requests` array

```ts
// during NETTING period
await vaultContract.methods
  .requestWithdraw(lpAmount, vault.address)
  .send({ from: walletAddress });
// during cycle RUNNING
await withdrawQueueContract.methods
  .requestWithdraw(lpAmount)
  .send({ from: walletAddress });
```
{% endstep %}
{% endstepper %}

## Managing requests

There are 3 types of request:&#x20;

* `DEPOSIT`: available during the  `RUNNING` state of the vault cycle
* `WITHDRAW`: available during the `RUNNING` state of the vault cycle. This request can be "standard" or "instant" depending on the next cycle APR.
* `REDEEM`: available during the `WAITING` state of the vault cycle. This request can be "standard" or "instant" depending on the next cycle APR.

All requests must be claimed by users after they have been processed:&#x20;

* `DEPOSIT`: this request can be claimed starting from the next `WAITING` period
* `WITHDRAW`:&#x20;
  * if **standard**: the request can be claimed at the end of the next cycle, during the `WAITING` period
  * if **instant**: the request can be claimed during the `RUNNING` period of the next cycle, after the instant withdrawal delay (usually few days).
* `REDEEM`:
  * if **standard**: the request can be claimed at the end of the next cycle, during the `WAITING` period
  * if **instant**: the request can be claimed during the `RUNNING` period of the next cycle, after the instant withdrawal delay (usually few days).

\
All  vault requests are tracked in the `requests` array of the `vaultBlock` object. Each entry in this array represents a user-initiated interaction awaiting processing.

{% code title="Request schema" %}
```ts
interface VaultBlockRequest {
  type: "DEPOSIT" | "WITHDRAW" | "REDEEM";
  amount: iBigInt;
  block: Block;
  isInstant?: boolean;
  requestedOn: string;
  walletId: string;
  walletAddress: string;
  status:
    | "PENDING"
    | "PROCESSED"
    | "CLAIMABLE"
    | "INSTANT_CLAIMABLE"
    | "CLAIMED";
  epochNumber?: number;
}
```
{% endcode %}

where

* `type`: Indicates if it's a `DEPOSIT`, `WITHDRAW`, or `REDEEM`.
* `status`: Tracks the lifecycle of the request (e.g., `PENDING`, `CLAIMABLE`, etc.).
* `isInstant`: Only present for withdrawals that qualify as "instant".
* `block`: Indicates the vault block in which the request was recorded.
* `epochNumber` : the epochNumber that must be passed to the requests methods

Requests are retrieved from the latest vault block:

```ts
GET /v1/vault-latest-block?vaultId={vaultId}
```

### Deposit requests

Handled via the queue contract `vault.cdoEpoch.depositQueue`

* It's possible to claim the request if `status === 'CLAIMED'`

```ts
await depositQueueContract.methods
  .claimDepositRequest(request.epochNumber)
  .send({ from: walletAddress });
```

* It's possible to cancel the request if `status === 'PENDING'`

```ts
await depositQueueContract.methods
  .deleteRequest(request.epochNumber)
  .send({ from: walletAddress });
```

### Withdraw requests

Handled via the queue contract `vault.cdoEpoch.withdrawQueue`

* For both standard and instant requests, it's possible to claim the request if  `status === 'CLAIMED'`

```ts
await withdrawQueueContract.methods
  .claimWithdrawRequest(request.epochNumber)
  .send({ from: walletAddress });
```

* It's possible to cancel the request if `status === 'PENDING'`

```ts
await withdrawQueueContract.methods
  .deleteWithdrawRequest(request.epochNumber)
  .send({ from: walletAddress });
```

### Redeem requests

Handled via the main vault contract `vault.cdoEpoch`

* It's possible to claim the request if `status === 'CLAIMED'`:

```ts
await cdoEpochContract.methods
  .claimWithdrawRequest()
  .send({ from: walletAddress });
```

* If `request.isInstant === true`  it's possible to use:

```ts
await cdoEpochContract.methods
  .claimInstantWithdrawRequest()
  .send({ from: walletAddress });
```

### Lifecycle summary&#x20;

| Request type        | When     | Claimable From                                         |
| ------------------- | -------- | ------------------------------------------------------ |
| DEPOSIT             | RUNNING  | Next WAITING period                                    |
| WITHDRAW (standard) | RUNNING  | End of next cycle (WAITING)                            |
| WITHDRAW (instant)  | RUNNING  | RUNNING of next cycle (after instant withdrawal delay) |
| REDEEM (standard)   | WAITING  | End of next cycle (WAITING)                            |
| REDEEM (instant)    | WAITING  | RUNNING of next cycle (after instant withdrawal delay) |
