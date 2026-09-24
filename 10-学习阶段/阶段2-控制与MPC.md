---
type: stage
stage: 2
week: W9-W16
status: not-started
start: 2026-11-23
end: 2027-01-17
tags:
  - control
  - mpc
  - nmpc
  - acados
---

# 阶段 2：控制与 MPC

## 阶段目标

做到 MPC 能手推、能从零写，并能用 acados 写出实时 NMPC。每个设计选择都要能解释原因。

## 核心任务

### 控制基础

- [ ] 复习状态空间、可控性、可观性和 LQR。
- [ ] 实现倒立摆或 2D 四旋翼的线性化和离散 LQR。
- [ ] 对比不同 Q、R 权重下的状态响应。
- [ ] 故意制造 20% 模型误差，观察鲁棒性。

### 线性 MPC

- [ ] 把 MPC 写成 QP，并手推 H、f 矩阵。
- [ ] 用 CVXPY/OSQP 实现双积分器 MPC。
- [ ] 换成 2D 四旋翼线性化模型。
- [ ] 加入输入和速度约束，与 LQR 比较。
- [ ] 实现 warm start，并记录求解时间。

### CasADi 与 acados

- [ ] 用 CasADi + IPOPT 实现差速小车 NMPC。
- [ ] 用 multiple shooting 组织 OCP。
- [ ] 安装 acados 并跑通 `minimal_example_ocp.py`。
- [ ] 用 acados `SQP_RTI` 重写同一问题。
- [ ] 对比 IPOPT 与 acados 的均值和最大求解时间。

### 工程进阶

- [ ] 加入 slack 软约束，处理约束冲突。
- [ ] 加入扰动估计和积分作用，实现 offset-free MPC。
- [ ] 验证质量失配 20% 时稳态误差收敛到 0。
- [ ] 写博客《从零实现 MPC：LQR -> 线性 MPC -> NMPC -> acados》。

## 验收

- [ ] 能画出手推 MPC 转 QP 的过程。
- [ ] 有“MPC 与 LQR 在有约束场景”的对比图。
- [ ] 有 IPOPT 与 SQP_RTI 的求解时间表。
- [ ] 能解释终端代价、终端约束、RTI、warm start 和软约束。
- [ ] 能在 5 分钟内回答“MPC 超时或无解怎么处理”。

## 阶段产物

| 产物 | 链接 | 状态 |
|---|---|---|
| 线性 MPC 代码 |  | 未开始 |
| NMPC / acados 代码 |  | 未开始 |
| LQR/MPC 对比图 |  | 未开始 |
| 求解时间表 |  | 未开始 |
| MPC 博客 |  | 未开始 |
| MPC 知识笔记 | [知识库](../30-知识库/Control-MPC/README.md) | 未开始 |

## 必须能回答

1. MPC 相比 PID、LQR 的优势和代价是什么？
2. 预测时域 N 和采样时间怎么选？
3. 求解失败、超时或约束无解时怎么办？
4. 终端代价如何影响稳定性？
5. single shooting、multiple shooting、RTI 有什么区别？

## 阶段复盘

- 已能独立推导：
- 仍然依赖资料的部分：
- 最适合写进简历的量化结果：

