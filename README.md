# MoonTV

<div align="center">
  <img src="public/logo.png" alt="LibreTV Logo" width="120">
</div>

> 🎬 **MoonTV** 是一个开箱即用的、跨平台的影视聚合播放器。它基于 **Next.js 14** + **Tailwind&nbsp;CSS** + **TypeScript** 构建，支持多资源搜索、在线播放、收藏同步、播放记录、本地/云端存储，让你可以随时随地畅享海量免费影视内容。

<div align="center">

![Next.js](https://img.shields.io/badge/Next.js-14-000?logo=nextdotjs)
![TailwindCSS](https://img.shields.io/badge/TailwindCSS-3-38bdf8?logo=tailwindcss)
![TypeScript](https://img.shields.io/badge/TypeScript-4.x-3178c6?logo=typescript)
![License](https://img.shields.io/badge/License-MIT-green)
![Docker Ready](https://img.shields.io/badge/Docker-ready-blue?logo=docker)

</div>

---

## ✨ 功能特性

- 🔍 **多源聚合搜索**：内置数十个免费资源站点，一次搜索立刻返回全源结果。
- 📄 **丰富详情页**：支持剧集列表、演员、年份、简介等完整信息展示。
- ▶️ **流畅在线播放**：集成 HLS.js & ArtPlayer。
- ❤️ **收藏 + 继续观看**：LocalStorage 存储，后续扩展 DB 存储。
- 📱 **PWA**：离线缓存、安装到桌面/主屏，移动端原生体验。
- 🌗 **响应式布局**：桌面侧边栏 + 移动底部导航，自适应各种屏幕尺寸。
- 🚀 **极简部署**：一条 Docker 命令即可将完整服务跑起来，或免费部署到 Vercel。

<details>
  <summary>点击查看项目截图</summary>
  <img src="public/screenshot.png" alt="项目截图" style="max-width:600px">
</details>

## 🗺 目录

- [技术栈](#技术栈)
- [部署](#部署)
- [配置说明](#配置说明)
- [Roadmap](#roadmap)
- [安全与隐私提醒](#安全与隐私提醒)
- [License](#license)
- [致谢](#致谢)

## 技术栈

| 分类      | 主要依赖                                                                             |
| --------- | ------------------------------------------------------------------------------------ |
| 前端框架  | [Next.js 14](https://nextjs.org/) · App Router                                       |
| UI & 样式 | [Tailwind&nbsp;CSS 3](https://tailwindcss.com/)                                      |
| 语言      | TypeScript 4                                                                         |
| 播放器    | [ArtPlayer](https://artplayer.org/) · [HLS.js](https://github.com/video-dev/hls.js/) |
| 代码质量  | ESLint · Prettier · Jest                                                             |
| 部署      | Docker · Vercel                                                                      |

## 部署

本项目支持 Vercel 和 Docker 部署，注意**不支持 Cloudflare**，后续亦无支持计划。

### Vercel 部署

> 推荐使用，零运维成本，免费额度足够个人使用。

1. **Fork** 本仓库到你的 GitHub 账户。
2. 登陆 [Vercel](https://vercel.com/)，点击 **Add New → Project**，选择 Fork 后的仓库。
3. （强烈建议）设置 PASSWORD 环境变量。
4. 保持默认设置完成首次部署。
5. 如需自定义 `config.json`，请直接修改 Fork 后仓库中该文件。
6. 每次 Push 到 `main` 分支将自动触发重新构建。

部署完成后即可通过分配的域名访问，也可以绑定自定义域名。

### Docker 部署

> 适用于自建服务器 / NAS / 群晖等场景。

#### 1. 直接运行（最简单）

```bash
# 拉取预构建镜像
docker pull ghcr.io/senshinya/moontv:latest

# 运行容器
# -d: 后台运行  -p: 映射端口 3000 -> 3000
docker run -d --name moontv -p 3000:3000 ghcr.io/senshinya/moontv:latest
```

访问 `http://服务器 IP:3000` 即可。

#### 2. docker-compose 示例

```yaml
version: '3.9'
services:
  moontv:
    image: ghcr.io/senshinya/moontv:latest
    container_name: moontv
    restart: unless-stopped
    ports:
      - '3000:3000'
    environment:
      - PASSWORD=your_password
    # 如需自定义配置，可挂载文件
    # volumes:
    #   - ./config.json:/app/config.json:ro
```

执行：

```bash
docker compose up -d
```

随后同样访问 `http://服务器 IP:3000`。

### **请勿使用 Pull Bot 自动同步**

Pull Bot 会反复触发无效的 PR 和垃圾邮件，严重干扰项目维护。作者可能会直接拉黑所有 Pull Bot 自动发起的同步请求的仓库所有者。

**推荐做法：**

建议在 fork 的仓库中启用本仓库自带的 GitHub Actions 自动同步功能（见 `.github/workflows/sync.yml`）。

如需手动同步主仓库更新，也可以使用 GitHub 官方的 [Sync fork](https://docs.github.com/cn/github/collaborating-with-issues-and-pull-requests/syncing-a-fork) 功能。

## 配置说明

所有可自定义项集中在根目录的 `config.json` 中：

```json
{
  "cache_time": 7200,
  "api_site": {
    "dyttzy": {
      "api": "http://caiji.dyttzyapi.com/api.php/provide/vod",
      "name": "电影天堂资源",
      "detail": "http://caiji.dyttzyapi.com"
    }
    // ...更多站点
  }
}
```

- `cache_time`：接口缓存时间（秒）。
- `api_site`：你可以增删或替换任何资源站，字段说明：
  - `key`：唯一标识，保持小写字母/数字。
  - `api`：资源站提供的 `vod` JSON API 根地址。
  - `name`：在人机界面中展示的名称。
  - `detail`：（可选）部分无法通过 API 获取剧集详情的站点，需要提供网页详情根 URL，用于爬取。

MoonTV 支持标准的苹果 CMS V10 API 格式。

修改后 **无需重新构建**，服务会在启动时读取一次。

## Roadmap

- [ ] DB 存储
- [ ] 深色模式

## License

[MIT](LICENSE) © 2025 MoonTV & Contributors

## 致谢

- [ts-nextjs-tailwind-starter](https://github.com/theodorusclarence/ts-nextjs-tailwind-starter) — 项目最初基于该脚手架。
- [LibreTV](https://github.com/LibreSpark/LibreTV) — 由此启发，站在巨人的肩膀上。
- [ArtPlayer](https://github.com/artplayer-org/artplayer) — 提供强大的网页视频播放器。
- [HLS.js](https://github.com/video-dev/hls.js) — 实现 HLS 流媒体在浏览器中的播放支持。
- 感谢所有提供免费影视接口的站点。
