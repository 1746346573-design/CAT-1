# CAT CORE FINAL — Historical Migration Register

目的：登记CAT-1现存历史资料向BASE 4.0结构迁移的状态。此文件只记录来源、版本和迁移状态，不重写或删除原始历史档案。

## 1. 迁移原则

- 原始文件只读保留；新规范通过新增文件建立。
- 只有原始记录中明确存在的字段才可迁入；缺失字段保持null。
- 只有明确封存的赛前预测才可用于严格赛前回测。
- 只有赛果或赛后复盘的资料，标记为历史结果/复盘样本，不补造赛前方向、概率、比分簇或数据截止时间。
- 旧版固定权重、自动调权等内容保留为历史版本，不视为BASE 4.0现行规则。
- 每条迁移记录保留源路径、源版本/日期、目标记录ID、迁移时间、转换说明和校验状态。

## 2. 已识别源文件（初始登记）

| 源路径 | 内容类型 | 初始处理 | 状态 |
|---|---|---|---|
| README.md | 仓库说明与旧分析索引 | 保留旧索引，追加BASE 4.0入口 | 待README更新 |
| data/CAT_CORE_FINAL_V2_1_review_20260810.md | 旧版复盘样本 | 作为历史复盘，不推定赛前预测完整性 | 已识别，待逐项结构化 |
| database/Sample_Library_Core.md | 成功/失败/方向对比分错样本库 | 保留原文，逐条核验来源及预测证据 | 已识别，待逐项结构化 |
| workflow/CAT_CORE_FINAL_3.0_Sweep_Integration.md | 旧版扫盘规则 | 作为历史规则参考，与BASE 4.0差异标注 | 已识别，待差异映射 |
| workflow/CAT_CORE_FINAL_2.7_Forced_Rules.md | 旧版强制规则 | 历史版本归档，不覆盖正式基线 | 已识别，待差异映射 |
| workflow/CAT_CORE_FINAL_2.9_Goal_Structure.md | 旧版进球结构规则 | 作为Goal Structure演进来源 | 已识别，待差异映射 |
| workflow/Failure_Database.md | 历史失败数据库 | 失败样本保留，补充结构化审计字段时不得伪造缺失预测 | 已识别，待逐项结构化 |
| workflow/daily-analysis.md | CAT-X旧工作流 | 保留旧流程，标记旧规则不等同BASE 4.0 | 已识别，待差异映射 |

## 3. 迁移状态定义

- identified：已发现源文件；
- parsed：已读取并理解结构；
- mapped：字段映射已确定；
- migrated：目标记录已建立；
- verified：目标记录与源文件逐项核对；
- blocked：存在来源、格式或权限问题。

本登记为初始清单，不代表所有历史比赛已完成迁移。后续每次迁移须追加记录，不得将“已识别”写成“已迁移”。
