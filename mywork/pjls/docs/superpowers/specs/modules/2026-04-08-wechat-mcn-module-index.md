# 微信小游戏模块策划文档总索引

## 1. 说明

本目录用于承接《[女主播逆袭与 MCN 公司成长模拟经营](G:\mywork\pjls\docs\superpowers\specs\2026-04-07-wechat-mcn-idle-game-design.md)》的模块级策划拆分。

文档目标：

1. 把总需求文档拆成可单独讨论、评审、制作的模块 PRD。
2. 让策划、美术、程序可以按模块并行推进。
3. 保持与《[MVP 排期计划](G:\mywork\pjls\docs\superpowers\plans\2026-04-08-wechat-mcn-mvp-schedule-plan.md)》中的 P0 任务一一对应。

## 2. 模块清单

1. [模块 01：开场剧情与新手引导](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-01-opening-onboarding-prd.md)
2. [模块 02：主角成长系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-02-hero-growth-prd.md)
3. [模块 03：直播间成长系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-03-room-growth-prd.md)
4. [模块 04：开播产流系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-04-live-traffic-prd.md)
5. [模块 05：带货收益系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-05-commerce-revenue-prd.md)
6. [模块 06：公司成长与章节推进系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-06-company-progression-prd.md)
7. [模块 07：招募主播系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-07-streamer-recruitment-prd.md)
8. [模块 08：任务与奖励系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-08-tasks-rewards-prd.md)
9. [模块 09：广告与离线收益系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-09-ads-offline-prd.md)
10. [模块 10：分享裂变系统](G:\mywork\pjls\docs\superpowers\specs\modules\2026-04-08-module-10-share-fission-prd.md)

## 3. 使用顺序建议

建议评审顺序如下：

1. 先评审模块 01-05，确认前期个人逆袭闭环。
2. 再评审模块 06-07，确认后期 MCN 扩张闭环。
3. 最后评审模块 08-10，确认商业化、回流和裂变能力。

## 4. 依赖关系

关键依赖如下：

1. 模块 01 依赖模块 02、03、04、05 的最小可用版本。
2. 模块 02 和模块 03 是模块 04 的主要产流输入。
3. 模块 04 是模块 05 的收益输入。
4. 模块 05 产出的金币反向驱动模块 02、03、06、07。
5. 模块 06 解锁模块 07 的更多内容。
6. 模块 08、09、10 挂靠在 01-07 的主循环之上，不应反客为主。

## 5. 首版优先级

P0 模块优先级建议：

1. 第一优先级：01、02、03、04、05
2. 第二优先级：06、07
3. 第三优先级：08、09、10

## 6. 模块文档统一约定

各模块文档统一包含以下内容：

1. 模块定位
2. 设计目标
3. 玩家体验目标
4. 功能范围
5. 核心规则
6. 页面与交互
7. 配置建议
8. MVP 边界
9. 验收标准