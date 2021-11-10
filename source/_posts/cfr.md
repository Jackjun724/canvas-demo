---
title: 零和博弈 - CFR
tags: 机器学习
date: 2021-09-11
---


# 零和博弈 - CFR

Created: June 29, 2021 1:24 PM
Property: Unfinish
Tags: Texas Hole'em

我们把策略`σᵢ`选择行动`s`的概率表示为$`σᵢ(s)`，然后把所有其他玩家的综合策略表示为σ₋ᵢ，那我们可以把一个策略的预期效用定义为`uᵢ(σᵢ, σ₋ᵢ) = ∑ₛ ∑ₜ σᵢ(s)σ₋ᵢ(t)uᵢ(s, t)`

## 符号

其中s ∈ S为所有玩家的action中的某个action。sᵢ表示为玩家i的action，t表示所有其他玩家的action组合，u表示效用，Z表示终止状态的集合。

## Counterfactual Regret Minimization

首先我们定义以策略σ到达一个游戏节点的概率为π(σ, h) (h 是 history，也可以称作游戏状态，游戏节点也可以表示为一系列的偶然结果和决策导致它的产生）

I表示信息集，π(σ, I)表示当前游戏中所有状态到达信息集I的概率。对应的反事实到达率为π₋ᵢ(σ, I)

对于游戏状态h的 Counterfactual value 定义如下

![https://miro.medium.com/max/664/1*jUkHepWhIc9xBYF2cSrC0g.png](https://miro.medium.com/max/664/1*jUkHepWhIc9xBYF2cSrC0g.png)

定义表示的含义为以策略σ所有其他玩家的到达概率乘以到达终局状态z的概率乘以当前玩家在终局取得的效用u，然后对于所有终局状态进行求和，因此，我们可以将在游戏状态h没有采取行动 a的反事实遗憾写为

![https://miro.medium.com/max/552/1*l8dMuMDYq6FqxQ-aFd4bnw.png](https://miro.medium.com/max/552/1*l8dMuMDYq6FqxQ-aFd4bnw.png)

这意味着我们将在游戏状态 h 中始终采取行动 a 的值与给定我们当前策略的当前游戏状态的值进行比较。
