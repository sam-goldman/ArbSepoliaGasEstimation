# Arbitrum Sepolia Gas Estimation Bug

The gas estimates in the broadcast files for Arbitrum Sepolia are significantly lower than the actual gas used. This doesn't break `forge script`, but it breaks off-chain tooling that uses `forge script` to estimate the gas without broadcasting the transactions.

To reproduce this bug, you'll need a funded private key on Arbitrum Sepolia. [Here's a faucet.](https://faucet.quicknode.com/arbitrum/sepolia)

Steps to reproduce:
1. `git clone git@github.com:sam-goldman/ArbSepoliaGasEstimation.git`
2. `cd ArbSepoliaGasEstimation`
3. `forge install`
4. Run the following Forge script, replacing `<your_private_key>` with a funded private key on Arbitrum Sepolia:
```bash
PRIVATE_KEY=<your_private_key> forge script script/Counter.s.sol --rpc-url https://sepolia-rollup.arbitrum.io/rpc --broadcast
```
5. Open the broadcast file, which should be at `broadcast/Counter.s.sol/421614/run-latest.json`
6. Go to the `transactions` array, then go to the second element in the array, which corresponds to the `counter.increment()` call. You'll see that the `"transaction.gas"` field is `0x6af7`, which is 27383 gas.
7. In the same file, go to the second element in the `receipts` array (i.e. the receipt that corresponds to the `increment` call). The `gasUsed` is `0xa98c`, which is `43404` gas. This is ~1.5x the estimated gas of 27383.