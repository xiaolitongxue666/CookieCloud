# CookieCloud — 项目记忆（压缩）

本仓为 easychen/CookieCloud 部署副本；生产在 VPS `/home/ubuntu/Code/VPS/CookieCloud`。

## 约束

1. 监听：`127.0.0.1:8088` + `${TAILSCALE_IP}:8088`（`TAILSCALE_IP=$(tailscale ip -4)`）。
2. `API_ROOT=/cookiecloud`；密钥仅 `cookiecloud.local.env`（gitignore）。
3. **勿**公网暴露 8088（靠 UFW 未放行）；Nginx `/cookiecloud/` 仅 Tailscale，且扩展上传勿依赖它。
4. 与 RSS 栈独立 compose，勿并入 `stack-down`。

## 扩展配置（生产）

- Server：`http://100.74.201.82:8088/cookiecloud`
- 域名关键词建议：`bilibili.com` + `x.com`
- 验收：rss 仓 `verify-cookiecloud-sync.sh`

## 踩坑

- Chrome 经 Nginx `:80` gzip POST → **408**；直连 `:8088` 可写成功（扩展 UI 仍可能误报失败）。
- 镜像 `easychen/cookiecloud:latest`（2023）无请求体 gzip 中间件问题已用直连规避。

_更新：2026-07-20_
