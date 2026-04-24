# Index

## 《主动投资组合管理（第2版）》

- 001_前言与方法论框架.md：概述本书要解决的主动管理“工程化”问题，并用 IR/IC/Breadth/因子暴露搭框架；给出港股15m项目在 IC 追踪、流动性硬约束、有效Breadth 去聚簇三项落地改进。
- 002_主动管理架构与CAPM共识收益.md：以 CAPM 的“一致预期收益”作为基准起点，讲清收益拆分（β+残差）与 alpha 的落点；给出残差触发过滤与分位数Score阈值两项可执行改造。
- 003_主动管理基本定律：IR=IC*sqrt(Breadth).md：围绕风险模型/主动风险/信息率为主动管理基本定律铺垫，强调相关性折损后的有效Breadth；落地到15m策略的effective_trig/cluster_ratio与IC_decay+停用规则。
- 004_附加值ValueAdded与主动风险预算.md：用 VA=α-λ·ω² 把“收益-风险”统一成目标函数，强调成本与边际风险贡献会改变最优仓位；落地到15m策略的VA_surr（含成本惩罚）与 per-ticker 风险预算字段。
- 005_信息率IR与主动管理基本定律（续）.md：系统说明 IR 的定义与与 VA/风险厌恶的关系（ω*=IR/2λ、VA*=IR²/4λ），并把IC/Breadth/半衰期与样本外验证落到可执行字段与约束（refined_alpha、VA_surr、half_life+cost gate）。
- 006_主动管理基本定律：信息处理链条与实现.md：梳理原始信息→score标准化→refined_alpha→组合→执行的全链路，并补上IR可加性、APT因子定价、估值核等桥梁；落地到15m策略的refined_alpha标准化+IC上限与impact penalty（容量惩罚）+分层risk_budget。
- 007_APT与估值视角下的因子收益与alpha.md：用 APT/估值视角区分因子溢价与真正 alpha，强调暴露漂移与样本外失效；落地到15m策略的残差口径 stable/strong（beta中性化）与 sector 暴露监控/中性化+cluster gate。
- 008_预测基础：朴素预测vs精炼预测与信号校准.md：用Raw(t)=±1的涨跌预测例子说明“无条件均值+σ·IC·Score”的精炼预测框架，并给出(10-30)/(10-31)关于风险预测受IC影响极小的结论；落地到15m策略的score_z+IC_prior+vol scaling流程与随机输入sanity check/禁止随观点改相关系数。
- 009_预测模型选择与稳健性：从简单到复杂.md：强调由简入繁与随机输入测试（sanity check），梳理AR/MA/ARIMA、ARCH/GARCH、Kalman与信息半衰期的适用边界；落地到15m策略的random_input_check+random baseline准入与half-life驱动的动态阈值/cooldown。
- 010_信息分析与事件研究：信息价值评估.md：围绕IC/half-life/信息衰减回答“信息是否有用、能持续多久、扣成本后是否还值钱”，并给出事件研究最小流程；落地到15m策略的IC_decay/half_life审计+动态阈值与random baseline+permutation test反p-hacking准入。

## 《Linear factor models in finance (2005)》

- F01_线性因子模型导论与基本框架.md：引入线性因子模型在定价与风险模型中的两类用途，给出SDF/APT/收益分解与协方差分解框架；落地到15m策略的残差收益口径标签与协方差收缩（shrinkage）以抑制噪声导致的极端判断。
- F02_多因子定价文献与模型设定要点.md：进入模型误设/检验视角，强调遗漏因子/β时变会让统计检验失真；落地到15m策略的残差口径稳定性评估与暴露漂移报警/门槛化停用。
- F03_线性因子模型的估计误差与稳健性（PDF 81-130 页）.md：聚焦截面/时序估计误差、EIV、收缩/正则化与暴露稳定性检验；落地到15m策略的参数收缩、rolling 稳定性监控与噪声降噪处理。
