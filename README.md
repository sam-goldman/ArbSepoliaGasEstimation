# Arbitrum Sepolia Gas Estimation Bug

The transaction responses in the broadcast files for Arbitrum Sepolia are significantly lower than the actual gas used. This doesn't break `forge script`, but it breaks off-chain tooling that relies on the transaction responses to estimate the gas that will be used in the transactions.

To reproduce this bug, you'll need a funded private key on Arbitrum Sepolia. [Here's a faucet.](https://faucet.quicknode.com/arbitrum/sepolia)

Steps to reproduce:
1. Download this repo
2. `forge install`
3. Run the following command TODO
```bash
PRIVATE_KEY=<your_private_key> forge script script/Counter.s.sol --rpc-url https://sepolia-rollup.arbitrum.io/rpc --broadcast
```
4. Open the broadcast file, which should be at `broadcast/Counter.s.sol/421614/run-latest.json`
5. Go to the `transactions`, then go to the second element in the array, which corresponds to the `counter.increment()` call. You'll see that the `"transaction.gas"` field is `0x6af7`, which is 27383 gas.
6. In the same file, go to the second element in the `receipts` array. The `gasUsed` is `0xa98c`, which is `43404` gas. This is ~1.5x the estimated gas of 27383.