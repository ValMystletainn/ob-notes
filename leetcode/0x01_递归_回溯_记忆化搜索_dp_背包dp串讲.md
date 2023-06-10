---
created: 2023-03-23 08:40
aliases: []
tags:
  - leetcode 
  - recursive 
  - backtracking
  - memory_search
  - dp 
---

# 递归

递归是十分经典的一种“计算机思维”的破题思考角度。通过定义1. 基本情况解(base case)，2. 高规模情况到更低规模情况的转换调用，就能拿到结果。而且这样写出来的代码，简洁、直观好维护。以下是一个经典的递归问题，和它思考的角度

## 例题: lc104_二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

示例：
`给定二叉树 [3,9,20,null,null,15,7]，`

```
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。

### 常规解法

先说最教科书的常规思考角度，后续的另一些解法没有这个解法自然，但是是过渡到其他解法的一个阶梯，所以也在此记录。

二叉树树本身的定义就是一个递归式的定义：一个二叉树可以是1. 一个空节点 2. 一个根节点以及与之相连的左右子二叉树 。从这个定义，我们就可以有如下思考角度，首先把示例输入模糊成这样：

```
    3
   / \
  △   △
```

那么原始的树高度，在模糊后，只能说成是，从根节点，尝试向左或者向右走，这两种尝试中走的最大值+1，或者说就是$1 + \max(h_l, h_r)$，而注意到求$h_l, h_r$本身也是可以调用原始的求值函数的，这样就完成了递归的第二个思考，从高规模情况到低规模情况的转换。这个通常是最先考虑的，因为在解题过程中，你得先思考思考这题能不能递归解出来。

然后再考虑base case，这里从二叉树的定义，或者从输入处理的本身就很容易得到，base case是二叉树退化成空树的情况，高度按照的定义不设为$0$。就得到了如下解答

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
	        return 0
	        
        l_depth = self.maxDepth(root.left)
        r_depth = self.maxDepth(root.right)
        return 1 + max(l_depth, r_depth)
```

总结一下，这种思考方式的要点在于，要在第一步分析递推关系的时候，就相信自己的函数能返回正确的结果，着重思考怎么把当前问题的**规模降低**，然后去**利用低规模问题的答案**，构建当前问题的答案。这是一个基于答案/函数返回值的思考视角。

复杂度分析部分。时间复杂度分析，根据递归的执行过程，天然的会有几种分析方法。

1. 数列递推式：如果递归函数入参跟问题规模直接相关，则可以天然地列出 $T(n) = t_{\text{operation beyond recursive}} + \sum_{\text{calltime}} T(n_\text{small})$，然后从base case得到数列的起始项，通常是$1, O(1)$的，然后完成通项的求解
2. 画出递推调用时的树状调用栈展开图，数节点，看节点对应的单步复杂度，最后得到总计的复杂度。

空间复杂度分析，除了和时间复杂度分析类似的部分，也是如上两种分析方法。此外，需要格外注意的是，在计算机执行递归的过程中，创建local frame，压栈的过程本身就是一个消耗空间的过程。所以最大栈深往往就是递归解法的空间复杂度。

在本题中，若记录节点数为$n$，则时间复杂度$O(n)$，空间复杂度$O(n)$(在树退化成链表的时候发生)

### 非常规解法

如果紧扣题目描述的定义，从根节点走到叶子结点的长度的最大值，定义为树的高度。那么，就可以拆解成如下。

1. 用递归来做树的变量，同时用入参来记录当前走过的节点数。这样可以得到每个从根节点到叶子节点的路径的长度。递归到空节点的时候，就认为寻到了一条到叶子结点的路，将其长度记录下来
2. 将所有路径求最大。

这个记录长度的步骤，需要以入参而非结果的形式进行传递，那么会得到如下代码

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        len_list = []
        def dfs_visit(node: Optional[TreeNode], cnt: int) -> None:
	        if node is None:
		        len_list.append()
		        return
		        
			 cnt += 1
			dfs_visit(node.left, cnt)
			dfs_visit(node.right, cnt)
        
		dfs_visit(root)
		return max(len_list)
```

这里新的递归函数的入参做如下的诠释，参数node表示即将访问的节点，参数cnt，表示当前走过的路径长度。

时间复杂度和空间复杂度都类似，还是$O(n)$。

最后，在本题中，我们只需要求最大，所以维护所有路径长度是没有必要的，只维护最大值即可，这可以省一个列表的空间，将代码优化如下：

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        result = 0
        def dfs_visit(node: Optional[TreeNode], cnt: int) -> None:
	        if node is None:
		        result = cnt if cnt > result else result 
		        return
		        
			 cnt += 1
			dfs_visit(node.left, cnt)
			dfs_visit(node.right, cnt)
        
		dfs_visit(root)
		return result
```

## 回溯

以上问题第二种解法，可以自然地引申到回溯，我们来看这样一道经典的问题 

### lc77_组合

给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

你可以按 **任何顺序** 返回答案。

```
输入：n = 4, k = 2
输出：
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

从组合数公式推导本身，我们很容易有如下递推的思路：
1. 第一个数，选或者不选，有两个分支。不管如何，将其记录在一个答案列表中，然后考虑第二个数选或者不选....
2. 当答案列表满`k`时，则停止，记录结果。

则很容易得到如下代码

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        def dfs(i, nums_taken):
            if len(nums_taken) == k:
                ans.append(nums_taken.copy())
                return
            if i > n:
	            return # 长度不够k的非法组合
            
            dfs(i + 1, nums_taken.copy())

            nums_taken = nums_taken.copy()
            nums_taken.append(i)
            dfs(i + 1, nums_taken)
        
        dfs(1, [])
        return ans
```

但这样，将已选元素每次都要复制一遍的做法，会带来很大的额外的空间开销，以及执行复制操作本身的时间开销。注意到，我们这里传参执行复制，仅仅是因为，如果下层调用里产生了一个append，因为用的是同一个数组地址，所以会影响上层里的`nums_taken`的值，会让后续的执行变乱。那所以，只需要记得在append之后，再写一个pop这个元素，撤销这些操作的影响，由于递归的性质，后续操作的就会维持原样了，所以将代码修改如下，

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        def dfs(i, nums_taken):
            if len(nums_taken) == k:
                ans.append(nums_taken.copy())
                return
            if i > n:
	            return # 长度不够k的非法组合
            
            dfs(i + 1, nums_taken)

            nums_taken.append(i)
            dfs(i + 1, nums_taken)
            nums_taken.pop()  # 恢复现场
        
        dfs(1, [])
        return ans
```

这个恢复现场的过程，就是所谓的**回溯**，就是将修改的状态，回拨到修改前，以备下一次使用的过程。

那再形式化为更模板的样式。注意到这里传的`nums_taken`实际是一个地址，从来没变过，所以不妨将它弄成全局变量。此外“回溯”一词的由来，也是来自于在整个大空间中搜索可行的路径，如果当前路径到头了(比如可行到终点了，或不可行了)则可以利用之前的一些探索，往回走，回溯到上一个路口分叉，而不是重新走。所以这个记录中间状态的变量，常常命名为`path`，将代码修改如下：

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        path = []
        def dfs(i):
            if len(path) == k:
                ans.append(path.copy())
                return
            if i > n:
	            return # 长度不够k的非法组合
            
            dfs(i + 1)

            path.append(i)
            dfs(i + 1)
            path.pop()  # 恢复现场
        
        dfs(1)
        return ans
```
这样做还有一个好处，就是可以在执行后，通过打印`path`，查看它和你赋的初值有没有什么变化，来检查自己有没有在复杂的回溯代码中，漏写一些恢复现场的代码。

时间复杂度分析：是这样分析的，我们一共有$C_n^k$个答案，每个答案需要用$O(k)$的时间来构造，所以时间复杂度为$O(kC_n^k)$，更一般地，可以通过展开递归树，分析树的深度和叶子结点个数来得到答案。

空间复杂度$O(n + k) = O(n)$，分别是最大的递归栈深度和构造答案的临时数组的长度(排除掉最后需要输出的答案)

此外回溯还能剪枝，我们可以将长度不够`k`的非法组合的判断提前，即如果闭区间`[i, n]`的长度加上当前路径的长度，已经小于$k$了，说明接下来即使全选了，也达不到最后的要求，那这样的尝试就没必要了，可以省去很多次递归尝试(在$k$较大时比较管用)

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        ans = []
        path = []
        def dfs(i):
			if n - i + 1 + len(path) < k:
	            return # 长度不够k的非法组合
            if len(path) == k:
                ans.append(path.copy())
                return

            
            dfs(i + 1)

            path.append(i)
            dfs(i + 1)
            path.pop()  # 恢复现场
        
        dfs(1)
        return ans
```

## 记忆化搜索

在数值为主的递归计算中，十分常用。比如，用递归计算斐波那契数列$f(n)$。从定义很容易写出如下的递归版本的函数

```python
def f(n: int) -> int:
    if n <= 1:
        return n
    return f(n - 1) + f(n - 2)
```

但在这里，如果我们输入一个数$n$，我们可以看到说，函数$f(n - 2)$在最开始的递归被调用了一次，而在$f(n - 1)$的再展开时又有一个$f(n - 2)$。所以如果我们有一个数组/哈希表，缓存/记忆了答案，在有缓存/记忆了的情况下，直接返回就行。得到如下：

```python
memo = {}
def f(n: int) -> int:
    if n <= 1:
        return n
    if n in memo:
       return memo[n]
    result = f(n - 1) + f(n - 2)
    memo[n] = result 
    return result
```

## dp
实际上就是递归的正向推导过程。以上递归过程，自然地定义了一个状态转移方程

$$ f[n] = f[n - 1] + f[n - 2] $$

递归的方式是让计算机自动的根据状态转移方程去寻找它的依赖。而动态规划，则是自己处理好这里的依赖关系，通过写好的循环，从基本子问题，到最后的答案，来进行计算。

```python
def f(n: int) -> int:
    dp_state = [0] * len(n + 1)
    dp_state[0] = 0
    dp_state[1] = 1
    for i in range(2, n + 1):
        dp_state[i] = dp_state[i - 1] + dp_state[i - 2]
    return dp_state[-1]
```

而这里，从状态方程还可以注意到，在很久之后，最开始计算的$f[0], f[1]$是多余的，其实有用的只是当前位置向左滑窗的三个长度是有用的东西，只用三个长的数组存就行。这样就引入了滚动数组进行优化，降低了空间复杂度

```python
def f(n: int) -> int:
    dp_state = [0] * 3
    dp_state[0] = 0
    dp_state[1] = 1
    for i in range(2, n + 1):
        dp_state[i % 3] = dp_state[(i - 1) % 3] + dp_state[(i - 2) % 3]
    return dp_state[n % 3]
```

## 背包dp

0-1背包问题，是可以将以上几种方式，自然而有必要地串起来的一个问题。

总括地来说，0-1背包问题是这样的一系列问题：

有$n$个物品和一个容量为$W$的背包，每个物品有重量$w_i$和价值$v_i$两个属性，要求选若干物品放入背包，使得背包中的物品价值最大且背包中物品总的重量不超过背包的容量。(价值、重量、容量一般都是非负的)

在这样的题目中，由于每个物体只有两种可能的状态——取与不取，也即0, 1，这类问题就被成为0-1背包问题

### 递归和回溯的思路

直接按着题目描述正向思考，我们需要举出所有的组合，提前剪枝掉不满足容量约束的选择方法，在到达末尾的时候，结算价值，然后维护最大值即可。

```python
def zero_one_package(weights: List[int], values: List[int], volume: int) -> int:
	n = len(weights)
	result = 0
	
	@cache
	def dfs(i: int, cum_val: int, cum_weight: int) -> None:
		if cum_weight > volume:
			return

		if i == n:
			nonlocal result 
			result = cum_val if cum_val > result else result
			return
		
		# 不选
		dfs(i + 1, cum_val, cum_weight)
		# 选
		dfs(i + 1, cum_val + values[i], cum_weight + weights[i])
	dfs(0, 0, 0)
	return result
```

在这里，因为入参是当前备选的物品id，累计的价值和累计的重量，都是数值化的，很自然地可以用记忆化递归的方法，降低复杂度。

但这样，最坏的情况还会是$O(2^n)$的复杂度(比如全选都不超过容量限制的话，那这种方法就要结算所有的情况)。

这显然是有优化空间的，比如现在一个合法的选择组合，我们那这个组合中，有一部分物品变成不选的解法，显然不会成为最优解，因为价值肯定变少了，而重量也变少，原来就不满足题意，现在更不满足题意。

以上的观察提示我们，要在选择的过程，提前取最大，把一些显然是次优的写法刨除掉。所以为了“提前最大”，原本是在路径末端才取最大做判断，我们就把这个递推函数反过来考虑，从最后一个一路选到第一个

$f[i][w]$表示：可选物品范围是$[0, i]$时，容量为$w$的背包，装载的最大价值。则考虑第$i$个物品选或者不选，然后和只能选到在$[0, i - 1]$的最值构成如下关系

$$ f[i][w] = \max(v_i + f[i - 1][w - w_i], f[i - 1][w]) $$

用人话说一下这个公式就是，如果将第$i$个物品装进去，则它会给总价值额外带来$v_i$的提升，但同时也占用了背包的容量，也就等效于将背包的最大容量降低了$w_i$

自然地递归的起点则应当是$f\left[-1\right]\left[w\right]=0, \forall w \geq 0, f\left[i\right]\left[w\right]=-\infty, \forall w < 0$ 。在实践中，比如这个背景下，由于我们知道其他位置函数值一定是大于$0$，所以可以把$-\infty$处理成$-\max(v_i)$，或者是在状态转移的时候提前判定，不让它调用$w < 0$的函数

```python
def zero_one_package(weights: List[int], values: List[int], volume: int) -> int:
	n = len(weights) 
	@cache
	def dfs(i: int, cum_weight: int) -> int:
		if cum_weight < 0:
			return -float('inf')

		if i < 0:
			return 0
		
		return max(
			dfs(i - 1, cum_weight), 
			values[i] + dfs(i - 1, cum_weight - weights[i])
		)
	
	return dfs(n - 1, volume)
```

到这里时间复杂度和空间复杂度都已经下降成了$O(nW)$

### 动态规划的思路

在后一种递归的思路中，列出了递推的转换关系，这不难将它转换为动态规划。把状态转移方程再摘抄一遍

$$ f[i][w] = \max(v_i + f[i - 1][w - w_i], f[i - 1][w]) $$

在以下例程中，变量`dp_state[i]`实际对应的是$f[i + 1]$，这样不需要对初值做额外的初始化

```python
def zero_one_package(weights: List[int], values: List[int], volume: int) -> int:
    n = len(weights)
    dp_state = [[0 for _ in range(volume + 1)] for _ in range(n + 1)]
    
    for i in range(n):
        for w in range(volume + 1):
            if w - weights[i] < 0:
                dp_state[i + 1][w] = dp_state[i][w]
            else:
                dp_state[i + 1][w] = max(dp_state[i][w], values[i] + dp_state[i][w - weights[i]])

    return dp_state[n][volume]
```

时空复杂度都还是没有改变，还是$O(nW)$。

但转成dp后，利用滚动数组的技巧，可以做空间复杂度的优化，由于只需要前一步的状态值，所以用模数同余法构造一个$2 \times (W + 1)$的滚动数组即可。 


```python
def zero_one_package(weights: List[int], values: List[int], volume: int) -> int:
    n = len(weights)
    dp_state = [[0 for _ in range(volume + 1)] for _ in range(2)]
    
    for i in range(n):
        for w in range(volume + 1):
            if w - weights[i] < 0:
                dp_state[(i + 1) % 2][w] = dp_state[i % 2][w]
            else:
                dp_state[(i + 1) % 2][w] = max(dp_state[i % 2][w], values[i] + dp_state[i % 2][w - weights[i]])

    return dp_state[n % 2][volume]
```

那么时间复杂度不变，空间复杂度就变成了$O(W)$

(附赠一个Python oop版本，在逻辑代码比较长的时候，可以不用把每个`i`相关的都改成`i % xx`，而只用改get, setitem，比较好查好写好优化)，但在python中可能不明显，因为会带来构造类的额外开销。但在C++中配合inline，达到0抽象成本，达到性能和可读性双高的场面

```python
def zero_one_package(weights: List[int], values: List[int], volume: int) -> int:
    n = len(weights)
    class DPState():
        def __init__(self, n, volume) -> None:
            self.dp_state = [[0 for _ in range(volume + 1)] for _ in range(2)]

        def __getitem__(self, state):
            idx, vol = state
            return self.dp_state[idx % 2][vol]

        def __setitem__(self, state, value):
            idx, vol = state
            self.dp_state[idx % 2][vol] = value
            
    dp_state = DPState(n, volume)
    
    for i in range(n):
        for w in range(volume + 1):
            if w - weights[i] < 0:
                dp_state[i + 1, w] = dp_state[i, w]
            else:
                dp_state[i + 1, w] = max(dp_state[i, w], values[i] + dp_state[i, w - weights[i]])

    return dp_state[n, volume]
```

在空间更紧张的题目里，开两个$W$大小的数组，可能都超内存。需要我们在以上这种不需要考虑更新顺序的滚动数组优化的基础上，再进一步优化，压缩成只有一个$W + 1$数组。

如果我们写的是并行的程序，将状态方程：

$$ f[i][w] = \max(v_i + f[i - 1][w - w_i], f[i - 1][w]) $$

直接写成$W + 1$个并行的语句

$$ f[w] \leftarrow \max(v_i + f[w - w_i], f[w]), \forall w \in [0, W] $$

这是可以的。

但是通常来说我们只能串行的执行，所以$w$这个维度的循环顺序就很重要了。从状态方程可以看到，记当前步为$i$，较大的$w$是依赖于$i - 1$步较小的$w$；而反之则不然，因为重量非负，所以$i$步较小的$w$不会依赖于$i - 1$步较大的$w$。所以，把$w$从大到小进行更新，就可以保证依赖关系正确，代码如下：

```python
def zero_one_package(weights: List[int], values: List[int], volume: int) -> int:
    n = len(weights)
    dp_state = [0 for _ in range(volume + 1)]
    
    for i in range(n):
        for w in range(volume, weights[i] - 1, -1):
            ## 这里不再需要了，可以并入循环条件中，减少一些实际的数据写入
            ## if w - weights[i] < 0:
            ##     dp_state[(i + 1) % 2][w] = dp_state[i % 2][w]
            dp_state[w] = max(dp_state[w], values[i] + dp_state[w - weights[i]])

    return dp_state[volume]
```

以上就是传统背包dp的所有内容了。也可以通过这个将(子集型)递归、回溯、动态规划串在一起进行理解。在背包dp里可能最后写出来的代码是这样的只有背包容量一个维度的dp，但是思考的时候，还是应该回到两维的状态，这样思考比较自然好切入。

## 背包dp的外延

### 约束和目标函数的改变

0-1背包可以演化成如下的问题

1. 至多装$W$，求方案数/最大价值和
2. 恰好装$W$，求方案数/最大/最小价值和
3. 至少装$W$，求方案数/最小价值和

求方案数时，状态值就令成方案数，则考虑选与不选，就有状态转移方程$dp\left[i\right]\left[W\right] = dp\left[i - 1\right]\left[W\right] + dp\left[i - 1\right]\left[W - w_i\right]$。这里容量越界时，方案数应当令成0(这是与价值做为状态值，应当令成正负无穷的区别)


### 完全背包问题

当列出的每个物品不止能选一个而是能无限地取出的时候，则称为完全背包问题。那直接将选与不选的思路拓展为选$0, 1,2,3,\dots$，就可以得到如下状态转移方程

$$ f[i][W] = \max_{k=0}^{\infty}(f[i-1][W-kw_i] + kv_i) $$
如果重量最差为$1$，每个步骤求最大值的时候，也要枚举$W$个不同的$k$，那时间复杂度就来到了$O(nW^2)$，不是很划算

但其实这个状态转移方程也可以这么写，是不选vs多选了一个`item[i]`

$$ f[i][W] = \max(f[i-1][W], f[i][W-w_i]) $$

但这样，我们就需要考虑在第$i$步的更新顺序了，考虑依赖关系，$f[i][W]$依赖之前步的$f[i - 1][W]$，和当前步更小容量的$f[i][W-w_i]$，所以更新顺序应该是从小容量更新到大容量(用不用滚动数组都得考虑)。而当$W - w_i$小于$0$时，最大值后面的函数取值是$-\infty$，自然只能取前面的这个。如果是非滚动数组的情况下，直接复制过到新的$i$，如果是滚动数组优化的话，可以考虑不改这一部分的结果，从$w_i$向上开始循环。并且时间复杂度也降回了$O(nW)$

```python
def complete_package(weights: List[int], values: List[int], volume: int) -> int:
    n = len(weights)
    dp_state = [0 for _ in range(volume + 1)]
    
    for i in range(n):
        for w in range(weights[i], volume + 1):
            dp_state[w] = max(dp_state[w], values[i] + dp_state[w - weights[i]])

    return dp_state[volume]
```

### 多重背包问题

多重背包是0-1背包和完全背包的一种中间状态，输入会额外给出第$i$种物品的最多的个数$k_i$。那么可以有靠近0-1背包的修改方法，即将第$i$种物品重复$k_i$遍。那么就变成了$\sum_{i=1}^{n}k_i$个长度阶段的0-1背包问题了。

或者用靠近完全背包的角度思考，这只是给完全背包问题递归的时候加上了一个上界$k_i$

$$ f[i][W] = \max_{k=0}^{k_i}(f[i-1][W-kw_i] + kv_i) $$

但不管怎么样，其时间复杂度都是$O(W\sum_{i=1}^nk_k)$

但这里的计算，其实是有很大冗余的。为方便举例，用0-1背包的转换视角查看，记$A_{i, j}$表示第$i$种物品拆分出来的第$j$个物品。那在0-1背包的迭代过程中，其实可能会有重复而等价的考虑，比如考虑了选$A_{i,1}, A_{i, 2}$和$A_{i,2}, A_{i, 3}$的两种情况但其实这样的情况是等价的。所以为了减少重复枚举，我们可以将$k_i$的拆分，从$k_i$个1变成少于$k_i$个的大于等于1的数相加，将商品在捆绑成对应数额价值和重量的商品，使得通过拆分出的这些数的选与不选，可以表达$[0, k_i]$中的每个数。举几个具体的数例

- $6 = 1 + 2 + 3$
- $8 = 1 + 2 + 4 + 1$

一般地，这样拆分的最优方案是，用2的幂次尽可能地加上去，加到不溢出$k_i$(假设有$p$项目)，然后再补一个$k_i - (2^{p + 1} - 1)$的拆分即可。这样每个物品组就变成了$O(\log k_i)$个子0-1商品，时间复杂度就变成了$O(W\sum_{i=1}^n \log_2 k_i)$


