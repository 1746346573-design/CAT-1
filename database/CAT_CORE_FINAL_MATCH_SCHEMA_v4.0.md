# CAT CORE FINAL — Match Record Schema v4.0

用途：统一保存赛前输入、预测封存、赛后结果与误差审计。  
原则：一场比赛一个稳定match_id；预测版本追加保存，不覆盖；缺失字段留空并标注原因，不推测补齐。

## A. 主记录字段

| 字段 | 类型/格式 | 说明 |
|---|---|---|
| match_id | string | 唯一ID，建议 competition-date-home-away |
| match_no | string/null | 彩票或赛程编号；无则空 |
| competition | string | 联赛/杯赛名称 |
| season | string/null | 赛季 |
| stage | string/null | 联赛轮次、资格赛、淘汰赛阶段 |
| leg | string/null | 单场/首回合/次回合 |
| kickoff_local | ISO-8601 | 当地开球时间，含时区 |
| kickoff_timezone | string | IANA时区 |
| home_team / away_team | string | 主客队标准名称 |
| venue | string/null | 球场 |
| handicap_line | number/null | 官方让球数；记录正负号约定 |
| source_snapshot_at | ISO-8601 | 本次数据快照时间 |
| data_cutoff_at | ISO-8601 | 预测允许使用的信息截止时间 |
| source_log | array | 来源名称、URL/引用、抓取时间、字段、可信度、备注 |
| data_completeness | enum | complete / partial / poor |
| unresolved_conflicts | array | 来源冲突及未解决事项 |

## B. 比赛环境与球队信息

| 字段 | 类型/格式 | 说明 |
|---|---|---|
| match_context | object | 赛制、积分/晋级形势、首回合比分、战意依据 |
| schedule_load | object | 近赛程、休息天数、旅行/连续客场 |
| weather_pitch_referee | object | 天气、场地、裁判；记录来源和未知项 |
| home_strength_assessment | object | 实力、主场表现、战术匹配、证据 |
| away_strength_assessment | object | 实力、客场表现、战术匹配、证据 |
| injuries_suspensions | array | 球员、位置、状态、重要性、替代者、信息来源 |
| expected_lineups | object/null | 预计阵容、可信度、来源 |
| confirmed_lineups | object/null | 正式首发及公布时间 |
| rotation_assessment | object | 轮换概率、体系影响、替补质量 |
| recent_form | object | 近期表现与对手强度背景，避免只看胜负序列 |

## C. 进球结构输入与输出

| 字段 | 类型/格式 | 说明 |
|---|---|---|
| home_xg / away_xg | number/null | xG，注明提供方 |
| home_open_play_xg / away_open_play_xg | number/null | 运动战xG |
| home_set_piece_xg / away_set_piece_xg | number/null | 定位球xG |
| home_non_penalty_xg / away_non_penalty_xg | number/null | 非点球xG |
| goal_data_provider | string/null | xG数据提供方/口径 |
| goal_data_window | string/null | 统计时间窗 |
| home_expected_goals | object | 主队预期进球区间、核心落点、依据 |
| away_expected_goals | object | 客队预期进球区间、核心落点、依据 |
| goal_environment | object | 节奏、机会质量、转换、定位球、防守风险 |
| goal_scenarios | array | A/B/C(/D)情景及触发条件 |
| score_cluster | array | 核心比分簇、概率/置信度、依据 |
| tail_score_risks | array | 4+进球、崩盘等尾部风险与触发器 |

## D. 预测输出（每次预测为独立版本）

| 字段 | 类型/格式 | 说明 |
|---|---|---|
| prediction_id | string | 唯一预测版本ID |
| model_name | string | CAT CORE FINAL |
| model_version | string | 例如 BASE 4.0 |
| prediction_created_at | ISO-8601 | 预测生成时间 |
| prediction_sealed_at | ISO-8601/null | 封存时间 |
| prediction_status | enum | draft / sealed / superseded（旧版仅标记，不删除） |
| result_1x2 | object | 主胜/平/客胜概率估计、首选方向、置信等级 |
| handicap_result | object | 官方让球数、让球胜平负判断；让球未知则明确null |
| total_goals | object | 单一主判断0/1/2/3/4/5/6/7+、辅助区间 |
| score_prediction | object | 主客进球结构、比分矩阵、核心比分簇 |
| half_full_time | object | 半全场独立判断及依据 |
| match_script | object | 预期节奏、控球/压迫/转换、领先后策略 |
| market_snapshot | object/null | 欧赔、亚盘、资金、变化及时间戳；不得作为基本面替代 |
| market_anomaly_checks | array | 欧亚资金背离、热门聚合、BPI等校验 |
| module_diagnostics | object | MR、CBC、TTE、RSE、DBI、DSR等触发与证据 |
| risk_tags | array | 数据不足、阵容不确定、尾部风险等 |
| confidence | enum | high / medium / low；需有解释 |
| pass_flag / pass_reason | boolean/string | 是否PASS及原因 |
| llm_final_ruling | string | 最终综合裁决与证据链 |

## E. 赛后实际结果与审计（追加，不覆盖预测）

| 字段 | 类型/格式 | 说明 |
|---|---|---|
| actual_recorded_at | ISO-8601 | 实际结果录入时间 |
| actual_source | string | 赛果来源 |
| actual_half_time_score | string/null | 半场比分 |
| actual_full_time_score | string | 常规时间/规定口径比分 |
| actual_extra_time_score | string/null | 加时比分（适用时） |
| actual_penalty_score | string/null | 点球大战比分（适用时） |
| actual_red_cards | array | 红牌及时间 |
| actual_key_events | array | 点球、伤退、乌龙、早球等关键事件 |
| audit_1x2_correct | boolean/null | 胜平负方向是否正确 |
| audit_handicap_correct | boolean/null | 让球判断是否正确 |
| audit_total_goals_error | number/null | 预测总进球与实际差值 |
| audit_home_goals_error | number/null | 主队进球结构误差 |
| audit_away_goals_error | number/null | 客队进球结构误差 |
| audit_score_cluster_hit | boolean/null | 实际比分是否落入预先记录的比分簇 |
| audit_htft_correct | boolean/null | 半全场判断是否正确 |
| error_classification | array | data_error / strength_error / lineup_error / script_error / direction_error / goal_structure_error / tail_event / market_overfit / recording_error |
| post_match_review | string | 证据驱动复盘；区分方向错与比分错 |
| followup_experiment_id | string/null | 对应实验登记编号 |

## F. 数据质量与审计规则

1. 所有时间必须包含时区或明确时区字段。
2. 所有赔率、盘口、阵容和伤停必须记录来源及时间戳。
3. xG必须记录提供方和统计口径；不同提供方数据不可直接混用。
4. 预测封存后不可覆盖；更正通过新版本并说明原因。
5. 没有赛前证据的历史样本，只能标记为“历史赛果/复盘样本”，不得伪造赛前预测字段。
6. 缺失值使用null/空值，并在data_completeness或备注中说明，不使用0代替未知。
7. 赛后审计必须同时保留成功、失败、方向正确但比分错误的样本。
