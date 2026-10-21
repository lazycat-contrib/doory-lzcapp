# Doory - LazyCat App

[Doory](https://github.com/shaongitbd/doory)（开源 Canva 替代：在线设计编辑器，支持实时多人协作）的懒猫微服（LazyCat）打包。

## 模式

- **镜像模式（mirror）**：`ghcr.io/shaongitbd/doory-backend` 与 `doory-frontend` 经 `ghcr.nju.edu.cn` 加速拉取，`require_digest_match: true` 校验与上游一致。
- **自动跟随上游**：每日检查上游 semver tag，发现新版本自动构建发布。
- **社区商店发布**：仅喵喵商店（community.private store），不发布官方商店。

## 架构（与上游 docker-compose.yml 一致）

| 服务 | 说明 |
| --- | --- |
| web | 前端 SPA（nginx + 反代 API/WS，:8080） |
| api | 后端 API（gunicorn，图片/PDF 导出用 Chromium） |
| realtime | 实时协作服务（uvicorn WebSocket，:8001） |
| db | PostgreSQL 16 |

`api` 容器使用 `shm_size: 512m`（Chromium 导出需要）。nginx 通过 `doory-api` / `doory-realtime` 网络别名访问后端，经 `lzc-build.yml` 的 `compose_override` 注入。

## 说明

- **数据卷**：`/lzcapp/var/doory/{pgdata,media,exports}`
- `DJANGO_SECRET_KEY`、数据库密码均为稳定随机生成
- `SITE_URL` / `CSRF_TRUSTED_ORIGINS` 自动指向懒猫子域名
- 首次访问注册账号即可使用
- 未启用：MCP 服务（`/mcp`）、AI 去背景服务、Redis（单实例无需）

## 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `APPSTORE_URL` / `APPSTORE_TOKEN` | 喵喵商店 API 地址与发布令牌 |
| `APP_ID` | 可选 |
| `PRIVATE_STORE_GROUP_CODES` | 可选，私有分组码 |

## 许可

Attribution Assurance License（上游）
