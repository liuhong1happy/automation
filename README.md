# 运营自动化平台（automation）

中心站调度多台「运营终端」，用真实浏览器完成发帖、素材采集与线索发现；任务、日志、产物与业务数据统一回传。
本仓库只发布**终端程序**与**版本说明**，源码与开发文档不在本仓库。

**当前版本：v0.1.1**

## 组件

| 组件 | 说明 | 状态 |
|---|---|---|
| `ops-runner` | 终端守护进程（CLI）：配对/登录、心跳、长轮询取任务、执行 skill 并回传 | 已发布 |
| `ops-runner-desktop` | 终端桌面应用（内嵌本地 UI：运行时、工具、skill 清单与修复动作） | 规划中，暂未发布 |

> 终端 UI 在桌面应用发布前由 `ops-runner ui` 打开本地页面。

## 快速开始

1. 从 [Releases](https://github.com/liuhong1happy/automation/releases) 下载对应系统的 `ops-runner`，校验 `sha256`
2. 自动补齐依赖（Node / Python 运行时、sidecar 依赖）：
   ```bash
   ops-runner doctor
   ```
3. 登录注册本机（浏览器登录后自动授权注册）：
   ```bash
   ops-runner login --server https://automation.17ued.top
   ```
4. 常驻运行 / 注册为系统服务：
   ```bash
   ops-runner regist --start
   ```
5. 打开运营后台（`/admin`）建任务、看终端与线索

## 目录

| 路径 | 内容 |
|---|---|
| `CHANGE_LOG.md` | 按版本记录变更 |
| `releases/v0.1.1.md` | 版本介绍（能力、变更、已知限制） |
| Releases | `ops-runner` 二进制与校验和 |

## 版本

- v0.1.1（2026-11-22）— 终端登录注册、doctor 自举、UI 设计、Facebook 能力方案
- v0.1.0（2026-11-18）— 首个可用版本：终端接入、任务闭环、真实浏览器 skill、Nairaland 发帖

## 联系

成都洞见引擎科技有限公司 · sinoautocars@163.com
