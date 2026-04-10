# 微信小游戏首版关键数值样例 V1

## 1. 文档用途

本文件用于承接《[数值策划表框架：可执行版](G:\mywork\pjls\docs\superpowers\specs\2026-04-08-wechat-mcn-balance-table-framework.md)》，提供一版可直接开会讨论的首版关键数值样例。

这不是最终定版数值，而是用于验证：

1. 开局是否足够爽
2. 前 3 章节奏是否顺
3. 招募主播是否开得合适
4. 广告收益是否过强或过弱

## 2. 核心假设

本样例采用以下假设：

1. 主角基础流量初始值为 `8/秒`
2. 女主固定上阵，不占可替换直播位
3. 首版商品收益以自动结算为主
4. 首版 `带货加成系数` 统一拆成：
   `主角技能系数 x 房间带货系数 x 公司带货系数 x 团队带货系数`
5. 团队带货系数首版可先取“已上阵主播带货系数的平均值”，若当前没有主播，则为 `1.00`

## 3. `00_global_constants` 样例

| id | group_type | value | desc |
| --- | --- | --- | --- |
| hero_base_traffic_value | traffic | 8 | 主角初始基础流量/秒 |
| offline_income_coeff | offline | 0.55 | 离线收益折算系数 |
| offline_max_sec | offline | 21600 | 最大离线累计秒数，6 小时 |
| ad_double_coeff | ad | 2.0 | 双倍类广告倍率 |
| ad_live_accelerate_coeff | ad | 2.0 | 开播加速倍率 |
| ad_live_accelerate_duration_sec | ad | 180 | 开播加速持续 180 秒 |
| ad_sales_burst_coeff | ad | 2.2 | 爆单翻倍倍率 |
| ad_sales_burst_duration_sec | ad | 120 | 爆单持续 120 秒 |
| share_traffic_pack_value | share | 400 | 分享奖励流量包 |
| free_recruit_ad_count | ad | 1 | 单次广告免费招募次数 |

## 4. `01_chapter_config` 样例

| chapter_id | chapter_name | chapter_stage_type | target_hero_level | target_room_stage | target_company_level | target_streamer_count | target_total_traffic | target_sales_gold | reward_gold | reward_unlock_type | reward_unlock_id |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 被辞退后的出租屋重启 | personal | 3 | 1 | 1 | 0 | 800 | 350 | 150 | room_category | decor |
| 2 | 腼腆新人的第一间像样直播房 | personal | 8 | 2 | 2 | 0 | 8000 | 2500 | 400 | room_category | atmosphere |
| 3 | 小有名气的个人工作室 | transition | 12 | 3 | 3 | 1 | 50000 | 18000 | 900 | feature | streamer_recruit |
| 4 | 初具规模的主播团队 | mcn_start | 18 | 4 | 4 | 2 | 180000 | 65000 | 1800 | live_slot | slot_2 |
| 5 | 本地热门 MCN 机构 | mcn_start | 24 | 4 | 5 | 3 | 450000 | 180000 | 3600 | live_slot | slot_3 |

说明：

1. `target_hero_level` 建议理解为主角三条成长线累计总等级。
2. 第 3 章开始解锁招募，是从个人阶段进入 MCN 阶段的关键节点。

## 5. 主角成长样例

### 5.1 `02_hero_growth_lines`

| line_id | line_name | line_type | main_bonus_type | unlock_chapter_id | sort_order |
| --- | --- | --- | --- | --- | --- |
| makeup | 化妆 | makeup | traffic | 1 | 1 |
| skill | 直播技能 | skill | commerce | 1 | 2 |
| outfit | 穿搭 | outfit | traffic | 1 | 3 |

### 5.2 `03_hero_growth_levels`：化妆线样例

| line_id | level | cost_gold | traffic_coeff | commerce_coeff | visual_stage_id |
| --- | --- | --- | --- | --- | --- |
| makeup | 1 | 0 | 1.00 | 1.00 | makeup_stage_1 |
| makeup | 2 | 25 | 1.10 | 1.00 | makeup_stage_1 |
| makeup | 3 | 55 | 1.22 | 1.00 | makeup_stage_1 |
| makeup | 4 | 100 | 1.36 | 1.00 | makeup_stage_2 |
| makeup | 5 | 170 | 1.52 | 1.00 | makeup_stage_2 |
| makeup | 6 | 280 | 1.70 | 1.00 | makeup_stage_2 |
| makeup | 7 | 430 | 1.90 | 1.00 | makeup_stage_3 |
| makeup | 8 | 650 | 2.12 | 1.00 | makeup_stage_3 |
| makeup | 9 | 950 | 2.36 | 1.00 | makeup_stage_3 |
| makeup | 10 | 1350 | 2.62 | 1.00 | makeup_stage_4 |

### 5.3 `03_hero_growth_levels`：技能线样例

| line_id | level | cost_gold | traffic_coeff | commerce_coeff | visual_stage_id |
| --- | --- | --- | --- | --- | --- |
| skill | 1 | 0 | 1.00 | 1.00 | skill_stage_1 |
| skill | 2 | 30 | 1.00 | 1.08 | skill_stage_1 |
| skill | 3 | 65 | 1.00 | 1.18 | skill_stage_1 |
| skill | 4 | 120 | 1.00 | 1.30 | skill_stage_2 |
| skill | 5 | 210 | 1.00 | 1.44 | skill_stage_2 |
| skill | 6 | 340 | 1.00 | 1.60 | skill_stage_2 |
| skill | 7 | 520 | 1.00 | 1.78 | skill_stage_3 |
| skill | 8 | 780 | 1.00 | 1.98 | skill_stage_3 |
| skill | 9 | 1120 | 1.00 | 2.20 | skill_stage_3 |
| skill | 10 | 1580 | 1.00 | 2.44 | skill_stage_4 |

### 5.4 `03_hero_growth_levels`：穿搭线样例

| line_id | level | cost_gold | traffic_coeff | commerce_coeff | visual_stage_id |
| --- | --- | --- | --- | --- | --- |
| outfit | 1 | 0 | 1.00 | 1.00 | outfit_stage_1 |
| outfit | 2 | 35 | 1.06 | 1.02 | outfit_stage_1 |
| outfit | 3 | 75 | 1.14 | 1.04 | outfit_stage_1 |
| outfit | 4 | 140 | 1.24 | 1.07 | outfit_stage_2 |
| outfit | 5 | 230 | 1.36 | 1.10 | outfit_stage_2 |
| outfit | 6 | 370 | 1.50 | 1.14 | outfit_stage_2 |
| outfit | 7 | 580 | 1.66 | 1.18 | outfit_stage_3 |
| outfit | 8 | 880 | 1.84 | 1.23 | outfit_stage_3 |
| outfit | 9 | 1280 | 2.04 | 1.29 | outfit_stage_3 |
| outfit | 10 | 1820 | 2.26 | 1.36 | outfit_stage_4 |

数值意图：

1. 化妆负责最直观的前期流量拉升。
2. 技能负责带货收益成长，是中期赚钱主轴。
3. 穿搭兼顾流量和轻微带货加成，偏视觉反馈和女性向满足感。

## 6. 直播间成长样例

### 6.1 `04_room_upgrade_categories`

| category_id | category_name | category_type | main_bonus_type | unlock_chapter_id |
| --- | --- | --- | --- | --- |
| device | 基础设备 | device | traffic | 1 |
| decor | 房间布置 | decor | traffic | 1 |
| atmosphere | 氛围包装 | atmosphere | traffic | 2 |
| commerce_device | 带货设备 | commerce_device | commerce | 2 |

### 6.2 `05_room_upgrade_levels`：基础设备样例

| category_id | level | cost_gold | traffic_coeff | commerce_coeff | room_score |
| --- | --- | --- | --- | --- | --- |
| device | 1 | 0 | 1.00 | 1.00 | 0 |
| device | 2 | 40 | 1.04 | 1.00 | 4 |
| device | 3 | 90 | 1.09 | 1.00 | 9 |
| device | 4 | 180 | 1.15 | 1.00 | 15 |
| device | 5 | 340 | 1.22 | 1.00 | 22 |
| device | 6 | 620 | 1.30 | 1.00 | 30 |

### 6.3 `05_room_upgrade_levels`：房间布置样例

| category_id | level | cost_gold | traffic_coeff | commerce_coeff | room_score |
| --- | --- | --- | --- | --- | --- |
| decor | 1 | 0 | 1.00 | 1.00 | 0 |
| decor | 2 | 30 | 1.03 | 1.00 | 3 |
| decor | 3 | 70 | 1.07 | 1.00 | 7 |
| decor | 4 | 140 | 1.12 | 1.00 | 12 |
| decor | 5 | 260 | 1.18 | 1.00 | 18 |
| decor | 6 | 460 | 1.25 | 1.00 | 25 |

### 6.4 `05_room_upgrade_levels`：氛围包装样例

| category_id | level | cost_gold | traffic_coeff | commerce_coeff | room_score |
| --- | --- | --- | --- | --- | --- |
| atmosphere | 1 | 0 | 1.00 | 1.00 | 0 |
| atmosphere | 2 | 80 | 1.02 | 1.01 | 5 |
| atmosphere | 3 | 170 | 1.05 | 1.03 | 10 |
| atmosphere | 4 | 320 | 1.09 | 1.05 | 16 |
| atmosphere | 5 | 560 | 1.14 | 1.08 | 23 |
| atmosphere | 6 | 900 | 1.20 | 1.12 | 31 |

### 6.5 `05_room_upgrade_levels`：带货设备样例

| category_id | level | cost_gold | traffic_coeff | commerce_coeff | room_score |
| --- | --- | --- | --- | --- | --- |
| commerce_device | 1 | 0 | 1.00 | 1.00 | 0 |
| commerce_device | 2 | 100 | 1.00 | 1.05 | 5 |
| commerce_device | 3 | 220 | 1.00 | 1.12 | 11 |
| commerce_device | 4 | 420 | 1.00 | 1.20 | 18 |
| commerce_device | 5 | 760 | 1.00 | 1.30 | 26 |
| commerce_device | 6 | 1200 | 1.00 | 1.42 | 35 |

### 6.6 `06_room_stage_visuals`

| stage_visual_id | stage_name | min_room_score | 说明 |
| --- | --- | --- | --- |
| room_stage_1 | 简陋直播角 | 0 | 出租屋临时直播区 |
| room_stage_2 | 整洁直播角 | 20 | 开始像样，适合章节 2 |
| room_stage_3 | 像样直播房 | 50 | 适合章节 3-4 |
| room_stage_4 | 精致工作室 | 90 | 适合章节 5 |

## 7. 公司成长样例

### 7.1 `07_company_levels`

| company_level | upgrade_cost_gold | traffic_coeff | commerce_coeff | unlock_live_slot_count | unlock_feature_type | unlock_feature_id |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 1.00 | 1.00 | 1 | none | none |
| 2 | 800 | 1.08 | 1.05 | 1 | room_category | atmosphere |
| 3 | 2200 | 1.18 | 1.12 | 2 | feature | streamer_recruit |
| 4 | 5200 | 1.30 | 1.22 | 2 | product_slot | slot_2 |
| 5 | 10500 | 1.45 | 1.35 | 3 | live_slot | slot_3 |
| 6 | 19500 | 1.60 | 1.50 | 3 | feature | chapter_5_plus |

### 7.2 `08_live_slot_unlocks`

| slot_id | unlock_company_level | slot_name | slot_type | is_default_open |
| --- | --- | --- | --- | --- |
| 1 | 1 | 主播主位 | hero_fixed | true |
| 2 | 3 | 辅助直播位 A | streamer | false |
| 3 | 5 | 辅助直播位 B | streamer | false |

## 8. 主播数值样例

### 8.1 `09_streamer_base`

| streamer_id | streamer_name | quality_type | style_tag | base_traffic | base_commerce_coeff | unlock_chapter_id |
| --- | --- | --- | --- | --- | --- | --- |
| s001 | 甜妹助理 | common | sweet | 3.5 | 1.02 | 3 |
| s002 | 元气舞担 | common | dance | 4.2 | 1.00 | 3 |
| s003 | 冷静导购 | rare | sales | 3.8 | 1.12 | 3 |
| s004 | 氛围姐姐 | rare | lifestyle | 5.0 | 1.08 | 4 |
| s005 | 美妆达人 | epic | beauty | 4.6 | 1.18 | 4 |
| s006 | 话题新星 | epic | topic | 6.0 | 1.10 | 5 |

### 8.2 `10_streamer_growth_levels` 样例模板

首版建议先按品质套模板，再复制到每位主播。

#### common 模板

| quality_type | level | cost_gold | traffic_mult | commerce_mult |
| --- | --- | --- | --- | --- |
| common | 1 | 0 | 1.00 | 1.00 |
| common | 2 | 120 | 1.12 | 1.03 |
| common | 3 | 260 | 1.26 | 1.06 |
| common | 4 | 480 | 1.42 | 1.10 |
| common | 5 | 800 | 1.60 | 1.15 |

#### rare 模板

| quality_type | level | cost_gold | traffic_mult | commerce_mult |
| --- | --- | --- | --- | --- |
| rare | 1 | 0 | 1.00 | 1.00 |
| rare | 2 | 160 | 1.14 | 1.04 |
| rare | 3 | 340 | 1.30 | 1.08 |
| rare | 4 | 620 | 1.48 | 1.13 |
| rare | 5 | 1000 | 1.68 | 1.19 |

#### epic 模板

| quality_type | level | cost_gold | traffic_mult | commerce_mult |
| --- | --- | --- | --- | --- |
| epic | 1 | 0 | 1.00 | 1.00 |
| epic | 2 | 220 | 1.16 | 1.05 |
| epic | 3 | 460 | 1.34 | 1.10 |
| epic | 4 | 820 | 1.55 | 1.16 |
| epic | 5 | 1300 | 1.80 | 1.23 |

说明：

1. 主播成长节奏建议略慢于主角成长。
2. 第一个主播最好在解锁后 2-3 分钟内就能招到。

### 8.3 `11_streamer_recruit_pool`

| pool_id | streamer_id | unlock_chapter_id | unlock_company_level | weight | recruit_cost_gold | ad_free_available |
| --- | --- | --- | --- | --- | --- | --- |
| basic_pool | s001 | 3 | 3 | 30 | 1800 | true |
| basic_pool | s002 | 3 | 3 | 25 | 1800 | true |
| basic_pool | s003 | 3 | 3 | 18 | 2400 | true |
| basic_pool | s004 | 4 | 4 | 12 | 2400 | true |
| basic_pool | s005 | 4 | 4 | 10 | 3200 | true |
| basic_pool | s006 | 5 | 5 | 5 | 3200 | true |

## 9. 商品收益样例

### 9.1 `12_product_base`

| product_id | product_name | category_type | convert_rate | profit_coeff | unlock_chapter_id |
| --- | --- | --- | --- | --- | --- |
| p001 | 发圈套装 | entry | 0.22 | 0.90 | 1 |
| p002 | 手机支架 | entry | 0.20 | 1.05 | 1 |
| p003 | 平价唇釉 | entry | 0.18 | 1.22 | 2 |
| p004 | 零食礼盒 | growth | 0.16 | 1.55 | 2 |
| p005 | 居家香薰 | growth | 0.14 | 1.95 | 3 |
| p006 | 基础护肤套组 | growth | 0.12 | 2.35 | 3 |
| p007 | 轻奢彩妆盘 | hit | 0.09 | 3.40 | 4 |
| p008 | 家电小爆品 | hit | 0.07 | 4.60 | 5 |
| p009 | 联名礼盒 | hit | 0.06 | 5.40 | 5 |

### 9.2 `13_product_slot_unlocks`

| product_slot_id | unlock_company_level | unlock_chapter_id | slot_desc |
| --- | --- | --- | --- |
| 1 | 1 | 1 | 初始商品位 |
| 2 | 4 | 4 | 第二商品位 |
| 3 | 5 | 5 | 第三商品位 |

## 10. 任务奖励样例

### 10.1 `14_task_chapter` 样例

| task_id | chapter_id | task_type | target_value | reward_gold | reward_type | reward_value |
| --- | --- | --- | --- | --- | --- | --- |
| ch1_task_01 | 1 | hero_level | 3 | 80 | gold | 80 |
| ch1_task_02 | 1 | room_level | 15 | 100 | gold | 100 |
| ch1_task_03 | 1 | sales_gold | 350 | 150 | ad_ticket | 1 |
| ch2_task_01 | 2 | hero_level | 8 | 150 | gold | 150 |
| ch2_task_02 | 2 | total_traffic | 8000 | 250 | gold | 250 |
| ch2_task_03 | 2 | sales_gold | 2500 | 300 | speed_buff | 1 |
| ch3_task_01 | 3 | company_level | 3 | 300 | gold | 300 |
| ch3_task_02 | 3 | streamer_count | 1 | 400 | recruit_ticket | 1 |
| ch3_task_03 | 3 | sales_gold | 18000 | 600 | gold | 600 |

说明：

1. `room_level` 在任务里可先理解为房间评分值或房间阶段分值。
2. `reward_type` 可统一映射为金币、加速、招募券等简单结果。

### 10.2 `15_task_daily` 样例

| task_id | task_type | target_value | reward_gold | reward_extra_type | reward_extra_value | refresh_type |
| --- | --- | --- | --- | --- | --- | --- |
| daily_01 | upgrade_count | 5 | 200 | none | 0 | daily |
| daily_02 | sales_gold | 3000 | 250 | none | 0 | daily |
| daily_03 | watch_ad | 1 | 300 | none | 0 | daily |
| daily_04 | live_count | 3 | 180 | speed_sec | 120 | daily |
| daily_05 | share_count | 1 | 150 | traffic_pack | 300 | daily |

## 11. 广告、离线与分享样例

### 11.1 `16_ad_reward_config`

说明：由于首版未设计长建造 CD，这里暂时不做“立即完成建造”，统一替换为“短时加速/翻倍收益”。

| ad_id | ad_type | reward_target_type | reward_coeff | reward_duration_sec | daily_limit | unlock_chapter_id |
| --- | --- | --- | --- | --- | --- | --- |
| ad_offline_double | double | offline_gold | 2.0 | 0 | 6 | 1 |
| ad_live_accelerate | accelerate | traffic | 2.0 | 180 | 5 | 1 |
| ad_sales_burst | double | sales_gold | 2.2 | 120 | 4 | 2 |
| ad_free_recruit | free | recruit | 1.0 | 0 | 2 | 3 |

### 11.2 `17_offline_income_config`

| id | max_offline_sec | offline_coeff | double_by_ad_coeff | unlock_chapter_id |
| --- | --- | --- | --- | --- |
| offline_default | 21600 | 0.55 | 2.0 | 1 |

### 11.3 `18_share_reward_config`

| share_id | share_type | reward_type | reward_value | daily_limit | unlock_chapter_id |
| --- | --- | --- | --- | --- | --- |
| share_poster | poster | traffic_pack | 400 | 1 | 1 |
| share_assist | assist | free_recruit | 1 | 1 | 3 |
| share_wait_skip | assist | speed_sec | 300 | 2 | 3 |

## 12. 三个关键阶段演算样例

### 12.1 开局 0-3 分钟样例

假设：

1. 主角 `makeup=1`、`skill=1`、`outfit=1`
2. 房间 `device=1`、`decor=1`
3. 公司等级 `1`
4. 当前商品使用 `p001 发圈套装`
5. 无主播

计算：

1. 主角流量 = `8 x 1.00 x 1.00 = 8.00`
2. 房间流量系数 = `1.00 x 1.00 = 1.00`
3. 公司流量系数 = `1.00`
4. 总流量/秒 = `8.00 x 1.00 x 1.00 = 8.00`
5. 带货加成系数 = `1.00 x 1.00 x 1.00 x 1.00 = 1.00`
6. 金币/秒 = `8.00 x 0.22 x 0.90 x 1.00 = 1.584`

结论：

1. 约 `13-16 秒` 可以拿到第一笔可感知升级资金。
2. 前 1 分钟可完成多次小升级，符合新手爽感目标。

### 12.2 第 2 章中段样例

假设：

1. 主角 `makeup=4`、`skill=4`、`outfit=4`
2. 房间 `device=4`、`decor=4`、`atmosphere=2`
3. 公司等级 `2`
4. 当前商品使用 `p004 零食礼盒`
5. 无主播

计算：

1. 主角流量 = `8 x 1.36 x 1.24 = 13.49`
2. 房间流量系数 = `1.15 x 1.12 x 1.02 = 1.314`
3. 公司流量系数 = `1.08`
4. 总流量/秒 = `13.49 x 1.314 x 1.08 = 19.15`
5. 带货加成系数 = `1.30 x 1.00 x 1.05 x 1.00 = 1.365`
6. 金币/秒 = `19.15 x 0.16 x 1.55 x 1.365 = 6.47`

结论：

1. 此时每分钟约 `388 金币`。
2. 第 2 章应进入“几分钟就能看到一次大提升”的节奏。

### 12.3 第 3 章解锁招募后样例

假设：

1. 主角 `makeup=5`、`skill=5`、`outfit=5`
2. 房间 `device=5`、`decor=5`、`atmosphere=3`、`commerce_device=2`
3. 公司等级 `3`
4. 当前商品使用 `p006 基础护肤套组`
5. 已上阵主播 `s001`、`s003`
6. 团队带货系数取平均值：`(1.02 + 1.12) / 2 = 1.07`

计算：

1. 主角流量 = `8 x 1.52 x 1.36 = 16.54`
2. 主播总流量 = `3.5 + 3.8 = 7.3`
3. 流量基数 = `16.54 + 7.3 = 23.84`
4. 房间流量系数 = `1.22 x 1.18 x 1.05 = 1.511`
5. 公司流量系数 = `1.18`
6. 总流量/秒 = `23.84 x 1.511 x 1.18 = 42.53`
7. 带货加成系数 = `1.44 x 1.05 x 1.12 x 1.07 = 1.81`
8. 金币/秒 = `42.53 x 0.12 x 2.35 x 1.81 = 21.81`

结论：

1. 进入第 3 章后，每分钟约 `1308 金币`。
2. 普通主播招募价 `1800-2400`，意味着招到第一个主播后，玩家仍需要 `1.5-2.5 分钟` 左右积累第二个主播，节奏是合理的。

## 13. 当前样例的总体判断

这版样例的节奏意图是：

1. 前 1 分钟连续小升级
2. 前 10 分钟完成明显个人逆袭
3. 前 20-30 分钟内解锁主播招募并进入 MCN 起步阶段
4. 广告主要用于缩短等待，而不是替代正常成长

## 14. 最建议优先调的 5 个参数

如果试玩后觉得节奏不对，建议优先调以下参数：

1. `hero_base_traffic_value`
2. `03_hero_growth_levels.cost_gold`
3. `05_room_upgrade_levels.cost_gold`
4. `12_product_base.convert_rate`
5. `16_ad_reward_config.reward_coeff`

优先原因：

1. 它们对开局手感影响最大。
2. 调这 5 组，通常就能覆盖 70% 以上的早期体验问题。