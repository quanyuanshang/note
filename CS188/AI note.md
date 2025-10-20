![[Pasted image 20251013151331.png]]
# State Spaces Uninformed Search
A search problem consists of the following elements:

- A **state space** - The set of all possible states that are possible in your given world. 不同场景包括不同信息：![[Pasted image 20251011172153.png]]
state space size计算：![[Pasted image 20251011172226.png]]
- A **set of actions** available in each state
- A **transition model** - Outputs the next state when a specific action is taken at current state
- An **action cost** - Incurred when moving from one state to another after applying an action
- A **start state** - The state in which an agent exists initially
- A **goal test** - A function that takes a state as input, and determines whether it is a goal state

## Search algorithm properties：
 Complete: Guaranteed to find a solution if one exists? 
 Optimal: Guaranteed to find the least cost path?
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
什么是arc consistency：如果对于变量 **X** 的每一个可能取值 **x**，都存在变量 **Y** 的某个取值 **y**，使得 **(x, y)** 满足它们之间的约束那么我们就说：**变量 X 对变量 Y 是弧一致的（arc-consistent）**。
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

- 检查其父节点到它的约束（即 $Parent(X_i)→X_i\text{Parent}(X_i) \rightarrow X_i）$。
    
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


# Propositional Logic命题逻辑
- conjunction
- disjunction
- implication
- biconditional
- $S1\ \rightarrow\ S2$ iff S1 false or S2 true
- **Validity:** valid in all models $A \vee A$ 如果对于任意一种变量赋值方式，使得整个公式为真。
- knowledge based agent：Such an agent maintains a knowledge base, which is a **collection of logical sentences that encodes what we have told the agent and what it has observed**
![[Pasted image 20251006164927.png]]
- **Satisfiability**：在**命题逻辑**中，给定一个逻辑公式，我们说它是“**可满足的（satisfiable）**”，如果存在一种变量赋值方式，使得整个公式为真。
> - $A\Rightarrow  B \equiv \neg A\vee B$:
> - A|=B 需要证明$A\and \neg B$ is unsatisfiable:如果我们令 $  $，那么整个公式为真 → ✅ 可满足

## Horn logic
为了让推理更高效，![[image-9.png]]
![[image-10.png]]
命题逻辑局限性：
1. 太依赖T、F赋值，难以identify individual
2. 没办法涉及个体的性质，只有确定的值无法拥有变量Bill is tall”
3. 缺乏generalization：无法提炼规律all triangles have 3 sides这里就需要enumerate所有三角形
# Firstorder Logic一阶逻辑
更像自然语言：世界包含：
- Objects: people, houses, numbers, colors, baseball games, wars, …
- Relations: red, round, prime, bigger than, part of, comes between, …
- Functions: father of, best friend of, one more than

| 元素               | 说明                                                        |
| ---------------- | --------------------------------------------------------- |
| **常量符号**         | 表示具体的对象，如 `John`、`Shanghai`                               |
| **变量符号**         | 表示任意对象，如 `x`、`y`                                          |
| **谓词符号**         | 表示对象之间的关系或属性，如 `Loves(John, Mary)` 表示 John 爱 Mary         |
| **函数符号**         | 表示对象之间的映射，如 `FatherOf(x)` 表示 x 的父亲                        |
| **量词quantifier** | 用于表达范围：universal`∀x` 表示“对所有 x”，existential`∃x` 表示“存在某个 x” |
| **逻辑连接词**        | 如 ∧（与）、∨（或）、¬（非）、⇒（蕴含）等                                   |
|                  |                                                           |
假设我们要表达“所有人都有母亲”：
- 用一阶逻辑可以写成： $∀x Person(x) ⇒ ∃y\ Mother(y, x)$ 这表示：对所有 x，如果 x 是人，那么存在一个 y，使得 y 是 x 的母亲。
再比如，“John 是 Mary 的兄弟”可以写成： $Brother(John,Mary)$

properties of quantifier：
![[image-49.png]]
![[image-50.png]]

一个案例：![[image-48.png|这是错误的，因为只要有人不在STU就一定成立。]]
equality：![[image-51.png]]

一阶逻辑如何转变为命题逻辑？
- 找出所有kb，![[image-52.png]]
- ![[image-53.png|找出所有ground term，instantiating得到新KB]]
- 如果原先的知识库里有一个函数。那么容易造成无限循环。farther的farther的farther……
- Reduction：semi-decisive（递归往前，遇到可证明entail的停下，反之没法确定不entail，因为infinite）
Unification：变量替换让两式相等
![[image-54.png]]
FOL里的horn logic：
![[image-55.png|存在一个theta能匹配前面的条件就能够推出结果。也就是找到能使pi与pi‘相等的theta]]


SAT 的基本定义

给定一个由布尔变量（如 A、B、C）和逻辑运算符（如 ∧、∨、¬）组成的公式，SAT 问题就是判断：

> 有没有一种方式给这些变量赋值（True 或 False），使整个公式成立？

如果有 → 这个公式是 **可满足的（satisfiable）** 如果没有 → 这个公式是 **不可满足的（unsatisfiable）**
Inference algorithm：
1. Forward Chaining：
![[image-11.png]]
data-driven。 a-b-l-m-p-q
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
1. Backeard chaining：
![[image-12.png]]
goal-driven:q-p-l-m-a-b
# DPLL ForwardChaining
- entailment（蕴含）: $\alpha |= \beta$ where a true b true.
- rule of inference:

| 推理规则名称                            | 形式表达式                                      | 说明                              |
| --------------------------------- | ------------------------------------------ | ------------------------------- |
| **肯定前件（Modus Ponens）**            | P→Q,  P⊢Q$P \rightarrow Q$, ; $P \vdash Q$ | 如果 P 蕴含 Q，且 P 为真，则 Q 为真。        |
| **否定后件（Modus Tollens）**           | P→Q,  ¬Q⊢¬P                                | 如果 P 蕴含 Q，且 Q 为假，则 P 为假。        |
| **析取三段论（Disjunctive Syllogism）**  | P∨Q,  ¬P⊢Q                                 | 如果 P 或 Q 为真，且 P 为假，则 Q 为真。      |
| **假言三段论（Hypothetical Syllogism）** | P→Q,  Q→R⊢P→R                              | 链式推理：如果 P 推出 Q，Q 推出 R，则 P 推出 R。 |
| **构造性两难（Constructive Dilemma）**   | P→Q,  R→S,  P∨R⊢Q∨S                        | 从两个条件推出两个可能的结论。                 |

- 如果我们想判断一个命题 B 是否是由命题 A 推理出来的（即 A ⊨ B），我们可以通过构造一个新的公式：**A ∧ ¬B**，然后检查它是否**不可满足（unsatisfiable）**。也就是SAT找不到一个模型使得这个表达式可满足，也就是这个表达式最终能返回true
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

A logical Pacman
![[image-13.png]]
1. Sensor Model![[image-14.png]]
2. Transition model![[image-15.png]]
3. initial state:![[image-16.png]]
4. domain constraint
- cannot be in two places at once
- cannot take two actions at the same time!
- cannot go into a wall!
planning:
• SAT solver
– Input: a logic expression
– Output: a model (true/false assignments to symbols) that satisfies the expression if such a model exists
• Can we use it to make plans for Pacman (e.g., to move to a goal position)?
– For T = 1 to infinity, set up the KB as follows and run SAT solver: 
	• Initial state, domain constraints, sensor & transition model sentences up to time T 
	• Goal is true at time T– If a model is returned, extract a plan from action assignment

解决


# Intro to Probability
## Inference By Enumeration (IBE)
Given a joint PDF[1](https://kg.darstib.cn/note/cs188/note/10-Intro_to_Probability/#fn:1), we can trivially compute any desired probability distribution $$P(Q_i...Q_m|e_i...e_n)$$ using a simple and intuitive procedure known as **inference by enumeration**, for which we define three types of variables we will be dealing with
1. **Query variables**  $Q_i$, which are unknown and appear on the left side of the conditional bar(|) in the desired probability distribution.
2. **Evidence variables** $e_i$  , which are observed variables whose values are known and appear on the right side of the conditional bar(|) in the desired probability distribution.
3. **Hidden variables**, which are values present in the overall joint distribution but not in the desired distributio
![[image.png]]
## Bayes net
define：![[image-2.png]]
![[image-1.png]]
利用条件独立

## Variable Elimination

![[image-3.png]]
- **消除** CC
    
    - 找出所有包含 C 的因子：$P(C∣T)和 P(E∣C,S)$
    - 将它们相乘形成新因子 $f_1 = P(C|T) \cdot P(E|C,S)$
    - 对 C 求和（边缘化），得到新因子 $f_2(+e, T, S)$
        
- **消除** SS
    
    - 将 f2 与 P(S|T) 相乘，得到 f3(+e, T, S)
        
    - 对 S 求和，得到 f4(+e,T)
        
- **结合剩余因子**
    
    - 将 f4(+e,T)与 P(T)相乘，得到 f5(+e,T)
        
- **归一化**
    
    - 最后对 f5(+e,T)f_5(+e, T) 进行归一化，得到我们想要的 P(T∣+e)
    - 


![[image-4.png]]![[image-5.png]]
第一个公式右边得到的是$P(+e,T)$

## Approximate Inference
通过采样估计
### Prior Sampling
example：

```python
import random

def get_t():
    if random.random() < 0.99:
        return True
    return False

def get_c(t):
    if t and random.random() < 0.95:
        return True
    return False

def get_sample():
    t = get_t()
    c = get_c(t)
    return [t, c]
```

![[image-6.png]]
问题是：对于一些极端例子需要99%的采样。 If we wanted to compute$P(C|-t)$ , we’d have to throw away 99% of our samples.
### **Rejection** Sampling：
if we want to generate P(C|-t), we don't generate c if T=+t so we can throw most of bad samples to faster generating.
如果你直接**强制设置变量** t=falset = \text{false}，你可能会得到一些**不符合真实联合分布**的样本。为什么？

因为你跳过了对 tt 的采样过程，导致整个样本的概率只由其他变量的条件概率决定，而不是完整的联合分布。

**核心思想：** 虽然我们强制设置了证据变量，但我们可以通过给每个样本一个“权重”来补偿这个偏差。

这个权重是：
$\text{Weight} = P(\text{evidence} \mid \text{sampled variables})$
也就是说，每个样本的权重是：**在该样本下，证据出现的概率**。

这样做的好处是：
- 我们仍然可以强制设置证据变量（避免生成无效样本）。
- 但通过加权，我们恢复了对真实分布的近似。
- 所有样本的贡献由其权重决定，权重高的样本影响更大。

```python
def likelihood_weighting(X, e, bn, N):
    """
    Calculate the posterior probability P(X | e) for the query variable X given evidence e.

    Parameters:
    X: Query variable
    e: Observed values for evidence variables
    bn: Bayesian network specifying the joint distribution P(X1, ..., Xn)
    N: Total number of samples to be generated

    Returns:
    Normalized weight vector W
    """
    W = {value: 0 for value in X.values()}  # Initialize weight vector

    for j in range(N):
        x, w = weighted_sample(bn, e)  # Generate sample and weight
        W[x] += w  # Update weight

    return normalize(W)  # Normalize weights

def weighted_sample(bn, e):
    """
    Generate an event and a weight.

    Parameters:
    bn: Bayesian network
    e: Observed values for evidence variables

    Returns:
    x: Generated event
    w: Weight
    """
    w = 1  # Initialize weight to 1
    x = {}  # Initialize event

    for Xi in bn.variables:  # Iterate over all variables
        if Xi in e:  # If it is an evidence variable
            w *= P(Xi | parents(Xi))  # Update weight
            x[Xi] = e[Xi]  # Set event value to the evidence value
        else:
            x[Xi] = random_sample(P(Xi | parents(Xi)))  # Sample from the conditional distribution

    return x, w  # Return event and weight

def normalize(W):
    """
    Normalize the weight vector.

    Parameters:
    W: Weight vector

    Returns:
    Normalized weight vector
    """
    total_weight = sum(W.values())
    return {key: value / total_weight for key, value in W.items()}  # Normalize
```

### Gibbs sampling
In this approach, we first set all variables to some totally random value (not taking into account any CPTs). We then repeatedly pick one variable at a time, clear its value, and resample it given the values currently assigned to all other variables.
![[image-7.png]]
总的来说每此采样都会根据其他非证据变量来更新某个变量的条件概率，并且条件取概率更大的那个值。反复操作N次，
简单的模拟：
- P(T=T)=0.2P(T = T) = 0.2, P(T=F)=0.8P(T = F) = 0.8
    
- P(S=T∣T=T)=0.7P(S = T | T = T) = 0.7, P(S=T∣T=F)=0.2P(S = T | T = F) = 0.2
    
- P(E=T∣T=T)=0.9P(E = T | T = T) = 0.9, P(E=T∣T=F)=0.3
🔧 初始化：

- 固定 E=TE = T
    
- 随机设定初始值：T=FT = F, S=TS = T
    

🌀 第 1 次迭代：

更新 TT：

计算：

P(T∣S=T,E=T)∝P(T)⋅P(S=T∣T)⋅P(E=T∣T)P(T | S = T, E = T) ∝ P(T) \cdot P(S = T | T) \cdot P(E = T | T)
- T=TT = T: 0.2⋅0.7⋅0.9=0.1260.2 \cdot 0.7 \cdot 0.9 = 0.126
- T=FT = F: 0.8⋅0.2⋅0.3=0.0480.8 \cdot 0.2 \cdot 0.3 = 0.048

归一化：
- P(T=T)≈0.724P(T = T) ≈ 0.724, P(T=F)≈0.276P(T = F) ≈ 0.276
    
采样结果：**T = T**
 更新 SS：
- P(S=T∣T=T)=0.7P(S = T | T = T) = 0.7, P(S=F∣T=T)=0.3P(S = F | T = T) = 0.3 采样结果：**S = T**
记录：T=TT = T
🌀 第 2 次迭代：
更新 TT：
- S=TS = T, E=TE = T
- 同上，结果仍是：P(T=T)≈0.724P(T = T) ≈ 0.724
采样结果：**T = T**

#### 更新 SS：

采样结果：**S = F**

记录：T=TT = T

 🌀 第 3 次迭代：

更新 TT：

- S=FS = F, E=TE = T
    

计算：

- T=TT = T: 0.2⋅0.3⋅0.9=0.0540.2 \cdot 0.3 \cdot 0.9 = 0.054
    
- T=FT = F: 0.8⋅0.8⋅0.3=0.1920.8 \cdot 0.8 \cdot 0.3 = 0.192
    

归一化：

- P(T=T)≈0.219P(T = T) ≈ 0.219, P(T=F)≈0.781P(T = F) ≈ 0.781
    

采样结果：**T = F**

更新 SS：

- P(S=T∣T=F)=0.2P(S = T | T = F) = 0.2, P(S=F∣T=F)=0.8P(S = F | T = F) = 0.8 采样结果：**S = F**
    

记录：T=F

最后根据5次更新中T取值最多的那个来取。


# Markov Models
weather example:
$$
P(W_0, W_1, \ldots, W_n) = P(W_0)P(W_1|W_0)P(W_2|W_0, W_1)\ldots P(W_n|W_{n-1}) = P(W_0) \prod_{i=0}^{n-1} P(W_{i+1}|W_i)

$$
To track how our quantity under consideration (in this case, the weather) changes over time, we need to know both it’s **initial distribution** at time t = 0 and some sort of **transition model** that characterizes the probability of moving from one state to another between timesteps.
假设：stationary distribution
## mini-forward algorithm
$$
P(W_{i+1}) = \sum_{w_i} P(w_i, W_{i+1}) \quad \text{chain rule} \quad \Rightarrow \quad P(W_{i+1}) = \sum_{w_i} P(W_{i+1} \mid w_i) P(w_i)

$$
已知transition model：$P(W_{i+1}|W_i)$就可以求解$P(W_{i+1})$
## Stationary Distribution
当经过一定量时间之后$P(W_{i+1})=P(W_{i})$
![[image-8.png]]
解上述的方程。
 In general, if  $W_t$had a domain of size k, the equivalence $P(W_{i+1}) =  \sum_{w_i} P(W_{i+1} \mid w_i) P(w_i)$  yields a system of k equations, which we can use to solve for the stationary distribution.

# Hidden Marko Models
HMM：allows us to observe some evidence at each timestep, which can potentially affect the belief distribution at each of the states. Compared to the Markov model, the Hidden Markov model requires not only the initial distribution, the transition model, but also the **sensor model.**![[image-17.png|Wi:state variable Fi an evidence variable]]
![[image-24.png]]
性质：
1. ![[image-18.png]]
2. ![[image-19.png]]
3. ![[image-20.png]]
- Wi：隐藏状态（比如系统的真实状态）
- Fi：观测值（你能看到的信号）
- 每个状态只依赖于前一个状态（马尔可夫性）
- 每个观测值只依赖于当前状态（局部性）
![[image-21.png|605x185]]
![[image-25.png]]
![[image-26.png]]
解释：当我们获取到观测之后，不确定性会降低！
![[image-29.png|有了带伞的条件之后Rain的概率上升，第二天概率减少是因为不确定性增加，但如果观测到两次带伞，Rain的概率又会上去]]


## Viter Algorithm
![[image-30.png]]
edge weight：![[image-31.png]]
![[image-32.png]]

![[image-33.png|与前项关系]]
第一遍从前到后循环：记录能让m最大的x，第二次从后向前循环，走已经找到的最优路线![[image-34.png]]

HMM缺点：Hidden Markov Models have the same drawback as bayes net - the time it takes to run exact inference with the forward algorithm **scales with the number of values in the domains of the random variables**. 
## Particle Filtering
类似于贝叶斯净采样的隐马尔可夫模型称为**粒子滤波**，涉及通过状态图模拟一组粒子的运动，以近似相关随机变量的概率（信念）分布。我们认为粒子在任何给定时间步长处于任何给定状态的信念完全取决于我们模拟中该时间步长处于该状态的粒子数量。要从粒子列表中恢复信念分布，您需要做的就是计算粒子的数量并对其进行归一化。
- Simulation：
- ### 1️⃣ 粒子初始化（Particle Initialization）

- 随机生成一组粒子，每个粒子代表一个可能的初始状态。
    
- 通常是从先验分布中采样，比如均匀分布或高斯分布。
    

### 2️⃣ 时间推进更新（Time Elapse Update）

- 根据**状态转移模型**（transition model）更新每个粒子的状态。
    
- 模拟系统随时间演化的过程。
    

例如：如果粒子表示一个人的位置，状态转移模型可能是“每秒移动 ±1 米”。

### 3️⃣ 观测更新（Observation Update）

- 根据**观测模型**（sensor model）评估每个粒子的“可信度”。
    
- 给每个粒子分配一个权重，表示它与当前观测值的匹配程度。![[image-35.png]]
    ![[image-36.png]]
- 然后根据这些权重进行**重采样**（resampling），保留高权重粒子，丢弃低权重粒子。

## Utilities：
![[image-37.png]]
- **Axioms of Rationality**：
- ![[image-38.png]]
- ![[image-39.png]]
## Decision Network
In decision network: 
- Chance node (ovals) - Chance nodes in a decision network behave identically to Bayes’ nets. 
- Action node (rectangles) - Action nodes are nodes that we have complete control over. 
- Utility node (diamonds) - Utility nodes are children of some combination of action and chance nodes.
- ![[image-40.png]]
**GOAL：** select the action which yields the **maximum expected utility**(MEU), and the expected utility of taking an action a given evidence e and n chance nodes is computed with
![[image-41.png|a是action]]
![[image-42.png|b代表雨伞，W是随机的天气]]
### The Value of Perfect Information (VPI)
 mathematically quantifies the amount an agent’s maximum expected utility is expected to increase if it observes some new evidence.
 ![[image-43.png|s是set of states]]
 由于实际上并不知道新的evidence是啥，所以用概率分布![[image-44.png]]
 一些性质：![[image-45.png]]




## Markov Decision Processes
定义的properties：
![[image-46.png]]
- **markovianess**
    - if we know the present state, knowing the past doesn’t give us any more information about the future
    - $$ P(St+1​=st+1​∣St​=st​,At​=at​,...,S0​=s0​)=P(St+1​=st+1​∣St​=st​,At​=at​)$$
    - $$ T(s,a,s′)=P(s′∣s,a)$$
### Finite Horizons and Discount factors
设置一些时间限制
![[image-47.png|采用特定的gamma让U有一个上限\折扣因子 γ∈[0,1]：未来奖励的衰减程度]]
### Solving MDP with the Bellman Equation
MDP(makov decision process)目标是找到一个**最优策略** π∗:S→A\pi^*: S \to A，使得在每个状态下选择的动作能最大化长期累计奖励。
🔍 状态值函数 $U^*(s)$
这是每个状态的最优价值，定义为：
$U^*(s) = \max_a Q^*(s, a)$
意思是：在状态 s 下，选择最优动作 a 所能获得的最大期望回报。

🔁 Q值函数$Q^*(s, a)$
这是在状态 ss 执行动作 aa 的期望回报，定义为：$Q^*(s, a) = \sum_{s'} T(s, a, s') \left[ R(s, a, s') + \gamma U^*(s') \right]$
解释如下：
- 对所有可能的下一状态 s′ 求和
- 每个项是：转移概率 ×（即时奖励 + 折扣后的未来价值）
这就是**Bellman方程的Q形式**，它体现了**期望最大化原则**。

🌳 搜索树与不确定性
你提到“state-space graphs can be unraveled into search trees”，这是说：
- MDP可以展开成一个搜索树，每个节点是状态，每个分支是动作
- 在这种树中，不确定性通过**Q-state节点**建模，它们类似于**expectimax中的chance节点**
也就是说，Q值函数在搜索树中扮演了“期望评估”的角色，帮助我们在不确定性下做出最优决策