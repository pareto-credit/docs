# Curators

Curators are an integral part of the Credit Vault setup and are essential for stakeholder relations and vault activities. They can leverage underwriting on-chain expertise to enhance capital efficiency, mitigate counterparty risk, and elevate market transparency with institutional-grade credit structuring.

New curators should begin with this overview to build a solid foundation, while experienced curators may reference specific sections for targeted guidance.

### Key responsibilities

<figure><img src="../../.gitbook/assets/Docs_architecture_curator.png" alt=""><figcaption></figcaption></figure>

* The curator is responsible for opening and closing lending cycles. At the start of a cycle, funds are transferred from the CV to the borrower's wallet. At the end of the cycle, the curator closes the cycle and automatically retrieves interest and processes any withdrawal requests.
* The curator manages the execution of pending deposit and withdrawal requests in the queue contracts.
* If needed, the curator can adjust vault parameters such as APR, fees, and lending cycle length.&#x20;

### Curator app

The Pareto manager app is a specialized front-end interface designed for configuring and managing Credit Vaults. Available at [manager.pareto.credit](https://manager.idle.finance/), this tool provides real-time visibility into fund inflows and outflows. Only whitelisted addresses can interact with the manager app.

From the manager app, curators can easily see the status of the current lending cycle, a countdown to the epoch end, and the borrower's wallet allowance and balance. When a lending cycle ends, they can close it and adjust the length and APR for the following cycle.&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Implementation note:** Some actions are not yet implemented in the manager app. For this reason, curators should interact directly with [vault contracts](<../../README (1).md>) through verified sources such as [Etherscan](https://etherscan.io/) or [Blockscout](https://www.blockscout.com/). Always verify contract addresses before interaction.
