# Deposit

Once [onboarding](onboarding.md) is completed, users can proceed to deposit into the selected Credit Vault.&#x20;

Users can deposit during the cycle **buffer period**, i.e., the 6 to 24 hours between the end of the previous lending cycle and the start of the new one. Alternatively, users can use the Queue contract to schedule their deposit while a **cycle is running**.

## During a buffer period

Users should sign two transactions:

1. **Spending approval** to allow Pareto’s smart contract to process users’ deposits
2. **Deposit** to execute the deposit

Upon completion:

* The user’s wallet will reflect a reduction in the deposited stablecoin
* The user will receive [Credit Vault LP tokens](../../developers/addresses/product.md) representing their position in the pool

{% embed url="https://youtu.be/iBsuGk16qp0" %}

## While a lending cycle is running

Alternatively, users can use the Queue contract to schedule their deposit (**deposit request**) while a cycle is running. Users will queue a request of deposit, which will be processed at the first available buffer period.&#x20;

The same Spending Approval and deposit flow applies.&#x20;

Upon completion:

* The user’s wallet will reflect a reduction in the deposited stablecoin
* The user will **need to claim** Credit Vault LP tokens representing their position in the pool after the new lending cycle starts

{% embed url="https://youtu.be/zP68ipECTnU" %}
