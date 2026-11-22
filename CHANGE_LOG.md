# 变更日志

> 只记每个版本改了什么。设计细节看 `docs/00-系统设计总览.md` 与 `docs/01-需求/`，未实现的需求看 `docs/01-需求/08-未实现需求清单.md`。

## v0.1.1 — 2026-11-22

**新增方案（文档，未开发）**
- Facebook 关注/好友人群分析 → 社媒线索：`facebook.audience_analyze`，按 dealer / importer / buyer_intent 分类，近 30 天发帖、置信度 ≥ 0.6 入线索，保留帖子/个人主页/公共主页证据链接
- Facebook 个人主页与公共主页运营：`facebook.post`、`facebook.feed_watch`、`facebook.grow`、`facebook.friend_review`；写操作采用小配额自动 + `social_action` 台账，超配额转确认卡
- 新文档 [`01-需求/13-Facebook运营与社媒线索.md`](../01-需求/13-Facebook运营与社媒线索.md)；`07-线索与商机` 增加 `fb.following_analysis` / `fb.friends_analysis` 来源，`08-未实现需求清单` 增加 H2 分组
- 详细方案：`docs/facebook-audience-lead-skill-plan.md`、`docs/facebook-profile-page-actions-plan.md`
- 产品 UI 设计：[`01-需求` 同级新增 `03-01-产品UI设计/`](../03-01-产品UI设计/README.md)，按 OpenDesign 设计系统包结构（manifest/DESIGN/tokens）交付设计与 10 个界面的可点击原型与截图；原 `03-数据库设计` 调整为 [`03-02-数据库设计`](../03-02-数据库设计/)
- 登录与账号需求：登录页「记住账号」「忘记密码」、刷新令牌 90 天（3 个月免登录）、平台管理员创建公司并开通企业管理员（`01-需求/06-系统管理与多企业.md` §3.1/§3.2/§4.5）
- 终端接入简化：`ops-runner login` 浏览器登录即注册，MAC 指纹（只存哈希）作为设备唯一键；旧 `pair` 保留兼容（`01-需求/01-终端接入与配对.md` §3.1.1）
- 终端自举：`ops-runner doctor` 自动安装 Node / Python 运行时与 sidecar 依赖，幂等、免 sudo、校验 sha256（§3.8）
- 终端 UI 改为桌面应用：`ops-runner ui` 默认打开 Tauri 外壳，失败回退浏览器（`01-需求/09-终端UI设计.md` §2）
- 新增 `ops-runner upgrade`：统一检测/升级 CLI 与 UI，支持 `--check`、`--channel`、非交互（§3.7）
- 注册 key / 配对码停用并移入「历史」：新设备统一走 `ops-runner login`，历史记录只读可吊销（`01-需求/06` §5）
- 路由：`/` 改为项目介绍官网，运营后台统一 `/admin`；设备授权页 `/device/authorize` 单独约定（`01-需求/14-官网首页与路由.md`）

**变更**
- runner / server / web 版本号统一为 `0.1.1`
- 线索落库确定走 automation 新增 `social_person` / `social_post` / `social_lead` / `social_action`，不复用 saas-server 的 `t_buy_clue`
- 文档入口、根 README 同步 v0.1.1 状态，新增 [`v0.1.1` 存档](v0.1.1/README.md)

**运维**
- 生产环境接入真实运营终端 `mac-mini-01`：公司注册 key + 一次性配对码，LaunchAgent `com.sinoautocars.ops-runner.46e86c2d` 常驻
- 排障：LaunchAgent 需要补 `OPS_SIDECAR_DIR`、`OPS_NODE_BIN`、`PATH`，否则 sidecar 技能状态为 unknown、渠道显示「无就绪终端」

**已知限制**
- Facebook 相关 skill 均未开发；`facebook.group_post`、`autosandcartalk.post` 仍无实现，渠道灰显
- 目前只有 Nairaland 渠道具备就绪终端；Facebook 账号 `facebook-main` 未登录
- 写操作配额与风控阈值需上线后按实测调整

## v0.1.0 — 2026-11-18

**新增能力**
- 终端接入：配对码 + 公司注册 key、心跳、在线状态、系统服务注册
- 任务闭环：建任务 → 长轮询投递 → 执行 → 回传；fencing、租约、取消、跨公司隔离
- 真实浏览器 skill：`nairaland.board`（版块采集）、`vehicle.collect`（车辆素材 + 图片落盘）
- 发帖：`nairaland.post`（人工登录、dryRun、图片附件、编辑已有帖、LLM 文案）
- 发布幂等：同一 VIN 只能成功发一次；`post_unknown` / `need_human` → 待人工复核
- 任务重试：失败/超时任务一键重新下发，保留原任务历史
- 终端安装清单：运行时/本地工具/skills 上报，后台可查
- 终端 UI：`ops-runner ui` 打开本地页面查看三张清单
- 编排与对话：pipeline 解析内核、车辆发现内核、`/agent` 对话页（默认 mock）

**变更**
- 数据库迁移收敛为两个文件：`0001_schema.sql` + `0002_seed_dev.sql`
- 前端流水线 mock 开关统一为 `VITE_PIPELINE_MOCK`
- Cloudflare 挑战默认等 100 秒再交回人工（`need_human`）；实测约 20 秒放行

**修复**
- 图片下载白名单漏掉站点 CDN 导致图片数为 0
- 任务列表缺列导致的 500
- 跨租户去重漏洞（唯一索引缺 `company_id`）

**已知限制**
- 定时调度、pipeline 执行器、LLM 意图解析未实现
- 真站发帖不在自动回归内（会自动向 Nairaland 发帖）
- Cloudflare 放行凭据会过期，过期后需人工在终端过一次验证
