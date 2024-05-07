### WIP-4: WorldLand Token Economics

<pre>
  Title: WorldLand Token Economics
  Status: Draft
  Type: Core
  Author: Heungno Lee <@lincolnkerry>, Gyeongdeok Maeng <@siddharth0a>, Seungmin Kim <@smin-k>
  Created: 2023-05-03
  License: GNU Lesser General Public License v3.0
</pre>

## Table of Contents
* [Abstract](#abstract)
* [Motivation](#motivation)
* [Specification](#specification)
* [Economic Analysis](#economic-analysis)
* [Implementation](#implementation)
* [Reference](#reference)


## Abstract
WorldLand proposes a new token economics model to overcome the limitations of Bitcoin and the dollar. After going through 4 halving events over 8 years, an annual fixed inflation rate of 4% will be introduced, aiming for a stable and predictable money supply in the long term.

## Motivation
Bitcoin has a limited supply and undergoes halving every 4 years [1]. This can lead to deflationary characteristics in the long run and decrease the velocity of money circulation. On the other hand, the dollar is losing its credibility due to unpredictable money supply policies. The tokenomics of some other cryptocurrencies are unsustainable due to reckless supply and unmanageable inflation [2].

WorldLand seeks to solve these problems based on Milton Friedman's quantity theory of money, permanent income hypothesis, and real economic data. Friedman argued that the growth rate of money supply should match the inflation rate in the long run [3]. Also, according to the permanent income hypothesis, consumption is influenced not only by current income but also by expectations about the future.

## Specification
Parameters:
- HalvingEndTime: 25,228,800
- WorldLandInitialBlockReward: 4
- WorldLandFirstBlockReward: 40,996,800
- HALVING_INTERVAL: 6,307,200
- MATURITY_INTERVAL: 3,153,600
- SumRewardUntilMaturity: 47,304,000
- Maxhalving: 4

Block Reward:
1. Halving Reward: There will be 3 halving events over 8 years. Each time, the block reward will be reduced by half. 

\*Maxhalving is 4 does not mean that the halving event occurs 4 times, but rather that there are 4 epochs that experience halving.

2. Maturity Reward: 8 years after the genesis block (25,228,800 blocks), the block reward will increase by 4% annually.


## Economic Analysis
- The proposed model provides a stable and predictable money supply in the long term.
- The continuous block reward after the maturity period incentivizes miners, maintaining the stability of the network.
- However, for WorldLand tokens to be used as actual currency, challenges such as securing legal status and increasing market acceptance remain.

## Implementation

```go
// AccumulateRewards credits the coinbase of the given block with the mining
// reward. The total reward consists of the static block reward and rewards for
// included uncles. The coinbase of each uncle block is also rewarded.
func accumulateRewards(config *params.ChainConfig, state *state.StateDB, header *types.Header, uncles []*types.Header) {
	// Select the correct block reward based on chain progression
	var blockReward = big.NewInt(FrontierBlockReward.Int64())

	//blockReward := FrontierBlockReward
	if config.IsByzantium(header.Number) {
		blockReward = ByzantiumBlockReward
	}
	if config.IsConstantinople(header.Number) {
		blockReward = ConstantinopleBlockReward
	}
	if config.IsWorldland(header.Number) {
		blockReward = big.NewInt(WorldLandBlockReward.Int64())
		
		if config.IsWorldLandHalving(header.Number) {
			blockHeight := header.Number.Uint64()
			HalvingLevel := (blockHeight - 1 - config.WorldlandBlock.Uint64()) / HALVING_INTERVAL
			
			blockReward.Rsh(blockReward, uint(HalvingLevel))
			
		} else if config.IsWorldLandMaturity(header.Number) {
			blockHeight := header.Number.Uint64()
			blockReward = big.NewInt(1e+18)

			MaturityLevel := (blockHeight - 1 - config.HalvingEndTime.Uint64()) / MATURITY_INTERVAL
						
			blockReward.Mul(blockReward, SumRewardUntilMaturity)
			blockReward.Div(blockReward, new(big.Int).SetUint64(MATURITY_INTERVAL)) 
			
			blockReward.Mul(blockReward, big.NewInt(4))
			blockReward.Div(blockReward, big.NewInt(100))

			for i := 0; i < int(MaturityLevel); i++ {
				blockReward.Mul(blockReward, big.NewInt(104))
				blockReward.Div(blockReward, big.NewInt(100))
			}	
		}
	}
```
[4]



## Reference

1. "Bitcoin: A Peer-to-Peer Electronic Cash System" by Satoshi Nakamoto (2008)

2. "Some Simple Economics of the Blockchain" by Christian Catalini and Joshua S. Gans (2016)

3. "The Role of Monetary Policy" by Milton Friedman (1968)

4. https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/consensus.go