
## 32_最长的有效括号

### 题目
给你一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

!!! 示例1
    - 输入：s = ")<font color='red'>()()</font>)"
    - 输出：4
    - 解释：最长有效括号子串是 "()()"


!!! 示例2 
    - 输入: ")<font color='red'>()()</font>)<font color='orange'>()()</font>("
    - 输出: 4
    - 解释: 最长有效括号子串是 "<font color='orange'>()()</font>" 



### 解析

#### 解法 1: 栈

括号匹配问题通常想到的解法是使用栈，本题中需要注意的几点:

- 栈保存的是括号对应的{==索引==}, 而不是括号本身;
- 栈需预置 -1 作为参照；
- 索引出栈后需要分情况讨论:
  - 栈为空: 则将当前索引入栈;
  - 栈非空: 则 i 减去**栈顶**作为当前有效括号长度, 并更新最长有效括号长度。

```python hl_lines='12'
def longestValidParentheses(self, s: str) -> int:
    stack = [-1]                               # 预置 -1 作为参照
    res = 0
    for i, c in enumerate(s):
        if c == '(':
            stack.append(i)
        elif c == ')':
            stack.pop()
            if len(stack):
                res = max(res, i - stack[-1])  # i - stack.top()
            else:
                stack.append(i)                # 栈空, 则入栈作为参照
    return res
```

#### 解法 2: 动态规划

!!! TODO
- **状态定义**: $dp[i]$ 表示以下标为 i 的字符结尾的最长有效括号的长度;
