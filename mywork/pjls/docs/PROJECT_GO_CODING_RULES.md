# PatPet 项目级 Go 编码与架构规则

**版本：** 2026-03-31  
**适用范围：** `auth-service`、`core-backend`、`api-compat-service` 及其共享库  
**目标：** 将本轮整改中沉淀下来的经验，固化为以后新增功能、修复缺陷、做重构时必须遵守的限制性规则，避免同类问题反复出现。

**PR 短版检查清单：** [./PROJECT_GO_PR_CHECKLIST.md](./PROJECT_GO_PR_CHECKLIST.md)

## 1. 基本原则

- 正确性优先于“先跑起来”。
- 所有状态变更都必须是显式的、可推理的、可测试的。
- 对外契约必须稳定且强类型，不能依赖隐式约定。
- 生产环境默认 `fail closed`，不能以“静默降级后继续提供服务”为常态。
- 任何涉及并发、持久化、鉴权、支付、奖励、消息消费的路径，都必须优先从数据一致性角度设计，而不是先从接口便利性出发。

## 2. 架构规则

### AR-01 Handler 必须保持轻量

- Handler 只负责：
  - 参数解析
  - 基础校验
  - 调用 service
  - 返回响应
- Handler 不得直接承载复杂业务编排、状态流转判断、全量聚合逻辑。
- Handler 不得直接拼装持久化细节。

### AR-02 Service 负责业务语义

- 所有状态机、幂等规则、奖励规则、权限语义必须放在 service 层。
- Service 必须对“什么情况下允许更新”有清晰判断，不能只做 repository 的薄转发。
- Service 必须显式建模“成功 / 已完成 / 不存在 / 非法状态”这些业务分支。

### AR-03 Repository 只负责数据访问与原子持久化

- Repository 负责：
  - 查询
  - 原子更新
  - 事务边界内的数据写入
- Repository 不应承载业务决策，但必须提供足够细粒度的 mutation 能力，避免 service 层做 read-modify-write。

### AR-04 构造器必须显式声明依赖

- Service 构造器优先接收自己实际需要的小接口，而不是单个“大而全”的 composite repository。
- 允许保留聚合接口用于兼容或测试便利，但 service 构造器不能被 God interface 反向绑死。

### AR-05 Public API 响应必须使用强类型结构体

- 所有对外 HTTP 响应必须使用显式定义的 struct。
- 禁止在公开 API、公开 handler、middleware 错误响应中返回 `map[string]any`。
- `map[string]any` 只允许出现在极少数真正动态、结构无法预先定义的内部场景；如需使用，必须在 PR 中说明理由。

### AR-06 GET 必须保持无副作用

- `GET` 只能读，不能创建、修改、删除任何持久化状态。
- 创建 chat、order、reward、session、task 等动作必须使用 `POST` 或其他合适的写方法。

### AR-07 Debug 与 Admin 必须隔离

- Debug 路由不能挂在普通用户可访问路径上。
- 所有 debug/admin 路由必须经过显式鉴权与权限校验。
- Debug 响应不能默认暴露敏感数据，尤其是验证码、原始 token、内部密钥、底层存储快照中的危险字段。

### AR-08 Health / Ready 必须反映真实运行状态

- `/health` 可以展示状态。
- `/ready` 必须承担流量准入语义。
- 如果实例处于 degraded storage、关键依赖不可用或已知不应接流量的状态，`/ready` 必须失败。

## 3. 并发与数据一致性规则

### DC-01 禁止在可变主路径上使用 read-modify-write

- 对计数器、能量、经验、状态、奖励领取、消息消费、支付创建、旅行流转等路径：
  - 禁止先查出整条记录或整集合
  - 在内存中修改
  - 再整条或整集合写回
- 必须改为：
  - 原子 SQL 更新
  - 带行锁事务
  - CAS / affected rows 校验
  - 或显式幂等写路径

### DC-02 禁止整集合删除后重建

- 禁止对可变集合使用：
  - `DELETE all by user`
  - 再 `INSERT` 整份新列表
- 典型禁止对象：
  - friends
  - quests
  - postcards
  - notifications
  - realtime messages
  - third accounts
  - orders/items
- 必须提供细粒度增量操作，如：
  - add / remove
  - upsert one
  - mark consumed
  - append message
  - update single row

### DC-03 读路径必须是纯读

- 任何 `Get/List/Query/Read` 路径不得为了“补默认值”而写数据库。
- `ensureDefaults()` 一类逻辑只能放在：
  - bootstrap
  - 用户/实体创建时
  - 显式后台初始化任务
- 读路径不得执行 `INSERT IGNORE`、`UPSERT` 或隐式建 support row。

### DC-04 所有关键写路径必须具备幂等语义

- 以下路径必须天然幂等或显式幂等：
  - payment create
  - reward claim
  - quest complete
  - travel complete
  - logout / revoke
  - consume message
- 幂等性不能只依赖“上层调用别重复”，必须在 service 或 repository 层有保障。

### DC-05 业务唯一性必须由数据库约束兜底

- 对真正的业务唯一键，必须有数据库唯一约束。
- 不允许只靠 service 层“先查再插”假设唯一性成立。

### DC-06 需要并发正确性的路径必须补守护测试

- 任何新增的关键可变路径，如果存在并发访问可能，必须至少补一类测试：
  - repository 原子路径单元测试
  - MySQL 并发集成测试
- 以下场景默认必须考虑并发测试：
  - 计数累加
  - 奖励领取
  - 支付幂等
  - 状态流转
  - 集合 append / consume
  - 第三方绑定

### DC-07 状态机必须显式建模

- 禁止用裸字符串散落表示状态并在多处隐式流转。
- 状态必须有：
  - 常量定义
  - 合法流转规则
  - 非法流转处理
  - 幂等分支

## 4. 性能规则

### PF-01 禁止 N+1 查询

- 对列表场景：
  - 先查父记录
  - 再按父 ID 批量查子记录
  - 在内存中组装
- 禁止在循环里对每条记录再打一次数据库。

### PF-02 Fan-out 必须支持取消传播

- 并发任务工具函数必须在首个错误出现后取消兄弟任务。
- 不能在已知失败后继续无意义执行全部 goroutine。

### PF-03 Handler 中的 goroutine 只能用于真正的流式场景

- 普通 HTTP handler 默认禁止随意起 goroutine 做业务编排。
- 只有 WebSocket、SSE、长连接流式消费等场景可以保留 goroutine 结构。
- 即便保留，也必须绑定请求或连接生命周期的 context。

### PF-04 Request 路径中禁止随意使用 `context.Background()`

- HTTP 请求链路、WebSocket 收发链路、RPC 调用链路必须传递上游 context。
- 只有真正脱离请求生命周期的后台任务，才允许显式使用 `context.Background()`，且需要在注释或命名上说明原因。

### PF-05 跨服务同步依赖必须有边界

- 高频路径上，如果权限、配置、白名单等信息可以短时缓存，就必须使用有界缓存。
- 缓存必须同时具备：
  - TTL
  - 容量上限
  - 过期清理策略
- 禁止把每个 admin 请求都直接变成一次跨服务实时 HTTP 权限查询。

### PF-06 In-memory 实现也必须是并发安全的

- Memory repository 必须：
  - 加锁
  - 防御性拷贝返回值
  - 不把内部可变切片 / map 直接暴露给外部

## 5. 安全规则

### SE-01 JWT 校验必须固定签名算法

- 解析 token 时必须校验：
  - 签名算法
  - token type
  - `user_id > 0`
- 禁止只校验 secret 不校验算法。

### SE-02 Refresh token 与 access token 语义必须严格分离

- Refresh token 绝不能被 access middleware 当作业务访问 token 放行。
- 所有 token type 校验必须在边界层完成。

### SE-03 生产环境禁止危险默认值

- 禁止在生产环境依赖默认：
  - `JWT_SECRET`
  - `SEED_PASSWORD`
  - 开发账号
  - 内存仓储 fallback
- 缺配置时必须启动失败，而不是继续提供服务。

### SE-04 临时状态必须具备 TTL，生产应有外部存储

- 验证码、quick-login、token revocation 等临时状态必须带 TTL。
- 生产环境应优先使用 Redis 这类外部状态存储。
- 纯内存 fallback 只能作为受控降级，不应作为长期生产依赖。

### SE-05 Cookie 必须显式安全配置

- 鉴权 cookie 默认必须带：
  - `HttpOnly`
  - `Secure`
  - 合理的 `SameSite`

### SE-06 对外错误信息必须收敛

- 返回给客户端的错误信息要通用、稳定、低泄露。
- 详细堆栈、底层异常、内部表结构、内部鉴权细节只允许进日志，不允许直接出现在公开响应中。

### SE-07 Admin / Debug / Internal API 必须有显式权限边界

- 不能只靠“前端不会调”。
- 必须由后端权限模型强制保护。

### SE-08 存储降级必须 fail closed 或显式暴露

- 如果服务降级到内存模式，必须：
  - 要么 readiness 失败，不接流量
  - 要么在 health/debug 中明确暴露实际模式
- 禁止“表面健康、实际不持久化”的静默错觉。

## 6. 数据库与迁移规则

### DB-01 迁移必须有 ledger

- 所有 SQL 迁移都必须通过 `schema_migrations` 一类 ledger 记录执行历史。
- 禁止每次启动盲目重放全部迁移文件。

### DB-02 业务连接与迁移连接必须分离

- 只有迁移连接允许使用 `multiStatements=true`。
- 普通业务连接默认必须关闭 `multiStatements`。

### DB-03 默认数据初始化必须集中

- 默认种子数据只能在：
  - bootstrap
  - 初始化脚本
  - 创建实体时
  发生。
- 不允许在业务读路径里顺手 seed 数据库。

### DB-04 对外标识必须使用高熵 ID

- 订单号、chat id、journal id、兑换码、邀请 token、STS access key 等对外标识，必须使用高熵生成策略。
- 禁止继续使用低熵短 token 作为生产唯一标识。

## 7. Go 工程规范

### GO-01 所有提交必须过基础质量门

- 每次提交前至少通过：
  - `gofmt`
  - `go vet`
  - 相关包测试

### GO-02 接口由消费者定义，且尽量保持小而专注

- 接口只暴露当前消费方真正需要的方法。
- 如果一个 service 只需要 2 个能力，就不要为了“统一”依赖一个 12 个方法的大接口。

### GO-03 错误必须可读、可包装、可追踪

- 错误字符串默认使用小写、无句号结尾。
- 需要跨层传递时，使用 `%w` 包装上下文。
- 对外返回稳定错误，对内保留上下文。

### GO-04 禁止把 `map[string]any` 当默认返回类型

- Service / Handler / Middleware 响应默认使用 typed struct。
- 结构不确定时，优先重新思考数据建模，而不是先上 `map[string]any`。

### GO-05 公共逻辑必须抽象成可复用规则，而不是复制粘贴

- 状态机、幂等校验、分页、缓存、错误响应、健康状态等公共模式，要抽成统一规则或公共类型。

### GO-05A 跨服务出现第 2 份同构实现时，必须评估共享抽象

- 如果同一类逻辑已经在两个 service、两个 package 或两份测试里出现了同构实现，默认必须评估是否抽到共享层。
- “同构实现”包括但不限于：
  - 环境变量解析 helper
  - HTTP 错误/响应 envelope
  - 健康检查状态 envelope
  - CORS / appserver 生命周期模板
  - route-level `Bind -> invalidRequest -> ForwardX` 模板
  - 测试中的 fake server、fake closer、固定 payload builder、路径校验 server
- 如果最终不抽共享，PR 中必须说明为什么这两份实现虽然长得像，但职责边界不同，不适合共享。

### GO-05B 共享抽象只收稳定重复，不强行跨域统一

- 共享层只抽“稳定、重复、边界清楚”的逻辑。
- 不允许为了去重而把不同业务语义硬压进同一个抽象。
- `db/sqlc/*`、domain model/repository/service 结构、真正不同语义的 handler/adapter，不得因为“名字相似”被强行合并。

### GO-05C Service 本地 wrapper 只能是薄封装

- 当共享层已经承接公共逻辑后，service 内保留的 wrapper 只能负责：
  - service 特有参数
  - service 特有路径/配置
  - 兼容旧调用面
- 薄 wrapper 不得继续承载真实业务逻辑，否则视为“假去重”。
- 如果 wrapper 仍包含判断分支、状态转换、重复模板主体，应继续下沉到共享层或上移到真正的业务层。

### GO-05D 测试 scaffolding 也属于正式去重范围

- 重复出现在测试中的辅助逻辑，同样必须纳入去重范围。
- 当 fake server、fixture builder、JSON error assertion、固定 token payload、fake closer 等在两处及以上重复出现时，必须抽到：
  - package 内统一 test helper，或
  - `shared/testsupport`
- 禁止长期容忍“生产代码很整洁，测试代码到处复制”的双重标准。

### GO-06 注释要解释“为什么”，不要解释“代码正在做什么”

- 只给不明显的并发、事务、兼容性逻辑加注释。
- 不要写低信息量注释。

### GO-07 测试名称必须表达行为

- 测试名要直接说明规则，例如：
  - `...UsesAtomicRepositoryPathWhenAvailable`
  - `...RetainsConcurrentPublishes`
  - `...DoesNotWriteDefaults`

## 8. 新功能研发的强制准入规则

任何新功能在合并前，必须逐项回答下面这些问题：

- 这个接口是读还是写？如果是 `GET`，它是否真的没有副作用？
- 这个状态更新是否存在并发访问？如果有，原子性由谁保证？
- 这个写路径是否具备幂等语义？
- 这个集合更新是否在做整集合覆盖写？
- 这个读路径是否偷偷在写默认值？
- 这个对外响应是否用了强类型结构体？
- 这个 handler 是否过重，是否把业务逻辑塞进了边界层？
- 这个新接口是否会引入 N+1 查询？
- 这个跨服务依赖是否需要缓存？
- 这个临时状态是否有 TTL？
- 这个新 ID 是否有足够熵？
- 这个改动是否又复制了一份已有 helper / envelope / fake server / route 模板？
- 如果保留了 wrapper，它是否真的只是薄封装，而不是把重复逻辑换了个位置继续存在？
- 这个功能是否补了最小必要测试？

如果以上任一问题回答不清楚，默认不得合并。

## 9. 明确禁止的模式

以下模式在本项目中视为禁止写法：

- 在关键可变路径上使用 read-modify-write。
- 对可变集合执行“先删全部，再整份重建”。
- 在 `GET` 接口里创建 chat、order、reward、session 或其他持久化对象。
- 在公开响应里直接返回 `map[string]any`。
- 在请求链路里随手使用 `context.Background()`。
- 在生产临时状态中直接依赖无 TTL 的 `sync.Map`。
- 业务连接默认开启 `multiStatements=true`。
- 在读路径里执行 `ensureDefaults()` 并落库。
- 未加数据库唯一约束却假设业务天然唯一。
- 把权限检查做成每请求实时跨服务调用而无缓存。
- 在生产环境接受默认密钥、默认种子密码、默认内存仓储回退。
- 在第二处出现同构实现后，仍继续复制粘贴而不评估共享抽象。
- 用“本地 wrapper”掩盖重复主体，让真正的共享逻辑继续散落在多个文件里。

## 10. 例外机制

- 如果确实需要违反以上规则，必须在 PR 中显式说明：
  - 违反了哪条规则
  - 为什么当前场景必须例外
  - 风险是什么
  - 计划如何补偿
- 没有明确说明的例外，按不通过处理。

## 11. 推荐执行方式

以后每次做新功能、重构或大修时，默认按这个顺序推进：

1. 先做边界和状态设计。
2. 明确服务层与持久化层职责。
3. 先补测试，再写实现。
4. 对关键写路径额外做并发与幂等性检查。
5. 在合并前对照本规则做一次自检。

这份规则不是“建议参考”，而是本项目后续研发的默认约束。任何偏离都需要明确说明理由。

## 2026-04-02 补充规则

### EDGE-01 远端鉴权依赖必须有限重试并 fail-closed

- 任何远端 token introspection、权限校验或同级鉴权依赖，都必须具备“有限重试 + 断路器 + fail-closed”语义。
- 只允许对传输错误或上游 `5xx` 做有限重试，禁止无限重试。
- `401` 属于业务拒绝，不得计入断路器失败次数，也不得触发重试风暴。
- 断路器打开期间必须快速失败，避免持续打爆上游鉴权服务。

### EDGE-02 本地权限缓存必须受控

- 权限缓存必须同时具备 `TTL`、容量上限和并发 miss 合并能力。
- 禁止在 cache miss 时让同一 token 并发触发多次上游权限请求。
- 如无明确需求，不默认引入 Redis 共享权限缓存或 LRU；先保证本地缓存语义正确。

### EDGE-03 长生命周期客户端必须显式关闭

- Redis client、数据库连接、HTTP transport 或其他长生命周期 client，必须被服务持有，并在优雅退出时显式 `Close()`。
- 禁止在启动阶段创建 client 后立即丢弃引用。
- 资源关闭失败必须写结构化日志。

### EDGE-04 服务入口必须支持优雅退出

- 服务入口必须使用可取消上下文驱动 `Shutdown`，而不是只调用 `Start()`。
- 优雅退出时必须按顺序停止接流、等待请求退出、关闭外部资源。
- 新服务或新可执行入口如果没有 graceful shutdown，不得合并。

### EDGE-05 对外错误必须稳定脱敏

- 传输错误、下游依赖错误、网络错误对外只能返回稳定语义，不得直接暴露 `err.Error()`、URL、目标主机名或内部实现细节。
- 对内排障必须通过结构化日志保留上下文，而不是把原始错误透给客户端。

### EDGE-06 鉴权 Cookie 必须带明确生命周期

- 鉴权 cookie 除 `HttpOnly`、`Secure`、`SameSite` 外，还必须明确 `Max-Age` 或 `Expires`。
- 禁止把原本有明确过期语义的 access token cookie 退化成隐式 session cookie。
- 如上游无法提供可信过期时间，应 fail closed，不写 cookie。

### EDGE-07 慢消费者丢消息必须可观测

- realtime、消息 hub、广播通道如果因订阅者过慢而丢弃消息，必须输出结构化 `WARN` 日志。
- 日志至少包含主体标识、消息标识和稳定原因字段，例如 `reason=slow_subscriber_drop`。
- 禁止静默吞掉这类背压事件。

### EDGE-08 环境变量回退必须可观测

- 任意配置解析失败后如果回退到默认值，必须记录结构化日志。
- 禁止静默吞掉非法环境变量并继续运行。
- 对生产安全相关配置，优先失败而不是危险回退。
## 真实 MySQL 集成测试约定

- `core-backend` 的 MySQL 集成测试通过环境变量 `TEST_MYSQL_DSN` 启用。
- 任何涉及持久化、迁移、幂等、并发、读写纯度、钱包账本或 repository 原子语义的改动，都不能只依赖“未配置 DSN 时的 Skip”作为通过依据。
- `TEST_MYSQL_DSN` 必须使用独立测试库，禁止直接指向共享联调库或正在运行服务的业务库。
- 推荐本地格式：

```powershell
$env:TEST_MYSQL_DSN='patpet:patpet123@tcp(localhost:3306)/core_test_local?parseTime=true'
```

- 标准验证命令：

```powershell
$env:GOCACHE='G:\mywork\patpetserver\.gocache'
$env:TEST_MYSQL_DSN='patpet:patpet123@tcp(localhost:3306)/core_test_local?parseTime=true'
go test ./services/core-backend/... -count=1
go vet ./services/core-backend/...
```

- 如果某次修复只有在真实 MySQL 下才能证明正确，那么 PR 说明里必须明确写出已运行真库回归，而不是只写普通单测通过。
