# PatPet PR 短版检查清单

版本：2026-03-31

完整规则手册见 [PROJECT_GO_CODING_RULES.md](/G:/mywork/patpetserver/docs/PROJECT_GO_CODING_RULES.md)。
这份文档只保留提 PR 前最应该快速确认的阻断项。

## 1. 合并阻断项

- [ ] 这次改动的读写边界清晰，`GET` 没有副作用。
- [ ] 关键写路径没有新的 `read-modify-write` 竞态。
- [ ] 没有引入“先删全量，再整份重建”的集合覆盖写。
- [ ] 对外响应仍然是 typed struct，没有新增 `map[string]any`。
- [ ] 没有新增跨服务复制粘贴的 helper、response envelope、route 模板或测试 scaffolding。
- [ ] 如果同构逻辑已出现第 2 份，这次要么抽到了共享层，要么在 PR 里明确说明为何不抽。
- [ ] 鉴权、权限、cookie、debug/admin 边界没有放松。
- [ ] 高风险改动已经补上对应测试，而不是只靠手工联调。

## 2. 提交前最少要跑的命令

```powershell
$env:GOCACHE='G:\mywork\patpetserver\.gocache'
go test ./services/core-backend/... ./services/auth-service/... ./services/api-compat-service/... ./shared/... -count=1
```

如果这次改动涉及大量 Go 代码或公共包，再补跑：

```powershell
$env:GOCACHE='G:\mywork\patpetserver\.gocache'
go vet ./services/core-backend/... ./services/auth-service/... ./services/api-compat-service/... ./shared/...
```

## 3. 这轮整改必须特别确认的点

- [ ] 跨服务 token 有效性仍由 `auth-service` 兜底，`core-backend` 不会只信本地 JWT 解析。
- [ ] `auth-service` 的 transient state 在非开发环境不会静默退回进程内内存。
- [ ] `auth-service` 对外错误不会泄露 MySQL、Redis 或内部实现细节。
- [ ] AI chat 的创建、读取、重试、消费都受当前用户所有权约束。
- [ ] 宠物奖励领取有服务端冷却或一次性状态，不能重复刷 XP。
- [ ] 旅行 `current` 只代表 active travel，完成或放弃后不会继续推进事件流。
- [ ] 支付商品目录读取是纯读路径，不会在运行时偷偷 seed 数据。
- [ ] apartment 保存带版本前置条件，旧快照不会静默覆盖新修改。

## 4. Launch Blocker 提醒

`CodeReview` 主线虽然已经基本收口，但货币域仍然必须单独排期和验收。上线前需要确认：

- [ ] [2026-03-31-currency-domain-implementation-plan.md](/G:/mywork/patpetserver/docs/superpowers/plans/2026-03-31-currency-domain-implementation-plan.md) 已被接受并排期。
- [ ] 如果货币域本次不上线，产品和工程负责人已经明确签字降 scope，而不是默认忽略。

## 5. Reviewer 最少要问的 5 个问题

1. 这个改动在并发下会不会丢数据、重复发奖、重复扣款或重复创建对象？
2. 这个接口是不是偷偷把读路径做成了写路径？
3. 这个变更有没有把安全边界做松，比如 token、权限、debug 或 cookie？
4. 这个返回结构是否稳定，后续客户端和测试是否能继续可靠依赖？
5. 现有测试是否真的覆盖了这次最容易出问题的地方？
## 真实 MySQL 回归约定

- 如果本次改动涉及 `core-backend` 的持久化、迁移、幂等、并发、读写纯度或账本路径，必须跑一轮真实 MySQL 回归，而不只看“未配置 `TEST_MYSQL_DSN` 时的 Skip”。
- `TEST_MYSQL_DSN` 必须指向独立测试库，不能直接复用日常联调库 `core_db`。
- 推荐格式：

```powershell
$env:TEST_MYSQL_DSN='patpet:patpet123@tcp(localhost:3306)/core_test_local?parseTime=true'
```

- 当前项目的标准真库回归命令：

```powershell
$env:GOCACHE='G:\mywork\patpetserver\.gocache'
$env:TEST_MYSQL_DSN='patpet:patpet123@tcp(localhost:3306)/core_test_local?parseTime=true'
go test ./services/core-backend/... -count=1
go vet ./services/core-backend/...
```

- 如需长期保留本机变量，可使用：

```powershell
[System.Environment]::SetEnvironmentVariable(
  'TEST_MYSQL_DSN',
  'patpet:patpet123@tcp(localhost:3306)/core_test_local?parseTime=true',
  'User'
)
```

## 2026-04-02 追加检查项

- [ ] 如果本次改动依赖远端鉴权或权限查询，是否具备有限重试、断路保护和 fail-closed 语义？
- [ ] `401` 是否仍按业务拒绝处理，而不是被错误地纳入重试或断路失败统计？
- [ ] 是否引入了新的长生命周期 client，但没有在优雅退出时显式关闭？
- [ ] 是否所有新增 cookie 都带了明确生命周期，而不是意外变成 session cookie？
- [ ] 对外返回的下游传输错误是否仍然稳定脱敏，没有泄露底层网络细节？
- [ ] cache miss 是否可能并发打爆上游，是否已经做了并发去重？
- [ ] 慢消费者丢消息、降级回退、资源关闭失败、断路器打开等边缘事件是否都有结构化日志？
- [ ] 环境变量解析失败并回退默认值时，是否能在日志中明确观测到？
- [ ] 如果这次新增了测试 helper、fake server、固定 payload builder，它们是否复用了已有 test support，而不是再复制一份？
