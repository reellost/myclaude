# 微信小游戏数值策划表框架：可执行版

## 1. 文档定位

本文件用于为《[女主播逆袭与 MCN 公司成长模拟经营](G:\mywork\pjls\docs\superpowers\specs\2026-04-07-wechat-mcn-idle-game-design.md)》提供首版可执行的数值策划表框架。

目标不是直接给出全部最终数值，而是先建立一套：

1. 策划可以直接开始填表
2. 程序可以直接按表接配置
3. 美术和运营可以看懂阶段节奏

的统一结构。

## 2. 使用原则

首版数值设计遵循以下原则：

1. 资源尽量少，只保留 `流量`、`金币`、`公司等级` 三条主轴。
2. 所有成长点最终只作用于两类结果：`流量产出` 和 `带货收益`。
3. 前期节奏偏快，中期放缓，广告负责补等待感。
4. 配置表尽量平铺，不做过度嵌套结构，便于 Excel、CSV 或 JSON 管理。

## 3. 推荐表单目录

建议用一个工作簿或一个配置目录，按下面顺序建表：

1. `00_global_constants`
2. `01_chapter_config`
3. `02_hero_growth_lines`
4. `03_hero_growth_levels`
5. `04_room_upgrade_categories`
6. `05_room_upgrade_levels`
7. `06_room_stage_visuals`
8. `07_company_levels`
9. `08_live_slot_unlocks`
10. `09_streamer_base`
11. `10_streamer_growth_levels`
12. `11_streamer_recruit_pool`
13. `12_product_base`
14. `13_product_slot_unlocks`
15. `14_task_chapter`
16. `15_task_daily`
17. `16_ad_reward_config`
18. `17_offline_income_config`
19. `18_share_reward_config`

## 4. 表间关系总览

核心关系如下：

1. `02_hero_growth_lines` 定义成长线类型，`03_hero_growth_levels` 填每级数值。
2. `04_room_upgrade_categories` 定义房间成长分类，`05_room_upgrade_levels` 填每级数值。
3. `07_company_levels` 决定系统解锁、直播位解锁和章节门槛。
4. `08_live_slot_unlocks` 与 `07_company_levels` 联动，决定可上阵主播数量。
5. `09_streamer_base` 定义主播基础信息，`10_streamer_growth_levels` 定义主播成长。
6. `11_streamer_recruit_pool` 决定主播何时可被招募、通过什么池子被招募。
7. `12_product_base` 提供商品收益参数，`13_product_slot_unlocks` 决定商品位开放数量。
8. `14_task_chapter` 和 `15_task_daily` 直接引用章节、成长、收益、广告、分享行为。
9. `16_ad_reward_config`、`17_offline_income_config`、`18_share_reward_config` 挂在主循环结果之上。

## 5. 核心公式口径

首版建议统一使用以下公式口径。

### 5.1 总流量/秒

`总流量/秒 = (主角基础流量 + 主播总流量) x 房间流量系数 x 公司流量系数 x 临时加成系数`

### 5.2 带货金币/秒

`金币/秒 = 总流量/秒 x 当前商品转化率 x 当前商品利润系数 x 带货加成系数`

### 5.3 离线收益

`离线金币 = 在线金币/秒 x 离线折算系数 x 离线时长`

### 5.4 广告收益

广告收益不单独创造新资源，而是在现有结果上做：

1. 翻倍
2. 加速
3. 立即完成
4. 免费次数补充

## 6. 字段命名规范

建议统一遵循以下命名：

1. 主键字段统一使用 `id`
2. 枚举字段统一使用 `_type` 或 `_tag`
3. 等级字段统一使用 `level`
4. 章节字段统一使用 `chapter_id`
5. 解锁条件统一使用 `unlock_*`
6. 系数字段统一使用 `_coeff`
7. 固定值字段统一使用 `_value`

字段类型建议统一使用：

1. `int`：整数
2. `float`：浮点数
3. `string`：字符串
4. `bool`：布尔

## 7. 逐表框架

### 7.1 `00_global_constants`

用途：存放全局公式常量，避免把关键倍率写死在代码里。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 常量唯一 ID |
| group_type | string | 常量分组，如 `traffic`、`commerce`、`offline` |
| value | float | 常量值 |
| desc | string | 说明 |

首版建议常量：

1. `offline_income_coeff`
2. `ad_double_coeff`
3. `ad_accelerate_duration_sec`
4. `default_live_buff_coeff`
5. `starter_product_conversion`

### 7.2 `01_chapter_config`

用途：定义章节目标、章节奖励和主要解锁内容。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| chapter_id | int | 章节 ID |
| chapter_name | string | 章节名称 |
| chapter_stage_type | string | 阶段类型，如 `personal`、`mcn_start` |
| target_hero_level | int | 主角目标等级 |
| target_room_stage | int | 房间目标阶段 |
| target_company_level | int | 公司目标等级 |
| target_streamer_count | int | 目标主播数 |
| target_total_traffic | int | 累计流量目标 |
| target_sales_gold | int | 累计金币目标 |
| reward_gold | int | 章节完成奖励金币 |
| reward_unlock_type | string | 章节奖励解锁类型 |
| reward_unlock_id | string | 解锁内容 ID |

首版建议范围：

1. 章节数量 5 章
2. 第 1-2 章偏个人成长
3. 第 3 章解锁招募
4. 第 5 章形成 MCN 起步感

### 7.3 `02_hero_growth_lines`

用途：定义主角成长线类型。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| line_id | string | 成长线 ID |
| line_name | string | 成长线名称 |
| line_type | string | 如 `makeup`、`skill`、`outfit` |
| main_bonus_type | string | 主加成类型，如 `traffic`、`commerce` |
| unlock_chapter_id | int | 解锁章节 |
| sort_order | int | 排序 |

首版建议成长线：

1. `makeup`
2. `skill`
3. `outfit`

### 7.4 `03_hero_growth_levels`

用途：配置主角成长线每一级的消耗和收益。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 唯一 ID |
| line_id | string | 对应成长线 |
| level | int | 等级 |
| cost_gold | int | 升级花费金币 |
| traffic_coeff | float | 流量系数加成 |
| commerce_coeff | float | 带货系数加成 |
| visual_stage_id | string | 外观阶段 ID |
| unlock_desc | string | 解锁文案 |

首版建议范围：

1. 每条成长线 10-20 级
2. 每 4-5 级触发一次明显视觉变化
3. 前 2 章主要消耗集中在这里

### 7.5 `04_room_upgrade_categories`

用途：定义直播间成长分类。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| category_id | string | 分类 ID |
| category_name | string | 分类名 |
| category_type | string | 如 `device`、`decor`、`atmosphere`、`commerce_device` |
| main_bonus_type | string | 主加成类型 |
| unlock_chapter_id | int | 解锁章节 |

### 7.6 `05_room_upgrade_levels`

用途：定义每个房间分类的等级配置。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 唯一 ID |
| category_id | string | 房间分类 ID |
| level | int | 等级 |
| cost_gold | int | 升级花费 |
| traffic_coeff | float | 流量加成 |
| commerce_coeff | float | 带货加成 |
| stage_visual_id | string | 对应视觉阶段 |

首版建议范围：

1. 每类 8-15 级
2. 基础设备偏流量
3. 功能设备偏带货

### 7.7 `06_room_stage_visuals`

用途：定义直播间大阶段视觉变化，供美术和程序联动。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| stage_visual_id | string | 视觉阶段 ID |
| stage_name | string | 阶段名 |
| min_room_score | int | 达到该阶段的最低房间评分 |
| bg_asset | string | 背景资源 ID |
| decoration_asset | string | 装饰资源 ID |
| effect_asset | string | 氛围资源 ID |

建议首版至少 3 个大阶段：

1. 简陋直播角
2. 像样直播房
3. 精致直播空间

### 7.8 `07_company_levels`

用途：定义公司成长和系统解锁。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| company_level | int | 公司等级 |
| upgrade_cost_gold | int | 升级花费 |
| traffic_coeff | float | 公司流量加成 |
| commerce_coeff | float | 公司带货加成 |
| unlock_live_slot_count | int | 解锁直播位数量 |
| unlock_feature_type | string | 解锁功能类型 |
| unlock_feature_id | string | 解锁内容 ID |

首版建议：

1. 公司等级 1-10 级足够
2. 第 3 级左右解锁主播招募
3. 第 5 级左右解锁更多直播位

### 7.9 `08_live_slot_unlocks`

用途：定义直播位数量和承载关系。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| slot_id | int | 直播位 ID |
| unlock_company_level | int | 解锁公司等级 |
| slot_name | string | 直播位名称 |
| slot_type | string | 如 `hero_fixed`、`streamer` |
| is_default_open | bool | 是否默认开放 |

建议规则：

1. 女主占用固定主位
2. 其他位按公司等级逐步开放

### 7.10 `09_streamer_base`

用途：定义主播静态信息。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| streamer_id | string | 主播 ID |
| streamer_name | string | 主播名 |
| quality_type | string | 品质 |
| style_tag | string | 风格标签 |
| base_traffic | float | 基础流量 |
| base_commerce_coeff | float | 基础带货系数 |
| unlock_chapter_id | int | 解锁章节 |
| card_asset | string | 立绘资源 ID |

首版建议范围：

1. 6-10 名主播
2. 2-3 档品质
3. 差异主要体现在数值和人设

### 7.11 `10_streamer_growth_levels`

用途：定义主播升级成长。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 唯一 ID |
| streamer_id | string | 主播 ID |
| level | int | 等级 |
| cost_gold | int | 升级花费 |
| traffic_value | float | 该级流量值 |
| commerce_coeff | float | 该级带货系数 |

建议首版：

1. 每位主播 10-15 级
2. 升级节奏略慢于主角成长

### 7.12 `11_streamer_recruit_pool`

用途：定义主播招募池规则。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| pool_id | string | 池子 ID |
| streamer_id | string | 主播 ID |
| unlock_chapter_id | int | 解锁章节 |
| unlock_company_level | int | 解锁公司等级 |
| weight | int | 招募权重 |
| recruit_cost_gold | int | 招募花费 |
| ad_free_available | bool | 是否支持广告免费招募 |

建议首版：

1. 只做 1 个基础池即可
2. 用章节和公司等级控制投放

### 7.13 `12_product_base`

用途：定义商品基础数值。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| product_id | string | 商品 ID |
| product_name | string | 商品名 |
| category_type | string | 商品赛道 |
| convert_rate | float | 转化率 |
| profit_coeff | float | 利润系数 |
| unlock_chapter_id | int | 解锁章节 |
| sort_order | int | 排序 |

首版建议赛道：

1. 入门型
2. 成长型
3. 爆款型

### 7.14 `13_product_slot_unlocks`

用途：定义商品位解锁。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| product_slot_id | int | 商品位 ID |
| unlock_company_level | int | 解锁公司等级 |
| unlock_chapter_id | int | 解锁章节 |
| slot_desc | string | 商品位说明 |

建议首版：

1. 初始 1 位
2. 中后期最多 2-3 位

### 7.15 `14_task_chapter`

用途：定义章节任务。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| task_id | string | 任务 ID |
| chapter_id | int | 所属章节 |
| task_type | string | 任务类型 |
| target_value | int | 目标值 |
| reward_gold | int | 奖励金币 |
| reward_type | string | 奖励类型 |
| reward_value | int | 奖励值 |

任务类型建议：

1. `hero_level`
2. `room_level`
3. `company_level`
4. `streamer_count`
5. `total_traffic`
6. `sales_gold`

### 7.16 `15_task_daily`

用途：定义日常任务。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| task_id | string | 日常任务 ID |
| task_type | string | 类型 |
| target_value | int | 目标值 |
| reward_gold | int | 奖励金币 |
| reward_extra_type | string | 额外奖励类型 |
| reward_extra_value | int | 额外奖励值 |
| refresh_type | string | 刷新类型 |

建议日常任务方向：

1. 升级若干次
2. 完成若干销售额
3. 看若干次广告
4. 分享一次

### 7.17 `16_ad_reward_config`

用途：定义广告点位收益。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| ad_id | string | 广告点 ID |
| ad_type | string | 类型，如 `double`、`accelerate` |
| reward_target_type | string | 作用目标 |
| reward_coeff | float | 倍率 |
| reward_duration_sec | int | 时长 |
| daily_limit | int | 每日限制 |
| unlock_chapter_id | int | 解锁章节 |

建议首版广告点：

1. 离线双倍
2. 升级加速
3. 爆单翻倍
4. 免费招募

### 7.18 `17_offline_income_config`

用途：定义离线收益规则。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | string | 配置 ID |
| max_offline_sec | int | 最大离线累计秒数 |
| offline_coeff | float | 离线折算系数 |
| double_by_ad_coeff | float | 广告双倍系数 |
| unlock_chapter_id | int | 解锁章节 |

首版建议：

1. 最大离线 4-8 小时
2. 离线系数低于在线，但不能太低

### 7.19 `18_share_reward_config`

用途：定义分享和助力收益。

关键字段：

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| share_id | string | 分享点 ID |
| share_type | string | 类型，如 `poster`、`assist` |
| reward_type | string | 奖励类型 |
| reward_value | int | 奖励值 |
| daily_limit | int | 每日限制 |
| unlock_chapter_id | int | 解锁章节 |

建议首版奖励：

1. 宣传流量包
2. 免费招募次数
3. 等待缩短
4. 小额金币或加速

## 8. 首版建议数值节奏

### 8.1 前 10 分钟

目标：

1. 让玩家体验 3-5 次明显升级
2. 完成第一次收益回收
3. 愿意接受第一次广告

### 8.2 首日 30 分钟内

目标：

1. 完成前 2-3 章
2. 明显感受到女主和房间成长
3. 接近或刚完成主播招募解锁

### 8.3 广告节奏

目标：

1. 单日 4-8 次激励视频
2. 广告收益每次都足够明显
3. 不逼玩家连续看广告才能过主线

## 9. 推荐先填的表

如果策划准备开始填表，建议按这个顺序：

1. `00_global_constants`
2. `01_chapter_config`
3. `02_hero_growth_lines`
4. `03_hero_growth_levels`
5. `04_room_upgrade_categories`
6. `05_room_upgrade_levels`
7. `07_company_levels`
8. `09_streamer_base`
9. `10_streamer_growth_levels`
10. `12_product_base`
11. `16_ad_reward_config`

原因：

1. 先把主循环骨架定住
2. 再填中后期扩张内容
3. 最后补任务、离线和分享

## 10. 程序接表建议

程序层建议注意：

1. 所有系数字段统一按乘法处理
2. 所有成长项都不要在代码里写死
3. 解锁逻辑统一读 `chapter_id` 和 `company_level`
4. 表与表之间尽量用 ID 关联，不用中文名做主关联

## 11. 常见风险

1. 表过多但字段重复，会导致策划维护混乱
2. 公式口径不统一，会导致程序和策划算出来的收益不同
3. 广告奖励设计过强，会破坏自然成长
4. 前期数值给得太保守，会让开局无聊

## 12. 验收标准

1. 策划可以按这份文档直接开 Excel 或 CSV 表
2. 程序可以按字段名和关系开始接配置读取
3. 首版主循环的核心数值表已经覆盖完整
4. 所有关键模块都能找到对应的配置入口