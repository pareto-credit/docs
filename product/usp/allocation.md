# Allocation

This section describes how the USP board allocates collateral between the whitelisted yield sources. These are classified according to some liquidity tiers

* T1: is a cash equivalent product, instantly redeemable (0-2 days) --> $$w_s$$
* T2: is a short-term yield product, redeemable in less than 1 week ($$\leq$$ 7 days) --> $$w_7$$
* T3: is a long-term yield product, redeemable in less than 1 month (8-30 days) --> $$w_{30}$$

The weights should satisfy the condition $$w_s + w_{7} + w_{30} \;=\; 1.$$

### Allocation rules

1. Sort vaults by descending yield vs duration score $$S_i$$ (see [#formulae](allocation.md#formulae "mention"))
2. Fill Tier 2 weight up to the global cap $$c_7$$. One-fourth of T2 allocations unlock every week and are constantly moved back to T1
3. Allocate the remainder to Tier 3 until the target length (12d) is reached

where $$c_7$$ limits the exposure to T2 products: too small allocation decreases the yield, but too large one risks draining liquidity if many users redeem within one week.

<details>

<summary>Formulae</summary>

Given some key parameters:

* $$A$$ = total collateral (USDC + USDS)
* $$R_d$$ = net USP redemptions on the day $$d$$
* $$APR_{i, \tau_i}$$= APR and epoch (days) of vault $$i$$
* $$\sigma_w$$ = daily standard deviation of redemptions (look-back $$W \approx 90d$$)
* $$p$$ = instant-service percentile (e.g. 97.5%)

***

To compute the instant-liquidity buffer (T1)

1.  Statistical need --> $$L = z_p\,\sigma_w\,\sqrt{H}$$

    where $$H =$$ days promised instant liquidity (usually 1) and $$z_p \approx 1.96 \; \text{for} \:  p = 97.5\%$$&#x20;
2. Add operational cushion --> $$\text{buffer}_{\min}=L+\kappa_A$$ \
   where $$\kappa \approx 1\%$$ of $$A$$ (oracle lag, gas spikes)
3. Target balance --> $$B_{\text{sUSDS}}=\max \left(\text{buffer}_{\min},\,B_{\min}\right)$$
4. Convert to portfolio weight $$w_s^{*}=\dfrac{B_{\text{sUSDS}}}{A}$$

If the current $$w_s < w_s^*$$ then move funds into T1, otherwise sweep the surplus out of the best-scoring vaul&#x74;_._

To allocate the surplus, we compute a yield vs duration scoring for every vault $$i$$ using the formula

$$S_i = \dfrac{\text{APR}_i - f_i}{1 + \lambda \tau_i}$$

where $$f_i$$ is the annualized fee, and $$\lambda \approx 0.04$$ is a factor used to penalize long-term strategies.

T3 allocation should be larger than the target length ($$\tau_{\text{target}} \approx 12$$ days)

$$\dfrac{\sum_i w_i \tau_i}{1 - w_s^{*}} \;\le\; \tau_{\text{target}}$$

where $$w_{i, \max}$$ is an optional exposure limit to any single vault or protocol. It should be used only when two or more independent vaults exist.

A keeper routine should be set to rebalance funds either weekly **o**r whenever $$| (w_s - w_{s_\text{target}}) > \varepsilon |$$

```python
def rebalance():
    A = current_AUM()
    sigma_w = stdev(redemptions, window=W)
    L = z_p * sigma_w * sqrt(H)
    B_s = max(L + kappa * A, B_min)
    w_s_target = B_s / A

    scores = [
        (i, (apr[i] - fee[i]) / (1 + lambda_ * tau[i]))
        for i in vaults
    ]
    scores.sort(key=lambda x: x[1], reverse=True)

    alloc = {"sUSDS": w_s_target}
    remaining = 1 - w_s_target
    w7 = 0

    for i, _ in scores:
        if tau[i] <= 7 and w7 < c7:                 # fill T2
            x = min(c7 - w7, remaining)
            alloc[i] = x
            w7 += x
            remaining -= x
        elif remaining > 0:                         # fill T3
            alloc[i] = remaining
            remaining = 0
        if remaining <= 0:
            break

    execute_onchain_swaps(alloc)
```

</details>

### Stress-test checklist

* [ ] &#x20;Double worst redemption day, verify T1 ≥ demand
* [ ] 30-day Monte-Carlo (jump-diffusion redemptions)
* [ ] Confirm T1 + laddered T2 ≥ 99 % simulations without gating
* [ ] Check portfolio credit-VaR vs limits

### Initial parameters

The USP board can modify the allocation parameters. The default values are:

<table><thead><tr><th width="94.69140625">Variable</th><th width="482.59375">Description</th><th>Default value</th></tr></thead><tbody><tr><td><span class="math">p</span></td><td>Instant-redeem service level</td><td>97–99%</td></tr><tr><td><span class="math">\kappa</span></td><td>Operational cushion</td><td>1–2 % <em>A</em></td></tr><tr><td><span class="math">\lambda</span></td><td>Converts “epoch-day” into APR penalty</td><td>0.03–0.05</td></tr><tr><td><span class="math">\tau_{\text{target}}</span></td><td>Max weighted epoch</td><td>10–14 days</td></tr><tr><td><span class="math">c_7</span></td><td>Upper bound on the total weight of the 7-day vault sleeve</td><td>20–30 % <em>A</em></td></tr><tr><td><span class="math">w_{i, \max}</span></td><td>Optional per-vault cap. Use once the number of vaults is <span class="math">\geq 3</span></td><td>off / 30 % <em>A</em></td></tr></tbody></table>
