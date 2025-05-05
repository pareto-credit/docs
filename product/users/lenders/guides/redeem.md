# Redeem

Users can withdraw during the cycle **buffer period**, i.e., the 6 to 24 hours between the end of the previous lending cycle and the start of the new one. Alternatively, users can use the Queue contract to schedule their withdrawal while a **cycle is running**. The exit request will be processed by the pool curator at the first available buffer period.&#x20;

While deposits happen in a single step, withdrawals are a two-step process happening during two different cycles:

{% stepper %}
{% step %}
#### Spending approval

To allow Pareto’s smart contract to process users’ LP tokens exit request
{% endstep %}

{% step %}
#### Withdrawal request

A user requests to redeem funds (principal and/or accrued interest) at the end of Cycle I.
{% endstep %}

{% step %}
#### Funds claim

At the end of the following cycle (Cycle II), the borrower repays interest and any requested withdrawals. Users who requested to exit can now claim their funds.
{% endstep %}
{% endstepper %}

Upon completion of steps 1, and 2

* The user’s wallet will reflect a reduction in the [Credit Vault LP tokens](../../../../developers/addresses/product.md) representing his position in the pool
* The user will need to claim his stablecoins representing his initial deposit, plus any interest generated after the new lending cycle starts

{% hint style="success" %}
**Early exit**

When the interest rate of the next lending cycle is lower than the previous one by 1% or more (or otherwise provided in the Credit Agreement), lenders are entitled to an **early exit** and can redeem funds with a shorter waiting period, i.e., within 72 hours. &#x20;

For example, a lender requests a withdrawal on Jan 31 during Cycle I, where the interest rate is 15%.  If the Cycle II rate drops to 12%, the lender qualifies for an early exit and can claim his funds 72 hours after Cycle II starts (i.e., on Feb 3).
{% endhint %}

Similarly, users can use the Queue contract to schedule their redemptions while a cycle is running. Users will queue a withdrawal request, which will be processed by the pool curator at the first available buffer period.&#x20;

{% embed url="https://youtu.be/AP3pbQrNpoQ" %}
