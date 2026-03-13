# DL
mov：还原遮挡下的表情
baseline：在什么**指标**下比现有的工作做的好。
1. 识别输入遮挡人脸-》表情标签
2. 表情标签-》AU点调整
3. 表情变换之后的+被遮挡的融合。（exe gan）
困难：如何定义评价指标

# RL
多agent玩自走棋游戏。
baseline：没有博弈过的
呈现：demo让训练过的agent赢。
游戏：小demo，不需要可视化。只需要知道数值。需要一个现成的，
游戏怎么选取多个agent（写个类），多个agent怎么同时博弈。
目的：学习一种策略，在和多个agent博弈之后和之前对比。让agent适应其他玩家对策。
博弈决策（自走棋）+强化学习（单个agent）


project评估：
8页论文。
proposal：
1. BackGround是什么 
2. baseline是什么
3. 框架设计：
	1. reward是什么
	2. 方法有什么challenge
	3. 通过什么方面提升performance。

final pre要有分工呈现、demo展示：


# Proposal
大家好，我们组的Proposal主题是Auto Sapai Agent Tactic，也就是使用agent来自动玩自走棋。
## Part1
那么先介绍一下什么是自走棋。
**简单来说，自走棋的游戏流程就是：**
招募 & 布阵 → 自动战斗 → 准备下一轮


**核心玩法非常简单：**

每回合，你可以用金币从商店中招募各种不同的宠物。因此你需要决定是攒金币还是使用金币买战力。之后你需要根据战略调整宠物站位。战斗将自动开始。在一场多名玩家的对局中，你将逐一与其他玩家的队伍交战，失败会扣除生命值。战斗到最后的玩家胜利。


在这次project中，总体来说目标是让agent在经过强化学习或接入大模型训练之后能表现的比训练前更强。


## **Part2 Why Auto-Battlers? 
自走棋为何适合作为研究环境**。对于自走棋，各类操作可任意组合，策略空间呈指数级增长。同时，某个购买、升级或布阵决策的效果通常要在数回合后才显现。而且游戏里有随机商店、随机对手，**随机性贯穿整个流程**。不过自走棋的 **机制是透明且完全可复现**

------
我们选择使用强化学习的理由是一方面RL 通过试错、价值估计与策略迭代，自然适配延迟奖励问题。另一方面强化学习适合在巨大动作空间中探索有效组合策略，在随机环境中具有稳健性优势
  
## PART 3 BASELINE
接下来简单介绍一下baseline。
1. 随机采取action。
2. 尽量把钱花光，不卖宠物。
3. “先让同一个 pet 变得更强，提高单体战斗力”。
4. 提高整个 team 的平均强度（均衡发展）”.

语气上， RL 能超过这些baseline

# Sapai
项目是一个基于 sapai 的 Super Auto Pets 强化学习环境，整体可分成几个层次：

- setup.py、tox.ini、sapai_gym.egg-info/

- 用于打包/发布与测试的标准 Python 包装文件。setup.py 安装时会拉取 sapai、gymnasium（之前是 gym）和 scikit-learn 等依赖；tox.ini 里定义了测试环境；egg-info 是打包产生的元数据。

- 顶层脚本与示例

- test.py：最小示例，先定义一个 opponent_generator，再实例化 SuperAutoPetsEnv 并随机采样动作，主要用于 smoke test。

- tests/test_opponent_generators.py：单元测试，确保对手生成器按预期构造 Team。

- 核心包 sapai_gym/

- SuperAutoPetsEnv.py：核心 Gymnasium 环境，实现了 gym.Env 接口。它封装了 sapai.Player 的状态、动作空间、观测编码（包括宠物、食物、玩家数值等 one-hot/归一化）以及对局流程（商店操作、战斗、奖励计算）。文件内还包含动作编码常量、动作可用性检查、状态编码等所有主要逻辑。

- ai/：放置简单的基线“AI”策略。ai/baselines.py 提供随机 agent、最大数值 agent 等函数，这些函数接受玩家与可行动作列表，返回一个动作编号，可用于生成训练对手或作为示例策略。

- opponent_gen/：基于上述 AI 的对手生成工具。opponent_generators.py 中的 opp_generator 会在一个无对战的环境里模拟商店阶段，让某个 baseline agent 组队，然后把生成的 Team 序列输出；random_opp_generator、biggest_numbers_horizontal_opp_generator 是两种现成的对手供应器。

- __init__.py 暴露 SuperAutoPetsEnv（以及子模块），方便 from sapai_gym import SuperAutoPetsEnv。

- build/lib/...

- 这是 setup.py install 产生的构建产物（打包后的模块副本），结构与源码一致，但一般只在发布时使用。

框架运行流程：外部代码通过 SuperAutoPetsEnv 构建一个符合 Gym 接口的环境；使用者自行提供 opponent_generator（可直接用 sapai_gym.opponent_gen 中的函数），然后就能用标准 RL 库（如 Stable Baselines3 MaskablePPO）训练。内部依赖 sapai 负责游戏模拟，scikit-learn 负责观测向量的 one-hot 编码，gymnasium 提供空间定义与接口约束。


# 强化学习基础知识

**策略梯度（Policy Gradient）**是一类直接对“策略”进行优化的强化学习方法。核心思想是：把策略 πθ(a|s) 表示为一个带参数 θ 的概率分布（通常由神经网络输出），通过梯度上升来最大化长期回报 J(θ)。

关键要点：

1. **直接优化策略**  
    不是像 Q-learning 那样学习价值函数再间接得到策略，而是直接让模型输出在每个状态下选各动作的概率，便于处理连续动作或随机策略。
    
2. **目标函数**  
    目标是期望回报 J(θ)=Eτ~πθ[∑t γ^t r_t]。策略梯度利用“优势”或“回报”作为权重，对采样到的动作进行加权，以增加高回报动作的概率。
    
3. **梯度估计**  
    经典的 REINFORCE：  
    ∇θ J ≈ E[ ∑t ∇θ log πθ(a_t|s_t) · G_t ]  
    其中 G_t 是从 t 时刻开始的累计折扣回报。实际常减掉 baseline（如状态值函数）以降低方差。
    
4. **Actor-Critic 框架**  
    常把策略（Actor）和价值函数（Critic）结合：Actor 用策略梯度更新，Critic 估计值函数/优势，降低梯度噪声，提高样本效率。
    
5. **优点**
    
    - 能自然处理随机策略和连续动作
    - 有时收敛更平滑
    - 允许策略显式输出概率分布
    
6. **缺点**
    
    - 纯蒙特卡洛估计方差大、样本效率低
    - 需要 careful 的学习率/正则化
    - 容易陷入局部最优，需要技术如基线、优势估计、PPO/TRPO 的信任域约束等
    

PPO、TRPO、A2C/A3C 等都属于策略梯度家族，它们在上述基本框架上加入了方差降低、信任域或并行采样等改进。

## **PPO**（Proximal Policy Optimization）
是当前最常用的策略梯度算法之一，核心目标是在 **安全可控的范围内更新策略**，兼顾稳定性与样本效率。关键特点如下：

1. **Clipped Objective**
    
    - 直接最大化经验回报的比例 `r(θ)=πθ(a|s)/πθ_old(a|s)` 容易导致策略更新过大。
    - PPO 引入 “剪切” 上下界（如 1±0.2），将 `r(θ)` 限制在 [1−ε, 1+ε]。超出范围的优势被截断，避免剧烈更新导致性能崩掉。
    
2. **Trust-Region 思路但实现简单**
    
    - 相对 TRPO（需要解二次规划、计算 Fisher 信息矩阵），PPO 只需在损失函数中加入 clip 或 KL 惩罚，训练时依然可以用普通 SGD/Adam。
    
3. **多个 Epoch + Mini-batch 训练**
    
    - 能重复利用同一批采样（相较 REINFORCE 或原始 actor-critic 一次性用完更高效）。
4. **优势函数估计**
    
    - 常搭配 GAE（Generalized Advantage Estimation）平衡偏差与方差，提升样本效率。
    
5. **应用场景**
    
    - 适用于连续/离散动作空间，已有大量工程实践（MuJoCo、Atari、Super Auto Pets 等）。
- 
### **MaskablePPO**：
`MaskablePPO` 是 Stable-Baselines3-Contrib 模块里对经典 PPO（Proximal Policy Optimization）算法的扩展实现。它在标准 PPO 的基础上加入了 **Action Masking** 功能，用来过滤当前状态下非法或不可执行的动作。

关键点：

1. **继承 PPO**：核心优化流程（策略梯度、剪切目标函数等）与 PPO 一致。
2. **Maskable**：在每次决策前可以输入一个布尔 mask，标记哪些动作是允许的。模型会在计算策略分布时把这些非法动作的概率置零，避免策略去探索/采样错误行为。
	1. **用途**：在像 Super Auto Pets 这样的环境里，某些动作在特定状态下不可执行（比如没有宠物无法出售）。MaskablePPO 可以直接利用环境返回的 mask，训练更稳定、样本效率更高。


# Sapai ml
对手策略：`biggest_numbers_horizontal_opp_generator(25)`，也就是生成 25 回合的“数值最大”基线对手。3策略和4策略分别出发然后学习。
修改reward策略：目前只和10局里面赢的局数和采取非法举动扣分有关（ml使用的mask能直接避免违规操作）。
> 
以下摘要均来自 [sapai/sapai/data.py](cci:7://file:///d:/cs/AI/final-project/sapai/sapai/data.py:0:0-0:0) 中的官方设定。
### 12 只宠物机制
1. **蚂蚁 (pet-ant, tier1, 2/1)**：`Faint` 时随机友军永久加攻加血（+2/+1 → +6/+3）。@sapai/sapai/data.py#57-104  
2. **蚊子 (pet-mosquito, tier1, 2/2)**：`StartOfBattle` 向随机敌人打 1 伤害（随机敌人数随等级 1→3）。@sapai/sapai/data.py#1348-1389  
3. **蟋蟀 (pet-cricket, tier1, 1/2)**：`Faint` 召唤对应攻防的僵尸蟋蟀（1/1→3/3）。@sapai/sapai/data.py#648-695  
4. **水獭 (pet-otter, tier1, 1/2)**：`Buy(Self)` 时随机友军永久 +1/+1（随机目标数 1→3）。@sapai/sapai/data.py#1525-1571  
5. **马 (pet-horse, tier1, 2/1)**：`Summoned(EachFriend)` 时给触发单位临时 +1→+3 攻直到战斗结束，适合召唤流。@sapai/sapai/data.py#1117-1159  
6. **天鹅 (pet-swan, tier2, 1/3)**：`StartOfTurn` 获得 1→3 额外金币。适合经济奖励。@sapai/sapai/data.py#3543-3572  
7. **蜘蛛 (pet-spider, tier2, 2/2)**：`Faint` 随机召唤 tier3 宠物，固定基础 2/2、等级随自身 1→3。@sapai/sapai/data.py#3374-3421  
8. **乌龟 (pet-turtle, tier3, 1/2)**：`Faint` 给后方 1→3 位友军施加瓜甲（抵挡一次 20 伤）。@sapai/sapai/data.py#5441-5483  
9. **长颈鹿 (pet-giraffe, tier3, 2/4)**：`EndOfTurn` 按等级给前方 1→3 个友军永久 +1/+1。@sapai/sapai/data.py#4349-4396  
10. **袋鼠 (pet-kangaroo, tier3, 1/2)**：`FriendAheadAfterAttack` 自身永久 +2/+2 → +6/+6，越打越壮。@sapai/sapai/data.py#4452-4498  
11. **海豚 (pet-dolphin, tier4, 4/6)**：`StartOfBattle` 对敌方最低血单位造成 5→15 伤害。@sapai/sapai/data.py#6013-6054  
12. **臭鼬 (pet-skunk, tier4, 3/6)**：`StartOfBattle` 将敌方最高血单位生命降到 66%→34%→10%。(详见 `pet-skunk` 条目)。@sapai/sapai/data.py#6765-6806  

### 6 种食物机制
1. **苹果 (food-apple, tier1)**：购买即给目标永久 +1/+1。@sapai/sapai/data.py#9102-9123  
2. **蜂蜜 (food-honey, tier1)**：购买后赋予“蜜蜂”状态，`Faint` 召唤 1/1 蜜蜂。@sapai/sapai/data.py#9238-9256  
3. **安眠药 (food-sleeping-pill, tier1)**：使目标立即阵亡（触发 `Faint` 效果，常用来主动触发如蚂蚁/乌龟等）。详见 `food-sleeping-pill` 条。  
4. **肉骨头 (food-meat-bone, tier2)**：装备后攻击时额外 +5 伤害（通过状态 `status-bone-attack` 实现）。  
5. **大蒜 (food-garlic, tier3)**：给目标减伤护甲（每次受击 -2 伤害，最少 1，状态 `status-garlic-armor`）。  
6. **巧克力 (food-chocolate, tier5)**：为目标增加 +1 经验（立即触发升级/技能变更）。  

> 这些机制是Reward函数设计的依据：  
> - **经济型**（天鹅、苹果、蜂蜜等）适合奖励资源增长。  
> - **召唤/支持型**（马、蜘蛛、乌龟、蜂蜜、安眠药）适合奖励协同与触发频率。  
> - **成长型**（长颈鹿、袋鼠、巧克力）可按 buff 数量或等级变化给分。  
> - **进攻/控制型**（蚊子、海豚、臭鼬、肉骨头）可按造成伤害/削弱敌人效果计分。

可根据触发条件（StartOfTurn、Faint、Buy等）与效果（ModifyStats、DealDamage、Summon等）分别构造 reward 信号，比如：
1. **触发奖励**：当宠物成功触发其技能时增加 reward（按等级权重）。  
2. **结果奖励**：根据技能效果的产出（召唤数量、增加的攻血、伤害值、金币增益）给予额外奖励。  
3. **状态/装备奖励**：当食物赋予的状态在战斗中生效（瓜甲抵挡、大蒜减伤、蜂蜜召唤）时奖励。
如何修改为多agent博弈：

当前实现默认“单智能体对固定 AI 对手”。若要“同时训练多个 agent 互打”，需要处理以下关键点：

1. 环境结构变更  
   - [SuperAutoPetsEnv](cci:2://file:///d:/cs/AI/final-project/sapai-gym/sapai_gym/SuperAutoPetsEnv.py:13:0-435:24) 只有一个 [Player](cci:2://file:///d:/cs/AI/final-project/sapai/sapai/player.py:34:0-584:24)，回合结束时用 `opponent_generator` 生成静态对手并调用 `Battle(player.team, opponent)`。要多智能体互战，需要：  
     1.1 支持两个以上 [Player](cci:2://file:///d:/cs/AI/final-project/sapai/sapai/player.py:34:0-584:24) 对象（或扩展为 `players: List[Player]`，按轮流顺序处理商店阶段）。  
     1.2 让战斗阶段调用 `Battle(team_i, team_j)`，并根据结果更新双方 `wins/lives`。  
     1.3 [step()](cci:1://file:///d:/cs/AI/final-project/sapai-gym/sapai_gym/SuperAutoPetsEnv.py:79:4-87:38)/`action_space`/`observation_space` 需要按 agent 索引区分（Gym 多智能体常见做法是封装成 `PettingZoo` 风格或自定义多 observation）。  
     1.4 Reward 需要改为每个 agent 独立返回（例如胜负 +0.1/-0.1），或者按多智能体算法需求设计。

2. 对手生成与回合调度  
   - 目前 `opponent_generator` 返回一串固定 Team 列表，只服务单人。多智能体情况下，可删除该依赖，改为：  
     2.1 在环境内部维护赛程（round robin、随机匹配等），每名 agent 完成商店阶段后与另一活跃 agent 对战。  
     2.2 若仍需“AI 填充”，可保留 generator 作为备用队伍，当玩家数量不足时补齐。

3. 动作掩码 / 合法性  
   - 仍可沿用 [_avail_actions()](cci:1://file:///d:/cs/AI/final-project/sapai-gym/sapai_gym/SuperAutoPetsEnv.py:305:4-321:32) + [action_masks()](cci:1://file:///d:/cs/AI/final-project/sapai-gym/sapai_gym/SuperAutoPetsEnv.py:326:4-330:20) 来给每个 agent 分别生成掩码。需要确保在多 agent 框架中分别返回这些掩码，并在 RL 算法（如 Multi-Agent PPO、PettingZoo+MaskablePPO）中正确消费。

4. 训练脚本  
   - `super-ml-pets/smp/train_agent.py` 目前创建单个 [SuperAutoPetsEnv](cci:2://file:///d:/cs/AI/final-project/sapai-gym/sapai_gym/SuperAutoPetsEnv.py:13:0-435:24)。你需要选择合适的多智能体 RL 框架（例如 RLlib、PettingZoo + MAPPO、自写自博弈循环），并调整训练循环以同时收集多 agent 的经验、同步更新或自博弈。  
   - 若要自博弈，常见做法是：维护多个策略实例，相互对战，或使用同一策略的不同拷贝作为对手（self-play）。

5. 其他细节  
   - `manual_battles` 参数目前只允许 `opponent_generator=None`，可改为专为多智能体模式设置：商店阶段结束后，把两名 agent 的队伍传给一个统一的战斗调度器。  
   - 日志/回放需要扩展以记录多 agent 的状态。  
   - 若使用 SB3，需要自行封装成“多环境 -> 单智能体更新”或改用支持多智能体的库。

综上，核心修改集中在 [SuperAutoPetsEnv](cci:2://file:///d:/cs/AI/final-project/sapai-gym/sapai_gym/SuperAutoPetsEnv.py:13:0-435:24)（多 Player、战斗调度、奖励输出）、对手生成/赛程逻辑，以及训练脚本对多策略/多观察的适配。建议先定义目标训练框架（自博弈、PettingZoo 等），再据此改造环境接口。




1. ml代码框架接进去+能顺利跑通
2. 先把对手改成两个自主学习的agent。
3. 改一下reward
一个episode最多有25回合，如果player wins>10判断胜利，这个episode结束。
每次 env.step(action) 算一步
pretrain训练个2048步效果：
100局里能赢20几个局
自己调整reward函数训练出来的效果很差，赢不了一局，并且reward和步数都会卡在某一阶段。
尝试使用模仿学习，步骤一是先模仿对手的策略，然后再开始强化学习。





