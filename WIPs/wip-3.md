# WIP-3: WorldLand Mining Difficulty System

<pre>
  Title: WorldLand Mining Difficulty System
  Status: Draft
  Type: Core
  Author: Heungno Lee<@lincolnkerry>, Minho Yoon <@Pytype>, Seungmin Kim <@smin-k>, Gyeongdeok Maeng <@siddharth0a> 
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
### Difficulty Table
The difficulty table is created including all pre-calculated miningProb values using the parameters n, wc, wr, decisionFrom, decisionTo, and decisionStep values. These LDPC parameters of ECCPoW are described in the paper 'Time-variant proof-of-work using error correction codes' [3].
```golang
type difficulty struct {
	level        int
	n            int
	wc           int
	wr           int
	decisionFrom int
	decisionTo   int
	decisionStep int
	_            float32
	miningProb   float64
}
```

Before launching WorldLand mainnet, the testnet used the table below with pre-calculated mining probabilities sorted in descending order.
```golang
var Table = []difficulty{
	{0, 32, 3, 4, 10, 22, 2, 0.329111, 3.077970e-05},
	{1, 32, 3, 4, 10, 22, 2, 0.329111, 3.077970e-05},
	{2, 32, 3, 4, 10, 16, 2, 0.329111, 2.023220e-05},
	{3, 32, 3, 4, 16, 16, 1, 0.329111, 9.684650e-06},	
    ⋯
	{379, 128, 3, 4, 34, 94, 1, 1.345734, 5.192020e-23},
	{380, 128, 3, 4, 34, 34, 1, 1.345734, 2.600000e-23},
}
```

At the time of WorldLand Seoul launch, the table was changed so that the mining probability corresponding to the difficulty level decreases more consistently. In the changed difficulty table, the parameter n, the length of the codeword only increases by 4, thereby decreasing the mining probability by 68.97%.
```golang
func getTable(level int ) difficulty {
	n := 64 + level * 4
	return difficulty{level, n, 3, 4, 1/4 * n, 3/4 * n, 1, 0, 0}
}
```

### Difficulty Adjustment
In Ethereum 1.0, miners repeat the task of finding a nonce value that makes the result of Keccak-256 less than the number of dividing $2^{256}$ by the block difficulty. Since the result of the hash function is pseudo-random, the mining probability has a uniform distribution.
$$
(Mining\,Probability)=\frac{n_{valid}}{2^{256}}≈\frac{\frac{2^{256}}{H_d}}{2^{256}}=\frac{1}{H_d}
$$

Ethereum PoW, which has the above property, the difficulty is simply calculated based on the time difference between the creation of the current block and the previous block  [1].

In a similar way to Ethereum 1.0, the table is used to the difficulty of the next block of WorldLand.
```golang
func MakeLDPCDifficultyCalculator_Seoul() func(time uint64, parent *types.Header) *big.Int {
	return func(time uint64, parent *types.Header) *big.Int {
		bigTime := new(big.Int).SetUint64(time)
		bigParentTime := new(big.Int).SetUint64(parent.Time)

		x := new(big.Int)
		y := new(big.Int)

		x.Sub(bigTime, bigParentTime)
		x.Div(x, BlockGenerationTimeSeoul)

		if parent.UncleHash == types.EmptyUncleHash {
			x.Sub(big1, x)
		} else {
			x.Sub(big2, x)
		}

		if x.Cmp(bigMinus99) < 0 {
			x.Set(bigMinus99)
		}

		y.Div(parent.Difficulty, Sensitivity)
		x.Mul(y, x)
		x.Add(parent.Difficulty, x)

		if x.Cmp(SeoulDifficulty) < 0 {
			x.Set(SeoulDifficulty)
		}

		return x
	}
}
```


## Implementation
https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/consensus.go

https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/LDPCDifficulty.go

https://github.com/cryptoecc/ETH-ECC/blob/master/consensus/eccpow/LDPCDifficulty_utils.go


## Reference
1. G. Wood, “Ethereum: A secure decentralised generalised transaction ledger,” Ethereum Project, Zug, Switzerland, Yellow Paper, 2014.

2. H. Jung and H. N. Lee, "ECCPoW: ErrorCorrection Code based Proof-of-Work for ASIC Resistance", Symmetry, Vol. 12, No. 6, Art. no.6, Jun. 2020.

3. S. Park, H. Choi, and H. Lee, "Time-variant proof-of-work using error correction codes," Jun. 2020, arXiv:2006.12306. [Online]. Available: https://arxiv.org/abs/2006.12306
