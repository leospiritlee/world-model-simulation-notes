## 序：从一个动作，到一次可推演的未来

一次视频生成给出一段好看的画面，一次控制策略给出一个动作，世界模型给出的是「如果我这样做，接下来会发生什么」。它把环境压进一个能在内部推演的近似模型，让智能体在真正动手之前先在想象里走几步。这不是把视频生成换个名字，而是把「看世界」变成「在模型里试世界」。

这本专题精读写的是这条链路的十二道关：先判断该不该建世界模型，再走状态表示、潜在动态、想象规划、Dreamer 路线、JEPA 路线、生成式交互环境、仿真到现实、具身数据与平台、失败模式、评测，最后落到上线工程与治理。每关回答同一个问题——多推演一步，换来了什么可迁移的判断，又带来什么新代价。

**分工**：与《AI 音视频生成精读》《多模态理解开发精读》不同，那两本讲画面怎么生成、一张图怎么进模型；这本只讲一个动作怎么在模型里走成可推演的未来，以及这个未来怎么回到真实世界。与《端侧AI推理精读》《推理模型精读》也不同：那两本讲压缩部署与回答时的算力，这本讲行动前的环境推演。与《多智能体系统精读》分工：那本讲多个执行者怎么相处，这本讲一个执行者心里的世界怎么建立。

## 推演链路全景图

+----------------------------------------------------------------------+
| WORLD MODELS: FROM AN ACTION TO A SIMULATED FUTURE                    |
|                                                                      |
| [1] boundary -> [2] state -> [3] dynamics -> [4] imagination         |
| [5] Dreamer -> [6] JEPA -> [7] interactive env -> [8] sim2real        |
| [9] data -> [10] failure -> [11] eval -> [12] serving                |
|                                                                      |
| one action -> latent rollout -> a future you can act on               |
+----------------------------------------------------------------------+

这张图不是模块调用顺序，而是「一个动作变成一次可依赖的推演时，新出现的代价依次落在哪里」：先问该不该建，再问状态怎么表示、动态怎么学、想象怎么用、误差怎么累积、怎么跨回现实、怎么验、怎么上线。越往后，代价越隐性，也越需要在部署前想清楚。

## 十二关速查表

| 关 | 主题 | 一句话 |
|---|---|---|
| 1 | 边界 | `world model` 只在需要行动前评估后果时才划算 |
| 2 | 状态 | `latent state` 比像素更省算力，但要用下游任务验真 |
| 3 | 动态 | `latent dynamics` 的 `compounding error` 决定想象时域 |
| 4 | 想象 | `model predictive control` 与在模型里训练是两条路 |
| 5 | Dreamer | `RSSM` 把确定性与随机性拼起来再预测 |
| 6 | JEPA | `joint embedding predictive architecture` 在表示空间预测 |
| 7 | 交互环境 | `generative interactive environments` 要有 `action-conditioned prediction` |
| 8 | 仿真现实 | `domain randomization` 是起点，`sim2real gap` 每次部署重开 |
| 9 | 数据平台 | `data flywheel` 之前先做 `failure attribution` |
| 10 | 失败 | `model exploitation` 与 `representation collapse` 各有各的防法 |
| 11 | 评测 | `perception-functionality gap`：画面好不等于任务强 |
| 12 | 上线 | `action chunking` 与 `e-stop` 一个管延迟一个管安全 |

## 三层读法：机制、工程、判断

看到一个新方法，先问它属于哪一层。`World Models`、`PlaNet`、`Dreamer`、`DreamerV3`、`MuZero`、`JEPA` 是机制层，论文讨论世界如何被表示、预测与规划；`VideoGPT`、`Genie`、`Cosmos`、`Isaac Sim`、`MuJoCo`、`Gymnasium` 是工程与平台层，给出可运行的系统、环境与接口；`sample efficiency`、`sim2real gap`、`perception-functionality gap`、`physics adherence` 是判断层，告诉你什么时候这些方法根本不该上。

三层不能互相替代。机制漂亮不等于工程上跑得稳，工程能跑也不等于这笔账划算。综合报告口径建议按「先反应式基线、再模型内规划、最后才生成式交互环境」的顺序推进，每一步都用自己的任务做对照，而不是照搬别人的榜单分数。

## 一个可验收的出口

读完十二关，出口标准不是记住某个框架名，而是能对着一个具体任务回答四句话：这条系统有没有真正的 `action-conditioned prediction`；它的 `latent dynamics` 在长展开下怎么控 `compounding error`；它的想象结果用什么下游指标验真而不是只看画面；它在 `sim-to-real` 之后靠什么校准 `sim2real gap`。四句都答得出，说明这条链路已经能用于判断，而不只是读过一篇综述。

对做系统的人还要多一句：`latency` 的优化目标不是平均值，而是高百分位与 `jitter`；平均很快但偶发停顿的系统无法用于高节拍任务。这一句答不出来，前面四句答得再好也算不对工程账。

## 语料边界与口径

一手论文逐字留存，用于机制与术语的精确写法：World Models、Recurrent World Models、PlaNet、Dreamer、DreamerV2、DreamerV3、MuZero、Decision Transformer、Trajectory Transformer、Diffuser、VideoGPT、Genie、I-JEPA、V-JEPA、域随机化、两篇 Sora 世界模型综述、具身智能与世界模型综述，以及 AgiBot World、Gemini Robotics、Unified Video Action Model、Genie Envisioner、Video Prediction Policy 2 与语言条件世界模型。官方文档给出平台形态：NVIDIA Cosmos、Isaac Sim、MuJoCo、Gymnasium 与世界模型专题站点。

综合报告只补工程判断，比例、成本与趋势一律标注「综合报告口径」。十二关的顺序、场景与速查表属于「延伸，非语料原文」，不加行内代码标记；行内代码只用语料中确实出现的标识符。安全与合规内容只作工程提示，不构成法律意见。