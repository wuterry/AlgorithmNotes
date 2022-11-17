
## 53_最大子数组和

### 题目

给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
注: 子数组是数组中的一个连续部分。

示例:
```text
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6。
```

### 解析

- **定义子问题**: dp[i]: 表示以 nums[i] **结尾**的连续子数组最大和
- **状态转移方程**: 分两种情况讨论,

$$
\begin{equation*}
dp[i]=\left\{
\begin{aligned}
 dp[i - 1] + nums[i]&, & dp[i - 1] \geq 0, \\
nums[i] &, & dp[i - 1] < 0.
\end{aligned}
\right.
\end{equation*}
$$

上式可以合并为一个公式 $dp[i]=\max(dp[i-1] + nums[i], nums[i])$

- **初始值**: dp[0] = nums[0]

- **优化空间**: 全部保存 DP 数组没有必要, 只用存储 dp[i-1] 和 dp[i] 就可以。

依据如上分析可以写出最原始的 DP 代码

=== "空间优化前"

    ```python
    def maxSubArray(self, nums: List[int]) -> int:
        """
        1. DP 定义: dp[i] 表示以 nums[i] 结尾的连续子数组的最大和
        2. 递推公式: dp[i] = max(dp[i - 1] + nums[i], nums[i])
        3. DP 初始化: dp[0] = nums[0]
        """
        dp = [0] * len(nums)
        dp[0] = nums[0]
        max_val = dp[0]
        for i in range(1, len(nums)):
            dp[i] = max(dp[i - 1] + nums[i], nums[i])
            if max_val < dp[i]:
                max_val = dp[i]
        return max_val
    ```

=== "空间优化后"

    ```python
    def maxSubArray(self, nums: List[int]) -> int:
        """
        定义 prev, curr = dp[i - 1], dp[i]
        """
        prev = nums[0]
        max_subsum = prev
        for num in nums[1:]:
            curr = max(prev + num, num)
            prev = curr
            if max_subsum < curr:
                max_subsum = curr
        return max_val
    ```

## 152_乘积最大子数组

### 题目

给你一个整数数组 nums，请你找出数组中乘积最大的非空连续子数组(该子数组中至少包含一个数字)，并返回该子数组所对应的乘积。

示例:
```text
输入: nums = [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

### 解析

基本的解题思路同 [53_最大子数组和](#53_), 但需要考虑 nums[i] 中分别为正数和负数的情形。
1. 若 nums[i] < 0, 且其前某段连续子数组之积也为负数，则负的绝对值越大，则其积越大;
2. 若 nums[i] > 0, 则期望其前某段连续子数组之积也为正数，正得越大，其积越大。

因此, 需要定义两个 DP, 即

- $DP_{max}$: 表示以 nums[i] 结尾的积的最大值；
- $DP_{min}$: 表示以 nums[i] 结尾的积的最小值.

代码如下:

```python
def maxProduct(self, nums: List[int]) -> int:
    prev_max = nums[0]
    prev_min = nums[0]
    res = nums[0]
    for num in nums[1:]:
        curr_max = max(prev_max * num, prev_min * num, num)
        curr_min = min(prev_max * num, prev_min * num, num)
        res = max(curr_max, res)
        prev_max = curr_max
        prev_min = curr_min
    return res
```

## 300_最长递增子序列

### 题目
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

说明: 子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

示例:
```text
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

### 解析

#### 基础解法：动态规划

- **状态定义**: dp[i] 表示以 nums[i] 结尾的最长上升子序列的长度;
- **状态转移方程**: 当 $nums[i] > nums[j]$ 时, $dp[i] = \max(dp[j] + 1, dp[i]), j \in [0, i)$, 其中 $j<i$; 也即是说, <font color=blue>需 $nums[i] > nums[j]$ 时, 才能将 $nums[i]$ 放到 $nums[0...j]$ 后面形成上升子序列</font>;
- **初始状态**: dp[i] 所有元素置 11，含义是每个元素都至少可以单独成为子序列，此时长度都为 1.

```python hl_lines="9"
def lengthOfLIS(self, nums: List[int]) -> int:
    """
    dp[i]: 以 nums[i] 结尾的最长递增子序列的长度
    """
    result = 0
    dp = [1] * len(nums)
    for i, num in enumerate(nums):
        # 对于每个 nums[i], 需从头开始遍历
        for j in range(i): 
            if nums[i] > nums[j]:
                dp[i] = max(dp[j] + 1, dp[i])
        result = max(dp[i], result)
    return result
```

#### 晋级解法: 动态规划 + 二分查找

**核心思想**: 为了找到 $nums[i]$ 之前的上升子序列, 可以定义一个**升序**数组 ascend_arr 存放上升子序列, 越小的数字越往前放，这样后面就会有更多的数字可以加入该数组中。 具体操作是: 如果 nums[i] 比 ascend_arr[-1] 大, 则追加至 ascend_arr 末尾; 负责覆盖第一个刚好不小于 nums[i] 的数字。基本的

```python
def lengthOfLIS(self, nums: List[int]) -> int:
    res = 1
    ascend_arr = [nums[0]]         # 有序上升数组
    for num in nums[1:]:
        if num > ascend_arr[-1]:   # 追加
            ascend_arr.append(num)
        else:                      # 覆盖第一个刚好不小于 num 的数字
            for idx in range(len(ascend_arr))[::-1]:
                if ascend_arr[idx] < num:
                    break
            if idx == 0 and ascend_arr[0] >= num:
                idx -= 1
            ascend_arr[idx + 1] = num
    return len(ascend_arr) 
```

如 nums = [100, 90, 1, 20, 30, 40, 80, 2, 3, 4, 5, 6], 每一步操作后的 ascend_arr 的情形如下
```
[100]
[90]                 (90 < 100, 替换)
[1]                  (1 < 90, 替换)
[1, 20]              (20 > 1, 追加)
[1, 20, 30]          (30 > 20, 追加)
[1, 20, 30, 40]      (40 > 30, 追加)
[1, 20, 30, 40, 80]  (80 > 40, 追加)
[1, 2, 30, 40, 80]   ( 1 < 2 < 20, 替换 20)
[1, 2, 3, 40, 80]    ( 2 < 3 < 30, 替换 30)
[1, 2, 3, 4, 80]     ( 3 < 4 < 40, 替换 40 )
[1, 2, 3, 4, 5]      ( 4 < 5 < 80, 替换 80 )
[1, 2, 3, 4, 5, 6]   (6 > 5, 追加)
```

更进一步, 由于 ascend_arr 是有序上升数组, 则可以将上述代码中覆盖的操作优化为二分查找。

```python
def lengthOfLIS(self, nums: List[int]) -> int:
    # 定义有序上升数组
    ascend_arr = [nums[0]]

    for num in nums[1:]:
        if num > ascend_arr[-1]:   
            # 追加
            ascend_arr.append(num)
        else:                      
            # 覆盖第一个刚好不小于 num 的数字
            l, r = 0, len(ascend_arr) - 1
            while l < r:
                mid = (l + r) // 2
                if ascend_arr[mid] < num:
                    l = mid + 1
                else:
                    r = mid
            ascend_arr[l] = num
    return len(ascend_arr) 
```