# 数值配置 CSV 模板目录

## 说明

本目录承接以下九份文档：

1. `2026-04-08-wechat-mcn-balance-table-framework.md`
2. `2026-04-08-wechat-mcn-balance-sample-values-v1.md`
3. `2026-04-08-wechat-mcn-chapter-balance-pack-v1.md`
4. `2026-04-08-wechat-mcn-chapter-progression-samples-v1.md`
5. `2026-04-08-wechat-mcn-economy-ad-simulation-v1.md`
6. `2026-04-08-wechat-mcn-offline-income-revision-v1.md`
7. `2026-04-08-wechat-mcn-ad-placement-rhythm-v1.md`
8. `2026-04-08-wechat-mcn-page-ad-popup-flow-v1.md`
9. `2026-04-08-wechat-mcn-first-10min-ad-gates-v1.md`

目标是提供可直接给策划填表、给程序接表的 CSV 模板。

## 推荐填表顺序

1. `00_global_constants.csv`
2. `01_chapter_config.csv`
3. `02_hero_growth_lines.csv`
4. `03_hero_growth_levels.csv`
5. `04_room_upgrade_categories.csv`
6. `05_room_upgrade_levels.csv`
7. `07_company_levels.csv`
8. `09_streamer_base.csv`
9. `10_streamer_growth_levels.csv`
10. `12_product_base.csv`
11. `16_ad_reward_config.csv`
12. `17_offline_income_config.csv`
13. `其余表按需补充`

## 文件说明

- `00_global_constants.csv`：全局常量
- `01_chapter_config.csv`：章节配置
- `02_hero_growth_lines.csv`：主角成长线定义
- `03_hero_growth_levels.csv`：主角成长等级表
- `04_room_upgrade_categories.csv`：直播间成长分类
- `05_room_upgrade_levels.csv`：直播间成长等级表
- `06_room_stage_visuals.csv`：直播间视觉阶段
- `07_company_levels.csv`：公司等级表
- `08_live_slot_unlocks.csv`：直播位解锁表
- `09_streamer_base.csv`：主播基础表
- `10_streamer_growth_levels.csv`：主播成长表
- `11_streamer_recruit_pool.csv`：主播招募池表
- `12_product_base.csv`：商品基础表
- `13_product_slot_unlocks.csv`：商品位解锁表
- `14_task_chapter.csv`：章节任务表
- `15_task_daily.csv`：日常任务表
- `16_ad_reward_config.csv`：广告奖励表
- `17_offline_income_config.csv`：离线收益表，当前推荐值为 `0.25`
- `18_share_reward_config.csv`：分享奖励表
- `19_chapter_progression_samples.csv`：章节逐级成长样例表
- `20_chapter_economy_ad_samples.csv`：旧版经济诊断样例表，基于离线系数 `0.55`
- `21_offline_revision_samples.csv`：离线收益改版对比样例表
- `22_ad_placement_rhythm.csv`：广告点位节奏样例表
- `23_page_ad_popup_sequences.csv`：页面流程与广告弹窗顺序样例表
- `24_first_10min_ad_gates.csv`：新手前 10 分钟广告开关样例表

## 程序接表约定

1. 所有表使用 UTF-8 编码。
2. 主键优先使用英文 ID，不用中文名做关联。
3. 所有系数字段默认按乘法解释。
4. 所有解锁逻辑优先读取 `chapter_id` 和 `company_level`。
5. 章节目标和章节任务优先以 `2026-04-08-wechat-mcn-chapter-balance-pack-v1.md` 为准。
6. 章节内回放和里程碑调试优先以 `19_chapter_progression_samples.csv` 为准。
7. 商业化收益诊断优先参考 `20_chapter_economy_ad_samples.csv`。
8. 当前离线收益正式推荐值优先以 `17_offline_income_config.csv` 和 `21_offline_revision_samples.csv` 为准。
9. 广告页面露出与频控优先以 `22_ad_placement_rhythm.csv` 为准。
10. 页面弹窗先后顺序与阻塞规则优先以 `23_page_ad_popup_sequences.csv` 为准。
11. 新手前 10 分钟广告开关优先以 `24_first_10min_ad_gates.csv` 为准。
