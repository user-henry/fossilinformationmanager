# Fossil Information Manager

化石信息托管 · 化石信息管理器

## 架构说明

本项目已从「GitHub Pages 部署时注入 config.js、前端直接携带 Token 调用 GitHub API」重构为：

```
前端 (index.html)
      │  HTTPS（不含任何密钥）
      ▼
Cloudflare Worker (fossilinformationmanager)
      │  使用环境变量 GITHUB_TOKEN
      ▼
GitHub Issues (user-henry/fossilinformationmanager, label=fossil)
```

- 前端不再依赖 `config.js`，GitHub Token 仅存于 Cloudflare Worker 的环境变量（Secret）中，不会再暴露到浏览器或仓库。
- 化石数据保存在本仓库的 Issues 区，每条化石对应一个带 `fossil` 标签的 Issue，正文以 JSON 代码块存储。

## Worker API

| 方法  | 路径                         | 说明                 |
| ----- | ---------------------------- | -------------------- |
| GET   | `/api/fossils`               | 列出所有未关闭的化石 |
| POST  | `/api/fossils`               | 新建化石（创建 Issue） |
| DELETE | `/api/fossils/:issueNumber` | 删除化石（关闭 Issue） |
| GET   | `/health`                    | 健康检查             |

请求体（POST）：`{ "id": "F-001", "name": "霸王龙化石", "description": "..." }`

Worker 地址：`https://fossilinformationmanager.nflshcchat.cc.cd`

## 本地开发与部署（wrangler）

```bash
npm install

# 设置 GitHub Token 环境变量（Secret）
npx wrangler secret put GITHUB_TOKEN

# 本地调试
npm run dev

# 部署（含自定义域名路由）
npm run deploy
```

可选环境变量：`GITHUB_USERNAME`（默认 `user-henry`）、`GITHUB_REPO`（默认 `fossilinformationmanager`）。

## GitHub Pages

`index.html` 仍通过 GitHub Pages 托管（`.github/workflows/deploy.yml`），部署流程不再生成 `config.js`。
