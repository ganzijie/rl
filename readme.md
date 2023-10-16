# 1.4 强化学习

## 1.4.1 简介

强化学习又称增强学习，是指一类从与环境交互中不断学习的问题以及解决这类问题的方法。强化学习问题可以描述为一个智能体从与环境的交互中不断学习以完成特定目标。与深度学习类似，强化学习的关键问题也是贡献度分配问题，每一个动作不能直接得到监督信息，需要通过整个模型的最终监督信息（奖励）得到，并且有一定延时性。强化学习是机器学习的一个分支，和监督学习的区别在于，强化学习问题不需要给出正确策略作为监督信息，只需要给出策略的延迟回报，并通过调整策略取得最大化的期望回报。

## 1.4.2 典型例子
### 1.4.2.1 多臂赌博机问题
给定K个赌博机，拉动每个赌博机的拉杆，赌博机会按照一个事先设定的概率掉出一块钱或不掉钱。每个赌博机掉钱的概率不一样。多臂赌博机问题是指给定有限的机会次数T，如何玩这些赌博机才能使得期望累积收益最大化。多臂赌博机问题在广告推荐、投资组合领域有着重要应用。
### 1.4.2.2 悬崖行走问题
在一个网格世界中，每个格子表示一个状态。如下图所示的一个网格世界，每个状态为(i,j)，1<=i<=7,1<=j<=3，其中格子(2,1)到(6,1)是悬崖。有一个醉汉，从左下角的开始位置S，走到右下角的目标位置E。如果走到悬崖，醉汉会跌落悬崖并死去。醉汉可以选择行走的路线，即在每个状态时，选择行走的方向：上下左右。但每走一步，都有一定概率滑落到周围其他格子。醉汉的目标是如何安全地到达目标位置。
<div align=center>

![](./imgs/1.4.1.jpg)

</div>

### 1.4.2.3 自动驾驶
自动驾驶中，环境的观测是摄像头和激光雷达采集到的周围环境的图像、点云，以及其他传感器的输出(如行驶速度、GPS定位和行驶方向)。驾驶中的环境的奖励根据任务不同，可以通过到达终点的速度、舒适度和安全性指标等确定。自动驾驶中的感知模块不可能做到完全可靠，但即使在某些模块失效的情况下，强化学习也能做出稳妥的行为。如果只有标注数据，学习到的模型每个时刻都偏移一点，到最后累积偏移会很大，而强化学习能够学会自动修正偏移。
## 1.4.3 强化学习定义
在强化学习中，有两个可以进行交互的对象：智能体和环境。
(1)智能体可以感知外界环境的状态和反馈的奖励，并进行学习和决策。智能体的决策功能是根据外界环境的状态来做出不同的动作，而学习功能是指根据外界环境的奖励来调整策略。
(2)环境是智能体外部的所有事物，并受智能体动作的影响而改变其状态，并反馈给智能体相应的奖励。
强化学习的基本要素包括：
(1)状态s是对环境的描述，可以是离散的或连续的，其状态空间为S。
(2)动作a是对智能体行为的描述，可以是离散的或连续的，其动作空间为A。
(3)策略Π(a|s)是智能体根据环境状态s来决定下一个动作a的函数。
(4)状态转移概率p(s'|s,a)是在智能体根据当前状态s做出一个动作a之后，环境在下一个时刻转变为状态s'的概率。
(5)即时奖励r(s,a,s')是一个标量函数，即智能体根据当前状态s做出动作a之后，环境会反馈给智能体一个奖励，这个奖励也经常和下一个时刻的状态s'有关。

## 1.4.4 强化学习算法
强化学习算法可以从多个不同的角度进行分类，例如基于模型和无模型的学习方法，基于价值和基于策略的学习方法(或两者相结合的演员-评论员算法)，在线策略和离线策略学习方法。下图展示了更详细的分类，加粗方框代表不同分类，其他方框代表具体算法：


<div align=center>

![](./imgs/1.4.2.jpg)

</div>

### 1.4.4.1 基于模型的方法和无模型的方法
这里首先讨论基于模型的方法和无模型的方法。在深度学习中，模型是指具有初始参数(预训练模型)或已习得参数(训练完毕的模型)的特定函数，例如全连接网络、卷积网络等。而在强化学习算法中，“模型”特指环境，即环境的动力学模型。在强化学习的五个基本要素中，加上奖励的折扣因子λ(用来给不同时刻的奖励赋予权重)，即构成了强化学习的环境。如果所有这些环境相关的元素都是已知的，那么模型就是已知的。此时可以在环境模型上进行计算，而不用再与真实环境进行交互，例如值迭代和策略迭代等规划方法。通常，智能体并不知道环境的即时奖励函数r和状态转移概率p，所以需要通过和环境交互，不断试错，观察环境相关信息并利用反馈的奖励信号不断学习。这个不断学习的过程既对基于模型的方法适用，也对无模型的方法适用。下图对现在流行的基于模型和无模型的方法作了分类：

<div align=center>
<img src="./imgs/1.4.3.jpg" width="500" height="150">
</div>
在这个不断试错和学习的过程中，可能有某些环境元素是未知的，如奖励函数R和状态转移函数P。此时，如果智能体尝试通过在环境中不断执行动作获取样本(s,a,s',r)来构建对R和P的估计，则p(s'|s,a)和r的值可以通过监督学习进行拟合。习得奖励函数R和状态转移函数P之后，所有的环境元素都已知，则之前所述的规划方法可以直接用来求解该问题。这种方式即称为基于模型的方法。无模型的方法则不尝试对环境建模，而是寻找最优策略。例如，Q-learning算法对状态-动作对(s,a)的Q值进行估计，通常选择最大Q值对应的动作执行，并利用环境反馈更新Q值函数，随着Q值收敛，策略随之逐渐收敛达到最优；策略梯度算法不对值函数进行估计，而是将策略参数化，直接在策略空间中搜索最优策略，最大化累积奖励。这种不需要对环境建模的方法称为无模型的方法。可见基于模型和无模型的区别在于，智能体是否利用环境模型，例如状态转移概率和奖励函数。


### 1.4.4.2 基于价值的方法和基于策略的方法
强化学习与深度学习相结合即产生了深度强化学习，用强化学习定义问题和优化目标，用深度学习解决策略和值函数(对策略Π的评估)的建模问题并通过反向传播来优化目标函数。深度强化学习中的策略优化主要有两类：基于价值的方法和基于策略的方法。两者结合产生了演员-评论员类算法等其他算法，它们利用价值函数的估计来帮助更新策略。基于价值的方法即对价值函数的优化，优点在于采样效率相对较高，值函数估计方差小，不易陷入局部最优；缺点是通常不能处理连续动作空间问题，且最终的策略通常为确定性策略而不是概率分布的形式。基于策略的方法直接对策略进行优化，通过对策略迭代更新，实现累积奖励最大化。与基于价值的方法具有策略参数化简单、收敛速度快的优点，适用于连续高维的动作空间。演员-评论员算法则是结合了二者优点，利用基于价值的方法学习值函数V来提高采样效率，并利用基于策略的方法学习策略函数，可以看作是基于价值的方法在连续动作空间中的扩展。下图对常见的基于价值和基于策略的方法进行了分类。
<div align=center>
<img src="./imgs/1.4.4.jpg" width="500" height="150">
</div>

### 1.4.4.3 在线策略方法和离线策略方法
在线策略方法和离线策略方法依据策略学习的方式对强化学习算法进行划分。在线策略方法评估并提升和环境交互生成数据的策略，要求智能体和环境交互的策略和要提升的策略相同，而离线策略方法可以利用其他智能体与环境交互的数据来提升自己的策略。例如常见的在线策略方法是Sarsa，它根据当前策略选择一个动作并执行，然后使用环境反馈的数据更新当前策略。Q-learning则是一种典型的离线策略方法。下图对常见的在线策略和离线策略的方法进行了分类。
<div align=center>
<img src="./imgs/1.4.5.jpg" width="500" height="150">
</div>

## 1.4.5 强化学习在自动驾驶中的应用

自动驾驶的人工智能包含了感知、决策和控制三个方面。其中感知是自动驾驶的基础，感知指的是如何通过摄像头和其他传感器的输入解析周围环境的信息，例如有哪些障碍物、障碍物的速度和距离、道路的宽度和曲率等；控制是如何通过调整汽车的机械参数达到驾驶目标，例如左转60度；决策则是给定感知模块解析出的环境信息，如何控制汽车行为达到驾驶目标，例如加速和超车。
自动驾驶决策过程中，模拟器起重要作用，负责对环境中常见的场景进行模拟，例如天气和路面情况等，还可将真实场景中采集到的数据进行回放。自动驾驶模拟器可以进行强化学习，通过在模拟器中模拟各种突发状况，强化学习算法可以利用其在这些突发状况中获得的奖励和反馈来学习如何应对突发状况。
但现有的强化学习虽然在自动驾驶的模拟环境取得不错的效果，但要真正落地还需要作出一些改进，例如提高强化学习的自适应能力，用少量样本学习到正确行为；增强模型的可解释性，这是因为深度强化学习中，深度学习的可解释性非常差，而策略函数和值函数都是由深度学习的DNN表示的，实际使用中出了问题由于可解释性差而难以排查出问题所在；提高强化学习的推理能力，避免危险行为及其后果。


