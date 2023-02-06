#! https://zhuanlan.zhihu.com/p/604008275

# 动态规划（Dynamic Programming）学习笔记

## 概述

### 什么是动态规划？

动态规划与分治方法相似，都是通过**组合子问题的解**来求解原问题。动态规划应用于**子问题重叠**的情况，即不同的子问题具有**公共**的子子问题。

### 动态规划的用途是？

动态规划方法通常用来求解最优化问题。这类问题可以有很多个可行解，每个解都有一个值，而问题需要具有最优值（最小值或最大值）的解。

### 如何设计动态规划算法？

1. 确定问题的最优子结构。
2. 递归地定义最优解的值。
3. 计算最优解的值。
4. 利用计算出的信息构造一个最优解。（可选）

步骤 1~3 是动态规划算法求解问题的基础。如果我们仅仅需要一个最优解的值，而非解本身，可以忽略步骤 。如果确实要做步骤 4, 有时就需要在执行步骤 的过程中维护一些额外信息，以便用来构造一个最优解。

## 原理

适合应用动态规划方法求解的最优化问题应该具备两个要素：**最优子结构**和**子问题重叠**。

### 最优子结构

如果一个问题的最优解包含其子问题的最优解，则称此问题具有最优子结构。使用动态规划算法时，可以使用子问题的最优解来构造原问题的最优解。

> 注意，具有最优子结构也可能意味着该问题可能适合贪心策略。

证明问题具有最优子结构遵循着如下的通用模式：

1. 证明问题的最优解的第一个组成部分是做出一个选择，做出这次选择后会产生一个或多个待解的子问题。
2. 对于一个给定问题，在其可能的第一步选择中，假定已经只知道哪种选择才会得到最优解。
3. 给定可获得最优解的选择后，确定这次选择会产生哪些子问题，并刻画出子问题空间。子问题空间应尽可能简单，只在必要时扩展它。
4. 证明作为原问题的最优解的组成部分，每个子问题的解就是它本身的最优解。一般可以用反证法证明。

> 一个寻找最优子结构的例子：在一个无权图中，寻找一条从 u 到 v 的最短路径。
>
> 由于从 u 到 v 的任意路径必须包含一个中间顶点，假定最短路径会经过 w，这样 u 到 v 的路径可以分割为 u 到 w 与 w 到 v。显然，u 到 v 的路径长度与  u 到 w 加上 w 到 v 的路径长度相等。（1~3）
>
> 可以使用反证法证明，当 u 到 v 的路径长度最短时，u 到 w 与 w 到 v 的路径长度同样也是最短的。（4）
>
> 这样，就可以考察所有的中间顶点来求 u 到 v 的最短路径，对每一个中间顶点 w，求 u 到 w 与 w 到 v 的最短路径，然后选择两条路径之和最短的顶点 w。

需要注意的是，最优子结构需要保证子问题是独立的，即，一个子问题的解不会影响到另一个子问题的解。

### 重叠子问题

如果递归算法反复求解相同的子问题，则称此问题具有重叠子问题。使用动态规划算法时，常将每一个求解了一次的子问题解存入一个表中，当再次需要这个子问题时直接查表。

> 注意，采取分治方法求解的问题通常在递归的每一步都生成了全新的子问题。

动态规划算法利用重叠子问题的性质能将递归算法的指数阶运行时间降低的次方阶。通常一个适用动态规划算法的问题可以使用带备忘的自顶向下方法和自底向上方法。

> 一般来说，自底向上方法会比带备忘的自顶向下方法快，因为自底向上算法没有递归调用的开销。备忘方法的优势体现在那些子问题空间存在不需要求解的子问题时，因为备忘方法只会求解那些绝对必要的子问题。

## 范例

### 0-1 背包问题

有 $n$ 个商品， 第 $i$ 个商品价值 $v_i$ 元，重 $w_i$ 磅，$v_i$ 和 $w_i$ 都是整数。有一个背包，最多能容纳 $W$ 磅重的物品，$W$ 是一个整数。将商品装入包中，如何才能使包中的商品总价值最高。

1. 确定问题的最优子结构。

    首先假设已经在包中放入了一部分商品，但依然有这余量可以放下更多的商品。可以确信，已经放入的商品也一定也满足了，在这些商品所占据的这部分重量下，这些商品的价值最高。

    使用反证法证明这一点，当这一部分重量的商品价值不是最高的时候，那总重量的商品价值也不会是最高的。

    显然，这个问题的最优解可以分解为子问题的最优解组合。

2. 递归地定义最优解的值。

    由于并不清楚最后究竟在包中放了多少个商品，所以设 $value(i, w)$ 表示对 $i$ 个商品进行了决策，在最高重量为 $w$ 下时取得的最大价值。

    根据最优子结构可以得到递归式：

    $$value(i, w) = max(value(i-1, w),\ value(i-1, w-w_i)+v_i)$$

    式中，$value(i-1, w)$ 表示第 $i$ 个商品不放入包中的决策，$value(i-1, w-w_i)+v_i$ 表示第 $i$ 个商品放入包中的决策。

3. 计算最优解的值。

    得到递归式后，就可以求解问题了。如果采用暴力递归的方法，很容易会导致不断地求解相同的子问题，这样的时间开销甚至是指数级的。

    实际解决动态规划问题一般将先求得的子问题解放入一个表中，当需要这个问题的时候再查表。求解子问题的顺序可以自顶向下也可以自顶向上。

    - 自顶向下的实现方法：

        ```cpp
        int top_to_bottom(int i, int w) {
          if (V_W_TB[i][w] != 0) {
            return V_W_TB[i][w];
          }
          if (i == 0) {
            return 0;
          }
          if (w - wt[i] < 0) {
            return top_to_bottom(i - 1, w);
          }
          V_W_TB[i][w] =
              max(top_to_bottom(i - 1, w), top_to_bottom(i - 1, w - wt[i]) + vt[i]);
          return V_W_TB[i][w];
        }
        ```

    - 自底向上的实现方法：

        ```cpp
        int bottom_to_top() {
          for (int i = 1; i <= n; i++) {
            for (int w = 1; w <= W; w++) {
              if (w - wt[i] < 0) {
                V_W_BT[i][w] = V_W_BT[i - 1][w];
                continue;
              }
              V_W_BT[i][w] = max(V_W_BT[i - 1][w], V_W_BT[i - 1][w - wt[i]] + vt[i]);
            }
          }
          return V_W_BT[n][W];
        }
        ```

    观察两种方法的 V_W 表可以发现：

    ```res
    res 1: 10
    0 0 0 0 0 0 0 0 0
    0 0 0 3 3 3 0 0 3
    0 0 0 4 4 0 0 0 7
    0 0 0 0 0 0 0 0 9
    0 0 0 0 0 0 0 0 10
    res 2: 10
    0 0 0 0 0 0 0 0 0
    0 0 3 3 3 3 3 3 3
    0 0 3 4 4 7 7 7 7
    0 0 3 4 5 7 8 9 9
    0 0 3 4 5 7 8 9 10
    ```

    自顶向下的实现方法虽然使用了递归函数，但是只求解了需要求解的子问题，而自底向上的实现方法求解了所有可能的子问题。

4. 利用计算出的信息构造一个最优解。

    现在得到了问题的最优解的值，但如果要确切地知道是那些商品被装进了背包中，还需要一个额外的数组记录运算中产生的信息。

- [范例完整代码](https://github.com/kopella/algorithms-basic-learn/blob/main/design_and_analysis_techniques/dynamic_programming/dynamic_programming.cc)