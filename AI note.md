# State Spaces Uninformed Search
A search problem consists of the following elements:

- A **state space** - The set of all possible states that are possible in your given world
- A **set of actions** available in each state
- A **transition model** - Outputs the next state when a specific action is taken at current state
- An **action cost** - Incurred when moving from one state to another after applying an action
- A **start state** - The state in which an agent exists initially
- A **goal test** - A function that takes a state as input, and determines whether it is a goal state
## State Space Graphs and Search Trees
![[Pasted image 20250919111832.png]]
对于有环的space graph，tree是无穷大的。
fringe（边界 / frontier）存储的是“当前正在等待扩展的节点”，前面已经展开之后就remove.
1. 
![[Pasted image 20250919114233.png]]
>每一层展开有b种选择，m层，最终O(bm).这样的空间复杂度比较有优势

2. 
![[Pasted image 20250919113847.png]]
能提前在终止点结束，但是在找到最终节点之前要保留所有节点。
> 对于浅层的路径，BFS更有优势。

如果说路径都是1，可以结合DFS和BFS：
 Idea: get DFS’s space advantage with BFS’s time / shallow-solution advantages  Run a DFS with depth limit 1. If no solution… 
 Run a DFS with depth limit 2. If no solution… 
 Run a DFS with depth limit 3. ….. 
 Isn’t that wastefully redundant? 
 Generally most work happens in the lowest level searched, so not so bad!


3. UCS(类似于dij)
![[Pasted image 20250921100433.png]]
4. A*

# Constraint Satisfaction Problems

Unlike search problems, CSPs are a type of **identification problem**, problems in which we must simply identify whether a state is a goal state or not, with no regard to how we arrive at that goal. CSPs are defined by three factors:
![[Pasted image 20251010102034.png]]
**NP-hard**
N variables with domain size{d}=>O(d^N)
- Partial assignment: variable assignments to CSPs where some variables have been assigned values while others have not
- Goal: all variables are assigned and all constraints are satisfied in the state it’s testing.
- Constraint Graph:
- ![[Pasted image 20250925164426.png]]
> Coloring Problem

## **backtracking search**:
>1. Fix an ordering for variables, and select values for variables in this order. Because assignments are commutative (e.g. assigning WA=Red,NT=Green is identical to NT=Green,WA=Red), this is valid.
>2. . When selecting values for a variable, only select values that don’t conflict with any previously assigned values. If no such values exist, backtrack and return to the previous variable, changing its value.
![[Pasted image 20250925164737.png]]
## improvement for bk:
  ### filtering:
   forward checking:A naïve method for filtering is **forward checking**, which whenever a value is assigned to a variable Xi, prunes the domains of unassigned variables that share a constraint with Xi that would violate the constraint if assigned.
	![[Pasted image 20250925165315.png]]
	- arc consistency: 
	1. 初始化队列 Q
	- 把所有变量之间的约束关系（弧）加入队列 Q。
	- 每个约束关系是一个方向的弧，比如 $$Xi→XjX_i \rightarrow X_j$$
	2. 迭代处理队列
	- 从 Q 中取出一个弧$$X_i \rightarrow X_j$$
	- 检查 X_i 的每个值 v：是否存在 X_j 的某个值 w，使得 vv 和 ww 不违反约束。
		- 如果没有这样的 ww，就把 vv 从 XiX_i 的域中删除。
3. 如果有值被删除
	- 如果在上一步中从 XiX_i 的域中删除了某些值：
		- 把所有指向 XiX_i 的弧 Xk→XiX_k \rightarrow X_i 加入队列 Q（除非已经在队列中）。
		- 这样可以重新检查这些变量是否仍然有合法取值。
	#### 4. 终止条件
	- 当队列 Q 为空时，算法结束。
	- 如果某个变量的域变成空集，说明没有合法解，触发回溯。
	![[Pasted image 20250925170440.png]]

> The AC-3 algorithm has a worst-case time complexity of O(ed3), where e is the number of arcs (directed edges) and d is the size of the largest domain. Overall, arc consistency is more holistic of a domain pruning technique than forward checking and leads to fewer backtracks, but requires running significantly more computation in order to enforce.

>This idea can be further extended through the idea of **strong k-consistency**. A graph that is strong k-consistent possesses the property that any subset of k nodes is not only k-consistent but also k−1,k−2,…,1-consistent as well. Not surprisingly, imposing a higher degree of consistency on a CSP is more expensive to compute. Under this generalized definition for consistency, we can see that arc consistency is equivalent to 2-consistency.对于任意 **K个变量**，如果你已经为其中的 **K−1个变量**赋值，并且这些赋值满足所有约束，那么你一定可以为第K个变量找到一个值，使得整个K个变量的赋值仍然满足所有约束。


### orderding
#### _Minimum Remaining Values (MRV)_:
When selecting which variable to assign next, using an MRV policy chooses whichever unassigned variable has the fewest valid remaining values (the _most constrained variable_). This is intuitive in the sense that the most constrained variable is most likely to run out of possible values and result in backtracking if left unassigned, and so it’s best to assign a value to it sooner than later.
#### _Least Constraining Value (LCV)_:
Similarly, when selecting which value to assign next, a good policy to implement is to select the value that prunes the fewest values from the domains of the remaining unassigned values. Notably, **this requires additional computation** (e.g. rerunning arc consistency/forward checking or other filtering methods for each value to find the LCV), but can still yield speed gains depending on usage.

#### Tree-structured CSP
如果一个 CSP 的约束图是树状结构（没有环），我们可以用一个高效算法在 **O(n·d²)** 时间内解决它，其中：
- n：变量数量
- d：每个变量的可能取值数量（域大小）
1. 选一个根节点

- 从约束图中任选一个变量作为根节点（因为树的任意节点都可以作为根）。
    
- 将所有无向边转换为**从根节点出发的有向边**，形成一个**有向无环图（DAG）**。
    

2. 对图进行线性排序（拓扑排序）

- 排序后的图中，所有边都从左指向右，表示从父节点到子节点的依赖关系。
    

3. **反向遍历：进行弧一致性检查（Backward Arc Consistency）**

从最后一个变量 XnX_n 开始，向前遍历到 X2X_2，对每个变量执行：

- 检查其父节点到它的约束（即 Parent(Xi)→Xi\text{Parent}(X_i) \rightarrow X_i）。
    
- 对每个变量的取值域进行**剪枝**：移除那些无法与父节点值兼容的取值。
    

这一步确保了：每个变量的取值都能与其父节点保持一致。

4. **正向遍历：进行赋值（Forward Assignment）**

从根节点 X1X_1 开始，依次为每个变量赋值：

- 每次选择一个与父节点值兼容的取值。
    
- 因为之前已经做了弧一致性检查，保证了每个变量至少有一个合法值。
    

这个过程是**线性的**，不会回溯，也不会冲突，能保证找到一个完整的解。

- Cutset Conditioning
现实中的 CSP 通常不是树状图，而是有环的图。Cutset Conditioning 的核心思想是：

> 找出一个最小的变量集合（称为 **cutset**），把它们从图中“拿掉”，让剩下的图变成树结构。

1. **找到最小 cutset**：设这个集合大小为 cc，例如地图着色问题中，South Australia 是一个 cutset。
    
2. **枚举 cutset 的所有可能赋值**：因为 cutset 中的变量可能互相有约束，我们需要尝试所有可能的组合，最多有 dcd^c 种。
    
3. **每次尝试一个 cutset 赋值**：
    
    - 对 cutset 的邻居进行 **域剪枝**（prune domains），排除不合法的取值。
        
    - 剩下的 CSP 是树结构，可以用树结构算法在 O((n−c)⋅d2)O((n - c)·d²) 时间内解决。
        
4. **如果某个 cutset 赋值导致无解**，就回溯，尝试下一个组合。
- cutset 有 cc 个变量，每个变量最多有 dd 个值 → 总共要尝试 dcd^c 种组合。
    
- 每次组合都要解决一个大小为 n−cn - c 的树结构 CSP → 每次花费 O((n−c)⋅d2)O((n - c)·d²)
    

所以总时间复杂度是：

O(dc⋅(n−c)⋅d2)



## Local Search
Local search works by iterative improvement—start with some random assignment to values, then iteratively select a random conflicted variable and reassign its value to the one that violates the fewest constraints until no more constraint violations exist (a policy known as the **min-conflicts heuristic**).

However, despite these advantages, local search is both incomplete and suboptimal and so won’t necessarily converge to an optimal solution. Additionally, there is a critical ratio around which using local search becomes extremely expensive:

![[Pasted image 20250925173714.png]]


# GAME
Deterministic Games:![[Pasted image 20251010105413.png]]
Zero-Sum Games: 一方最大化某个值，另一方想最小化这个值
General Games：每个agent的得分是independent的

## Minmax
The first zero-sum-game algorithm we will consider is **minimax**, which runs under the motivating assumption that the opponent we face behaves optimally, and will always perform the move that is worst for us .
- A **state’s value** is defined as the best possible outcome (utility) an agent can achieve from that state[3](https://kg.darstib.cn/note/cs188/note/05-Trees_Minimax_Pruning/#fn:2).
- The value of a **terminal state**, called a terminal utility, is always some deterministic known value and an inherent game property .
- In this example, the value of a **non-terminal state** is defined as the maximum of the values of its children.
> the minimax algorithm only maximizes over the children of nodes controlled by Pacman, while minimizing over the children of nodes controlled by ghosts.
![[Pasted image 20250925175950.png]]
![[Pasted image 20250925180523.png]]
最小化鬼的节点（分别-8，-10），最大化pacman节点，-8

![[Pasted image 20251010113049.png]]

> Recalling that b is the branching factor and m is the approximate tree depth at which terminal nodes can be found, this yields far too great a runtime for many games. For example, chess has a branching factor b ≈ 35 and tree depth m ≈ 100.


## Alpha-Beta Pruning
Conceptually, **alpha-beta pruning** is this: if you’re trying to determine the value of a node n by looking at its successors, stop looking as soon as you know that n’s value can at best equal the optimal value of n’s parent.
例子：![[Pasted image 20250925181122.png]]
![[Pasted image 20250925181131.png]]
伪代码：
![[Pasted image 20250925181217.png]]
## Expectimax
Because minimax believes it is responding to an optimal opponent, it’s often **overly pessimistic** in situations where optimal responses to an agent’s actions are not guaranteed. Such situations include scenarios with inherent randomness such as card or dice games or unpredictable opponents that move randomly or suboptimally. This randomness can be represented through a generalization of minimax known as **expectimax**.
Expectimax introduces **chance nodes** into the game tree, which instead of considering the worst case scenario as minimizer nodes do, considers the **average case**. More specifically, while minimizers simply compute the minimum utility over their children, chance nodes compute the expected utility or expected value.
在 **Expectimax** 里，**Pacman（agentIndex = 0）** 应该是 **max 节点**，选择能让自己的分数最大的动作；而 **鬼（agentIndex > 0）** 是 **chance 节点**，取合法动作的均值。
![[Pasted image 20250925205424.png]]


### Mixed Layer Types
In the Pacman example for a game with four ghosts, this can be done by having a maximizer layer followed by 4 consecutive ghost/minimizer layers before the second Pacman/maximizer layer.
![[Pasted image 20250925205914.png]]

- Expectiminimax:Environment is an extra “random agent” player that moves after each min/max agent.也就是对抗ghost下一个ghost是random ghost

### Multi-Agent Utilities
![[Pasted image 20251011113115.png]]
- Utilities:反应机制![[Pasted image 20251011113508.png]]
> 对于minmax，minmax算法不受单调的变化，但是expctimax是受这种影响的。


### Monte Carlo Tree Search

1. **Evaluation by rollouts（通过模拟评估）**

- 从当前状态 ss 开始，使用一个策略（比如随机走子）进行多次完整的游戏模拟，直到游戏结束。
- 每次模拟记录结果（胜/负），统计胜率。
- 胜率越高，说明这个状态越“有利”。

这就像是你在某个局面下，**试着玩很多次看看结果如何**，从而估计这个局面的价值。

2. **Selective search（选择性搜索）**
- 不去探索整个游戏树，而是**重点探索那些看起来更有希望的分支**。
- 每次模拟后，根据结果更新树的结构，优先扩展那些胜率高的节点。
- 没有固定的搜索深度限制（“没有 horizon constraint”），可以根据需要深入某些分支。

这就像你在玩游戏时，**更关注那些你觉得可能赢的策略**，而不是平均分配注意力。
## Evaluation Functions
Evaluation functions are widely employed in depth-limited minimax, where we treat non-terminal nodes located at our maximum solvable depth as terminal nodes, giving them mock terminal utilities as determined by a carefully selected evaluation function. 
Because evaluation functions can only yield estimates of the values of non-terminal utilities, this removes the guarantee of optimal play when running minimax.
有些情况剪枝后依旧难以计算 => 强行截断 game tree，其最底层的 non-terminal states 由 evaluate function 赋予其 known 而变为 terminal states => 降低了树的深度和其可靠性。

 Additionally, going deeper into the tree before using an evaluation function also tends to give us better results - burying their computation deeper in the game tree mitigates the compromising of optimality.
![[Pasted image 20250925181609.png]]


# Propositional Logic
- knowledge based agent：Such an agent maintains a knowledge base, which is a **collection of logical sentences that encodes what we have told the agent and what it has observed**
![[Pasted image 20251006164927.png]]
- Satisfiability：在**命题逻辑**中，给定一个逻辑公式，我们说它是“**可满足的（satisfiable）**”，如果存在一种变量赋值方式，使得整个公式为真。
> - $A∨¬BA \lor \neg B$- 如果我们令 $A=True,B=FalseA = \text{True}, B = \text{False}$，那么整个公式为真 → ✅ 可满足

# DPLL ForwardChaining
- entailment and satisfiability: - 如果我们想判断一个命题 B 是否是由命题 A 推理出来的（即 A ⊨ B），我们可以通过构造一个新的公式：**A ∧ ¬B**，然后检查它是否**不可满足（unsatisfiable）**。也就是SAT找不到一个模型使得这个表达式可满足，也就是这个表达式最终能返回true
## DPLL
- DPLL要求输入是CNF，即公式是若干个子句的“与”连接，每个子句是若干个文字的“或”连接。例如：$(A∨B)∧(¬C∨D)∧(E)(A \lor B) \land (\neg C \lor D) \land (E)$Then DPLL will continue assigning symbols truth values until either a satisfying model is found or a symbol cannot be assigned without violating a logical constraint, at which point the algorithm will backtrack to the last working assignment
However, DPLL makes three improvements over simple backtracking search:

1. **Early Termination**: A clause is true if any of the symbols are true. Also, a sentence is false if any single clause is false.
2. **Pure Symbol Heuristic**: A pure symbol is a symbol that only shows up in its positive form (or only in its negative form) throughout the entire sentence. Pure symbols can immediately be assigned true or false.[7](https://kg.darstib.cn/note/cs188/note/08-DPLL%26ForwardChaining/#fn:3)For example, N is not a pure literal because the first clause uses the negated ¬N, and the second clause uses the non-negated N.
3. **Unit Clause Heuristic**: A unit clause is a clause with just one literal or a disjunction with one literal and many falses. In a unit clause, we can immediately assign a value to the literal, since there is only one valid assignment.[8](https://kg.darstib.cn/note/cs188/note/08-DPLL%26ForwardChaining/#fn:4)
4. 伪代码：
```
function DPLL-SATISFIABLE?(s) returns true or false  
inputs: s, a sentence in propositional logic  
    # 将输入的命题逻辑句子转换为 CNF 形式的子句集合  
    clauses ← the set of clauses in the CNF representation of s  
    # 获取所有的命题符号  
    symbols ← a list of the proposition symbols in s  
     # 调用 DPLL 算法进行求解  
    return DPLL(clauses, symbols, {})
```

## Forward Chaining
什么是 Forward Chaining？

Forward Chaining 是一种**数据驱动的推理方法**，它从已知事实出发，不断应用规则，推导出新的事实，直到：

- 推导出我们关心的目标命题 q ✅
    
- 或者再也推不出新的事实 ❌
    

 工作流程详解

1. **初始化**：
    
    - 从知识库（KB）中找出所有已知为真的命题（比如 A 是真的）
        
    - 把这些命题放入一个队列（叫做 agenda）
        
2. **迭代推理**：
    
    - 每次从 agenda 中取出一个已知为真的命题 p
        
    - 检查知识库中有哪些规则的前提包含 p
        
    - 对这些规则的前提计数减一（表示我们已经知道其中一个前提为真）
        
    - 如果某条规则的所有前提都已知为真 → 那么它的结论也为真 → 加入 agenda
        
3. **终止条件**：
    
    - 如果我们推导出了目标命题 q → 返回 true
        
    - 如果 agenda 为空，说明无法再推理出新事实 → 返回 false
```
function PL-FC-ENTAILS?(KB, q) returns true or false  
    inputs:  KB, q

count ← table, where count[c] is the number of symbols in c’s premise  
inferred ← table, where inferred[s] is initially false for all symbols  
agenda ← a queue of symbols, initially symbols known to be true in KB  

while agenda is not empty do  
    p ← Pop(agenda)  # 从 agenda 中取出一个符号 p  
    if p = q then return true  # 如果 p 是查询 q，返回 true  
    if inferred[p] = false then  # 如果 p 尚未被推断  
        inferred[p] ← true  # 标记 p 为已推断  
        for each clause c in KB where p is in c.Premise do  
            decrement count[c]  # 对每个包含 p 的子句 c，减少其前提符号计数  
            if count[c] = 0 then  # 如果 c 的所有前提符号都已被推断  
                add c.Conclusion to agenda  # 将 c 的结论添加到 agenda  
return false  # 如果 agenda 处理完毕仍未找到 q，返回 false
```


> 1. `count: [1,1,2,2,2,0]`

- 这是一个列表，对应每条规则的前提数量。
    
- 每个元素表示该规则中还有多少个前提尚未被证明为真。
    
- 例如：
    
    - 第1条规则 A → B，有1个前提（A）→ `count[0] = 1`
        
    - 第3条规则 B ∧ C → D，有2个前提 → `count[2] = 2`
        
    - 第6条规则 A，是一个已知事实，相当于 True → A → `count[5] = 0`
        

这个列表会随着推理过程不断更新，表示哪些规则已经“快要触发”。

2. `inferred: {A : F,B : F,C : F,D : F,E : F,Q : F}`

- 这是一个字典，记录每个符号是否已经被证明为真。
    
- 初始时，所有符号都标记为 False（F），因为我们还没有推理出任何新事实。
    
- 当某个符号被推理出来后，我们会将它标记为 True（T）。
    

 3. `agenda: [A]`

- 这是一个队列，表示我们已经知道为真的符号，但还没有“传播”它的影响。
    
- 初始时，只有 A 是已知为真 → 所以 agenda 里只有 A。
    
- 接下来我们会从 agenda 中取出 A，看看它能触发哪些规则。


# Firstorder Logic
| 元素        | 说明                                                |
| --------- | ------------------------------------------------- |
| **常量符号**  | 表示具体的对象，如 `John`、`Shanghai`                       |
| **变量符号**  | 表示任意对象，如 `x`、`y`                                  |
| **谓词符号**  | 表示对象之间的关系或属性，如 `Loves(John, Mary)` 表示 John 爱 Mary |
| **函数符号**  | 表示对象之间的映射，如 `FatherOf(x)` 表示 x 的父亲                |
| **量词**    | 用于表达范围：`∀x` 表示“对所有 x”，`∃x` 表示“存在某个 x”             |
| **逻辑连接词** | 如 ∧（与）、∨（或）、¬（非）、⇒（蕴含）等                           |
假设我们要表达“所有人都有母亲”：

- 用一阶逻辑可以写成： $∀x Person(x) ⇒ ∃y\ Mother(y, x)$ 这表示：对所有 x，如果 x 是人，那么存在一个 y，使得 y 是 x 的母亲。
    

再比如，“John 是 Mary 的兄弟”可以写成： $Brother(John,Mary)$

SAT 的基本定义

给定一个由布尔变量（如 A、B、C）和逻辑运算符（如 ∧、∨、¬）组成的公式，SAT 问题就是判断：

> 有没有一种方式给这些变量赋值（True 或 False），使整个公式成立？

如果有 → 这个公式是 **可满足的（satisfiable）** 如果没有 → 这个公式是 **不可满足的（unsatisfiable）**
# Intro to Probability
## Inference By Enumeration (IBE)
Given a joint PDF[1](https://kg.darstib.cn/note/cs188/note/10-Intro_to_Probability/#fn:1), we can trivially compute any desired probability distribution $$P(Q_i...Q_m|e_i...e_n)$$ using a simple and intuitive procedure known as **inference by enumeration**, for which we define three types of variables we will be dealing with
1. **Query variables**  $Q_i$, which are unknown and appear on the left side of the conditional bar(|) in the desired probability distribution.
2. **Evidence variables** $e_i$  , which are observed variables whose values are known and appear on the right side of the conditional bar(|) in the desired probability distribution.
3. **Hidden variables**, which are values present in the overall joint distribution but not in the desired distributio

