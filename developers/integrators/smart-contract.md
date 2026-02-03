# Smart contract

## Credit Vaults Integrator Guide

### Overview

Credit Vaults are epoch-based lending vaults built on top of `IdleCDOEpochVariant` (the vault) and `IdleCreditVault` (the strategy). Lenders deposit into AA or BB tranches and receive tranche ERC20 tokens. At the start of each epoch, funds are lent directly to a single borrower. At the end of the epoch, the borrower repays interest and funds needed to satisfy pending withdrawals. Tranche prices are updated at epoch stop, and lenders can request withdrawals only between epochs (except for the optional mid-epoch deposit flow).

Key properties:

* Epochs gate deposits and withdrawals. During an epoch, the vault is paused and normal deposits/withdraw requests are blocked.
* Withdrawals are two-step: request between epochs, claim after at least one full epoch has passed.
* If the APR drops enough between epochs and instant withdrawals are enabled, a request becomes an instant withdraw.
* The strategy token (`IdleCreditVault`) has a fixed price of 1 underlying unit and is used as a receipt token for withdrawal requests (because tranche tokens are burned on request).
* KYC gating is enforced via Keyring (`isWalletAllowed`).

Roles (for context only):

* Lenders/integrators: deposit into tranches and manage withdrawals.
* Borrower: receives funds at epoch start, repays at epoch end, may fulfill write-off escrow requests.
* Manager/owner: starts/stops epochs and configures parameters (not covered here).

### Epoch lifecycle (high level)

1. **Buffer period (between epochs)**
   * `isEpochRunning == false`
   * Deposits and withdrawal requests are allowed.
2. **Epoch start**
   * Manager calls `startEpoch()` (not covered here).
   * `isEpochRunning == true`, deposits/withdraw requests paused.
   * Underlyings are sent to the borrower; instant-withdraw funding is handled.
3. **Epoch running**
   * Optional `depositDuringEpoch()` may be enabled.
   * Optional Write-off escrow is available for lenders and borrower to request a withdraw (borrower needs to accept the request).
4. **Epoch stop**
   * Manager calls `stopEpoch()` (not covered here).
   * Interest and pending withdrawal funds are collected.
   * Tranche prices update; new APR set.
   * `isEpochRunning == false` and buffer period starts again.

### Contracts and data model

* **IdleCDOEpochVariant**: main vault contract. Inherits `IdleCDO` and adds epoch logic plus request/claim flows.
* **IdleCreditVault**: strategy/receipt-token contract. Holds pending withdrawal funds and mints receipt tokens for requests.
* **IdleCDOTranche (AA/BB tokens)**: ERC20 tranche tokens with 18 decimals (`ONE_TRANCHE_TOKEN = 1e18`) used as receipt for deposits.
* **IdleCreditVaultWriteOffEscrow**: escrow for in-epoch write-off exits; borrower can buy out lender positions and optionally write off debt.

### Integrator flows

#### 1) Deposit between epochs (standard)

Use this flow when `isEpochRunning == false` (vault not paused).

1. Confirm `IdleCDOEpochVariant.isWalletAllowed(user)` is `true`.
2. `approve` the vault (`IdleCDOEpochVariant`) to spend the underlying token.
3. Call `depositAA(amount)` or `depositBB(amount)`.
4. Receive AA or BB tranche tokens (ERC20, 18 decimals).

Notes:

* Deposits are blocked while the epoch is running because the vault is paused.
* Tranche token price for valuation should be read via `virtualPrice(tranche)`.
* Deposits always require `isWalletAllowed(user) == true` (no bypass).

#### 2) Deposit during an epoch (optional)

Use only if mid-epoch deposits are enabled.

1. Ensure `isEpochRunning == true`, `block.timestamp < epochEndDate`, and `isDepositDuringEpochDisabled == false`.
2. Ensure AYS is not active (`isAYSActive == false`).
3. Approve underlying to the vault.
4. Call `depositDuringEpoch(amount, tranche)`.

Notes:

* The first deposit into a tranche cannot be mid-epoch (tranche supply must be > 0).
* The minted amount is prorated based on remaining epoch time plus the buffer period.
* Funds are immediately transferred to the borrower; strategy tokens are minted to the vault.

#### 3) Normal withdraw (request + claim)

Use this flow between epochs (`isEpochRunning == false`).

1. Call `requestWithdraw(trancheAmount, tranche)`.
   * If `trancheAmount == 0`, the vault treats it as "withdraw all."
   * The function burns tranche tokens and mints receipt tokens in `IdleCreditVault` for the user.
   * The return value is the **underlyings requested**, including next-epoch net interest (no buffer-period interest).
2. Wait at least one epoch. Claim is allowed when:
   * `IdleCreditVault.epochNumber > IdleCreditVault.lastWithdrawRequest(user)`
   * or the vault is closed (`epochEndDate == 0`).
3. Call `claimWithdrawRequest()`.
   * This burns receipt tokens and transfers underlyings from `IdleCreditVault`.

Notes:

* Requests require `isWalletAllowed(user) == true` unless `keyringAllowWithdraw == true`.

#### 4) Instant withdraw (APR drop)

Instant withdrawals are triggered automatically by `requestWithdraw` when:

* `disableInstantWithdraw == false`, and
* `lastEpochApr > IdleCreditVault.unscaledApr() + instantWithdrawAprDelta`.

Flow:

1. Call `requestWithdraw(trancheAmount, tranche)` between epochs.
2. If conditions are met, the request becomes instant:
   * Tranche tokens are burned.
   * Receipt tokens are minted in `IdleCreditVault`.
   * The requested amount equals current underlyings (no next-epoch interest).
3. Claim when `allowInstantWithdraw == true`:
   * This may be true immediately after epoch start if new deposits cover the requests.
   * Otherwise it becomes true after `instantWithdrawDeadline` and manager calls `getInstantWithdrawFunds()`.
4. Call `claimInstantWithdrawRequest()`.

#### 5) Write-off escrow flow (during epoch)

Used to let lenders exit during an epoch by selling tranche tokens to the borrower. This flow is only available if a `IdleCreditVaultWriteOffEscrow` is deployed for the specific credit vault, and the exit is only completed once the borrower accepts it by fulfilling the request.

Lender flow:

1. Ensure `IdleCDOEpochVariant.isEpochRunning() == true` and wallet is allowed.
2. `approve` the escrow to transfer tranche tokens.
3. Call `createWriteOffRequest(trancheAmount, underlyingsRequested)`.
   * Requests accumulate per user; amounts are not auto-priced.
4. Optional: call `deleteWriteOffRequest()` to cancel and reclaim tranche tokens.

Borrower flow:

1. Call `fullfillWriteOffRequest(user, trancheAmount, underlyingsRequested)`.
   * Borrower transfers underlyings to escrow.
   * Exit fee (if set) is taken, remainder is sent to the lender.
   * Tranche tokens are transferred to the borrower.
2. Borrower may then call `writeOffDeposit()` on `IdleCDOEpochVariant` to burn tranche + strategy tokens and reduce expected interest (borrower-only).

#### 6) Default considerations

If the borrower defaults, the vault sets `defaulted == true`, pauses deposits/withdraw requests, and enables transfers of receipt tokens via `IdleCreditVault.allowTransfers()`. Integrators should monitor the `BorrowerDefault` event and treat receipt tokens as claims on whatever recovery process is used.

### Integrator-facing methods (by contract)

#### IdleCDOEpochVariant (vault)

User/integrator calls:

* `depositAA(uint256 amount) returns (uint256 minted)` - Deposit underlying into AA; mints AA tranche tokens (not strategy tokens). `amount` is underlying.
* `depositBB(uint256 amount) returns (uint256 minted)` - Deposit underlying into BB; mints BB tranche tokens (not strategy tokens). `amount` is underlying.
* `depositDuringEpoch(uint256 amount, address tranche) returns (uint256 minted)` - Mid-epoch deposit; `tranche` is `AATranche` or `BBTranche`.
* `requestWithdraw(uint256 trancheAmount, address tranche) returns (uint256 underlyingsRequested)` - Create a withdrawal request; `trancheAmount` is tranche tokens, `tranche` is AA/BB.
* `claimWithdrawRequest()` - Claim a normal withdraw request once eligible (>= 1 epoch after last request).
* `claimInstantWithdrawRequest()` - Claim an instant withdraw request when `allowInstantWithdraw` is true.

Views (from `IdleCDO` / `IdleCDOEpochVariant`):

* `virtualPrice(address tranche) view returns (uint256)` - Tranche price, in underlyings, including accrued interest.
* `getApr(address tranche) view returns (uint256)` - Current APR for the tranche.
* `getContractValue() view returns (uint256)` - Vault NAV in underlying terms.
* `maxWithdrawable(address user, address tranche) view returns (uint256)` - Max underlyings for a normal request.
* `isWalletAllowed(address user) view returns (bool)` - Keyring/KYC allowlist check.

Key public state to read:

* `AATranche`, `BBTranche`, `token`, `strategy`
* `isEpochRunning`, `epochEndDate`, `epochDuration`, `bufferPeriod`
* `instantWithdrawDelay`, `instantWithdrawDeadline`, `instantWithdrawAprDelta`
* `allowInstantWithdraw`, `disableInstantWithdraw`
* `allowAAWithdrawRequest`, `allowBBWithdrawRequest`
* `keyringAllowWithdraw`, `defaulted`, `lastEpochApr`, `lastEpochInterest`, `expectedEpochInterest`
* `fee`, `feeReceiver`, `trancheAPRSplitRatio`, `isAYSActive`, `isDepositDuringEpochDisabled`

Not supported (no-op in Credit Vaults):

* `withdrawAA`, `withdrawBB`, `depositAARef`, `depositBBRef`

#### IdleCreditVault (strategy / receipt token)

Read-only for integrators (calls are restricted to `IdleCDOEpochVariant` for state-changing methods).

User-visible reads:

* Receipt token ERC20: `balanceOf(user)`, `totalSupply()`, `decimals()` - Receipt tokens for pending requests.
* Request tracking: `withdrawsRequests(user)`, `instantWithdrawsRequests(user)`, `lastWithdrawRequest(user)` - Per-user request state.
* Totals: `pendingWithdraws`, `pendingInstantWithdraws`, `totEpochDeposits` - Aggregate counters.
* Epoch tracking: `epochNumber` - Strategy epoch counter for claim eligibility.
* APR data: `getApr()` (scaled), `unscaledApr()` - APR used for epoch interest math.
* Metadata: `token`, `tokenDecimals`, `borrower`, `manager`, `price()` (always `oneToken`) - Vault metadata.
* Transferability: `canTransfer` (false unless defaulted) - Receipt token transfer flag.

#### IdleCreditVaultWriteOffEscrow

User/borrower calls:

* `createWriteOffRequest(uint256 trancheAmount, uint256 underlyingsRequested)` - Lender proposes exit terms (requires escrow deployed).
* `deleteWriteOffRequest()` - Lender cancels and retrieves tranche tokens.
* `fullfillWriteOffRequest(address user, uint256 trancheAmount, uint256 underlyingsRequested)` - Borrower accepts and settles the request (borrower-only).

Views:

* `userRequests(user) -> (tranches, underlyings)`
* `idleCDOEpoch`, `strategy`, `underlying`, `tranche`, `borrower`
* `exitFee`, `feeReceiver`

### Monitoring positions and claimability

#### Active tranche position

* Tranche balance: `IERC20(AATranche|BBTranche).balanceOf(user)`
* Valuation: `balance * virtualPrice(tranche) / 1e18`
* `virtualPrice` includes accrued interest since last accounting update.

#### Pending withdrawal position

* Normal: `IdleCreditVault.withdrawsRequests(user)`
* Instant: `IdleCreditVault.instantWithdrawsRequests(user)`
* Receipt balance: `IdleCreditVault.balanceOf(user)`
  * In steady state, this equals normal + instant requests.

#### Escrowed position

* `IdleCreditVaultWriteOffEscrow.userRequests(user)`
  * `tranches` and `underlyings` requested are both tracked.

#### Claim eligibility

* Normal withdraw claim:
  * `IdleCreditVault.epochNumber > IdleCreditVault.lastWithdrawRequest(user)`
  * or `IdleCDOEpochVariant.epochEndDate == 0` (vault closed)
* Instant withdraw claim:
  * `IdleCDOEpochVariant.allowInstantWithdraw == true`

#### Timing checks

* Epoch status: `isEpochRunning`, `epochEndDate`
* Instant deadline: `instantWithdrawDeadline`

### Practical integration tips

* Tranche tokens are 18 decimals; underlying decimals vary. Convert carefully.
* `requestWithdraw(0, tranche)` withdraws the full tranche balance.
* `maxWithdrawable(user, tranche)` is useful for estimating a normal request amount (net of fees).
* Mid-epoch deposits are disallowed when `isDepositDuringEpochDisabled == true` or `isAYSActive == true`.
* Receipt tokens are non-transferable until `IdleCreditVault.canTransfer == true` (typically on default).
