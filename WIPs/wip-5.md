
## WIP-5: Update Sensitivity of WorldLand Difficulty Control Algorithm

<b>Author</b> Seungmin Kim 
<b>Created</b> 2024-05-05

### Table of Contents
* Abstract
* Motivation
* Specification
* Simulation Result
* Conclusion
* Implementation
* Reference

### Abstract
This document describes the update to the difficulty adjustment algorithm in the Worldland hard fork[1].

### Motivation
The current sensitivity of Worldland's difficulty adjustment algorithm is set to 1/8, which is too high. In this setting, if the Block Generation Time(BGT) of a block exceeds 90 seconds, the difficulty of the next block is drastically reduced to the minimum difficulty value.

To fix this problem you need to reduce the sensitivity. In this paper, we aim to propose appropriate sensitivity values to properly track the network computational power.

Changing the sensitivity value modifies the consensus protocol, thus triggering a hard fork in the blockchain. Sensitivity determines the difficulty of the block, which is an important variable that must be verified through the consensus protocol.

After this update is made, nodes following the updated protocol will no longer accept blocks mined by nodes following the deprecated protocol, and vice versa.

### Specification
Worldland (ECCPoW)’s difficulty adjustment algorithm is as follows. The difficulty of the next block is determined by increasing or decreasing the current difficulty corresponding to the $BGT$ of that block. Worldland's current sensitivity $S$ is $\frac{1}{8}$.

$$
d_{i+1} = 
\begin{cases} 
d_i + d_i \frac{ \max\left(2 - \frac{|BGT_i|}{10}, -99\right)}{8}, & \text{if uncle block exists,} \\
d_i + d_i\frac{ \max\left(1 - \frac{|BGT_i|}{10}, -99\right)}{8}, & \text{elsewhere.}
\end{cases}
$$

When network computing power changes rapidly, high sensitivity helps maintain average block creation times by ensuring the difficulty value tracks this. However, at the same time, if the actual network computing power does not change, it may cause serious difficulty disturbances. The change in difficulty according to $BGT$ can be expressed as a function of sensitivity and can be interpreted as follows.

- If $10 <= BGT < 20$, the difficulty will not be changed.
- If $BGT < 10$, increase the difficulty to $(1+S)D$.
- If $BGT > 20$, lower the difficulty to $(1−kS)D$. At this time, $k = \lfloor \frac{BGT_i}{10} \rfloor - 1$ and if $k$ is greater than $-99$, $k$ is fixed to $-99$ (lower limit of difficulty reduction).

Here we can see that the maximum growth rate is bounded by $(1+S)$ and the maximum decrease rate is $\frac{-99}{S}$. The increasing difficulty should be conservative. If the difficulty of the blockchain increases dramatically, the BGT for that block can become significantly longer, causing significant delays to all transactions. Conversely, lowering the difficulty may be more aggressive than increasing it because it poses no serious problems for the user.

For the initial WorldLand network, the sensitivity was set to $1/8$ to adapt to rapid increases and decreases in network hash power. Recently, as the Worldland network has become popular, many users are participating in mining competitions. As a result, WorldLand's network hash power has increased dramatically. At this point, hash power no longer significantly changes when a few nodes join or leave. This means that there is no longer a reason to maintain high sensitivity at the expense of high volatility in BGT.

Lowering the sensitivity will cause unintended changes in the average BGT. To complement this, we propose the following formula to control the difficulty of WL:

$$ 
d_{i+1} = 
\begin{cases} 
d_i + d_i \frac{ \max\left(2 - \frac{|BGT_i|}{7}, -99\right)}{1024}, & \text{if uncle block exists,} \\
d_i + d_i\frac{ \max\left(1 - \frac{|BGT_i|}{7}, -99\right)}{1024}, & \text{elsewhere.}
\end{cases}
$$

That is, we set the sensitivity to $\frac{1}{1024}$ and the threshold to $7$. We expect that the target BGT(10 seconds) can be achieved with small variations in difficulty.

### Simulation Result

We assume that BGT is a random variable with a geometric distribution and that there are no uncle blocks. We simulated the difficulty and BGT for 20000 blocks using various scenarios of network computing performance.

<b>i. When hash power is constant</b>
You can see that the difficulty is maintained within $\pm 6$% of the initial value. We can confirm that BGT also has the distribution as intended.
<p align="center">
  <img src="img/difficulty_constant.png" alt="Difficulty Graph" width="45%">
  <img src="img/BGT_histogram_constant.png" alt="BGT Histogram" width="45%">
</p>

| Statistic   | Value   |
|-------------|---------|
| Mean BGT    | 9.85605 |
| Variance    | 87.34060|
| Maximum BGT | 90.0    |

<b>ii. When computing performance has small changes</b>
Results for a scenario where computing performance is modeled as a constant with additional white Gaussian noise satisfying normalized $MSE = 0.01$. Similar to the first scenario, but with slightly increased variance. Difficulty is maintained within $\pm 8$% of the initial value.
<p align="center">
  <img src="img/difficulty_perturbation.png" alt="Difficulty Graph" width="45%">
</p>
 
| Statistic   | Value   |
|-------------|---------|
| Mean BGT    | 9.87925 |
| Variance    | 91.40784|
| Maximum BGT | 93.0    |

<b>iii. Scenario for rapid increase in computing power</b>
This time we consider a scenario where network computing power increases rapidly. This scenario can occur when a new large miner joins the blockchain. We modeled that at block 8000 the network computing power increases to four times its initial value. The table below shows the response time required for difficulty to keep up with changes in network computing power.
<p align="center">
  <img src="img/difficulty_steepincrease.png" alt="Difficulty Graph" width="45%">
</p>

| Statistic   | Value   |
|-------------|---------|
| 90% Response Time (blocks)        | 2341   |
| 70.71% Response Time (blocks)     | 1504   |
| Theoretical Response Time (blocks)| 1421   |

<b>iv. Sharp decline</b>
This time, we consider a scenario where network computing performance decreases rapidly. This scenario can occur when a large miner stops mining or is temporarily disconnected. Similar results can be seen in the steep increase scenario.
<p align="center">
  <img src="img/difficulty_steepdecrease.png" alt="Difficulty Graph" width="45%">
</p>

| Statistic   | Value   |
|-------------|---------|
| 90% Response Time (blocks)        | 1546 |
| 70.71% Response Time (blocks)     | 832  |

<b>v. Steady increase</b>
We assume that the network computing power increases linearly over 20000 blocks and finally reaches 4 times its initial value. Please note that the growth rate per block is less than the maximum growth rate. Assuming there are no uncles, the maximum tolerable growth rate is $1+\frac{1}{1024} = 1.0009766$. The growth rate assumed in the simulation is $4^{\frac{1}{20000}} = 1.000069317$, which is below the maximum growth rate and can keep up well with changes in computing power.
<p align="center">
  <img src="img/difficulty_steadyincrease.png" alt="Difficulty Graph" width="45%">
</p>

### Conclusion
Simulation results show that the difficulty of our new formulation appropriately responds to changes in the computational volume of the network.

### Implementation
It is implemented at https://github.com/cryptoecc/WorldLand/blob/worldland/consensus/eccpow/LDPCDifficulty.go.