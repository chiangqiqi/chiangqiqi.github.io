---
layout: post
title:  "Sutton的强化学习第二版笔记"
date:   2020-01-13
categories: programming
---


Reinforcment Learning sutton
============================

最近有点闲暇时间，花了几天的时间又过了一下 Sutton 老人家的 reinforcment learning
第二版，这里是一些乱七八糟的笔记。

Overview
--------

Rewards are in a sense primary, whereas values, as predictions of
rewards, are secondary. Without rewards there could be no values, and
the only purpose of estimating values is to achieve more reward.

Bellman Equation 是 value 函数的一个核心。

contextual bandit
-----------------

A particularly useful version of the multi-armed bandit is the
contextual multi-armed bandit problem. In this problem, in each
iteration an agent has to choose between arms. Before making the choice,
the agent sees a d-dimensional feature vector (context vector),
associated with the current iteration. The learner uses these context
vectors along with the rewards of the arms played in the past to make
the choice of the arm to play in the current iteration. Over time, the
learner\'s aim is to collect enough information about how the context
vectors and rewards relate to each other, so that it can predict the
next best arm to play by looking at the feature vectors.

MDP
---

The MDP framework is a considerable abstraction of the problem of
goal-directed learning from interaction.

定义最优的时候先定义了 optimal value function 之后在用可以达到这个
optimal value fucntion 的 policy 来定义 optimal policy.

Dynamic Programming
-------------------

Classical DP algorithms are of limited utility in reinforcement learning
both because of their assumption of a perfect model and because of their
great computational expense

Dynamic programming 固然好，但是需要对 model of environment
有比较具体的了解。

Monte Carlo
-----------

大致意思就是可以先 generate 一个 路径，然后再按照这个路径以及这个路径的
reward 来更新 value 和 Policy

`off policy` 强化学习面临着一个悖论，首先 Agent 希望表现的更加 optimal
,另一方面，适度的 expolore 是必要的，这个时候就需要一个 target policy
和一个 behavior policy，我们 会希望 target policy 的表现更接近 optimal
而 behaviour policy 有更多的探索。 我们把这种方式称作 off-policy

off-policy 中通过 behaviour Policy b 得到的观测可以通过 importance
sampling 转化 为 target policy 对应的 value.

weighted importance sampling 和 标准的 importance sampling
是有区别的，区别在于

TD 方式
-------

TD 方式兼具 MC 和 DP 方法的优势，可以通过从环境中活得的观测来更新 value
以及 policy , 但相比于 MC 采用了 bootstrap
的方式来更新。所以相对来说是一个增量的方法， 而不是像 Monte Carlo
的方式那样要等到整个 episode 结束才能进行更新。

从 GPI 的角度来看，TD 方法和 MC 的区别在于 policy value evaluation

On policy prediction
--------------------

随着状态空间的不断增大，很多时候已经不能用表格的方式表示，所以需要使用
function approximation 的方式来表示比如值函数。

On policy control
-----------------

包含一个 policy gradient 的完整推倒 之前讲的都是基于值的方法
相对比较有理论基础，也更容易拿来处理连续动作空间的问题。

Chapter 14 Psychology
---------------------

一些动物实验的结果和强化学习的关系，在一些设定的实验中可以发现动物的一些动物学习
的方式和强化学习的方式有比较类似之处。

Chapter 15 Neuroscience
-----------------------

强化学习和神经科学的新研究也有一些关系，比如 多巴胺 看起来就是作为
reward 的一个 存在。神经科学和强化学习也在相互

chapter 16 Case studies
-----------------------

### TD Gammon

### Optimized Memory Control

比如内存调度，这个04年的文章是使用了 Sarsa 的方式来实现.
可能是因为算法自身运行 有开销的缘故并没有真正的在 DRAM 上运行，
12年的一篇文章又扩展了这个结果。

### Human-level control

### AlphaGo and AlphaZero

### Personalized Web Service

大概是一种推荐引擎的思路，Yahoo 在 2010 年使用了 contextual bandit
方法来将 CTR 提升了 12.5% ，后面的一些思路在于优化更长周期的目标。
在实际的系统中 depoly 一个强化学习系统存在诸多挑战，Adobe
研究员采用了一种 off-policy evaluation 的方式来让自己对将要 部署的
policy 有更大的信心。 在线上系统上部署强化学习算法还有个问题就是 reward
会特别的稀疏因为大部分的时候用 户是不会点击广告的。
