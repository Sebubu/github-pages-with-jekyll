---
title: "Reverse-engineering Lightning Labs Terminal Score"
date: 2021-12-05
---

### Background

Hi guys
I would like to describe an incident here that we run into that led to the loss of half of the nodes channels.

#### The setup
A client of mine runs one Bitcoin Core and connects several LND nodes to it with Neutrino. Bitcoin Core and LND are on different machines.

#### The story
At one point we observed that one LND node is acting weirdly. A little later it was stuck in a restart loop.

A lot of errors showed up in the logs. Here is an excerpt. Some of these error showed up hundreds of times.

```
[ERR] BTCN: Can't accept connection: accept tcp [::]:9733: accept4: too many files open
[ERR] LTND: Unable to lookup witness: no witnesses
[ERR] NTFN: Failed getting UTXO: get utxo request cancelled
[ERR] NTFN: Failed to update rescan progress: database not open
[WRN] LNWL: Unable to remove invalid transaction **transactionId**: database not open
[ERR] NTFN: Failed to update rescan progress: database not open
[INF] BTCN: Canceling block subscription: id=1
[INF] BTCN: Block manager shutting down
[INF] BTCN: Address manager shutting down
[INF] LTND: Shutdown complete
unable to start server: unable to query fee estimator: web API does not include a fee estimation for block target of 1
```

The first thing we did is to increase the Linux open file limit (See #6057). It was at 1024 and we increased it to 16384. This resolved the `[ERR] BTCN: Can't accept connection: accept tcp [::]:9733: accept4: too many files open` errors. Fingers crossed it doesn't show up anymore in the future.

The other logs were still showing though. They were not expressive so it took me hours to find a possible cause.
Running `lnd --reset-wallet-transactions` resolved the `[ERR] NTFN: Failed to update rescan progress: database not open` issue. I assume the Linux open file limit issue led to the corruption of the wallet.db.

The final bug had to do with the fee estimator (See #6056). The estimator requested a fee for block 1 but only fees starting from block 2 are available. I fixed this by creating a custom API that provided fees for block 1.

After that the node started to work again.

Thanks to @openoms and @alexbosworth for the support.

### Related issues

I opened the following issues that cover the mentioned issues/bugs above:
- #6056
- #6057

### Your environment

* version of `lnd`: 0.13.4
* which operating system (`uname -a` on *Nix): Linux Nuc9 5.11.0-41-generic~20.04.1-Ubuntu SMP x86_64 x86_64 x86_64 GNU/Linux
* version of `btcd`, `bitcoind`, or other backend: Neutrino
* any other relevant environment details
