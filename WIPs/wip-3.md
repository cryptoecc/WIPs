# WIP-3: WorldLand Difficulty Adjustment

<pre>
  Title: WorldLand Difficulty Adjustment
  Status: Draft
  Type: Core
  Author: Heungno Lee<@lincolnkerry>, Seungmin Kim <@smin-k>, Gyeongdeok Maeng <@siddharth0a>, Minho Yoon <@Pytype>
  Created: 2023-05-03
  License: GNU Lesser General Public License v3.0
</pre>

## Table of Contents
* [Abstract](#abstract)
* [Motivation](#motivation)
* [Specification](#specification)
* [Implementation](#implementation)
* [Reference](#reference)


## Abstract
WorldLand proposes a new difficulty adjustment method that can be applied to its unique PoW Consensus algorithm, ECCPoW. In WorldLand Blockchain, the block difficulty is adjusted based on a table where the mining probability decreases by a certain percentage.


## Motivation
In Ethereum 1.0, a PoW blockchain, the range of valid nonce values when mining a block was $n = \frac{2^{256}}{H_d}$ [1], so the mining success probability was linear to the difficulty value. For this reason, it was easy to calculate the next block's difficulty to be mined at regular time intervals.

In WorldLand, the PoW consensus algorithm of Ethereum has been changed to ECCPoW [2], so the existing Ethereum difficulty adjustment method cannot be applied as is. Therefore, WorldLand created a difficulty table with mining probability calculated based on the parameter values of ECCPoW, a unique consensus algorithm, and uses this table to calculate the difficulty of the next block.


## Specification
The difficulty table [3] is created including all pre-calculated miningProb values using the ECCPoW parameters n, wc, wr, decisionFrom, decisionTo, and decisionStep values.

The Difficulty Table used in WorldLand increases only n, the length of the codeword, by 4, thereby increasing the mining probability by 68.97%.

In a similar way to Ethereum 1.0, difficulty is calculated based on the time difference between the creation of the current block and the previous block. In this process, a table is used to calculate the difficulty of the next block.


## Implementation
https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/consensus.go

https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/LDPCDifficulty.go

https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/LDPCDifficulty_utils.go


## Reference
1. G. Wood, “Ethereum: A secure decentralised generalised transaction ledger,” Ethereum Project, Zug, Switzerland, Yellow Paper, 2014.

2. H. Jung and H. N. Lee, "ECCPoW: ErrorCorrection Code based Proof-of-Work for ASIC Resistance", Symmetry, Vol. 12, No. 6, Art. no.6, Jun. 2020.

3. WorldLand Foundation (2023) Consensus [https://github.com/cryptoecc/ETH-ECC/tree/master/consensus]

