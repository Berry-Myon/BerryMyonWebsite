---
forced_scheme: default
forced_primary: white
forced_accent: blue
hide:
  - toc
---

# 招商银行

<div class="course-layout" markdown="1">
<article class="course-main course-article" markdown="1">

## 基本记录

| 项目 | 记录 |
| --- | --- |
| 单位 | 招商银行 |
| 岗位 | Agent 数据科学家 |
| 时间 | 2026.6 |
| 项目 | “AI + 行研”对公客户营销智能工作台 |

## 项目目标

项目围绕对公客户营销展开，把行业景气度、授信审批概率、客户画像和营销建议放进同一套工作流。答辩稿中把产品流程写成：

```text
行业认知 → 商机挖掘 → 策略制定 → 项目落地
```

我负责行业数据查询 Agent、Multi-Agent 景气度报告系统的一部分，以及客户营销工作台前端原型。项目最后形成了“看行业、选客户、查授信、出方案、做复核”的演示流程，团队获得“落地潜力奖”和“优秀英才团队奖”。

## 任务一：授信获批通过率预测

任务一使用企业客户特征数据预测授信审批通过概率，并输出人工复核工作台需要的灰区分析结果。

最终模型采用两版概率平均：

\[
p_{\mathrm{final}}
=0.5\,p_{\mathrm{V1}}(\mathrm{LightGBM}+\mathrm{CatBoost})
+0.5\,p_{\mathrm{V2}}(\mathrm{LightGBM}+\mathrm{CatBoost}+\mathrm{XGBoost}).
\]

模型指标如下：

| 指标 | 结果 |
| --- | ---: |
| OOF AUC | 0.82734 |
| KS | 0.50344 |
| Macro F1 | 0.69433 |
| 高缺失样本 AUC | 0.72605 |
| 最佳阈值 | 0.733 |
| 测试集预测通过率 | 68.60% |

特征工程包括：

- 对抗验证：训练集与测试集分布偏移诊断，对抗 AUC 为 0.89。
- OOF Target Encoding：行业大类、小类做贝叶斯平滑编码，5 折对齐避免标签泄露。
- 缺失模式特征：缺失率、缺失严重度、缺失模式 hash 频率。
- 财务衍生特征：债务杠杆、资产运营效率。
- 矛盾交互特征：银企关系好但财务弱、经营活跃但资料缺失、大资产低活跃度等。

灰度分用于衡量“模型犹豫”。实现中把概率接近阈值、同模型跨折标准差、不同模型间分歧合在一起：

```python
def compute_gray_score(prob, fold_std, model_range, threshold=0.5, margin=0.20):
    prob_unc = 1.0 - np.clip(np.abs(prob - threshold) / margin, 0, 1)
    fold_norm = np.clip(fold_std / 0.10, 0, 1)
    range_norm = np.clip(model_range / 0.20, 0, 1)
    return 0.4 * prob_unc + 0.3 * fold_norm + 0.3 * range_norm
```

测试集决策区域分布如下：

| 决策区域 | 数量 | 占比 | 处理方式 |
| --- | ---: | ---: | --- |
| 自动通过 | 6,126 | 48.1% | 概率高且稳定，建议进入营销名单 |
| 自动拒绝 | 2,915 | 22.9% | 概率低且稳定，暂缓推进 |
| 人工复核 | 2,230 | 17.5% | 模型不确定，需要补充判断 |
| 风险提示 | 1,454 | 11.4% | 模型倾向通过，但有风险信号 |

## 任务二：行业数据查询 Agent

子任务一的入口统一为：

```python
def run(inf: str) -> str:
    if not inf or not isinstance(inf, str) or not inf.strip():
        return "问题为空，请输入要查询的行业数据问题。"
    try:
        return _get_agent().answer(inf.strip())
    except Exception as exc:
        return f"查询执行异常：{type(exc).__name__}: {exc}"
```

系统把自然语言问题解析成行业、指标、月份、年份、季度、TopN、阈值、增长率等结构化信息。数值计算在本地规则查询模块完成，覆盖单点值、区间求和、均值、排名、季度极值、CAGR、高景气连续增长信号、条件筛选和行业对比。

数据读取使用标准库 `zipfile + xml` 解析 Excel，LLM 不可用时使用正则规则兜底。这个结构保证评测入口稳定，也避免大模型直接读取原始全量数据。

## 任务二：Multi-Agent 景气度报告

Multi-Agent 系统由主控 Agent 串联：

```text
IntentAgent
→ IndustryProfileAgent
→ InternalDataAgent
→ ExternalDataAgent
→ PolicyAgent
→ ScoringAgent
→ StabilityAgent
→ ReportAgent
→ AuditAgent
→ QAAgent
```

主控逻辑按结构化对象逐步传递：

```python
task = self.intent_agent.parse(industry, industry_code, report_date)
profile = self.profile_agent.build(task)
internal_data = self.internal_agent.collect(task, profile)
external_data = self.external_agent.collect(task, profile)
policy_result = self.policy_agent.analyze(task, profile, external_data["policy_docs"])
score_result = self.scoring_agent.score(task, profile, internal_data, external_data, policy_result)
report = self.report_agent.generate(task, profile, internal_data, external_data, policy_result, score_result, stability_result)
```

评分采用六个维度：

- 政策导向
- 供需格局
- 成本盈利
- 增长空间
- 竞争态势
- 创新与资本

核心分数由规则引擎计算。以供需格局为例，行内定量分、行业基础分和市场动量共同参与：

```python
supply_demand = blend("供需格局")
supply_demand = _clamp(supply_demand * 0.75 + momentum * 0.25)
```

半导体行业示例中，答辩稿记录综合得分为 64.23，评级为“中性偏景气”。报告输出为结构化 JSON，包含景气度分数、行业生命周期、维度明细、驱动因素、风险因素、银行对公操作建议、证据 ID 和审计字段。

## 任务三：客户营销智能工作台

前端原型使用 HTML、CSS、JavaScript 和 ECharts。工作台包括首页总览、行业洞察、客户管理、风险监控、营销管理和人工审批。

首页用于展示全量客户状态、客户行业矩阵和营销优先级列表；行业洞察展示景气度评分、风险提示和趋势；授信评估展示通过概率、SHAP 特征解释和反事实推演；人工复核工作台展示高矛盾客户列表、正反特征对比和复核决策区。

共享交互逻辑围绕统一数据对象展开：

```javascript
const APP = {
  getZoneClass(zone) {
    const map = {
      '自动通过': 'tag--pass',
      '人工复核': 'tag--review',
      '风险提示': 'tag--risk',
      '自动拒绝': 'tag--reject'
    };
    return map[zone] || '';
  },
  getCustomer(id) {
    return DASHBOARD_DATA.customers.find(c => c.cust_uid == id);
  },
  getIndustry(code) {
    return DASHBOARD_DATA.industries[code];
  }
};
```

产品设计中把客户划分为四个决策区域，并用颜色语义辅助判断：

| 区域 | 颜色 | 含义 |
| --- | --- | --- |
| 自动通过 | 绿色 | 高概率优质客户 |
| 人工复核 | 黄色 | 灰度地带客户 |
| 风险提示 | 棕色 | 存在潜在风险 |
| 自动拒绝 | 红色 | 高风险客户 |

答辩稿里把效率目标写成“全程耗时 < 5 分钟，一键导出拜访材料”。我的前端工作重点是把模型结果和行业报告变成一条可点击的客户经理工作流。

</article>

<aside class="course-index" aria-label="香霖堂索引">
  <p class="course-index__title">文章列表</p>
  <a class="course-index__home" href="index.html">总览</a>
  <div class="course-index__group"><p>实习</p><a href="hemu-street.html">和睦街道办事处</a><a class="is-active" href="cmb.html">招商银行</a><a href="huawei-camera.html">华为</a></div>
  <div class="course-index__group"><p>校园经历</p><a href="party-branch.html">党支委</a></div>
</aside>
</div>
