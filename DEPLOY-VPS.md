# CookieCloud VPS 部署

独立于 RSS 栈；勿并入 `stack-down` 生命周期。

## 约束

- 监听：`127.0.0.1:8088`（禁止公网裸露）
- 访问：Tailscale 经 vps_nginx **`/cookiecloud/`**（永不加入 `VPS_NGINX_PUBLIC_EXPOSE`）
- 密钥：`cookiecloud.local.env`（gitignore）

Hub 镜像无 semver；compose 钉 `easychen/cookiecloud:2023.01.20.16.39`（与 Hub `:latest` 同 digest，避免漂移）。勿把 `api/` `ext/` `web/` 上游树当运行时源。

本机无 Tailscale 时：`docker compose -f Docker-compose.local.yml up -d`（只绑 `127.0.0.1:8088`）。

## 部署

```bash
# 本机 → VPS（路径以实际为准）
rsync -av --exclude data --exclude .git --exclude node_modules \
  ./ ubuntu@xiaolitongxue.com.cn:/home/ubuntu/Code/VPS/CookieCloud/

ssh ubuntu@xiaolitongxue.com.cn
cd /home/ubuntu/Code/VPS/CookieCloud
cp -n cookiecloud.local.env.example cookiecloud.local.env
# 填入 COOKIE_CLOUD_UUID / COOKIE_CLOUD_PASSWORD
docker compose -f Docker-compose.yml up -d
curl --noproxy '*' -sS -o /dev/null -w '%{http_code}\n' http://127.0.0.1:8088/cookiecloud/
```

扩展 Server 地址：`http://<TailscaleIPv4>:8088/cookiecloud`（当前生产：`http://100.74.201.82:8088/cookiecloud`）。  
**勿**用 `:80` 的 `/cookiecloud`（Chrome gzip POST 易 408）。

UUID/密码：VPS `cookiecloud.local.env`（`chmod 600`）。

## 与 RSS 桥接

见 rss 仓：`scripts/sync-cookies-from-cookiecloud.sh`、`docs/agent/E2E-AND-COOKIES.md`。
