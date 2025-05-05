# Deposit

Once [onboarding](onboarding.md) is completed, users can proceed to deposit into the selected Credit Vault.&#x20;

Users can deposit during the cycle **buffer period**, i.e., the 6 to 24 hours between the end of the previous lending cycle and the start of the new one. Alternatively, users can use the Queue contract to schedule their deposit while a **cycle is running**.

## During a buffer period

Users should sign two transactions:

{% stepper %}
{% step %}
#### Spending approval

To allow Pareto’s smart contract to process users’ deposits
{% endstep %}

{% step %}
#### Deposit

To execute the lending action
{% endstep %}
{% endstepper %}

Upon completion:

* The user’s wallet will reflect a reduction in the deposited stablecoin
* The user will receive [Credit Vault LP tokens](../../../../developers/addresses/product.md) representing his position in the pool

{% embed url="https://youtu.be/TO6t9Kx1ulg" %}

## While a lending cycle is running

Alternatively, users can use the Queue contract to schedule their deposit while a cycle is running. Users will queue a deposit request, which will be processed by the pool curator at the first available buffer period.&#x20;

The same Spending Approval and deposit flow applies

{% stepper %}
{% step %}
#### Spending approval

To allow Pareto’s smart contract to process users’ deposits
{% endstep %}

{% step %}
#### Request deposit

To execute the deposit request action
{% endstep %}
{% endstepper %}

Upon completion:

* The user’s wallet will reflect a reduction in the deposited stablecoin
* The user will **need to claim** Credit Vault LP tokens representing his position in the pool after the new lending cycle starts

<figure><img src="../../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Claim LP tokens preview when a deposit was made during a cycle running</p></figcaption></figure>

{% embed url="https://youtu.be/zP68ipECTnU" %}
