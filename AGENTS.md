# CookieCloud — Agent 入口

细节只认 [DEPLOY-VPS.md](DEPLOY-VPS.md)。

## 生产路径

`/home/ubuntu/Code/VPS/CookieCloud`（生产无完整 `.git`，本机提交后 rsync overlay）。本机 `Code/VPS/CookieCloud`。

## 部署（生产唯一入口）

```bash
TAILSCALE_IP=$(tailscale ip -4) docker compose -f Docker-compose.yml up -d
# 勿覆盖 cookiecloud.local.env 与 data/
# 回滚：上一镜像 digest；不要 bootstrap；不要并进 rss stack-down
```

生产容器现为 `easychen/cookiecloud:latest`。仓内 compose 钉 `2023.01.20.16.39`；勿在未核对 digest 时把生产拉回钉 tag。

## 测试

- **single**：`docker compose -f Docker-compose.yml config` + `curl --noproxy '*' http://127.0.0.1:8088/cookiecloud/`
- **related**：Tailscale `/cookiecloud/`（要 vps_nginx + tailscaled）。扩展直连 `http://<TailscaleIPv4>:8088/cookiecloud`。

## 硬约束

- **永不**加入 `VPS_NGINX_PUBLIC_EXPOSE`。
- 只绑 `127.0.0.1:8088` + Tailscale IPv4 `:8088`。
- 勿提交 `*.env` / `*.local.env`（保留 `*.example`）。
- 禁止 `compose down -v` / `volume rm`。
