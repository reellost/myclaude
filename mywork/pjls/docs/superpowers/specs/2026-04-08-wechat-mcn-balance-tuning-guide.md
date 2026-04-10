# 微信小游戏试玩调参说明 V1

## 1. 文档用途

本文件用于配合以下文档一起使用：

1. [数值策划表框架：可执行版](G:\mywork\pjls\docs\superpowers\specs\2026-04-08-wechat-mcn-balance-table-framework.md)
2. [首版关键数值样例 V1](G:\mywork\pjls\docs\superpowers\specs\2026-04-08-wechat-mcn-balance-sample-values-v1.md)
3. [CSV 模板目录 README](G:\mywork\pjls\docs\superpowers\specs\balance_tables\README.md)

它的目标不是重新定义数值，而是告诉策划、制作人和程序：

1. 试玩时应该看什么
2. 出现问题时先调哪张表
3. 每次建议改多大
4. 改完会连带影响什么

## 2. 调参基本原则

首版调参必须遵守以下顺序：

1. 先调“体验节奏”，再调“长期平衡”。
2. 先调“少量核心参数”，不要一次改太多表。
3. 单次调整建议控制在 `5%-15%`，避免直接改崩。
4. 每次调整后至少完整试玩 `前 10 分钟` 和 `前 3 章`，再决定下一步。

## 3. 试玩时优先观察的 10 个指标

### 3.1 开局阶段

1. 玩家第一次点击升级是否在 `15-20 秒内` 发生。
2. 玩家是否在 `60 秒内` 完成第一次完整闭环。
3. 玩家是否理解“升级自己 -> 开播 -> 收益 -> 再升级”。

### 3.2 前 10 分钟

4. 玩家是否在 `前 1 分钟` 内完成 3 次以上可感知升级。
5. 玩家是否在 `前 10 分钟` 内完成明显个人逆袭感。
6. 玩家是否主动点击主角成长和直播间成长，而不是只盯着主界面。

### 3.3 第 3 章前后

7. 玩家是否在 `20-30 分钟` 内接近或完成主播招募解锁。
8. 解锁主播后，数值是否突然爆炸。
9. 玩家是否愿意看第一次或第二次广告。

### 3.4 回流阶段

10. 玩家上线后离线收益是否有明显惊喜，但不至于直接跳过主线成长。

## 4. 推荐调参顺序

如果一轮试玩下来感觉“不对”，建议按这个顺序排查：

1. `00_global_constants.csv`
2. `03_hero_growth_levels.csv`
3. `05_room_upgrade_levels.csv`
4. `12_product_base.csv`
5. `07_company_levels.csv`
6. `10_streamer_growth_levels.csv`
7. `16_ad_reward_config.csv`
8. `17_offline_income_config.csv`
9. `18_share_reward_config.csv`

原因：

1. 前 4 张表决定前期手感。
2. 中间 2 张表决定中期扩张节奏。
3. 后 3 张表决定商业化和回流是否失衡。

## 5. 常见问题与调参方法

### 5.1 开局太慢，玩家 30 秒内还没有明显升级

优先调这些表：

1. `00_global_constants.csv`
2. `03_hero_growth_levels.csv`
3. `12_product_base.csv`

优先改这些字段：

1. `hero_base_traffic_value`
2. `03_hero_growth_levels.cost_gold`
3. `12_product_base.convert_rate`
4. `12_product_base.profit_coeff`

建议调整方式：

1. `hero_base_traffic_value` 上调 `10%-20%`
2. 前 3 级主角成长花费下调 `10%-15%`
3. 首个商品 `convert_rate` 上调 `0.02-0.04`

连带影响：

1. 会让前 1 分钟更爽。
2. 也会让第一支广告价值相对下降一些。
3. 如果改太多，第 2 章会推得过快。

### 5.2 开局太快，1-2 分钟内就把前期内容点空了

优先调这些表：

1. `03_hero_growth_levels.csv`
2. `05_room_upgrade_levels.csv`
3. `01_chapter_config.csv`

优先改这些字段：

1. `cost_gold`
2. `target_total_traffic`
3. `target_sales_gold`

建议调整方式：

1. 前 5 级主角和房间成长花费上调 `8%-12%`
2. 第 1、2 章目标值上调 `10%-15%`
3. 不要优先砍主角基础流量，否则开局会显得没反馈

连带影响：

1. 主线推进会变慢。
2. 广告双倍奖励会显得更有价值。
3. 需要同步确认新手阶段是否仍然在 60 秒内完成第一次闭环。

### 5.3 玩家只点主角，不点房间

说明：房间成长的性价比或展示感不够。

优先调这些表：

1. `05_room_upgrade_levels.csv`
2. `06_room_stage_visuals.csv`
3. `14_task_chapter.csv`

优先改这些字段：

1. `traffic_coeff`
2. `commerce_coeff`
3. `room_score`
4. 章节任务里的 `room_level` 目标与奖励

建议调整方式：

1. 房间前 3 级 `traffic_coeff` 上调 `5%-10%`
2. 房间大阶段视觉门槛略提前
3. 在第 1、2 章任务中增加房间成长奖励

连带影响：

1. 房间会从“可选项”变成“必点项”。
2. 过度上调会让房间压过女主成长，破坏代入感。

### 5.4 玩家只点房间，不点主角

说明：主角成长性价比低，或者房间收益太直观。

优先调这些表：

1. `03_hero_growth_levels.csv`
2. `05_room_upgrade_levels.csv`

建议调整方式：

1. 主角前 5 级 `traffic_coeff` 或 `commerce_coeff` 上调 `5%-10%`
2. 房间前 3 级 `traffic_coeff` 下调 `3%-8%`
3. 主角成长花费下调 `5%-10%`

调参目标：

1. 前 2 章里，主角成长始终应该是最有情绪价值的投入项。
2. 房间成长应该是“第二重要”，而不是绝对最优解。

### 5.5 第 3 章解锁招募太晚

说明：玩家长时间停留在“个人主播”阶段，扩张感来得太慢。

优先调这些表：

1. `01_chapter_config.csv`
2. `07_company_levels.csv`
3. `03_hero_growth_levels.csv`
4. `12_product_base.csv`

优先改这些字段：

1. `target_company_level`
2. `target_sales_gold`
3. `upgrade_cost_gold`
4. `profit_coeff`

建议调整方式：

1. 第 3 章的 `target_sales_gold` 下调 `10%-20%`
2. 公司 3 级升级花费下调 `10%-15%`
3. 第 2-3 章主推商品利润系数上调 `5%-10%`

连带影响：

1. 会更早进入 MCN 阶段。
2. 如果招募开启太早，女主逆袭阶段会显得不完整。

### 5.6 第 3 章解锁招募太早

说明：玩家还没建立对女主的投入，就被推去玩团队扩张。

优先调这些表：

1. `01_chapter_config.csv`
2. `07_company_levels.csv`
3. `11_streamer_recruit_pool.csv`

建议调整方式：

1. 第 3 章目标值上调 `10%-15%`
2. 公司 3 级花费上调 `10%`
3. 招募池在章节 3 只放 2-3 名基础主播，减少扩张冲动

### 5.7 招募主播后数值爆炸太快

说明：团队流量或带货加成过高。

优先调这些表：

1. `09_streamer_base.csv`
2. `10_streamer_growth_levels.csv`
3. `07_company_levels.csv`
4. `08_live_slot_unlocks.csv`

优先改这些字段：

1. `base_traffic`
2. `base_commerce_coeff`
3. `traffic_value`
4. `commerce_coeff`
5. `unlock_live_slot_count`

建议调整方式：

1. 主播基础流量整体下调 `8%-12%`
2. 主播成长模板中后段系数下调 `5%-10%`
3. 第二个直播位解锁时间略后移半章到一章

连带影响：

1. 会削弱招募爽感。
2. 但可以避免第 3 章后直接进入失控通胀。

### 5.8 玩家赚钱太慢，但流量涨得挺快

说明：问题不在产流，而在变现效率。

优先调这些表：

1. `12_product_base.csv`
2. `03_hero_growth_levels.csv`
3. `05_room_upgrade_levels.csv`
4. `07_company_levels.csv`

优先改这些字段：

1. `convert_rate`
2. `profit_coeff`
3. `commerce_coeff`

建议调整方式：

1. 第 1-3 章商品 `convert_rate` 上调 `0.01-0.03`
2. 技能线 `commerce_coeff` 前半段上调 `5%-10%`
3. 带货设备和公司带货系数轻微上调 `3%-8%`

### 5.9 玩家赚钱太快，升级没压力

优先调这些表：

1. `12_product_base.csv`
2. `03_hero_growth_levels.csv`
3. `05_room_upgrade_levels.csv`
4. `07_company_levels.csv`

建议调整方式：

1. 商品 `profit_coeff` 下调 `5%-10%`
2. 技能线中后段 `commerce_coeff` 下调 `5%`
3. 公司升级花费上调 `8%-12%`

注意：

1. 尽量先调收益，不要先砍流量。
2. 因为玩家更容易感知“流量涨了”，不容易接受“直播看着火但赚不到钱”。

### 5.10 商品选择没有意义，玩家无脑选最贵的

说明：商品之间的“高转化低利润”和“低转化高利润”差异不明显。

优先调这些表：

1. `12_product_base.csv`

建议调整方式：

1. 提高赛道差异
2. 入门型：高转化、低利润
3. 成长型：中转化、中利润
4. 爆款型：低转化、高利润

建议差异控制：

1. 相邻赛道 `convert_rate` 差至少 `0.02-0.04`
2. 相邻赛道 `profit_coeff` 差至少 `0.4-0.8`

### 5.11 广告太强，不看广告就像吃亏

优先调这些表：

1. `16_ad_reward_config.csv`
2. `17_offline_income_config.csv`
3. `00_global_constants.csv`

优先改这些字段：

1. `reward_coeff`
2. `reward_duration_sec`
3. `daily_limit`
4. `double_by_ad_coeff`

建议调整方式：

1. 广告倍率每次只下调 `0.1-0.2`
2. 持续时长每次只下调 `15-30 秒`
3. 不要同时下调倍率和时长，否则体感会突然变差

### 5.12 广告太弱，玩家不愿意点

优先调这些表：

1. `16_ad_reward_config.csv`
2. `17_offline_income_config.csv`

建议调整方式：

1. `reward_coeff` 上调 `0.1-0.3`
2. `reward_duration_sec` 上调 `30-60 秒`
3. 免费招募广告每日次数从 `1` 提到 `2`

调参目标：

1. 第一次广告体验必须让玩家明显觉得“值”。
2. 但不能让广告收益超出自然成长太多。

### 5.13 离线收益太少，玩家回流没惊喜

优先调这些表：

1. `17_offline_income_config.csv`
2. `00_global_constants.csv`

建议调整方式：

1. `offline_coeff` 从 `0.55` 上调到 `0.60-0.70`
2. `max_offline_sec` 从 `6 小时` 上调到 `8 小时`

### 5.14 离线收益太多，玩家上线只领钱不玩

建议调整方式：

1. `offline_coeff` 下调 `0.05-0.10`
2. `max_offline_sec` 缩短到 `4-6 小时`
3. 增强“上线后继续操作才能拿满收益”的体验目标

### 5.15 分享奖励太强，破坏平衡

优先调这些表：

1. `18_share_reward_config.csv`

建议调整方式：

1. 降低 `reward_value`
2. 增加 `daily_limit`
   这里的含义不是提高奖励次数，而是重新控制可触发上限，避免无限刷
3. 不让分享奖励超过一次高质量广告奖励

## 6. 推荐单次改动幅度

为了避免一次调崩，建议按以下幅度改：

1. 核心基础值：`5%-10%`
2. 升级花费：`8%-15%`
3. 商品转化率：`0.01-0.03`
4. 商品利润系数：`5%-10%`
5. 广告倍率：`0.1-0.3`
6. 广告时长：`15-60 秒`
7. 章节目标：`10%-20%`

## 7. 建议每轮调参只做一种方向

推荐每轮只选一个核心问题解决：

1. 开局节奏问题
2. 前中期赚钱问题
3. 招募解锁问题
4. 广告平衡问题
5. 离线回流问题

不要一轮同时解决 3 个问题，否则你很难知道到底是哪次改动生效。

## 8. 三轮试玩建议

### 第一轮试玩

目标：只看前 10 分钟是不是顺。

重点看：

1. 开局升级速度
2. 第一次收益反馈
3. 女主成长和房间成长是否都有人点

### 第二轮试玩

目标：看第 3 章招募解锁是否合理。

重点看：

1. 是否过早或过晚进入 MCN 阶段
2. 招募后是否突然爆炸
3. 公司升级是否有明确意义

### 第三轮试玩

目标：看广告和离线收益是否平衡。

重点看：

1. 玩家是否愿意看广告
2. 不看广告是否还能正常推进
3. 回流后离线收益是否足够有吸引力

## 9. 最值得先盯住的 7 个字段

如果试玩时间有限，最先盯这 7 个字段：

1. `00_global_constants.hero_base_traffic_value`
2. `03_hero_growth_levels.cost_gold`
3. `05_room_upgrade_levels.cost_gold`
4. `12_product_base.convert_rate`
5. `12_product_base.profit_coeff`
6. `07_company_levels.upgrade_cost_gold`
7. `16_ad_reward_config.reward_coeff`

原因：

1. 它们几乎决定了前 30 分钟的全部手感。
2. 调这几个字段，通常就能覆盖绝大多数首版体验问题。

## 10. 一句话结论

首版调参不要追求“绝对平衡”，而要优先保证：

1. 开局够爽
2. 前期够顺
3. 第 3 章转折够自然
4. 广告有价值但不压玩法