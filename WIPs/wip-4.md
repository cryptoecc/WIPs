### WIP-4: WorldLand Token Economics

Author: Gyeongdeok Maeng, Seungmin Kim, Minho Yoon, Heungno Lee
DATE: 2024-05-03

| [Abstract](#abstract) |
| [Motivation](#motivation) |
| [Specification](#specification) |
| [Economic Analysis](#economic-analysis) |
| [Implementation](#implementation) |
| [Reference](#reference) |


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
1. Halving Reward: There will be 4 halving events over 8 years. Each time, the block reward will be reduced by half.
<p align="center">
  <img src="../assets/wip-4/reward1.png" alt="alt text" style="width:30%;">
</p>


2. Maturity Reward: 8 years after the genesis block (25,228,800 blocks), the block reward will increase by 4% annually.
<p align="center">
  <img src="../assets/wip-4/reward2.png" alt="alt text" style="width:30%;">
</p>


## Economic Analysis
- The proposed model provides a stable and predictable money supply in the long term.
- The continuous block reward after the maturity period incentivizes miners, maintaining the stability of the network.
- However, for WorldLand tokens to be used as actual currency, challenges such as securing legal status and increasing market acceptance remain.

## Implementation
https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/consensus.go [4]



## Reference

1. "Bitcoin: A Peer-to-Peer Electronic Cash System" by Satoshi Nakamoto (2008)

2. "Some Simple Economics of the Blockchain" by Christian Catalini and Joshua S. Gans (2016)

3. "The Role of Monetary Policy" by Milton Friedman (1968)

4. https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/consensus.go