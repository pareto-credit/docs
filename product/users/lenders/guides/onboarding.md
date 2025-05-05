# Onboarding

This guide outlines the steps required to complete the onboarding for liquidity providers on Pareto. The process consists of two main phases: verification and contract signature.

To begin:

* Visit [app.pareto.credit](https://app.pareto.credit)
* Select a Credit Vault (e.g., the Fasanara pool)
* Connect a supported wallet

{% embed url="https://youtu.be/_FRz584LFwo" %}

{% stepper %}
{% step %}
#### Verification

By default, Credit Vaults offer a zero-knowledge (ZK) verification method powered by Keyring Connect. In select cases, a standard KYC process with document uploads should be followed instead.&#x20;

Keyring Connect integrates existing KYC credentials from centralized exchanges such as Binance, Kraken, and Coinbase. This enables seamless verification while preserving privacy and minimizing data exposure.

* Install the Keyring Chrome extension
* Launch the verification process
* Create or log in to a Keyring account. Verify the account
* Select a centralized exchange (CEX) within the extension to link credentials
* Authenticate and complete the verification process
* Generate credentials. A blockchain transaction will be triggered to cover attestation fees
* Refresh the app page

{% hint style="info" %}
Note: Verified credentials aren't shared across all vaults on Pareto. New credentials must be generated for each vault the user interacts with. Keyring may request periodic re-authentication, similar to session revalidation flows (e.g., Google OAuth).
{% endhint %}
{% endstep %}

{% step %}
#### Contract signature

Before depositing, users are required to sign two agreements:

1. Terms and Conditions (T\&C) that govern interaction with Pareto
2. Master Loan Agreement (MLA) that governs the lending relationship between the liquidity provider and the borrower (e.g., Fasanara Digital)

These contracts are signed on-chain, linking a unique ID to the user’s address via e-signature. The contract signature requires the execution of a transaction from the verified wallet. Users will be prompted to sign an on-chain message to complete this step.
{% endstep %}
{% endstepper %}
