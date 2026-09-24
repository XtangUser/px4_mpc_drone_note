---
type: knowledge-index
status: not-started
updated: 2026-09-24
---

# 强化学习

## 必会主题

- [ ] MDP、回报、价值函数和贝尔曼方程。
- [ ] 策略梯度与 actor-critic。
- [ ] GAE 和 advantage normalization。
- [ ] PPO 的 clip 目标和实现细节。
- [ ] 奖励设计与稀疏奖励。
- [ ] 并行环境与 on-policy 采样。
- [ ] domain randomization。
- [ ] sim2sim 与 sim2real。

## 训练记录

| 任务 | 算法 | 环境 | 最终回报 | 视频/曲线 |
|---|---|---|---|---|
| Pendulum | PPO | Gymnasium |  |  |
| 悬停 | PPO | gym-pybullet-drones |  |  |
| 轨迹跟踪 | PPO | RotorPy / MuJoCo |  |  |

## 关键问题

- 为什么 PPO 比原始策略梯度稳定？
- advantage 为什么要归一化？
- reward shaping 如何改变最终策略？
- RL 与 MPC 在约束、鲁棒性和计算成本上如何比较？

