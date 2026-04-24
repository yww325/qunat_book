# 002_主动管理架构与CAPM共识收益

 来源：《主动投资组合管理》（第二版）PDF 第 21–60 页（本段主要覆盖第1章绪论 + 第2章CAPM一致预期收益率与技术附录）。

## 1) 本段覆盖的章节/主题（按可识别内容）

- 第 1 章：绪论
  - 投资管理从“艺术”走向“科学”的动因
  - 主动管理（Active Management）的结构化语言：把收益拆成 β（市场）与 α（残差）
- 第 2 章：一致预期收益率（Consensus Expected Returns）：资本资产定价模型（CAPM）
  - CAPM 作为市场对收益的“共同起点”（共识基准）
  - 用 CAPM 先解释系统性收益，再讨论真正的主动收益（alpha）
- 第 2 章技术附录
  - 特征组合（Characteristic Portfolio）与有效前沿的推导线索

## 2) 核心公式/定律（含符号解释）

### 2.1 收益分解：β + 残差（alpha）的基本框架

```text
r_p = beta_p * r_m + theta_p
```

- `r_p`：组合 P 的超额收益（相对无风险利率）
- `beta_p`：组合 P 的 Beta（对市场因子的暴露）
- `r_m`：市场组合的超额收益
- `theta_p`：残差收益（Residual Return），即不与市场同步的部分

直觉：
- `beta_p * r_m` 是“跟着市场走”的那部分（系统性）；
- `theta_p` 才是你真正的主动管理空间（更接近 alpha 的来源）。

### 2.2 CAPM 的一致预期（共识收益）

```text
E{r_p} = beta_p * E{r_m}
```

- `E{r_p}`：一致预期收益率（Consensus Expected Return）
- CAPM 的意义：当你说“我看好/看空”某只股票时，本书建议先问：
  - 你偏离的是 CAPM 的共识部分（β），还是你有信息优势的残差部分（α）？

### 2.3 主动管理的“预测基本方程”（本段作为引子出现）

```text
Alpha = Volatility * IC * Score
```

- `Volatility`：残差波动率（越“动”的标的/残差空间越大，理论 alpha 空间越大，但风险也更大）
- `IC`：信息系数（Information Coefficient），衡量预测与实现之间的相关性（预测质量）
- `Score`：预测标准分（把不同来源的信号标准化后，描述“强度/方向”）

这条公式的核心含义：
- alpha 不来自单次“神预测”，而来自：可量化的预测质量（IC）× 可执行的风险载体（残差波动）× 可规模化的信号表达（Score）。

## 3) 关键概念（首次出现：中文 + (English)）

- 信息比率（Information Ratio, IR）
  - 直觉：单位主动风险能换来多少主动收益，是主动管理的“效率”。
- 跟踪误差（Tracking Error, TE）
  - 主动收益（组合相对基准）的波动率；很多场景下可视为主动风险。
- 阿尔法（Alpha）
  - 相对基准、相对市场因子的“残差收益”部分（更接近能力的体现）。
- 信息系数（Information Coefficient, IC）
  - 信号与未来收益之间的相关性，衡量预测能力。
- 广度（Breadth）
  - 一年中可视为“独立决策”的次数。广度高能把小 IC 放大成可观 IR（后续会进一步形式化）。
- 评分/标准分（Score）
  - 将原始信号标准化（如 z-score），把不同量纲信号统一在可比较尺度。

## 4) 3 个实操陷阱/误区，以及如何在回测/风控里避免

### 误区 1：用历史均值当作未来预期
问题：历史收益的统计误差很大（波动率高时尤其明显），容易把噪声当信号。

如何避免（在回测里落地）：
- 在回测报告里固定输出“样本误差意识”字段：
  - `n_trades`（样本量）
  - `boot_ci_95`（用 bootstrap 给 stable_rate/strong_rate 做置信区间；哪怕简化成 Wilson 区间也行）
- 把“显著性不足”的参数组标记为 `low_confidence`，避免被网格搜索误选。

### 误区 2：混淆总风险与残差风险
问题：策略看似赚钱，可能只是押中了市场 β（大盘上涨时都赚钱）。

如何避免：
- 在事件级别输出：
  - `mkt_ret_eod`（恒指/行业指数从触发到收盘的收益）
  - `residual_ret_eod = stock_ret_eod - beta * mkt_ret_eod`
- 以后“强/稳”不只看绝对收益，还能看残差收益是否为正。

### 误区 3：只追求“更高命中率”，忽略独立性与广度
问题：触发集中在同一段行情/同一票上，胜率很高但不可复制，实际有效宽度很低。

如何避免：
- 引入“聚簇度/有效触发数”概念：
  - `effective_trig`（例如 8 根 15m 内重复触发只算一次）
  - `cluster_ratio = effective_trig / trig`
- 网格搜索目标函数加一个惩罚项（哪怕很轻）：
  - 若 `cluster_ratio < 0.6`，objective 扣分。

## 5) 对港股 15m 提醒项目的 2 条落地改造（具体到字段/阈值/实验）

### 改造 1：加入“残差触发”过滤（β 中性化思想的简化版）
目的：过滤大盘带动造成的假信号。

具体怎么做：
- 在触发点增加字段：
  - `HSI_ret_15m`（同一根 15m 恒指收益）
  - `beta_60d`（用日频或 60d 15m 简化回归估计 beta）
  - `residual_move = stock_ret_15m - beta_60d * HSI_ret_15m`
- 触发门槛改成“双门槛”：
  - 原策略条件成立 AND `residual_move <= -X`（下跌方向的残差幅度足够大）
  - X 的初值：`2 * std(residual_move, lookback=20d)`（两倍标准差）

实验设计：
- A 组：原触发
- B 组：原触发 + residual 过滤
对比：`test_trig`、`stable_rate`、`strong_rate`、以及 `cluster_ratio`。

### 改造 2：把“Score”标准化成分位数阈值，而不是绝对阈值
目的：不同流动性/波动率的港股不应共用同一绝对阈值。

具体怎么做：
- 新增字段：
  - `volume_score = zscore(volume, within_same_timebucket, lookback=5d)`（同一时间段分布）
  - `dd_score = zscore(dd_pct, lookback=20d)`
  - `total_score = w1*volume_score + w2*dd_score + ...`
- 用分位数阈值触发：
  - `total_score >= 1.65`（约等于前 5%）作为初值

实验设计：
- A 组：当前 score/阈值
- B 组：分位数阈值
对比：触发减少量、稳定率提升量、以及不同 ticker 的一致性。

---

## 本笔记一句话
本段用 CAPM 给出“共识预期收益”的起点，并把收益拆成 β 与残差，目的是让主动管理聚焦在可验证的 alpha；对应到港股 15m 项目，优先落地的是残差过滤与标准化 Score（分位数阈值），以减少大盘噪声与跨标的不可比问题。
