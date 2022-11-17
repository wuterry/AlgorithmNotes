
> 背包问题 (knapsack problem) 是一种*组合优化*的 *NP 完全*问题。

## 建模

假定我们有 $N$ 种物品, 每种物品的重量为 $w_i$, 价格为 $p_i$, 背包所能装载的最大重量为 $W$, $x_i$ 是装入背包的第 $i$ 个物品的个数, 公式化表述

- **0-1 背包问题**: 每种物品只能选择 0 个或者 1 个

$$
\max{\sum_{i=1}^N} x_i  p_i \quad s.t. \; \sum_{i=1}^N x_i  w_i \leq W, \; x_i \in \{0, 1\}
$$

- **有界背包问题**: 每种物品最多只能选择 $b_j$ 个

$$
\max{\sum_{i=1}^N} x_i  p_i \quad s.t. \; \sum_{i=1}^N x_i  w_i \leq W, \; x_i \in \{0, 1, \cdots , b_j\}
$$

- **无界背包问题**: 又称完全背包问题，不限定每种物品的数量

$$
\max{\sum_{i=1}^N} x_i  p_i \quad s.t. \; \sum_{i=1}^N x_i  w_i \leq W, \; x_i \in \{0, 1, \cdots , \infty \}
$$

